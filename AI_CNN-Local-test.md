상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# Source Code
- Classify Model Download([desk_classify.h5](https://drive.google.com/file/d/1l65DbR2-lndBYk0A1NDKzqsGo1L7SFol/view?usp=sharing))

## desk_classify.py

```python
import numpy as np
from tensorflow.keras.models import load_model
from tensorflow.keras.preprocessing import image
from tensorflow.keras.applications.vgg16 import preprocess_input

class Desk_classifier:
    def __init__(self, threshold = 0.5):
        #threshold, model_path 추후 수정
        model_path = '...'
        self.model = load_model(model_path)
        self.threshold = threshold

    def predict(self, img_path: str) -> bool:
        img = image.load_img(img_path, target_size = (224, 224))
        x = image.img_to_array(img)
        x = np.expand_dims(x, axis = 0)
        x = preprocess_input(x)

        prob = self.model.predict(x)[0][0]
        return bool(prob >= self.threshold)
```
<br/>

## main.py

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from desk_classify import Desk_classifier
import requests
import shutil
import os
import uuid

app = FastAPI()
classifier = Desk_classifier()

class ImageRequest(BaseModel):
    image_url: str

@app.post("/classify")
async def classify_image_url(request: ImageRequest):
    temp_dir = "temp_downloads"
    os.makedirs(temp_dir, exist_ok=True)

    temp_path = os.path.join(temp_dir, f"{uuid.uuid4().hex}.jpg")

    try:
        response = requests.get(request.image_url, stream=True)
        if response.status_code != 200:
            raise HTTPException(status_code=400, detail="Failed to download image from URL")

        with open(temp_path, "wb") as f:
            for chunk in response.iter_content(1024):
                f.write(chunk)

        result = classifier.predict(temp_path)
        return {"is_desk": result}

    finally:
        if os.path.exists(temp_path):
            os.remove(temp_path)
```
<br/>
<br/>
<br/>

# Test
- Test Image<br/>
  [Cloud S3 Guide](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/클라우드-S3가이드) example image

- Local Hardware<br/>
  MacBook Pro 14(Apple M2 Pro // 16GB)

## Post
curl -X POST http://127.0.0.1:8000/classify -H "Content-Type: application/json" -d '{"image_url":"https://onthe-top-s3-example.s3.amazonaws.com/ac938676-0388-436a-9561-21d8ab4c0dcf.jpeg"}'
<br/>

## Result

### return
{"is_desk":false}
<br/>

### Console

INFO:     Started server process [17229]<br/>
INFO:     Waiting for application startup.<br/>
INFO:     Application startup complete.<br/>
1/1 ━━━━━━━━━━━━━━━━━━━━ 0s 148ms/step<br/>
INFO:     127.0.0.1:50454 - "POST /classify HTTP/1.1" 200 OK<br/>
<br/>
<br/>
<br/>

# Trouble Shooting

## Classify Error
- Noise Image, Animal Image, Documents Screenshot Image 등을 True로 Return 하는 Trouble 발생

- Fix: Image Random 생성 및 Noise Image를 추가 학습

## Type Error
- 기존 return 방식이 `numpy.bool_`으로 Type Error 발생

- Fix: desk_classify.py `return prob >= self.threshold` → `return bool(prob >= self.threshold)` 수정