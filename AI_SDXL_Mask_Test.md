상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# Issue 정리

- 각 Object마다 Mask를 처리하면 어색한 문제

<br/>

## Solution

- Object Mask를 부드럽게 처리하는 것이 아닌 각 Object Bounding box로 Mask 처리

|Object Mask|Bounding Box|
|--|--|
|![clean_mask](https://github.com/user-attachments/assets/154746cf-1d29-4766-953a-51bcef0d1eb0)|![inpainting_background_mask-8](https://github.com/user-attachments/assets/1f85804f-e47e-4f78-8358-384fae2c12a3)|

<br/>

## Result

|Object Mask|Bounding Box|
|--|--|
|![inpainted_result-29_out](https://github.com/user-attachments/assets/371564bf-e058-44df-99d5-9a91ae5cb555)|![inpainted_result_out-10](https://github.com/user-attachments/assets/390a9253-3669-4ac9-97bd-6d0e4394257e)|

<br/>

## Other Test
||Test 1|Test 2|Test 3|
|--|--|--|--|
|Origin|![resized_1024](https://github.com/user-attachments/assets/c9920531-5e22-4a1a-aa89-1342c883dcf5)|![resized_1024-2](https://github.com/user-attachments/assets/7c9add97-351a-4c26-abdc-779ab5d94b67)|![resized_1024-3](https://github.com/user-attachments/assets/56e818c7-2298-41dd-803e-a51adabdf6ac)|
|Generated|![inpainted_result_out-7](https://github.com/user-attachments/assets/25b6abfa-199a-465c-b45c-e1055f561a18)|![inpainted_result_out-8](https://github.com/user-attachments/assets/64ce8c81-a925-459e-8615-b0837ddf2355)|![image](https://github.com/user-attachments/assets/c8cfe518-3fcc-4872-9ce7-be0290863614)|

- prompt = "a cozy and stylish desk setup with a potted plant, a sleek wireless charger, a modern desk clock, and a ceramic coffee cup placed naturally, warm ambient lighting, soft shadows, minimalist interior"
- negative_prompt = "blurry, low quality, extra hands, extra monitors, text, logo, watermark, distorted, duplicate items, poorly drawn, bad anatomy, disfigured, overexposed, grainy, frame, cropped"
<br/>

# Next Step

- BLIP 제거
  - Captioning은 Groundino 선에서 가능
  - Label과 Box 좌표 Prompt(GPT-4o에 전달)

- Prompt 수정
  - Prompt에 좌표 넣어보기(e.g. (3, 4)가 모니터인데, 오른쪽에 선인장 넣어줘 등)





