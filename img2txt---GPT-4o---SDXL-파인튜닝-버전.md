상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# img2txt + GPT 4o + SDXL 파인튜닝 버전
- colab에서 진행 L4 고용량 RAM 사용하였는데 T4도 가능해보임
## img2txt + GPT 4o
```python
# 2. 라이브러리 임포트
import torch
from PIL import Image
from transformers import Blip2Processor, Blip2ForConditionalGeneration
import openai

# 3. API 키 설정
client = openai.OpenAI(api_key="OPENAI_API_KEY")  # 🔐 당신의 OpenAI 키를 입력하세요

# 4. BLIP2 모델 로드
processor = Blip2Processor.from_pretrained("Salesforce/blip2-flan-t5-xl")
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-flan-t5-xl",
    torch_dtype=torch.float16,
    device_map="auto"
)

# 5. 이미지 로드
image = Image.open("desk.jpg").convert("RGB")  # 🔄 경로 수정

# 6. 캡션 생성 (BLIP2)
inputs = processor(images=image, return_tensors="pt").to("cuda", torch.float16)
generated_ids = model.generate(**inputs, max_new_tokens=50)
caption = processor.tokenizer.decode(generated_ids[0], skip_special_tokens=True)

print(f"📝 [BLIP2 Caption]: {caption}")

# GPT-4o 프롬프트 생성
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a creative prompt engineer for image generation."},
        {"role": "user", "content": f"Make this into a vivid prompt for image generation: {caption}"}
    ],
    temperature=0.8,
    max_tokens=70
)

# ✅ 객체 속성 방식으로 접근
generated_prompt = response.choices[0].message.content
print(f"🎨 [GPT-4o Prompt]: {generated_prompt}")
```

- **output**
```jsons
📝 [BLIP2 Caption]: a monitor screen is shown on a desk
🎨 [GPT-4o Prompt]: Create a detailed and visually striking scene of a modern, high-tech workspace. Center the composition around a sleek, ultra-wide monitor screen displaying vibrant graphics. The monitor is positioned on a minimalist, polished wooden desk. Surround the screen with subtle reflections and ambient lighting that highlights the contours of the monitor. Add small, realistic details such as a wireless keyboard and
```

- VRAM : 8215MiB 사용

- memory 비워주기
```python
import gc
import torch

# 모델 삭제
del model
del processor
del inputs
del generated_ids

# 가비지 컬렉션 + 캐시 비우기
gc.collect()
torch.cuda.empty_cache()
```

## SDXL 파인튜닝
### base 모델
```python
base_model_path = "fluently/Fluently-XL-Final"
vae_path = "/content/drive/MyDrive/Lora_test/sdxl_vae_madebyollin.safetensors"
```

### 파이프라인 설정
```python
from diffusers import DiffusionPipeline
# 파이프라인 설정
pipe = DiffusionPipeline.from_pretrained(
    base_model_path,
    torch_dtype=torch.float16,
    # variant="fp16",
    use_safetensors=True,
)
pipe.to("cuda")

if vae_path and os.path.exists(vae_path):
    pipe.vae = AutoencoderKL.from_single_file(
        vae_path, torch_dtype=torch.float16
    ).to("cuda")
```

### 파인튜닝
```python
# lora 설정
# pipe.load_lora_weights("lora경로", weight_name="defalut", adapter_name="lora 이름 하고 싶은거")
pipe.load_lora_weights("/content/drive/MyDrive/Lora_test/dalle-3-xl-lora-v2.safetensors", weight_name="default", adapter_name="base_lora")
pipe.load_lora_weights("/content/drive/MyDrive/Lora_test/ott_fluently.safetensors", weight_name="default", adapter_name="ott_lora")
pipe.load_lora_weights("/content/drive/MyDrive/Lora_test/3D_Office.safetensors", weight_name = "default", adapter_name="3d_officer")


# lora 어떻게 설정할건지, 여러 lora를 한 번에 테스트 하기위해 dict 구조로 설정
lora_settings = {
    "base_only": (["base_lora"], [1.0]),
    "ott_only": (["ott_lora"], [1.0]),
    "3d_only": (["3d_officer"], [1.0]),
    "base_ott": (["base_lora", "ott_lora"], [0.7, 0.5]),
    "base_3d": (["base_lora", "3d_officer"], [0.7, 0.5]),
    "ott_3d": (["ott_lora", "3d_officer"], [0.7, 0.5]),
    "mix": (["base_lora", "ott_lora", "3d_officer"], [0.6, 0.4, 0.4]),
}
```

