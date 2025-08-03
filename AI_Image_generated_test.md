상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# Training
## Training Dataset

|img_num|24|34|54|74|75|
|--|--|--|--|--|--|
|image|![24](https://github.com/user-attachments/assets/7826a289-c6cd-46b0-8b29-2b031cad35f6)|![34](https://github.com/user-attachments/assets/ff4f62b8-ce76-4a69-bcbc-c3a992b79eb9)|![54](https://github.com/user-attachments/assets/9f7f851a-3c15-4193-b18d-77e22c7dd07c)|![74](https://github.com/user-attachments/assets/713fecd1-1e80-4446-8e5b-c9aeb9c93f34)|![75](https://github.com/user-attachments/assets/d9e3787e-dc74-4bd5-b1fb-909c7e2d63c7)|
|caption|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, wireless charger, microphone|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse|OTT, laptop, white tone, one monitor, mechanical keyboard, wireless mouse, trackpad, tablet|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, tablet|OTT, desktop, white tone, one monitor, mechanical keyboard, mouse|

|img_num|76|79|84|101|111|
|--|--|--|--|--|--|
|image|![76](https://github.com/user-attachments/assets/c402a323-f836-4010-b701-16a64237f377)|![79](https://github.com/user-attachments/assets/142e92ae-ffe7-4a38-9ca8-28d3008629ca)|![84](https://github.com/user-attachments/assets/2bd8e872-85a9-4539-b303-6df354b4bc29)|![101](https://github.com/user-attachments/assets/ad3d03d9-9aae-4501-9f71-cbadd64114a4)|![111](https://github.com/user-attachments/assets/6c9ca9dd-79bb-4652-8c4f-2d32ca6a1972)|
|caption|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, tablet|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, wireless charger|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, tablet|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, wireless charger, microphone|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse|

|img_num|119|132|134|147|153|
|--|--|--|--|--|--|
|image|![119](https://github.com/user-attachments/assets/842c0f37-0405-4140-8c2a-be10b50c66e3)|![132](https://github.com/user-attachments/assets/1f887d93-306c-4ff8-b8a8-b9982f8eac50)|![134](https://github.com/user-attachments/assets/99f8663d-0c19-401e-aeee-6594dfd0d5c9)|![147](https://github.com/user-attachments/assets/e459ecbb-82c3-415c-874d-56e15dc44e89)|![153](https://github.com/user-attachments/assets/c0bd5001-02bf-4bca-a12c-95af64c8464e)|
|caption|OTT, laptop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, wireless charger, microphone|OTT, desktop, white tone, one monitor, mechanical keyboard, wireless mouse|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, tablet|OTT, laptop, white tone, one monitor, keyboard, track pad, wireless charger|OTT, laptop, white tone, one monitor, mechanical keyboard, wireless mouse, tablet|

|img_num|155|165|171|173|177|
|--|--|--|--|--|--|
|image|![155](https://github.com/user-attachments/assets/1a311f96-9bc9-4035-8743-18eb99c0656c)|![165](https://github.com/user-attachments/assets/d2a34b23-c80a-4590-886b-950cd3faea3d)|![171](https://github.com/user-attachments/assets/dc7213d8-0168-445d-bb24-fd4d684696fd)|![173](https://github.com/user-attachments/assets/46b196c2-fb86-42d7-9231-884acc1264d6)|![177](https://github.com/user-attachments/assets/054108c9-1a72-4b8d-bafe-df97e33eeb54)|
|caption|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse|OTT, laptop, white tone, one monitor, speaker, mechanical keyboard|OTT, desktop, white tone, one monitor, speaker, mechanical keyboard, wireless mouse, wireless charger, tablet|

## Kohya_LoRA_Trainer_XL

- Base Model: stabilityai/stable-diffusion-xl-base-1.0
- VAE: Original VAE
- Network Category: LoRA_LierLa
- Network dim: 32
- Network alpha: 16
- Optimizer type: Prodigy
- Learning Rate: 0.5
- Epochs: 20
- Train Batch Size: 1
- Mixed Precision: fp16

# Generating Test
- Base Model: sd_xl_base_1.0.safetensors
- VAE: sdxl_vae.safetensors

## T4
![lora_test_output (4)](https://github.com/user-attachments/assets/c718be1b-6cd7-4587-bae1-b0ce8e97daa4)
- prompt: "OTT, white tone, one monitor, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 25.28초
- 최대 VRAM 사용량: 10163.17 MB

![lora_test_output (3)](https://github.com/user-attachments/assets/35d05f49-3eda-4cf7-b4c3-5d087917d287)
- prompt: "OTT, **laptop**, white tone, one monitor, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 25.43초
- 최대 VRAM 사용량: 10163.17 MB

![lora_test_output (2)](https://github.com/user-attachments/assets/b352345d-33cf-4ac7-8e33-ba1a153decdc)
- prompt: "OTT, laptop, white tone, one monitor, **speaker**, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 26.39초
- 최대 VRAM 사용량: 10163.17 MB

## L4
![lora_test_output (5)](https://github.com/user-attachments/assets/27518d4a-c530-4933-8229-4947c3f91521)
- prompt: "OTT, white tone, one monitor, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 12.55초
- 최대 VRAM 사용량: 10933.42 MB

![lora_test_output (6)](https://github.com/user-attachments/assets/5f3e7693-4aa0-4925-a7d9-ddcd6dc9bb08)
- prompt: "OTT, **laptop**, white tone, one monitor, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 11.70초
- 최대 VRAM 사용량: 10933.42 MB

![lora_test_output (7)](https://github.com/user-attachments/assets/659882c2-79ed-42f1-b7b9-5bf9410880a0)
- prompt: "OTT, laptop, white tone, one monitor, **speaker**, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 11.74초
- 최대 VRAM 사용량: 10933.42 MB

## A100
![lora_test_output (8)](https://github.com/user-attachments/assets/59886f67-3d18-499f-880e-9145f621a8ad)
- prompt: "OTT, white tone, one monitor, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 4.93초
- 최대 VRAM 사용량: 10933.42 MB

![lora_test_output (9)](https://github.com/user-attachments/assets/6212d9f3-bcfc-4cdf-9c32-54c05f224272)
- prompt: "OTT, **laptop**, white tone, one monitor, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 3.80초
- 최대 VRAM 사용량: 10933.42 MB

![lora_test_output (10)](https://github.com/user-attachments/assets/20c66779-f245-4c62-b8db-53a214d8f635)
- prompt: "OTT, laptop, white tone, one monitor, **speaker**, mechanical keyboard, wireless mouse, highly detailed, 4k"
- negative_prompt: "blurry, low quality, deformed, cropped, watermark, text"
- num_inference_steps: 30
- guidance_scale: 7.5
- image size: 1024 x 1024
- 이미지 생성 소요 시간: 3.83초
- 최대 VRAM 사용량: 10933.42 MB
