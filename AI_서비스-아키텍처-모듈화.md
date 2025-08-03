상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

## 서비스 아키텍처 다이어그램

![image](https://github.com/user-attachments/assets/bb0c0494-9fa9-4e68-87eb-1d4bb525b64f)


---

## 모듈별 책임 및 기능

| 모듈명                 | 책임(도메인)           | 주요 기능                                                         |
|-----------------------|-----------------------|------------------------------------------------------------------|
| **API Gateway**       | 진입점                | - HTTP 요청 수신<br/>- 인증/인가<br/>- 요청을 각 Router로 라우팅             |
| **Routers (/classify)** |  Desk 사진 확인     | - 전달 받은 이미지가 Desk 이미지면 처리 완료 후 콜백 요청               |
| **Routers (/healthcheck)** | 서버 상태     | - 서버 상태 확인(GCP 이용하므로 필요하다고 판단됨           |
| **Routers (/generated/v1)** | v1 엔드포인트 제공     | - `/generated`, `/prompt` 등 v1 API 정의<br/>- 요청 유효성 검사           |
| **Routers (/generated/v2)** | v2 엔드포인트 준비(Stub)| - 자체 모델 서빙을 위한 v2 API 스켈레톤                              |
| **Services:DALL·E3**  | DALL·E 3 연동          | - DALL·E 3 HTTP API 호출로 이미지 생성                              |
| **Services:GPT‑4o**   | GPT‑4o 연동           | - GPT‑4o HTTP API 호출로 프롬프트 생성·후처리                         |
| **Services:Custom**   | 커스텀 모델 추론        | - 자체 모델(추론 서버) 호출                                        |
| **Services:Storage**  | S3 업로드/다운로드     | - 입력/생성 이미지 S3 업로드<br/>- Presigned URL 관리               |

---

## 폴더 구조

```plaintext
fastapi_project/                   # 프로젝트 루트
├── app/                           # FastAPI 애플리케이션
│   ├── main.py                    # 진입점: FastAPI() 인스턴스 생성 및 라우터 등록
│   ├── routers/                   # HTTP 엔드포인트 정의
│   │   ├── classify.py            # 전달받은 이미지가 desk 사진이면 처리 완료 후 콜백 요청
│   │   ├── healthcheck.py         # 서버 상태를 확인하는 엔드포인트
│   │   ├── info.py                # 사용 중인 모델 정보 및 설정 반환(서버 버전, 해상도 등)
│   │   └── generated/             # 백엔드 요청
│   │       ├── v1.py              # v1: image2image 워크플로우 라우터(비동기 넣기)
│   │       └── v2.py              # v2: 커스텀 모델 서빙용 스텁
│   ├── services/                  # 비즈니스 로직 & 외부 API 통합
│   │   ├── desk_classify.py       # Desk 확인 유무 판단 함수
│   │   ├── vision2text.py         # GPT‑4o 기반 image→text 변환 함수
│   │   ├── dall_e3.py             # DALL·E 3 기반 text+image→image 생성 함수
│   │   ├── inference.py           # image2text→image2image 전체 Orchestration
│   │   └── storage.py             # S3 업로드/다운로드 및 Presigned URL 관리
│   ├── core/                      # 공통 설정 & 유틸리티
│   │   ├── config.py              # 환경변수 로드, 로깅 설정, IAM 정책
│   │   └── errors.py              # 공통 예외 클래스 정의
│   └── ml_models/                 # ML 모델 파일 저장소
│       ├── dalle3/                # v1 DALL·E 3 가중치·설정 파일
│       ├── classify_cnn/          # v1 classify cnn model 가중치 파일
│       └── custom/                # v2 자체 모델 가중치·설정 파일
├── tests/                         # 테스트 코드
│   ├── unit/                      # 단위 테스트
│   │   ├── test_services.py       # 서비스 로직 테스트
│   │   └── test_crud_task.py      # CRUD 기능 테스트
│   └── integration/               # 통합 테스트
│       └── test_api_v1.py         # /api/v1 end-to-end 테스트
├── .env                           # 환경 변수 파일
├── requirements.txt               # Python 패키지 의존성 목록
└── README.md                      # 프로젝트 개요 및 실행 가이드
```


## 모듈 간 인터페이스 설계

### 1) API 명세

| 엔드포인트                         | Method               | 요청 본문 (Request)                                 | 응답 본문 (Response)                                                         |
|-----------------------------------|----------------------|-----------------------------------------------------|------------------------------------------------------------------------------|
| POST `/app/routers/classify`           |application/json  | `{initial_image_url: "str"}`                                  | - `TaskStatus` : True  <br/> - `TaskStatus` : False                                        |
| GET `/app/routers/healthcheck` | —                    | —                                                   | `"status": "healthy"`        |
| GET `/app/routers/info`             | —     | —                         | `Info`                                                     |
| POST `/app/routers/v1/generate` | application/json     | `{ "image_url": "string" }`                           | `PromptResponse` (initial_image_url, processed_image_url, products)                                                   |

### 2) 데이터 포맷

- **`TaskStatus` : True**  
  ```json
  {
    "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png", 
    "classify": true
  }
  ```

- **`TaskStatus` : False**  
  ```json
  {
    "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png", 
    "classify": False
  }
  ```

- **INFO**
  ```bash
  gcloud compute health-checks create http gpu-health-check \
    --port=8080 \
    --request-path="/health" \
    --check-interval=10s \
    --timeout=5s \
    --unhealthy-threshold=3 \
    --healthy-threshold=2 \
    --description="GPU 서버 nvidia‑smi 검사용"
  ```


- **PromptResponse**  
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


### 3) 통신 방식

- **내부 모듈 호출**: Python 함수 호출  
  - ex) `inference.background_generate(task_id)`
- **외부 서비스 호출**: HTTP REST API  
  - DALL·E 3, GPT‑4o, Naver Shopping API
- **비동기 작업**: FastAPI `BackgroundTasks` (추후 Celery 교체 가능)

---

## 모듈화 기대 효과 및 장점

- **단일 책임 원칙(SRP) 적용**  
  - 모듈별 관심사 분리 → 코드 가독성·유지보수성 향상  
- **독립적 개발·테스트**  
  - 서비스, 스토리지, DB를 각자 Mock/Stubs로 분리 → 병렬 개발 가능  
- **확장성**  
  - v2 커스텀 모델 추가 시 `services/custom.py` + `routers/v2.py`만 구현 → 타 모듈 재사용
  - Classification model 추가하여 Desk 판단 유무 속도 항상으로 인한 트래픽 과다 방지
- **배포 유연성**  
  - 모듈별 컨테이너화/스케일링 가능 (API 서버, 워커, DB, 스토리지)  
- **장애 격리**  
  - 한 모듈 장애 시 전체 서비스 영향 최소화  
- **변경 용이성**  
  - 예: DALL·E 3 → 다른 모델 전환 시 `services/dall_e3.py`만 수정

---

## 모듈화 설계가 서비스 시나리오에 부합함을 보여주는 근거

1. **모델 교체 시나리오**  
   - DALL·E 3 API 버전 변경 혹은 중단 발생 → `services/dall_e3.py` 로직만 수정 후 배포  
2. **기능 확장 시나리오**  
   - 사용자 요청 시 이미지 태깅 + 감정 분석 추가 → `services/gpt4o.py`에 새로운 함수 추가 + `routers/v1.py` 엔드포인트 확장  
3. **성능 확장 시나리오**  
   - 추론 지연 증가 시 → CNN Classification model 사용하여 Desk 판단 유무 빠르게 확인하여 백엔드와의 서버 소통 시간 단축
4. **팀 협업 시나리오**  
   - Frontend 팀: API Gateway·Router 스펙 작업  
   - Backend 팀: Service·DB 모듈 구현  
   - DevOps 팀: 인프라(컨테이너·네트워크·IAM) 구성  
   → 역할 구분 명확, 병목 없는 병렬 작업 가능  
