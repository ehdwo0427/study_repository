상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# Comparison of Segmentation Anything 2.1 Model Weights

- Due to VRAM limitations, resizing the input image to 1024x1024 is required

- SAM 2.1 provides multiple model weight options based on different backbone architectures

- Original Image resized to 1024x1024
  
  ![1024](https://github.com/user-attachments/assets/4649b6f6-9e9f-4632-9cd5-065c0cdcbbb1)

## Comparison Table

||Large|Base_Plus|Small|Tiny|Large_generation option|
|--|--|--|--|--|--|
|Image|![sam2 1-large_1024(6 1)](https://github.com/user-attachments/assets/9d581e20-bd6e-492b-a7b9-7a5936654ae9)|![sam2 1-base_plus_1024(4 2)](https://github.com/user-attachments/assets/c02a0d84-1943-47bc-8e78-26f7c801e348)|![sam2 1-small_1024(4 4)](https://github.com/user-attachments/assets/a9b21471-b143-4126-88d8-b2de71898967)|![sam2 1-tiny_1024(4 3)](https://github.com/user-attachments/assets/1d31e82c-e534-42c1-b302-93563963ccbe)|![sam2 1-large_1024(14 4)](https://github.com/user-attachments/assets/f4bc2640-aff2-4064-9f3d-cdcd34fe34c3)|
|VRAM|About 6.1GB|About 4.2GB|About 4.4GB|About 4.3GB|About 14.4GB|

- While the generation time was not explicitly measured, inference on an NVIDIA A100 GPU takes approximately 4 seconds per image.

## Reference
- [Facebook research SAM2](https://github.com/facebookresearch/sam2)