### img2txt에 캡션 내용에 prompt 추가
- 글자수 제한으로 짤림(추후 업데이트해보면서 진행할 예정)
```jsons
generated_prompt = Create a detailed and visually striking scene of a modern, high-tech workspace. Center the composition around a sleek, ultra-wide monitor screen displaying vibrant graphics. The monitor is positioned on a minimalist, polished wooden desk. Surround the screen with subtle reflections and ambient lighting that highlights the contours of the monitor. Add small, realistic details such as a wireless keyboard and
```

- 원치않는 이미지 생성 캡션
```jsons
negative_prompt = "blurry, low quality, noisy, distorted, deformed, bad proportions, text, watermark, messy, cluttered background, cartoon, anime, painting, sketch"
```

### 이미지 생성
```python
# 이미지 생성
# lora_settings에 있는 모든 설정 다 한 번씩 생성
# 이미지 파일 이름은 key값
for setting_name, (lora_names, lora_weights) in lora_settings.items():
    print(f"\n=== {setting_name.upper()} ===")

    # LoRA 세팅
    pipe.set_adapters(lora_names, adapter_weights=lora_weights)

    if torch.cuda.is_available():
        torch.cuda.reset_peak_memory_stats()

    start_time = time.time()

    process = psutil.Process(os.getpid())

    # Step 1: Low resolution 생성
    low_res_image = pipe(
        prompt=generated_prompt,
        negative_prompt=negative_prompt,
        num_inference_steps=40,
        guidance_scale=7.5,
        width=768,
        height=512
    ).images[0]

    # Step 2: 업샘플 (High Resolution)
    low_res_image = low_res_image.resize((1152, 768), Image.LANCZOS)

    pipe.enable_vae_tiling()

    high_res_image = pipe(
        prompt=generated_prompt,
        negative_prompt=negative_prompt,
        image=low_res_image,
        strength=0.45,
        num_inference_steps=30,
        guidance_scale=7.0
    ).images[0]

    end_time = time.time()

    # 저장
    image_path = os.path.join(output_dir, f"{setting_name}.png")
    high_res_image.save(image_path)

    # 성능 측정
    if torch.cuda.is_available():
        peak_vram = torch.cuda.max_memory_allocated() / (1024 ** 2)  # MB 단위
        print(f"이미지 생성 소요 시간: {end_time - start_time:.2f}초")
        print(f"최대 VRAM 사용량: {peak_vram:.2f} MB")
    else:
        print("CUDA 사용 불가: GPU를 찾을 수 없습니다.")
```

### 생성된 이미지
| BASE_ONLY | OTT_ONLY | 3D_ONLY | BASE_OTT | BASE_3D | OTT_3D | MIX |
|--|--|--|--|--|--|--|
|![base_only](https://github.com/user-attachments/assets/af568760-d98a-4fbf-8628-7ada5981178f)|![ott_only](https://github.com/user-attachments/assets/001529f8-6039-46a2-bb58-7d767820afee)|![3d_only](https://github.com/user-attachments/assets/b6eedec4-182e-425b-a6f8-804f3b6880ef)|![base_ott](https://github.com/user-attachments/assets/e88cc68c-ef04-483c-ac94-014d097804b4)|![base_3d](https://github.com/user-attachments/assets/dba9632e-67c0-4dfb-86cb-46835eb2bcab)|![ott_3d](https://github.com/user-attachments/assets/0f64c6ce-ec67-46e6-bafa-e53d154e5ac3)|![mix](https://github.com/user-attachments/assets/96061e23-b179-4aba-bb59-54aab8e9d423)|


colab : [OTT_multi_modal_ai_test_v1](https://colab.research.google.com/drive/1wB6UdLQTLwUIomIvl5SHK_8xk-joFJFQ?usp=sharing)