# Naver Shopping API

Naver에서 제공하는 검색 API를 통해 추천 아이템 가격, 링크, 이미지를 return 할 수 있다.

예시 코드
```python
import requests
import json

# 네이버 API 인증 정보
client_id = "..."          # ← 여기에 본인의 Client ID
client_secret = "..."  # ← 여기에 본인의 Client Secret

# 테스트용 추천 아이템 리스트
recommended_items = [
    "마우스",
    "데스크 매트",
    "기계식 키보드",
    "무선 충전기",
    "탁상용 미니 식물"
]

def clean_html(raw_html):
    """<b> 같은 HTML 태그 제거"""
    clean = re.compile('<.*?>')
    return re.sub(clean, '', raw_html)

def extract_categories(item):
    # 가능한 카테고리 정보 추출
    cat2 = item.get("category2", "")
    cat3 = item.get("category3", "")
    cat4 = item.get("category4", "")
    
    # 하위 2개 카테고리 선택 로직
    if cat4:
        return cat3, cat4
    elif cat3:
        return cat2, cat3
    else:
        return "", cat2

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

# 결과 수집
search_results = {}

for item in recommended_items:
    results = search_naver_shop(item)
    enriched_results = []
    
    for i, product in enumerate(results):
        title = clean_html(product["title"])
        mall = product.get("mallName", "Unknown")
        cat3, cat4 = extract_categories(product)
        
        enriched_results.append({
            "title": title,
            "price": f"{product['lprice']}원",
            "mall": mall,
            "category3": cat3,
            "category4": cat4,
            "link": product["link"],
            "image": product["image"]
        })
    search_results[item] = enriched_results

initial_url = "https://bucket.s3.amazonaws.com/images/12.png"
processed_url = "https://bucket.s3.amazonaws.com/images/123.png"

final_result = {
    "initial_image_url": initial_url,
    "processed_image_url": processed_url,
    **search_results  # 기존 아이템 검색 결과 추가
}

print(json.dumps(final_result, indent=2, ensure_ascii=False))
```

출력 결과

