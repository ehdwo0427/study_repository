상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# v2 pipeline

- Load Models → Load Image → Grounding dino → (Make Prompt) → SDXL Inpainting → Image Upscaling → Labeling

- 이번 Test에서는 Make Prompt 부분은 생략하고 아래와 같이 대체
  - recommend = ["potted plant", "ambient lamp", "LED light", "coffee mug", "desk clock"]
  - prompt = "A cozy, add a stylish potted plant, an ambient lamp, soft LED lighting, a ceramic coffee mug, and a small modern desk clock to the existing desk setup."

- pipeline colab 파일은 따로 공유

## Result

![Unknown](https://github.com/user-attachments/assets/79f4b40b-efe7-445e-8177-f4c7e1998a3f)
- {'ambient lamp light': [(929, 422)]}


![Unknown-2](https://github.com/user-attachments/assets/24c3da02-a03d-462a-88ec-c600b6783a5a)
- Labeling 되지 않음

![Unknown-3](https://github.com/user-attachments/assets/53c28ae8-e414-42e3-946f-a4b3efaad2b0)
- {'ambient lamp led light': [(438, 166)], 'desk clock': [(267, 289)]}

![Unknown-4](https://github.com/user-attachments/assets/5d3e6c94-73b2-4656-832a-622c2d751a6c)
- Labeling 되지 않음

![Unknown-5](https://github.com/user-attachments/assets/00772429-56d3-4dea-b487-cff867176e25)
- {'ambient lamp led light': [(554, 343)]}

## Issue
- Label이 제대로 되지 않는 문제
- Model Load시 VRAM이 약 8.4GB 사용이지만 5장을 돌리고 난 후 VRAM이 약 8.5GB으로 소폭 상승, 어디선가 메모리 누수중



