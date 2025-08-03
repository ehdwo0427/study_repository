상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# API의 엔드포인트 목록과 기능 설명
***

|Endpoint|Method|설명|
|---|--|---------|
|`/v1/generate`|POST|텍스트 프롬프트를 입력받아 이미지를 생성|
|`/healthcheck`|GET|서버 상태를 확인 (GCP 이용하므로 check가 필요하다고 판단됨)|
|`/info`|GET|사용 중인 모델 정보 및 설정 반환 (서버 버전, 해상도 등)|

# 흐름도
```text
클라이언트 ──▶ FastAPI 서버
               └── User Input 수신 (이미지 + 텍스트)

               └── Image-to-Text 모델 실행
                   └── 이미지에서 물품 및 텍스트 정보 추출

               └── Queue 판별
                   └── 데스크 이미지인지 판단

                   └── Yes일 경우:
                       └── Prompt 구성 (Image + Text + LoRA)
                       └── Prompt Engineering 적용
                       └── Stable Diffusion 모델로 합성 이미지 생성

                   └── 아니거나 오류 발생 시:
                       └── Naver Shopping API 호출
                       └── 최저가 링크 및 물품 URL 확보

               └── 최종 이미지 S3에 저장
               └── S3 이미지 URL 생성

클라이언트 ◀── 응답
```
# `generate` 입⋅출력 명세

`/generate` - POST

```json
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
}
```
### 1. initial_image_url
**`type`** : string<br/>
<br/>
**S3에 저장된 원본 이미지의 URL**
<br/>

### 2. prompt
**`type`** : string<br/>
<br/>
**생성하고 싶은 이미지에 대한 설명**
<br/>
a realistic image : AI가 실제처럼 보이도록 자연스럽게 합성해달라는 의도전달<br/>
aesthetically matches and complements : 사진 속 데스크의 톤, 조명, 컬러, 스타일에 맞춰 조화롭게 구성해달라는 요청<br/>

### 3. negative Prompt
**`type`** : string<br/>
<br/>
**피하고 싶은 이미지 특성**
 <br/>
흐릿하거나 왜곡된 이미지 방지 : blurry, low resolution, distorted, deformed <br/>
사람 제거 : people, human <br/>
비현실적 스타일 배제 : cartoon, anime, painting <br/>
배경/노이즈 제어 : clutter, messy background, bad lighting <br/>
동일한 물체가 반복되는 현상 억제 : duplicate, twin, cloned <br/>

### 4. num_inference_steps
**`type`** : int<br/>
<br/>
**디퓨전 과정의 스텝 수**<br/>
defalut: 50

### 5. guidance_scale
**`type`** : float<br/>
<br/>
**텍스트 가이던스 강도**<br/>
defalut: 7.5

### 6. height, width
**`type`** : int<br/>
<br/>
**출력 이미지 크기**<br/>
defalut: 1024, 1024

## FastAPI 설계 예시

```python
@app.post("/generate")
async def generate_image(req: PromptRequest):
    image = run_diffusion(req.prompt)
    buffer = BytesIO()
    image.save(buffer, format="PNG")
    buffer.seek(0)
    
    image_url = upload_to_s3(buffer, bucket_name="bucket")
    
    return {"image_url": image_url}
```

## `/generate` 응답 예시
```json
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
  "processed_image_url": "https://bucket.s3.amazonaws.com/images/123.png",
  "마우스": [
    {
      "title": "shopping mall title",
      "price": "21990원",
      "mall": "네이버",
      "category3": "마우스",
      "category4": "유선마우스",
      "link": "https://search.shopping.naver.com/catalog/...",
      "image": "https://... .jpg"
    },
    {
      ...
    },
    {
      ...
    }
  ],
  "item 2": [
    {
      "title": "shopping mall item title",
      "price": "N원",
      "mall": "mall name",
      "category3": "하위 카테고리2",
      "category4": "하위 카테고리1",
      "link": "shopping mall link",
      "image": "item image url"
    },
    {
      ...
    },
    {
      ...
    }
  ],
  ...
}
```

