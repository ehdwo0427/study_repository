상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# 현황

- V1은 원본 이미지와 괴리감이 있음

- 우리의 서비스는 나의 desk를 꾸며주는 컨셉이며 내 desk에 없던 Item을 추천해줬다면 그 Item의 상품 정보도 return하여 실제 똑같이 꾸밀 수 있도록 하는 것

- 따라서 V2의 목표는 원본 이미지를 어느 정도 유지하면서 깔끔한 Desk Setup의 느낌을 주는 것

## V2 목표
- 원본 이미지를 유지하면서 깔끔한 Desk Setup 만들기

## 파이프라인
- 원본 이미지에서 mask 출력 → mask 바탕으로 유지할 부분, 생성 부분 선택 → 이미지 생성

- mask 이미지 예시<br/>
 ![inpainting_background_mask-3](https://github.com/user-attachments/assets/14b859ec-4c64-4f00-a462-7df77c1be46b)

## GPT와 개발 중인 모델 비교

- 원본 이미지<br/>
 ![test3](https://github.com/user-attachments/assets/bbee5610-64f4-4f6c-b21b-59f9b0ade999)

- GPT 생성 이미지<br/>
  ![ChatGPT Image 2025년 5월 20일 오후 03_18_54](https://github.com/user-attachments/assets/74856a87-4c2f-4cb2-be47-0f90aa1ec725)

- 현재 모델 진행 상황<br/>
 ![inpainted_result-29](https://github.com/user-attachments/assets/e4076d00-2e1e-4122-a6d6-9dd7eec326cf)

<br/>

### 도달한 부분

- GPT처럼 주요한 부분 Monitor, Laptop, Keyboard, Mouse 등은 살리고 나머지 부분만 수정한 것

- 즉, 느낌은 맞고 Flow는 어느정도 맞는 부분으로 예상 됨

<br/>

### 부족한 부분

- 유지하는 부분 끝처리 미숙

| GPT | 개발 중인 모델 |
| -- | -- |
|![GPT_detail1](https://github.com/user-attachments/assets/179e87c7-4e7e-4e47-9400-17e88f8bfadc)|![service_detail1](https://github.com/user-attachments/assets/b4b8ba87-8072-4746-93bc-88527f7a8c27)|

<br/>

- 유지하는 부분 합성한 듯 한 어색함

| GPT | 개발 중인 모델 |
| -- | -- |
|![GPT_detail2](https://github.com/user-attachments/assets/c9e8daed-a717-48c6-a4de-6338f97cf2f7)|![service_detail2](https://github.com/user-attachments/assets/d63494dd-bdae-4358-91ac-acf11b8398d1)|

- 전반적으로 디테일이 뭉개짐

<br/>

## 앞으로 해야할 일

- **GPT생성 이미지로 Fine tuning하는 DeepSeek 전략 (팀원 전부)**
  - 디테일의 개선이 있다고 사료됨

- 끝처리 미숙은 Mask 부분에서 문제가 있다고 판단됨
  - blurr 처리 및 mask 영역 부분의 수치를 변경해보며 계속 판단 예정
    ![GPT_detail3](https://github.com/user-attachments/assets/fdc88894-c40d-4f49-835c-e907af015f99)
  
  - 끝 부분이 blurr처럼 흐려지는게 mask처리 부분에 blurr 기법이 들어 간 것으로 판단됨