```
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
  "processed_image_url": "https://bucket.s3.amazonaws.com/images/123.png",
  "마우스": [
    {
      "title": "로지텍 로지텍G G102 2세대 LIGHTSYNC 블랙",
      "price": "21990원",
      "mall": "네이버",
      "category3": "마우스",
      "category4": "유선마우스",
      "link": "https://search.shopping.naver.com/catalog/52821285480",
      "image": "https://shopping-phinf.pstatic.net/main_5282128/52821285480.20250219134240.jpg"
    },
    {
      "title": "로지텍 MX MASTER 3S 그래파이트",
      "price": "129890원",
      "mall": "네이버",
      "category3": "마우스",
      "category4": "무선마우스",
      "link": "https://search.shopping.naver.com/catalog/52648750882",
      "image": "https://shopping-phinf.pstatic.net/main_5264875/52648750882.20250124152551.jpg"
    },
    {
      "title": "로지텍 시그니처 M650 그래파이트",
      "price": "38670원",
      "mall": "네이버",
      "category3": "마우스",
      "category4": "무선마우스",
      "link": "https://search.shopping.naver.com/catalog/52649429437",
      "image": "https://shopping-phinf.pstatic.net/main_5264942/52649429437.20250124164326.jpg"
    }
  ],
  "데스크 매트": [
    {
      "title": "카이젠스 데스커 호환 책상매트 보호커버 DSK-6(1100x590)",
      "price": "19960원",
      "mall": "KAIZENS",
      "category3": "데스크용품",
      "category4": "데스크패드",
      "link": "https://smartstore.naver.com/main/products/6195178550",
      "image": "https://shopping-phinf.pstatic.net/main_8373967/83739678127.2.jpg"
    },
    {
      "title": "국내산 데스크 매트 가죽 데스크 패드 책상 덮개 깔판 맞춤 제작",
      "price": "15900원",
      "mall": "아비드앤홈",
      "category3": "데스크용품",
      "category4": "데스크패드",
      "link": "https://smartstore.naver.com/main/products/10529609411",
      "image": "https://shopping-phinf.pstatic.net/main_8807411/88074114771.jpg"
    },
    {
      "title": "와이드 데스크매트 논슬립 책상 패드 커버 덮개 보호 깔판 깔개 베이직데이",
      "price": "11900원",
      "mall": "토비메모리",
      "category3": "데스크용품",
      "category4": "데스크패드",
      "link": "https://smartstore.naver.com/main/products/2456322230",
      "image": "https://shopping-phinf.pstatic.net/main_1336082/13360829096.2.jpg"
    }
  ],
  "기계식 키보드": [
    {
      "title": "키크론 K10 PRO SE2 레트로 무선 기계식 키보드 애플 맥 블루투스 저소음 바나나축",
      "price": "119000원",
      "mall": "키크론 공식스토어",
      "category3": "키보드",
      "category4": "무선키보드",
      "link": "https://smartstore.naver.com/main/products/10310833443",
      "image": "https://shopping-phinf.pstatic.net/main_8785533/87855337549.17.jpg"
    },
    {
      "title": "AULA F87Pro 유무선 독거미 한글 기계식 키보드 펀키스 국내 정품 올리비아 화이트, 회목축 V4",
      "price": "62000원",
      "mall": "지앤케이솔루션",
      "category3": "키보드",
      "category4": "유선키보드",
      "link": "https://smartstore.naver.com/main/products/10533419896",
      "image": "https://shopping-phinf.pstatic.net/main_8807792/88077925257.jpg"
    },
    {
      "title": "그루브스톤 GV10 수제풀윤활 104키 유선 기계식키보드 클라리온P GA 저소음밀키축 38g",
      "price": "164000원",
      "mall": "왓키 WHATKEY",
      "category3": "키보드",
      "category4": "유선키보드",
      "link": "https://smartstore.naver.com/main/products/10392743950",
      "image": "https://shopping-phinf.pstatic.net/main_8793724/87937248809.10.jpg"
    }
  ],
  "무선 충전기": [
    {
      "title": "QI2 갤럭시 워치 아이폰 고속 무선 충전기 애플 맥세이프 충전 3IN1 거치대",
      "price": "46900원",
      "mall": "노존",
      "category3": "휴대폰충전기",
      "category4": "충전기",
      "link": "https://smartstore.naver.com/main/products/10765675747",
      "image": "https://shopping-phinf.pstatic.net/main_8831018/88310181737.1.jpg"
    },
    {
      "title": "삼성 스마트 싱스 스테이션 (25W 초고속 충전기+1.8M케이블+갤럭시 고속 무선충전기)",
      "price": "58900원",
      "mall": "삼성공식파트너 dmac",
      "category3": "휴대폰충전기",
      "category4": "충전기",
      "link": "https://smartstore.naver.com/main/products/7933047617",
      "image": "https://shopping-phinf.pstatic.net/main_8547754/85477547940.9.jpg"
    },
    {
      "title": "만렙 맥세이프 충전기 애플워치 3in1 무선충전기 아이폰16 호환",
      "price": "32900원",
      "mall": "manlab. 만렙 공식몰",
      "category3": "휴대폰충전기",
      "category4": "충전기",
      "link": "https://smartstore.naver.com/main/products/6207554973",
      "image": "https://shopping-phinf.pstatic.net/main_8375205/83752054550.13.jpg"
    }
  ],
  "탁상용 미니 식물": [
    {
      "title": "핑크싱고니움 공기정화식물 탁상용 미니화분",
      "price": "17000원",
      "mall": "두왑플랜트",
      "category3": "원예/식물",
      "category4": "공기정화식물",
      "link": "https://smartstore.naver.com/main/products/5922645777",
      "image": "https://shopping-phinf.pstatic.net/main_8346714/83467145265.jpg"
    },
    {
      "title": "푸테리스 행잉식물 탁상용 미니초음파가습기 대형가습기 병원용",
      "price": "26330원",
      "mall": "마이올드퍼피",
      "category3": "원예/식물",
      "category4": "공기정화식물",
      "link": "https://smartstore.naver.com/main/products/10277803462",
      "image": "https://shopping-phinf.pstatic.net/main_8782230/87822307286.jpg"
    },
    {
      "title": "꽃다발 정원 인공 스타 장식 웨딩 식물 미니 탁상 시뮬레이션 Cm blue 1PC",
      "price": "36000원",
      "mall": "Hazelfive",
      "category3": "원예/식물",
      "category4": "공기정화식물",
      "link": "https://smartstore.naver.com/main/products/11691717130",
      "image": "https://shopping-phinf.pstatic.net/main_8923622/89236227597.jpg"
    }
  ]
}
```

