# 최종 서비스 아키텍처 다이어그램
## 전체 아키텍처
![image](https://github.com/user-attachments/assets/e06040a8-878b-4156-a48a-7c7fd8c34346)
<br/>
## AI 아키텍처
![image](https://github.com/user-attachments/assets/3b501e22-f05c-4ad9-806f-5598d79da9e9)
<br/>
<br/>
<br/>

# 단계별 설계 적용 결과 요약표
## Classify 단계
- Base Model: VGG16(weight = imageNet)
- Base Model을 기반으로 True, False 이진 분류로 설계
- 성능: A100 - 약 42ms, CPU - 약 175ms
- CPU 성능으로도 분류하는데, 큰 무리가 없으므로 추후에 Backend에 올려서 사용할 가능성 있음
<br/>

## Image to Text 단계
- Base Model : Blip-image-captioning-base [Link](https://huggingface.co/Salesforce/blip-image-captioning-base)
- 이미지에서 캡션을 꺼내서 프롬프트를 만들어주는 방식
- VRAM -> 1.2GB 사용
<br/>

## Text to Image 단계
- Base Model: Fluently-XL-v2
- SDXL 파인튜닝 모델로 이미지 생성 테스트시 조금 더 나은 성능을 보여주어 해당 모델로 설정 [Link](https://huggingface.co/fluently/Fluently-XL-v2)참조
- 데스크 셋업 이미지 366장으로 학습한 서비스 특화 LoRA 사용 (Kohya_ss_LoRA_trainer_XL로 학습 [Link](https://github.com/Linaqruf/kohya-trainer)참조)
- 성능: T4 - 약 26s, L4 - 약 12s, A100 약 4s
- 사용 VRAM: 8.7GB ~ 11GB
<br/>
<br/>
<br/>

# 성능 평가

## Test 1
- Prompt: "A clean, modern desk setup in a white-toned minimalist room with an ultrawide monitor and a neat mechanical keyboard, photorealistic, ultra high resolution."
- Negative Prompt: "blurry, low quality, noisy, distorted, deformed, bad proportions, text, watermark, messy, cluttered background, cartoon, anime, painting, sketch"

||dalle_only|ott_only|3d_only|dalle_ott|dalle_3d|ott_3d|mix|
|--|--|--|--|--|--|--|--|
|image|![base_only](https://github.com/user-attachments/assets/9a2d9b5f-cffb-462b-88f5-9efc563c22a3)|![ott_only](https://github.com/user-attachments/assets/07a760a2-750b-41ca-8f66-83ffd85bb133)|![3d_only](https://github.com/user-attachments/assets/97c1ba1a-ec96-42b1-b128-cbe300768e95)|![base_ott](https://github.com/user-attachments/assets/4352ee80-1994-44db-833a-49d29d16b3fa)|![base_3d](https://github.com/user-attachments/assets/6b364db9-67db-41c6-9d14-774523614d95)|![ott_3d](https://github.com/user-attachments/assets/1917ad75-6a0b-48f3-820b-d5d06aba276e)|![mix](https://github.com/user-attachments/assets/61f04444-b9a1-495a-8f92-8c590ca3065c)|
|VRAM|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|
<br/>

## Test 2
- Prompt: "A clean modern desk setup with a dark wooden desk, a large monitor, a white mini PC, potted plants, a wireless ergonomic mouse, a mechanical keyboard with colorful keycaps on a felt mat, natural daylight, cozy and minimalistic."
- Negative Prompt: "blurry, low quality, noisy, distorted, deformed, bad proportions, text, watermark, messy, cluttered background, cartoon, anime, painting, sketch"

||dalle_only|ott_only|3d_only|dalle_ott|dalle_3d|ott_3d|mix|
|--|--|--|--|--|--|--|--|
|image|![base_only-2](https://github.com/user-attachments/assets/ea7822ca-9f79-4b4d-b7c6-f2b770d4f81e)|![ott_only-2](https://github.com/user-attachments/assets/f0a825ba-22aa-44a4-b893-5efec790a4db)|![3d_only-2](https://github.com/user-attachments/assets/3b1ecd56-a627-4660-a430-0f7dffc9ed9c)|![base_ott-2](https://github.com/user-attachments/assets/bea3f1b1-112f-490e-8acd-21900c406141)|![base_3d-2](https://github.com/user-attachments/assets/0cca8ceb-09c6-4b12-9db8-0be0c4b5b278)|![ott_3d-2](https://github.com/user-attachments/assets/29aaaa95-9235-465d-b755-0a97ad967749)|![mix-2](https://github.com/user-attachments/assets/5b773d33-16fa-4c20-aded-45eb657b6e20)|
|VRAM|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|
<br/>

## Test 3
- text = "On the Desk" -> on the desk is a laptop and a monitor
- output = a desk with a laptop and a monitor
- prompts = [
    "This is a photo of",<br/> 
    "Q: What objects are on the desk? A:",<br/> 
    "desk_setup:"
]
- [This is a photo of] → this is a photo of a desk full of computers
<br/> [Q: What objects are on the desk? A:] → q : what objects are on the desk? a : laptop, a keyboard, and a monitor
<br/> [desk_setup:] → desk _ setup : _ with a laptop, monitor, keyboard, mouse, and mouse
- 중요도가 높은 키워드일수록 여러번 나옴
<br/>


### LoRA Settings
- dalle_only: Dalle(1.0)
- ott_only: ott(1.0)
- 3d_only: 3d(1.0)
- dalle_ott: dalle(0.7), ott(0.5)
- dalle_3d: dalle(0.7), 3d(0.5)
- ott_3d: ott(0.7), 3d(0.5)
- mix: dalle(0.6), ott(0.4), 3d(0.4)
<br/>
<br/>
<br/>

# 프로젝트 수행에 대한 회고 및 평가
- 처음 원하는 퀄리티가 ChatGPT 4o → Dall-e3 였어서 설계단계와 모델 테스트 중에 원하는 퀄리티가 나오지 않아 걱정
- 퀄리티는 그 정도 수준으로 아주 준수하지는 않지만 LoRA를 이용해 Fine tuning하고 Pretrained 모델을 적절히 이용하면서 최대한 퀄리티를 끌어올림
- 현재 유행하고 있는 이미지 생성 모델의 전반적인 구조 학습 및 LoRA 원리, 모델 양자화 원리 등을 학습
- 타 프로젝트와 달리 Local 수준에서 모델이 돌아가는 것이 아닌 서버 수준에서 돌아가기 때문에 파이프라인에 대한 학습
<br/>
<br/>

# 앞으로의 개선 제안 및 계획
- Dall-e3 수준의 퀄리티를 낼 수 있도록 지속적으로 모델 Fine tuning
- 생성된 이미지에 추천한 상품의 좌표
- 요일별 혹은 추천 컨셉에 맞는 이미지 생성
<br/>
<br/>

# 결론
- 서비스 특화 LoRA인 OTT LoRA와 3D Office LoRA를 사용하는 것이 해당 서비스에 알맞은 것으로 판단된다. 해당 LoRA를 기반으로 좀 더 Fine tuning하거나 가중치를 바꿔가며 적절히 섞어 사용하는 것이 합리적으로 보인다.
- Img2txt 관련 기법이 여러가지가 있는데, 추후에 벡터 DB 사용 여부에 대해서 생각해볼 필요가 있을 것으로 예상된다.