### 1. initial_image_url
**`type`** : string<br/>
<br/>
**S3에 저장된 원본 이미지의 URL**
<br/>

### 2. processed_image_url
**`type`** : string<br/>
<br/>
**AI모델을 이용한 합성된 이미지**
<br/>

### 3. Item_i
**`type`** : list<br/>
<br/>
**추천된 아이템 이름, 아이템에 대한 세부정보 포함**
<br/>
- title: 쇼핑몰 판매 페이지의 title<br/>
  `type`: string
- price: 해당 제품의 가격<br/>
  `type`: string
- mall: 쇼핑몰 이름(쇼핑몰이 없으면 네이버로 return)<br/>
  `type`: string
- category3: 하위 카테고리2 (ex. 마우스)<br/>
  `type`: string
- category4: 하위 카테고리1 (ex. 유선마우스)<br/>
  `type`: string
- link: 상품 판매 페이지 링크<br/>
  `type`: string
- image: 상품 대표 이미지<br/>
  `type`: string

## 오류 처리 방안 및 예외 사항
- 유효성 검사(Validation)
  - `prompt`가 비어 있거나(`""`), `height`와 `width`값이 비정상적으로 큰 경우(예: 8192 이상의 크기)
  - 반환 코드 및 메세지: 400(Bad Request), 422(Unprocessable Entity), 500(Internal Server Error)

  ### 400(Bad Request))
  ```python
  { 
    "detail" : "Prompt must not be empty."
  }
  ```
  <br/>

  ### 422(Unprocessable Entity))
  ```python
  {
    "error_code" : "INVALID_INPUT"
    "message" : "Guidance scale cannot be negative."
  }
  ```
  <br/>

  ### 500(Internal Server Error)
  ```python
  {
    "error_code": "S3_UPLOAD_FAILED",
    "message": "Failed to upload image to S3."
  }
  ```

  ## API 버전 관리(Versioning)
  - 변경 가능성: 추후 API 응답 구조 또는 파라미터가 변경될 가능성이 있다면, 버전 관리를 고려해볼 수 있다.
    - 예: `/v1/generate`, `/v2/generate`와 같이 엔드포인트 버전 표기
  - 이때 명세서에도 **버전별 변경점(Change Log)**과 마이그레이션 가이드를 첨부하면 사용자에게 도움이 된다.

  ## 고려사항
  1. 이미지 파일명 규칙
      - 예) UUID 등을 사용해 난수화
      - 타임스탬프, 혹은 “유저ID_날짜_랜덤” 형태로 정하는 방안
      - 문서에 예시로 들어두면 협업 시 편리

  2. 비동기 구조 필요
      - Stable Diffusion이나 기타 생성 모델은 1회 추론 시 수 초 ~ 수십 초 이상의 시간이 걸릴 수 있다.
        - 이때 클라이언트는 응답을 기다리는 동안 타임아웃 발생 또는 UX 저사 문제가 발생한다.
      1. Polling 방식(클라이언트 주기적 확인)
          - 예시 코드
          ```mermaid
          sequenceDiagram
              participant Client
              participant API Server
              participant Worker (GPU 서버)
              participant S3

              Client->>API Server: POST /generate
              API Server->>Worker: 작업 큐에 등록
              API Server-->>Client: {"task_id": "xyz123"}

              loop 주기적 polling
                  Client->>API Server: GET /result/xyz123
                  API Server->>Worker: 상태 확인
                  alt 작업 완료됨
                      API Server-->>Client: {"status": "done", "image_url": "..."}
                  else 진행중
                      API Server-->>Client: {"status": "processing"}
                  end
              end
          ```
          * API 예시 <br/>
            - `/generate` - POST
              ```json
              {
                "prompt": "minimal wooden desk setup",
                "negative_prompt": "people, cartoon, blurry"
              }
              ```

              응답
              <br/>

              ```jsonc
              {
                "task_id": "xyz123",
                "status": "queued"
              }
              ```

            - `/result/{task_id}` - GET
              ```json
              {
                "status": "processing"
              }
              ```
              작업이 완료되면 예시코드
              <br/>

              ```json
              {
                "status": "done",
                "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
                "processed_image_url": "https://bucket.s3.amazonaws.com/images/123.png",
                "마우스": [
                    {
                    "title": "shopping mall title",
                    "price": "21990원",
                    "mall": "네이버",
                    "category3": "마우스",
                    "category4": "유선마우스",
                    "link": "https://search.shopping.naver.com/catalog/...",
                    "image": "https://... .jpg"
                    },
                    {
                    ...
                    },
                    {
                    ...
                    }
                ],
                "item 2": [
                    {
                    "title": "shopping mall item title",
                    "price": "N원",
                    "mall": "mall name",
                    "category3": "하위 카테고리2",
                    "category4": "하위 카테고리1",
                    "link": "shopping mall link",
                    "image": "item image url"
                    },
                    {
                    ...
                    },
                    {
                    ...
                    }
                ],
                ...
              }
              ```
          - 장단점
            | 장점 | 단점 |
            | :---: | :--: |
            | 구현이 단순함 | 클라이언트가 계속 확인해야 하므로 비효율 발생 가능 |
            | 서버 부하 조절 쉬움 | 실시간 알림이 없음 (실시간성 낮음) |
            | 대부분의 플랫폼과 호환 | polling 간격이 짧으면 DDoS처럼 될 수 있다.
      2. Webhook 방식 (서버가 클라이언트에게 결과 전송)
          - 예시 코드
          ```mermaid
          sequenceDiagram
              participant Client
              participant API Server
              participant Worker
              participant S3

              Client->>API Server: POST /generate (with webhook_url)
              API Server->>Worker: 작업 큐 등록
              API Server-->>Client: {"task_id": "xyz123"}

              Worker->>S3: 업로드
              Worker->>Client.webhook_url: POST {task_id, image_url}
          ```
          * API 예시 <br/>
            - `/generate` - POST
              ```json
              {
                "prompt": "dark-themed desk with LED strip",
                "webhook_url": "https://myapp.com/notify"
              }
              ```
              응답
              <br/>

              ```json
              {
                "task_id": "xyz123",
                "status": "queued"
              }
              ```
            - webhook_url로 POST되는 JSON
            <br/>
            
              ```json
              {
                "status": "done",
                "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
                "processed_image_url": "https://bucket.s3.amazonaws.com/images/123.png",
                "마우스": [
                    {
                    "title": "shopping mall title",
                    "price": "21990원",
                    "mall": "네이버",
                    "category3": "마우스",
                    "category4": "유선마우스",
                    "link": "https://search.shopping.naver.com/catalog/...",
                    "image": "https://... .jpg"
                    },
                    {
                    ...
                    },
                    {
                    ...
                    }
                ],
                "item 2": [
                    {
                    "title": "shopping mall item title",
                    "price": "N원",
                    "mall": "mall name",
                    "category3": "하위 카테고리2",
                    "category4": "하위 카테고리1",
                    "link": "shopping mall link",
                    "image": "item image url"
                    },
                    {
                    ...
                    },
                    {
                    ...
                    }
                ],
                ...
              }
              ```
          - 장단점
            | 장점 | 단점 |
            | :---: | :--: |
            | 클라이언트가 기다릴 필요 없음 | 외부 서버가 webhook을 받도록 구성되어 있어야 함 |
            | 사용자에게 알림 기반 UX 구현 가능 | 보안 이슈 (서명/인증 필요) |
            | 빠른 반응성, 실시간 알림 가능 | Webhook 실패 시 retry 정책 필요  
      3. 결론
        - 프론트엔드 웹/앱에서 주기적으로 확인 가능한 Polling 방식을 사용할 예정
        - 사내 연동, 백엔드 -> 백엔드 고성능 서버 통신이 필요하면 Webhook
          - 초기 MVP, 구조 단순 -> Polling으로 시작하고 Webhook 확장 고려