## SAM2 (Object Masking)
- 장점
  1. 원하는 object의 경계를 제대로 인식함
  2. 사물이 겹쳐있더라도 해당 물체가 무엇인지 정확하게 prompting할 수 있음
  3. 해당 object를 제외한 나머지 부분에 대한 이미지를 고쳐줌

- 단점
  1. 경계선이 자글자글 거리는 이슈가 있음. 해당 부분은 blurr 처리 등 갖가지 방법을 사용해보았으나 해결하기 어려웠음

  |<img width="1442" alt="445842532-b4b8ba87-8072-4746-93bc-88527f7a8c27" src="https://github.com/user-attachments/assets/675256ab-7dab-44a0-982a-1e6576add3b5" />|<img width="1430" alt="445843159-d63494dd-bdae-4358-91ac-acf11b8398d1" src="https://github.com/user-attachments/assets/2fc41296-e394-417b-9a9f-d5e27c224d0b" />|
  |--|--|



## Grounding dino (Bound Box Masking)
- 장점
  1. Object Masking의 단점을 확실하게 해결해줌. 경계선에 대해 어색한 부분이 없어짐

- 단점
  1. 물체가 겹쳐있는 경우가 대다수이다. 겹쳐있는 부분은 굉장히 어색해짐.

  ![448288196-e094aa32-87a1-4e62-8907-61583e7d1ea3](https://github.com/user-attachments/assets/07c2ab09-ee88-444c-9576-2aab4e2c3a7a)

## Issue
- Object Masking의 단점은 자글자글한 끝부분 때문에 어색함을 유발
  - 위의 어색함은 사각형 masking을 통해 해결

- Bound Box Masking의 단점은 Box가 다른 object도 포함하기 때문에 어색함 유발
  - 위의 어색함은 object만 masking을 통해 해결

- 위 두가지 문제는 서로 상충하기 때문에 딱 중간이 필요함
 ![446477568-154746cf-1d29-4766-953a-51bcef0d1eb0](https://github.com/user-attachments/assets/4abecef4-6a9e-46de-84e9-6ed59fe63ef0)
 - 위의 예시 이미지처럼 Object Masking을 직선처리만 한다면 해결가능


## approxPolyDP() 함수 사용
- Object Masking을 바탕으로 `approxPolyDP()` 함수를 사용하여 object를 직선형태로 바꾸어 해결

## Result

- prompt: "ott_style, Organized home office with wooden desk, monitor, keyboard, printer, two lamps, stacked books, potted plant, and notepad under soft window lighting."
- negative_prompt: "3d render, smooth, plastic, blurry, grainy, low-resolution, deep-fried, oversaturated"

![Unknown-13](https://github.com/user-attachments/assets/dc0bc2e2-4d62-4abb-8b2e-55c163fd3d1a)
![Unknown-14](https://github.com/user-attachments/assets/4f0b983a-e3ec-41c9-a0cf-df25c7020941)
![Unknown-15](https://github.com/user-attachments/assets/797b5aa9-54cc-4664-bc4e-cbd2f5a41975)
![Unknown-16](https://github.com/user-attachments/assets/5a069958-7300-4b00-9f7b-0855dbde82e3)

  
