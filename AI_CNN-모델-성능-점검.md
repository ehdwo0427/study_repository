상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# CNN 모델 이진분류 성능점검

- True - 233개<br/>
  출처: 퀘이사존, 구글, 인스타그램 등
- False - 82개<br/>
  출처: 구글 등

- 이미지 증강 이용하여 True는 3배 증강, False는 5배 증강

- True 예측<br/>
![be9bf99c9186b1b2888a061c95a13db8](https://github.com/user-attachments/assets/c2022ede-18b7-4669-a4c5-b36ec56ce01a)

  A100 - 약 42ms, CPU - 약 175ms

- False 예측<br/>
![computer-desk-isolated_1308-52492](https://github.com/user-attachments/assets/71301395-1d8d-47e3-9b31-0245e56865f8)
![ark_dining_table_2m22m_001_1](https://github.com/user-attachments/assets/a603ca0d-8241-45e4-964b-31b3a735e652)

  A100 - 약 42ms, CPU - 약 175ms