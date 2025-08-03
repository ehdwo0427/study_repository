# LangChain 도입 검토
- 현재는 FastAPI 기반의 REST 구조만으로도 서비스가 충분히 작동하며 LangChain의 체인 구조를 도입할 필요성이 없을 것으로 판단

## 근거
- 각 AI 구성 요소는 명확한 입력과 출력 구조를 가지며 독립적으로 작동 가능하므로 단일 단계 추론
- 데스크 이미지 여부에 따른 분기 외에는 복잡한 추론 경로가 없는 조건부 실행
- NAVER API 조회는 생성된 Item기반 검색이므로 Tool 선택이나 동적 추론이 요구되지 않는 고정 경로
- 이미지 전달 → 텍스트 생성 → 이미지 생성으로 이어지는 순차적 구조이므로 Prompt 전달 방식도 고정적

## 향후 LangChain 확장 가능성
- 추후 사용자가 Prompt를 직접 입력하는 시나리오 검토 중 즉, 멀티턴 대화로 확장될 가능성 존재<br/>
    예: "해당 분위기에서 화이트 톤이면서 Logitec 마우스가 있는 이미지로 추천해줘"

# Service Architecture

![image](https://github.com/user-attachments/assets/548c3db9-7dec-439a-91ab-ae0e9281cc06)

## Classification Model
- VGG16(weight = imageNet)을 전이학습하여 True, False 이진분류 모델로 제작

### 모델 작동방식
- 서비스의 리소스를 줄이기 위한 핵심역할로 유저들이 데스크 셋업 사진이 아닌 사진을 올렸을 경우, 대형 모델에 들어가지 않기 위한 단계
    ```text
    이미지 입력 ──▶ Desk 인지 판단
                        └── if True:
                            └──▶ AI Server
                        └── elif False:
                            └──▶ Return False
    ```

## Image To Text
- BLIP 등 다양한 모델 테스트 예정

### 모델 작동방식
- 입력된 데스크의 주요 특징을 추출하는 역할
- 추출된 특징을 Prompt로 Text To Image 모델에 전달
- 해당 데스크 셋업에 어울리는 Item 추천 및 Text To Image 모델에 전달
- 추천 Item NAVER API에 전달
    ```text
    이미지 입력 ──▶ 데스크 셋업의 주요 특징 추출
                        └── 주요 특징 Prompt에 저장
                                └──▶ 추천된 아이템 Text To Image 모델에 전달
                        └── 해당 데스크 셋업에 어울리는 Item 추천
                                └──▶ 추천된 아이템 Text To Image 모델에 전달
                                └──▶ NAVER API에 전달
    ```

## Text To Image
- Stable Diffusion XL 모델을 기반
- LoRA를 사용한 서비스 특화 모델로 Fine Tuning

### 모델 작동방식
- Image To Text에서 전달받은 내용을 기반으로 Prompt 작성(Prompt 작성시 LoRA Trigger keyword 포함)
- 미리 작성된 Negative Prompt와 그 외 Hyper Parameter를 기반으로 데스크 셋업 이미지 생성
    ```text
    Prompt 등 Hyper Parameter 입력 ──▶ 이미지 생성
    ```


## NAVER API
- [NAVER Developers 쇼핑 검색 API](https://developers.naver.com/docs/serviceapi/search/shopping/shopping.md) 참조
- 추천된 Item을 실제 유저가 구입할 수 있도록 Item 정보와 url을 생성하기 위해 사용
  ```python
  def search_naver_shop(query, display=3):
        url = "https://openapi.naver.com/v1/search/shop.json"
        headers = {
            "X-Naver-Client-Id": client_id,
            "X-Naver-Client-Secret": client_secret
        }
        params = {
            "query": query,
            "display": display
        }
        response = requests.get(url, headers=headers, params=params)
        if response.status_code == 200:
            return response.json()["items"]
        else:
            print(f"❌ Error {response.status_code}: {response.text}")
            return []
  ```

- reponse 정보
    - title: 상품이름
    - lprice: 최저가
    - mallName: 상품을 판매하는 쇼핑몰
    - category3: 상품의 카테고리(소분류)
    - category4: 상품의 카테고리(세분류)
    - link: 상품 정보 URL
    - image: 섬네일 이미지의 URL
