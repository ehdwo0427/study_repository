상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# Grounding DINO
- GitHub말고 Hugging Face에서 `.safetensors` 파일 제공
- 따라서 architect를 수정할 필요없이 기존처럼 models에서 관리 가능

## Test Output

|Bounding Boxes|Mask|
|--|--|
|![groundingdino_annotated_image](https://github.com/user-attachments/assets/0e878c7c-4fe3-4207-ba5a-6d27229675bc)|![inpainting_background_mask-9](https://github.com/user-attachments/assets/d1bebf81-b19c-4df5-804d-9d9d448ce978)|


## Text Sent
{'keyboard': [(674, 530)], 'mouse': [(768, 466)], 'speaker': [(69, 549), (652, 374)], 'monitor': [(440, 252)]}