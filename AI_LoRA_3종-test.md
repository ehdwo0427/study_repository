상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

## LoRA 소개

### OTT LoRA
- 데스크 셋업 이미지 366장으로 학습한 우리 서비스 특화 LoRA
- Kohya_ss_LoRA_trainer_XL로 학습 [Link](https://github.com/Linaqruf/kohya-trainer)참조
- Base Model: Fluently-XL-v2, SDXL 파인튜닝 모델로 이미지 생성 테스트시 조금 더 나은 성능을 보여주어 해당 모델로 설정 [Link](https://huggingface.co/fluently/Fluently-XL-v2)참조

### 3D_Officer
- CivitAI에 제공된 SDXL 기반 LoRA, [Link](https://civitai.com/models/1378780/3d-office)참조

### Dalle-3-xl-v2
- SDXL기반 LoRA, [Link](https://huggingface.co/ehristoforu/dalle-3-xl-v2)참조

### LoRA Settings
- dalle_only: Dalle(1.0)
- ott_only: ott(1.0)
- 3d_only: 3d(1.0)
- dalle_ott: dalle(0.7), ott(0.5)
- dalle_3d: dalle(0.7), 3d(0.5)
- ott_3d: ott(0.7), 3d(0.5)
- mix: dalle(0.6), ott(0.4), 3d(0.4)

## Test 1
- Prompt: "A clean, modern desk setup in a white-toned minimalist room with an ultrawide monitor and a neat mechanical keyboard, photorealistic, ultra high resolution."
- Negative Prompt: "blurry, low quality, noisy, distorted, deformed, bad proportions, text, watermark, messy, cluttered background, cartoon, anime, painting, sketch"

||dalle_only|ott_only|3d_only|dalle_ott|dalle_3d|ott_3d|mix|
|--|--|--|--|--|--|--|--|
|image|![base_only](https://github.com/user-attachments/assets/9a2d9b5f-cffb-462b-88f5-9efc563c22a3)|![ott_only](https://github.com/user-attachments/assets/07a760a2-750b-41ca-8f66-83ffd85bb133)|![3d_only](https://github.com/user-attachments/assets/97c1ba1a-ec96-42b1-b128-cbe300768e95)|![base_ott](https://github.com/user-attachments/assets/4352ee80-1994-44db-833a-49d29d16b3fa)|![base_3d](https://github.com/user-attachments/assets/6b364db9-67db-41c6-9d14-774523614d95)|![ott_3d](https://github.com/user-attachments/assets/1917ad75-6a0b-48f3-820b-d5d06aba276e)|![mix](https://github.com/user-attachments/assets/61f04444-b9a1-495a-8f92-8c590ca3065c)|
|VRAM|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|

## Test 2
- Prompt: "A clean modern desk setup with a dark wooden desk, a large monitor, a white mini PC, potted plants, a wireless ergonomic mouse, a mechanical keyboard with colorful keycaps on a felt mat, natural daylight, cozy and minimalistic."
- Negative Prompt: "blurry, low quality, noisy, distorted, deformed, bad proportions, text, watermark, messy, cluttered background, cartoon, anime, painting, sketch"

||dalle_only|ott_only|3d_only|dalle_ott|dalle_3d|ott_3d|mix|
|--|--|--|--|--|--|--|--|
|image|![base_only-2](https://github.com/user-attachments/assets/ea7822ca-9f79-4b4d-b7c6-f2b770d4f81e)|![ott_only-2](https://github.com/user-attachments/assets/f0a825ba-22aa-44a4-b893-5efec790a4db)|![3d_only-2](https://github.com/user-attachments/assets/3b1ecd56-a627-4660-a430-0f7dffc9ed9c)|![base_ott-2](https://github.com/user-attachments/assets/bea3f1b1-112f-490e-8acd-21900c406141)|![base_3d-2](https://github.com/user-attachments/assets/0cca8ceb-09c6-4b12-9db8-0be0c4b5b278)|![ott_3d-2](https://github.com/user-attachments/assets/29aaaa95-9235-465d-b755-0a97ad967749)|![mix-2](https://github.com/user-attachments/assets/5b773d33-16fa-4c20-aded-45eb657b6e20)|
|VRAM|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|약 8.72GB|