상세 품목으로 출력 예시 ("마우스" 대신 "로지텍 MX Master 3S"로 검색 등)

```
{
  "initial_image_url": "https://bucket.s3.amazonaws.com/images/12.png",
  "processed_image_url": "https://bucket.s3.amazonaws.com/images/123.png",
  "BenQ WiT MindDuo": [
    {
      "title": "벤큐 WiT MindDuo 2 아이케어 LED 스탠드 블루",
      "price": "454900원",
      "mall": "쿠팡",
      "category3": "스탠드",
      "category4": "LED스탠드",
      "link": "https://link.coupang.com/re/PCSNAVERPCSDP?pageKey=8398417399&ctag=8398417399&lptag=I24276001921&itemId=24276001921&vendorItemId=91292415249&spec=10305199",
      "image": "https://shopping-phinf.pstatic.net/main_5117155/51171550500.jpg"
    },
    {
      "title": "벤큐 WiT MindDuo 2 아이케어 LED 스탠드 화이트",
      "price": "454900원",
      "mall": "쿠팡",
      "category3": "스탠드",
      "category4": "LED스탠드",
      "link": "https://link.coupang.com/re/PCSNAVERPCSDP?pageKey=8398417399&ctag=8398417399&lptag=I24276002731&itemId=24276002731&vendorItemId=91292415675&spec=10305199",
      "image": "https://shopping-phinf.pstatic.net/main_5108538/51085388217.jpg"
    },
    {
      "title": "벤큐 WiT MindDuo 2 아이케어 LED 스탠드 블루",
      "price": "426780원",
      "mall": "롯데ON",
      "category3": "스탠드",
      "category4": "LED스탠드",
      "link": "https://www.lotteon.com/p/product/LO2375102242?sitmNo=LO2375102242_2375102243&ch_no=100065&ch_dtl_no=1000030&entryPoint=pcs&dp_infw_cd=CHT",
      "image": "https://shopping-phinf.pstatic.net/main_5084900/50849007780.jpg"
    }
  ],
  "Orbitkey Desk Mat": [
    {
      "title": "오비키 데스크 매트 (L) 스톤 ORBITKEY Desk Mat (L) Stone",
      "price": "135000원",
      "mall": "아크오브디자인",
      "category3": "데스크용품",
      "category4": "데스크패드",
      "link": "https://smartstore.naver.com/main/products/5292664919",
      "image": "https://shopping-phinf.pstatic.net/main_8283715/82837157158.1.jpg"
    },
    {
      "title": "오비키 데스크매트 라지(L) (2색상)",
      "price": "135000원",
      "mall": "FRAME",
      "category3": "데스크용품",
      "category4": "데스크패드",
      "link": "http://f-rame.com/deskmat/?idx=535",
      "image": "https://shopping-phinf.pstatic.net/main_2534456/25344564388.1.jpg"
    },
    {
      "title": "오비키 데스크매트 슬림(Slim) (2색상)",
      "price": "110000원",
      "mall": "FRAME",
      "category3": "데스크용품",
      "category4": "데스크패드",
      "link": "http://f-rame.com/deskmat/?idx=1024",
      "image": "https://shopping-phinf.pstatic.net/main_4070537/40705372107.jpg"
    }
  ],
  "로지텍 MX Master 3S": [
    {
      "title": "로지텍 MX MASTER 3S 그래파이트",
      "price": "129890원",
      "mall": "네이버",
      "category3": "마우스",
      "category4": "무선마우스",
      "link": "https://search.shopping.naver.com/catalog/52648750882",
      "image": "https://shopping-phinf.pstatic.net/main_5264875/52648750882.20250124152551.jpg"
    },
    {
      "title": "로지텍 MX MASTER 3S FOR MAC 페일그레이",
      "price": "134690원",
      "mall": "네이버",
      "category3": "마우스",
      "category4": "무선마우스",
      "link": "https://search.shopping.naver.com/catalog/52778703162",
      "image": "https://shopping-phinf.pstatic.net/main_5277870/52778703162.20250203110506.jpg"
    },
    {
      "title": "(로지텍 정품) 로지텍 MX MASTER 3S 무선 마우스 그래파이트",
      "price": "119790원",
      "mall": "Aend",
      "category3": "마우스",
      "category4": "무선마우스",
      "link": "https://smartstore.naver.com/main/products/11551656571",
      "image": "https://shopping-phinf.pstatic.net/main_8909616/89096166977.jpg"
    }
  ],
  "Anker 무선 충전기": [
    {
      "title": "앤커 ANKER 프라임 충전 스테이션 100W A1902 블랙",
      "price": "67910원",
      "mall": "네이버",
      "category3": "휴대폰충전기",
      "category4": "충전기",
      "link": "https://search.shopping.naver.com/catalog/52420939652",
      "image": "https://shopping-phinf.pstatic.net/main_5242093/52420939652.20250113154531.jpg"
    },
    {
      "title": "앤커 ANKER 3in1 큐브 맥세이프 올인원 무선충전기 Y1811 그레이",
      "price": "159200원",
      "mall": "네이버",
      "category3": "휴대폰충전기",
      "category4": "충전기",
      "link": "https://search.shopping.naver.com/catalog/52420938636",
      "image": "https://shopping-phinf.pstatic.net/main_5242093/52420938636.20250219103442.jpg"
    },
    {
      "title": "앤커 ANKER 파워웨이브 마그네틱 2in1 스탠드 무선충전기 A2543 화이트",
      "price": "27990원",
      "mall": "네이버",
      "category3": "휴대폰충전기",
      "category4": "충전기",
      "link": "https://search.shopping.naver.com/catalog/52422454618",
      "image": "https://shopping-phinf.pstatic.net/main_5242245/52422454618.20250110141129.jpg"
    }
  ],
  "탁상용 미니 식물": [
    {
      "title": "핑크싱고니움 공기정화식물 탁상용 미니화분",
      "price": "17000원",
      "mall": "두왑플랜트",
      "category3": "원예/식물",
      "category4": "공기정화식물",
      "link": "https://smartstore.naver.com/main/products/5922645777",
      "image": "https://shopping-phinf.pstatic.net/main_8346714/83467145265.jpg"
    },
    {
      "title": "푸테리스 행잉식물 탁상용 미니초음파가습기 대형가습기 병원용",
      "price": "26330원",
      "mall": "마이올드퍼피",
      "category3": "원예/식물",
      "category4": "공기정화식물",
      "link": "https://smartstore.naver.com/main/products/10277803462",
      "image": "https://shopping-phinf.pstatic.net/main_8782230/87822307286.jpg"
    },
    {
      "title": "꽃다발 정원 인공 스타 장식 웨딩 식물 미니 탁상 시뮬레이션 Cm blue 1PC",
      "price": "36000원",
      "mall": "Hazelfive",
      "category3": "원예/식물",
      "category4": "공기정화식물",
      "link": "https://smartstore.naver.com/main/products/11691717130",
      "image": "https://shopping-phinf.pstatic.net/main_8923622/89236227597.jpg"
    }
  ]
}
```