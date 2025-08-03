상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# API의 엔드포인트 목록과 기능 설명
***

|Endpoint|Method|설명|
|---|--|---------|
|`/classify`|POST|전달받은 이미지가 desk 사진이면 처리 완료 후 콜백 요청|
|`/healthcheck`|GET|서버 상태를 확인 (GCP 이용하므로 check가 필요하다고 판단됨)|
|`/info`|GET|사용 중인 모델 정보 및 설정 반환 (서버 버전, 해상도 등)|
|`/generated`|POST|백엔드 요청|

# 흐름도
```text
[1] 백엔드 ──▶ POST /classify ──▶ AI 서버
                     └── classify image
                     └── if True:
                           └── 응답 True ──▶ 백엔드
                           └── img2txt
                           └── txt2img
                           └── 백엔드 request /generated
                     └── elif False:
                           └── 응답 False ──▶ 백엔드
```
# `classify` - POST 입⋅출력 예시

## `/classify` 입력 예시

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


## `/classify` 응답 예시
### True인 경우
```json
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
  "classify": true
}
```
### 1. initial_image_url
**`type`** : string<br/>
**S3에 저장된 원본 이미지의 URL**
<br/>

### 2. classify
**`type`** : boolean<br/>
**해당 이미지가 desk 이미지 인지 판단여부**
<br/>

### `/generated` - POST (Callback to Backend)
```json
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
  "processed_image_url": "https://bucket.s3.amazonaws.com/images/123.png",
  "products": [
    {
      "keyword": "마우스",
      "details": [
        {
          "title": "앱코 무소음 무선 마우스",
          "price": "21990",
          "mall": "네이버",
          "link": "https://search.shopping.naver.com/...",
          "image": "https://.../.jpg",
          "main_category": "마우스",
          "sub_category": "유선마우스"
        },
        {
          ...
        }
      ]
    },
    {
      "keyword": "추천 아이템명",
      "details": [
        {
          "title": "DeskProduct - name",
          "price": "DeskProduct - price",
          "mall": "DeskProduct - purchase_place",
          "link": "DeskProduct - purchase_url",
          "image": "DeskProduct - iamge_url",
          "main_category": "ProductMainCategory - name",
          "sub_category": "ProductSubCategory - name"
        },
        {
          ...
        }
      ]
    },
    {
      ...
    }
  ]
}
```

### 1. initial_image_url
**`type`** : string<br/>
**S3에 저장된 원본 이미지의 URL**
<br/>

### 2. processed_image_url
**`type`** : string<br/>
**AI모델을 이용한 합성된 이미지**
<br/>

### 3. products
**`type`** : list<br/>
**추천된 아이템 검색 키워드, 아이템에 대한 세부정보 포함**
<br/>
- keyword: 추천된 아이템 검색 키워드<br/>
  `type`: VARCHAR(255)
- details: 추천된 아이템 세부정보<br/>
  `type`: list
  - title: 쇼핑몰 내의 상품 이름<br/>
    `type`: VARCHAR(255)
  - price: 상품 가격<br/>
    `type`: INT
  - mall: 쇼핑몰 이름(쇼핑몰이 없으면 네이버로 return)<br/>
    `type`: VARCHAR(255)
  - link: 상품 판매 페이지 링크<br/>
    `type`: VARCHAR(255)
  - image: 상품 대표이미지 url<br/>
    `type`: VARCHAR(255)
  - main_category: 상품의 상위 카테고리(ex. 마우스, 키보드)<br/>
    `type`: VARCHAR(255)
  - sub_category: 상품의 하위 카테고리(ex. 무선마우스, 기계식키보드)<br/>
    `type`: VARCHAR(255)


### False인 경우
```json
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
  "classify": false
}
```

### 1. initial_image_url
**`type`** : string<br/>
**S3에 저장된 원본 이미지의 URL**
<br/>

### 2. classify
**`type`** : boolean<br/>
**해당 이미지가 desk 이미지 인지 판단여부**
<br/>

## 오류 처리 방안 및 예외 사항
  ### 400(필수 필드 누락)
  ```json
  { 
    "error": "Missing 'initial_image_url'"
  }
  ```
  <br/>

  ### 422(url 형식 오류)
  ```json
  {
    "error": "Invalid S3 URL format"
  }
  ```
  <br/>

  ### 500(내부 처리 오류)
  ```json
  {
    "error": "Failed to process generated image"
  }
  ```

## 기타 엔드 포인트
`/healthcheck` - GET
- GCP 등 클라우드 환경에서 서버의 정상 동작을 체크하기 위한 엔드포인트

`/info` - GET
- 현재 AI 서버에 적용된 모델 이름, 버전, 이미지 해상도, 가중치 크기 등을 반환