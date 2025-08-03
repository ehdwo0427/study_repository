상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# HiDream-I1-Full
[hugging face link](https://huggingface.co/HiDream-ai/HiDream-I1-Full)

구조: Llama-3.1-8B-Instruct 기반 이미지 생성 모델로 보임

## prompt
"A clean, modern desk setup in a bright open office, with a large LG monitor displaying "
"a forest wallpaper and a MacBook beneath it. Add a minimalist LED desk lamp on the left, "
"a sleek wireless charger pad beside the laptop, and all cables neatly organized and hidden. "
"The scene should feel calm, tidy, and professional, with natural lighting and no clutter."

## negative_prompt
"blurry, low resolution, distorted, clutter, people, human, cartoon, overexposed, deformed, text"

![output-2](https://github.com/user-attachments/assets/b1ac69e4-e472-42fb-aa77-807a5f966d0c)

## prompt
"A modern office workspace featuring a large monitor on a stand displaying a forest scene "
"with tall redwood trees, showing the time as 2:55. A laptop is placed on the desk, mirroring "
"the same forest image. Multiple cables are connected to the laptop, possibly for data and power. "
"A small device with a blue button sits on the desk, likely a USB hub or external drive. "
"The background shows other desks and people working, indicating a collaborative lab or office "
"environment. A visible sign on the desk reads 'Please do not touch the screens,' suggesting the "
"setup is for research, monitoring, or visual content analysis."

## negative_prompt
"blurry, low resolution, distorted, clutter, people, human, cartoon, overexposed, deformed, text"

![output-3](https://github.com/user-attachments/assets/c8069535-9f2f-48a2-b854-1960d3ecdc67)

* Token indices sequence length is longer than the specified maximum sequence length for this model (128 > 77). Running this sequence through the model will result in indexing errors<br/>
Token indices sequence length is longer than the specified maximum sequence length for this model (128 > 77). Running this sequence through the model will result in indexing errors<br/>
The following part of your input was truncated because `max_sequence_length` is set to  128 tokens: ['for research, monitoring, or visual content analysis.']<br/>

* 토큰 수 초과로 제대로 된 이미지 생성이 안되었을 수 있음