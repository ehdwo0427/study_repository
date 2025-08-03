상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# 🧪 SDXL LoRA Unload Test

Colab 환경에서 Stable Diffusion XL(SDXL)에 두 개의 LoRA를 적용한 후,  
특정 LoRA만 해제(delete)하는 기능을 테스트한 결과입니다.

---

## 1️⃣ 설치 및 임포트

```bash
!pip install diffusers transformers safetensors accelerate -U

import torch, gc, os, psutil
from diffusers import StableDiffusionXLPipeline
from safetensors.torch import load_file
from huggingface_hub import hf_hub_download
```

---

2️⃣ 메모리 사용량 확인 함수

```bash
def print_memory(tag=""):
    print(f"\n🔍 [메모리 - {tag}]")
    print(f"CUDA Allocated: {torch.cuda.memory_allocated() / 1024 ** 2:.2f} MB")
    print(f"RAM Used: {psutil.virtual_memory().used / 1024 ** 3:.2f} GB")
```

---

3️⃣ SDXL 파이프라인 로드
```bash
print_memory("Start")

pipe = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    torch_dtype=torch.float16,
    variant="fp16",
).to("cuda")

print_memory("After SDXL Load")

출력 예시:

🔍 [메모리 - Start]
CUDA Allocated: 0.00 MB
RAM Used: 1.83 GB

🔍 [메모리 - After SDXL Load]
CUDA Allocated: 6724.67 MB
RAM Used: 2.00 GB
```

---

4️⃣ LoRA 2개 적용
```bash
# 다운로드
papercut = hf_hub_download("TheLastBen/Papercut_SDXL", "papercut.safetensors")
lcm = hf_hub_download("latent-consistency/lcm-lora-sdxl", "pytorch_lora_weights.safetensors")

# 적용
pipe.load_lora_weights(papercut, adapter_name="papercut")
pipe.load_lora_weights(lcm, adapter_name="lcm")

📌 경고 메시지 (무시 가능):

No LoRA keys associated to CLIPTextModel... (이 경고는 무시해도 됩니다)
```

---


5️⃣ 어댑터 목록 확인 및 제거 테스트

```bash
print(pipe.get_list_adapters())
# 출력: {'unet': ['papercut', 'lcm']}

pipe.delete_adapters("papercut")

print(pipe.get_list_adapters())
# 출력: {'unet': ['lcm']}
```

---

✅ 결과 요약

항목	내용
SDXL 로드 후 CUDA 사용량	약 6.7 GB
적용한 LoRA	papercut, lcm
delete_adapters(\"papercut\") 후	lcm만 남음
결과	특정 LoRA 정상적으로 제거됨 ✅


---

- 참고
	•	pipe.get_list_adapters() 로 현재 적용된 LoRA 목록을 확인할 수 있습니다
	•	pipe.delete_adapters("name") 으로 특정 어댑터만 제거 가능
	•	제거 후 torch.cuda.empty_cache() 호출 시 VRAM도 해제됩니다

---

- 관련 리소스
	•	[TheLastBen/Papercut_SDXL (Hugging Face)](https://huggingface.co/TheLastBen/Papercut_SDXL)
	•	[latent-consistency/lcm-lora-sdxl](https://huggingface.co/latent-consistency/lcm-lora-sdxl)
	•	[Diffusers Docs – LoRA](https://huggingface.co/docs/diffusers/using-diffusers/lora)





# 이 방법이 안되서 다른 방법으로 해결
## pipe.unload_all_lora() 써서 전체적으로 지워주고, 다시 할당해줌.(OOM 해결 완료)
