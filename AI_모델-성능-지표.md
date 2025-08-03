상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# 목차
[1. 기존 모델 성능 지표](#기존-모델-성능-지표)<br/>
[2. 식별된 성능 분석 상세 내용](#식별된-성능-분석-상세-내용)<br/>
[3. 적용할 최적화 기법의 구체적 계획](#적용할-최적화-기법의-구체적-계획)<br/>
[4. 최적화 적용 후의 기대 성능](#최적화-적용-후의-기대-성능)<br/>

# 기존 모델 성능 지표
|모델명|이미지 해상도|처리속도|VRAM|처리방식|비고|사용여부|
| ----- |:-------:| ----: | ---- |:-----:| --------- | :---: |
|[DALL-E3](#1-dall-e3)|1024x1024|unknown|unknown|txt→img|API를 이용한 손쉬운 사용, 비용 문제|✅v1 예정|
|[DeepSeek Janus Pro 7b](#2-deepseek-janus-pro-7b)|768x768|4.2s|약 40GB|txt→img|VRAM 사용대비 아쉬운 성능|❌|
|[Stable Diffusion v1.5](#3-stable-diffusion-v15)|1024x1024|3s|약 11GB|img→img|가볍지만 아쉬운 성능|❌|
|[Stable Diffusion v2.1](#4-stable-diffusion-v21)|1024x1024|3s|약 11GB|img→img|가볍지만 아쉬운 성능|❌|
|[Stable Diffusion XL](#5-stable-diffusion-xl)|1024x1024|8s||img→img|LoRA를 이용해 Fine Tuning 하면 좋은 성능을 보일 것으로 예상|✅v1 이후|
|[Stable Diffusion XL + ControlNet](#6-stable-diffusion-xl--controlnet)|1024x1024|8s||txt→img|Fine Tuning 예시|❌|
|[HiDream I1 Full](#7-hidream-i1-full)|1024x1024|unknown|약 30GB|txt→img|Base Model이 Llama 3.1인 Fine Tuning 예시|❌|


# 식별된 성능 분석 상세 내용

Initial Image
![IMG_1205](https://github.com/user-attachments/assets/269a21f3-4699-408f-a09e-13073fb4b143)

## 1. DALL-E3

### prompt
Given this desk photo, generate a realistic image by compositing a desk setup that aesthetically matches and complements the existing environment

### negative prompt
blurry, low resolution, distorted, deformed, people, human, cartoon, anime, painting, clutter, messy background, bad lighting, duplicate, twin, cloned

### with ChatGPT-4o & Initial Image

### result
![ChatGPT Image 2025년 4월 15일 오후 05_23_12](https://github.com/user-attachments/assets/9fe20851-e884-4658-b9f7-e7d4296e1e94)

### prompt
"A clean, modern desk setup in a bright open office, with a large LG monitor displaying "
"a forest wallpaper and a MacBook beneath it. Add a minimalist LED desk lamp on the left, "
"a sleek wireless charger pad beside the laptop, and all cables neatly organized and hidden. "
"The scene should feel calm, tidy, and professional, with natural lighting and no clutter."

### negative prompt
blurry, low resolution, distorted, deformed, people, human, cartoon, anime, painting, clutter, messy background, bad lighting, duplicate, twin, cloned

### result
![ChatGPT Image 2025년 4월 15일 오후 09_18_28](https://github.com/user-attachments/assets/c605fd4a-3fae-4fa5-b4d2-0acd0203cf1a)

## 2. DeepSeek Janus Pro 7b

### prompt
explain details

### result
This image depicts a workspace setup with a focus on visual content and technology. Here are the details:

1. **Primary Display**:
   - There is a large monitor mounted on a stand, displaying a forest scene with tall redwood trees. The time on the screen is 2:55.

2. **Secondary Display**:
   - A laptop is placed on the desk, and its screen is displaying the same forest scene as the larger monitor.

3. **Cables and Accessories**:
   - Several cables are connected to the laptop, possibly for power, data transfer, or peripherals.
   - There is a small device with a blue button on the desk, which might be a USB hub or a small external hard drive.

4. **Work Environment**:
   - The workspace appears to be in an office or a lab setting, with other desks and chairs visible in the background.
   - There are people working in the background, indicating a collaborative or professional environment.

5. **Signage**:
   - A sign on the desk reads "Please do not touch the screens," suggesting that the screens are sensitive or important.

The setup seems to be designed for monitoring or analyzing visual content, possibly for research, development, or quality assurance purposes.

### image generate prompt

### result
|1|2|3|4|5|
|--|--|--|--|--|
|![image-3](https://github.com/user-attachments/assets/a68937e8-afcc-4e42-bde8-d8060c463c76)|![image-4](https://github.com/user-attachments/assets/3d1ab932-2c8e-444a-a297-83674afd397e)|![image-5](https://github.com/user-attachments/assets/5efe7a66-02bd-4496-97a5-728cc9291de8)|![image-6](https://github.com/user-attachments/assets/8d819032-92e3-4253-ac07-d4fa55850f6b)|![image-7](https://github.com/user-attachments/assets/55687ded-7e22-4eef-9c62-128f08fd992a)|


![ChatGPT Image 2025년 4월 10일 오전 09_06_37](https://github.com/user-attachments/assets/de48bf4c-1c9b-497c-b423-69d4392e7636)
### prompt
explain details

### result
The image depicts a modern workspace setup with a focus on productivity and comfort. Here are the details:

1. **Monitor and Laptop**:
   - There is a large monitor mounted on a stand above a laptop. Both display the same image of a serene forest with tall redwood trees.
   - The laptop is placed on a black mouse pad, and it is connected to the monitor via a cable.

2. **Lighting**:
   - A desk lamp with an adjustable arm is positioned to the left of the monitor. The lamp provides focused lighting, which is beneficial for tasks requiring attention to detail.

3. **Charging and Power**:
   - A power adapter is plugged into the laptop, indicating it is being charged.
   - There is a power strip on the desk, which likely accommodates multiple devices.

4. **Plant**:
   - A small potted plant is placed on the right side of the desk, adding a touch of greenery to the workspace.

5. **Clock**:
   - The time displayed on the laptop is 8:56, which could be the current time or a reminder.

6. **Workspace Setup**:
   - The desk is organized, with essential items like the laptop, monitor, lamp, and plant within easy reach.
   - The setup suggests a focus on both functionality and aesthetics, creating a pleasant and efficient working environment.

This setup is ideal for someone who needs to work for extended periods, requiring good lighting, a comfortable keyboard, and a touch of nature to stay motivated.

### result
|1|2|3|4|5|
|--|--|--|--|--|
|![image-8](https://github.com/user-attachments/assets/dca531b0-f822-464c-91b7-1b61fdda3c96)|![image-9](https://github.com/user-attachments/assets/57406165-387a-4af8-a9aa-ccc3c9cbec44)|![image-10](https://github.com/user-attachments/assets/5a9ded0e-497f-475c-81bd-75674e0ec2f6)|![image-11](https://github.com/user-attachments/assets/acb9c54a-fa92-42c8-b8a5-205a41bb673c)|![image-12](https://github.com/user-attachments/assets/8d85cdac-7232-40ca-84fe-23401c3176a0)|

### image generate prompt
"A clean, modern desk setup in a bright open office, with a large LG monitor displaying "
"a forest wallpaper and a MacBook beneath it. Add a minimalist LED desk lamp on the left, "
"a sleek wireless charger pad beside the laptop, and all cables neatly organized and hidden. "
"The scene should feel calm, tidy, and professional, with natural lighting and no clutter."

|1|2|3|4|5|
|--|--|--|--|--|
|![image-13](https://github.com/user-attachments/assets/9fb509dc-6e73-4b23-939d-664838b5498c)|![image-14](https://github.com/user-attachments/assets/396c1f8b-5d29-4285-9863-c7b783521716)|![image-15](https://github.com/user-attachments/assets/997b0581-c198-4962-9fe7-66047b70a7ce)|![image-16](https://github.com/user-attachments/assets/04b705ce-bfdb-4a8d-81d7-96f756665530)|![image-17](https://github.com/user-attachments/assets/49b772dd-a2dd-4a71-bf6d-a1c7d92812d7)

## 3. Stable Diffusion v1.5

### prompt
Given this desk photo, generate a realistic image by compositing a desk setup that aesthetically matches and complements the existing environment

### negative prompt
blurry, low resolution, distorted, deformed, people, human, cartoon, anime, painting, clutter, messy background, bad lighting, duplicate, twin, cloned

### result
|1|2|
|--|--|
|![output_img2img](https://github.com/user-attachments/assets/72680fdb-3552-4025-b9ae-47324f35a794)|![output_img2img-2](https://github.com/user-attachments/assets/0a113c3e-5434-412a-945d-f07b9435370f)|

* Hyperparameters like strength, guidance_scale, num_inference_steps were varied across images to observe their influence on the results.

## 4. Stable Diffusion v2.1

### prompt
Given this desk photo, generate a realistic image by compositing a desk setup that aesthetically matches and complements the existing environment

### negative prompt
blurry, low resolution, distorted, deformed, people, human, cartoon, anime, painting, clutter, messy background, bad lighting, duplicate, twin, cloned

|1|2|3|4|5|
|--|--|--|--|--|
|![output_stable_diffusion_v2 1_1](https://github.com/user-attachments/assets/7301c935-425c-49d2-af48-804d1ddac8ed)|![output_stable_diffusion_v2 1_1-2](https://github.com/user-attachments/assets/555e5675-49ea-482c-a8ff-f5670fb8f79f)|![output_stable_diffusion_v2 1_1-3](https://github.com/user-attachments/assets/bc8e6b6b-e572-4b13-a27b-e41a2a7e3880)|![output_stable_diffusion_v2 1_1-4](https://github.com/user-attachments/assets/f024085f-7e66-4d6a-afff-631261e85670)|![output_stable_diffusion_v2 1_1-5](https://github.com/user-attachments/assets/645a3c80-0607-4944-8b9e-36456849a32f)|

* Hyperparameters like strength, guidance_scale, num_inference_steps were varied across images to observe their influence on the results.

## 5. Stable Diffusion XL

### prompt
"A clean, modern desk setup in a bright open office, with a large LG monitor displaying "
"a forest wallpaper and a MacBook beneath it. Add a minimalist LED desk lamp on the left, "
"a sleek wireless charger pad beside the laptop, and all cables neatly organized and hidden. "
"The scene should feel calm, tidy, and professional, with natural lighting and no clutter."

### negative prompt
"blurry, low resolution, distorted, clutter, people, human, cartoon, overexposed, deformed, text"

### result
![sdxl_desk_result](https://github.com/user-attachments/assets/c852bba7-4b92-4df6-b595-394cbe689d22)

## 6. Stable Diffusion XL + ControlNet

### prompt
Given this desk photo, generate a realistic image by compositing a desk setup that aesthetically matches and complements the existing environment

### negative prompt
1. blurry, low resolution, distorted, deformed, people, human, cartoon, anime, painting, clutter, messy background, bad lighting, duplicate, twin, cloned
2. low quality, bad quality, sketches

### result
|1|2|
|--|--|
|![hug_lab](https://github.com/user-attachments/assets/28b64161-91ae-4629-b4ad-a27d308a8592)|![hug_lab-2](https://github.com/user-attachments/assets/dbf9ce90-c534-4148-a1ac-8082d5618751)|

# 7. HiDream-I1-Full
[hugging face link](https://huggingface.co/HiDream-ai/HiDream-I1-Full)

구조: Llama-3.1-8B-Instruct 기반 이미지 생성 모델로 보임

## prompt
"A clean, modern desk setup in a bright open office, with a large LG monitor displaying "
"a forest wallpaper and a MacBook beneath it. Add a minimalist LED desk lamp on the left, "
"a sleek wireless charger pad beside the laptop, and all cables neatly organized and hidden. "
"The scene should feel calm, tidy, and professional, with natural lighting and no clutter."

## negative_prompt
"blurry, low resolution, distorted, clutter, people, human, cartoon, overexposed, deformed, text"

![output-2](https://github.com/user-attachments/assets/b1ac69e4-e472-42fb-aa77-807a5f966d0c)

## prompt
"A modern office workspace featuring a large monitor on a stand displaying a forest scene "
"with tall redwood trees, showing the time as 2:55. A laptop is placed on the desk, mirroring "
"the same forest image. Multiple cables are connected to the laptop, possibly for data and power. "
"A small device with a blue button sits on the desk, likely a USB hub or external drive. "
"The background shows other desks and people working, indicating a collaborative lab or office "
"environment. A visible sign on the desk reads 'Please do not touch the screens,' suggesting the "
"setup is for research, monitoring, or visual content analysis."

## negative_prompt
"blurry, low resolution, distorted, clutter, people, human, cartoon, overexposed, deformed, text"

![output-3](https://github.com/user-attachments/assets/c8069535-9f2f-48a2-b854-1960d3ecdc67)

* Token indices sequence length is longer than the specified maximum sequence length for this model (128 > 77). Running this sequence through the model will result in indexing errors<br/>
Token indices sequence length is longer than the specified maximum sequence length for this model (128 > 77). Running this sequence through the model will result in indexing errors<br/>
The following part of your input was truncated because `max_sequence_length` is set to  128 tokens: ['for research, monitoring, or visual content analysis.']<br/>

* 토큰 수 초과로 제대로 된 이미지 생성이 안되었을 수 있음

# 적용할 최적화 기법의 구체적 계획

### LoRA를 이용한 Fine-Tuning
이미 다른 개발자들이 Fine Tuning한 LoRA들을 이용해 성능 테스트를 해본다.

[1. Desk topview](https://civitai.com/models/593123?modelVersionId=662442)<br/>
- Base Model: Stable Diffusion XL 1.0
- File Size: 217.88 MB
- Example Image<br/>
  ![image](https://github.com/user-attachments/assets/8dddfccd-055b-42c4-897c-e569951c9eb1)

[2. 3D Office LoRA](https://civitai.com/models/1378780/3d-office)<br/>
- Base Model: Stable Diffusion XL 1.0
- File Size: 217.87 MB
- Note: 3D 기능은 필요없으므로 Trigger Words에서 `3D 0ff1c3`를 제거, 그외 필요하지 않은 부분을 prompt에서 제거하여 사용
- Example Image<br/>
  ![image](https://github.com/user-attachments/assets/aa535188-3f10-4fce-b455-b8fd20f07af5)

### 서비스에 적합한 사진들을 많이 뽑아내어 증류후 서비스에 적합한 LoRA로 Fine-Tuning

1. ChatGPT-4o를 이용해 다양한 Desk에 대한 이미지 확보
2. 앞서 소개한 LoRA를 이용해 서비스에 적합한 다양한 이미지 확보

### 모델 양자화

1. 모델 양자화를 통해 모델 연산량을 줄인다.

# 최적화 적용 후의 기대 성능

1. 우리의 서비스는 만족도가 우선이므로 해당 서비스로 만족도를 확보하는 것을 우선으로 최적화한다.<br/>
   LoRA를 이용해 서비스에 최적화 된 이미지를 생성할 수 있도록 하는 것이 우선 목표
2. 서비스에 최적화 된 이미지가 생성되면 모델 양자화 등을 통해 연산량을 감소시켜 사용자가 대기해야하는 시간을 줄이고 서버 부하를 줄이는 것을 목표로 최족화한다.<br/>