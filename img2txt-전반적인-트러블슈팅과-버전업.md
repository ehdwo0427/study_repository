상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# img2txt 전반적인 트러블슈팅과 버전업
## 문제점
1. 속도 문제
 - Blip 모델별로 속도가 다른데 더 낮은 모델을 사용하여 비교
2. item_list를 뽑아내는 것이 없었음
 - item_list를 기반으로 상품 추천을 해주는데 이 부분이 누락됨.

## Blip - base 모델 사용.
desk output : "a laptop computer sitting on a desk with a monitor"
GPT-4o Prompt : A sleek wooden desk bathed in soft natural light, adorned with a modern lamp, elegant stationery, a potted succulent, and a minimalist clock. A cozy chair completes the serene workspace.

desk_2 output : "a desk with a laptop and a monitor"
GPT-4o Prompt: A modern wooden desk in a well-lit home office, featuring a sleek laptop open beside a high-resolution monitor. Soft natural light filters through a nearby window, casting gentle shadows. A coffee mug and a small potted plant add warmth and life to the scene.

VRAM : 약 2GB
## Blip2 - opt - 2.7b 모델 사용
desk output : a laptop and monitor sitting on a desk in front of a computer

GPT-4o Prompt: A sleek laptop and widescreen monitor on a modern wooden desk, illuminated by soft, ambient lighting. The computer tower, subtly glowing with LED lights, sits beside them. Cables are neatly arranged, and a potted plant adds a touch of greenery.

desk_2 output : a computer monitor and a laptop computer sitting on a desk
GPT-4o Prompt: A modern office desk with a sleek computer monitor and an open laptop. The monitor displays a vibrant desktop background, while the laptop shows a spreadsheet. Soft ambient lighting casts gentle shadows, highlighting the polished wooden surface of the desk. Cables are neatly arranged, and a small potted plant adds a touch of greenery.

🎨 GPT-4o Prompt: A sleek workspace featuring a high-resolution computer monitor and a sleek laptop on a modern wooden desk. Soft natural light streams through a nearby window, casting gentle shadows. The minimalist setup is complemented by a stylish ergonomic chair, creating a cozy yet professional atmosphere, perfect for productivity.
Recommended Items: - Wireless mechanical keyboard - Adjustable LED desk lamp - Minimalist desk organizer - Small potted succulent plant - Leather desk mat

VRAM : 9818MiB

---
## 프롬프트 엔지니어링
### 변경 전
```python
messages = [

{

"role": "system",

"content": (

"You are an expert prompt engineer for text-to-image models like Stable Diffusion XL. "

"Your job is to take short captions and rewrite them into detailed, vivid, and highly descriptive prompts "

"suitable for realistic image generation. Use natural, photographic descriptions and focus on visual details, "

"lighting, atmosphere, and realistic object arrangements. Keep the total prompt under 70 tokens."

)

},

{

"role": "user",

"content": f"Caption: {caption}\nPlease rewrite this into a vivid image generation prompt."

}

],
```

#### 변경 전 문제점

| 항목               | 문제                                                     |
| ---------------- | ------------------------------------------------------ |
| ✂️ 간단한 user 메시지  | `"Caption: ...\nPlease rewrite..."` → 설명 부족            |
| 📋 system prompt | item 추천이 descriptive phrase로 생성됨                       |
| 🔍 검색 최적화 지시 없음  | `"minimalist keyboard set with tactile feel"`처럼 너무 구체적 |
| ❌ 포맷 무시 가능성      | 출력 양식이 불안정하게 생성됨                                       |

---
### 변경 후
```python
messages = [

{

"role": "system",

"content": (

"You are an expert prompt engineer and interior stylist for text-to-image models like Stable Diffusion XL.\n"

"Your job is to take a simple image caption describing a basic desk layout, and do the following:\n\n"

"1. Rewrite the caption into a vivid, photorealistic prompt that imagines a beautifully styled and enhanced version of the **same** desk setup.\n"

" ✨ You MUST preserve the original objects described in the caption (e.g., a laptop and monitor) and build on top of them with lighting, layout, and atmosphere enhancements.\n"

" The rewritten prompt MUST be under 70 tokens.\n\n"

"2. Then, separately, suggest **exactly 5** practical and stylish desk accessories that match the upgraded prompt.\n"

" Each item should be listed on its own line, using markdown-style bullet list format (start with `-`).\n"

" If you're unsure, you must still generate 5 plausible, realistic items.\n\n"

"**Output Format (strict):**\n"

"Prompt:\n<your styled prompt here (under 70 tokens)>\n\n"

"Recommended Items:\n"

"- item 1\n"

"- item 2\n"

"- item 3\n"

"- item 4\n"

"- item 5\n\n"

"**Rules:**\n"

"1. Do NOT remove or replace original caption objects (e.g., don't remove the laptop/monitor if they were mentioned).\n"

"2. Do NOT change the scene type (e.g., do NOT move it to a coffee shop or bedroom).\n"

"3. Only improve the style and detail of the existing scene.\n"

"4. The generated scene should be captured from a clean, centered, front-facing camera angle — as if looking directly at the desk from eye level. Avoid tilted or top-down views.\n"

"5. You MUST generate 5 items. Never generate fewer than 5 items.\n"

"6. If necessary, you may include objects from the original caption (e.g., laptop or monitor) as part of the 5 items, but you must still generate exactly 5 items.\n"

)

}

,

{

"role": "user",

"content": f"Caption: {caption}\nPlease rewrite this into a vivid image generation prompt."

}

]
```

#### 오늘 수정한 주요 내용
|항목|수정 결과|설명|
|---|---|---|
|✅ **System Prompt**개선|상세한 규칙 + 포맷 고정|프롬프트 형식, 객체 유지, 시점, 아이템 수 엄격 명시|
|✅ **Item은 키워드로**|`"short, search-optimized keyword"`로 제한|문장형이 아닌 단어형 물품 생성 유도|
|✅ **Rule 추가**|"avoid long phrases", "never generate fewer than 5"|GPT가 format을 무시하지 않도록 제약 강화|
|✅ **User Prompt 개선**|`"Please follow all instructions strictly."`|시스템 프롬프트를 무시하지 않도록 명령어 강조|
|🔼 `max_tokens` 증가|70 → 300|프롬프트 + 아이템 5개를 출력하려면 충분한 토큰 필요

---
## 결과
Blip - base 모델과 Bilp2 - opt -2.7b 모델을 비교해보니 결과는 비슷했다.
VRAM의 차이는 약 4.5배정도 차이가 나니 base 모델로 바꾸는게 좋겠다.

생성된 상품 리스트 = ['desk lamp', 'cable organizer', 'ergonomic chair', 'mouse pad', 'desk plant']
SDXL : ott_3d lora 사용

---
## 회고
추가적으로 추후에 위치 정보를 기반으로 하는 모델을 중간에 포함시켜서 넘겨줘도 괜찮을 것 같다.

예 : 
 Caption: "a laptop and a monitor on a desk"
 Objects Detected:
  - laptop: center
  - monitor: right
  - mug: left front corner
