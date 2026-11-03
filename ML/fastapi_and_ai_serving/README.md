# FastAPI ও AI মডেল ডিপ্লয়মেন্ট মাস্টারক্লাস (FastAPI & AI Model Serving Encyclopedia)

> *"In modern AI engineering, FastAPI is not just another web framework; it is the universal high-performance bridge that transforms raw weights, tensors, and neural networks into secure, scalable, enterprise-grade cloud microservices."*
> 
> আপনি যদি জুপাইটার নোটবুকে একটি মেশিন লার্নিং বা ডিপ লার্নিং মডেল ট্রেন করে থাকেন, তবে বাস্তব দুনিয়ায় ব্যবহারকারীদের কাছে তা পৌঁছে দেওয়ার জন্য একটি শক্তিশালী সার্ভিং ব্যাকএন্ড অপরিহার্য। এই একটি গাইডেই FastAPI-এর একদম মৌলিক ভিত্তি থেকে শুরু করে Pydantic v2 ভ্যালিডেশন, `lifespan` মডেল লোডিং মেকানিক্স, ফাইল/ইমেজ/মডেল ওয়েট আপলোড, স্ট্রিমিং LLM রেসপন্স, কনকারেন্সি আর্কিটেকচার (`async def` বনাম `def` রহস্য), ডাইনামিক রিকোয়েস্ট ব্যাচিং, এবং ডকারাইজেশন বিস্তারিতভাবে আলোচনা করা হয়েছে—**যাতে FastAPI দিয়ে AI মডেল সার্ভিংয়ের জন্য আপনাকে অন্য কোনো ডকুমেন্টেশন বা টিউটোরিয়াল খুঁজতে না হয়!**

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [FastAPI পরিচিতি ও কেন এটি Flask/Django-কে প্রতিস্থাপন করেছে?](#১-fastapi-পরিচিতি-ও-কেন-এটি-flaskdjango-কে-প্রতিস্থাপন-করেছে)
   - ১.১ ASGI বনাম WSGI এবং ইভেন্ট লুপ আর্কিটেকচার
   - ১.২ স্বয়ংক্রিয় OpenAPI ডকুমেন্টেশন (Swagger UI ও ReDoc)
   - ১.৩ বেঞ্চমার্ক পারফরম্যান্স তুলনা
2. [FastAPI কোর ফান্ডামেন্টালস ও রিকোয়েস্ট লাইফসাইকেল](#২-fastapi-কোর-ফান্ডামেন্টালস-ও-রিকোয়েস্ট-লাইফসাইকেল)
   - ২.১ পাথ প্যারামিটার, কুয়েরি প্যারামিটার ও রিকোয়েস্ট বডি
   - ২.২ স্ট্যাটাস কোড ও রেসপন্স মডেল ফিল্টারিং
   - ২.৩ Pydantic v2 ইন-ডেপথ: Field Constraints, Custom Validators
   - ২.৪ ডিপেন্ডেন্সি ইনজেকশন সিস্টেম (`Depends`)
   - ২.৫ কাস্টম মিডলওয়্যার (CORS, রিকোয়েস্ট টাইমিং, কোরিলেশন আইডি)
   - ২.৬ গ্লোবাল এক্সেপশন হ্যান্ডলিং ও স্ট্যান্ডার্ড এরর স্কিমা
3. [FastAPI-তে AI/ML মডেল লোডিং ও মেমরি ম্যানেজমেন্ট](#৩-fastapi-তে-aiml-মডেল-লোডিং-ও-মেমরি-ম্যানেজমেন্ট)
   - ৩.১ আধুনিক Lifespan Context Manager (`asynccontextmanager`)
   - ৩.২ সিঙ্গেলটন প্যাটার্ন ও গ্লোবাল স্টেট মেকানিক্স
   - ৩.৩ বিভিন্ন ফরম্যাটের মডেল লোডিং (Scikit-Learn, PyTorch, ONNX, HuggingFace)
   - ৩.৪ ক্লাউড স্টোরেজ (S3/GCS/MLflow) থেকে ডায়নামিকালি মডেল ডাউনলোড
4. [FastAPI-তে ফাইল, ইমেজ ও AI মডেল আপলোড মেকানিক্স](#৪-fastapi-তে-ফাইল-ইমেজ-ও-ai-মডেল-আপলোড-মেকানিক্স)
   - ৪.১ `UploadFile` বনাম `bytes`: মেমরি স্পুলিং মেকানিক্স
   - ৪.২ কম্পিউটার ভিশন: ইমেজ আপলোড ও টেনসর প্রি-প্রসেসিং
   - ৪.৩ অডিও ও টেক্সট ফাইল আপলোড (Whisper ASR ও PDF Ingestion)
   - ৪.৪ নতুন মডেল ওয়েটস (`.pt`, `.onnx`, `.joblib`) আপলোড ও হট-সোয়াপিং (Hot-Swapping)
5. [স্ট্রিমিং রেসপন্স ও জেনারেটিভ AI (LLM Serving)](#৫-স্ট্রিমিং-রেসপন্স-ও-জেনারেটিভ-ai-llm-serving)
   - ৫.১ Server-Sent Events (SSE) ও `StreamingResponse`
   - ৫.২ টোকেন-বাই-টোকেন স্ট্রিমিং এলএলএম চ্যাটবট
6. [কনকারেন্সি, থ্রেডিং ও পারফরম্যান্স টিউনিং (The Golden Rules)](#৬-কনকারেন্সি-থ্রেডিং-ও-পারফরম্যান্স-টিউনিং)
   - ৬.১ গোল্ডেন রুল: কখন `async def` এবং কখন সাধারণ `def` ব্যবহার করবেন?
   - ৬.২ CPU-বাউন্ড প্রেডিকশনে ইভেন্ট লুপ ব্লকিং প্রতিরোধ (`run_in_threadpool`)
   - ৬.৩ Uvicorn ও Gunicorn মাল্টি-ওয়ার্কার টিউনিং
   - ৬.৪ ডাইনামিক রিকোয়েস্ট ব্যাচিং (Dynamic Request Batching)
7. [নিরাপত্তা, প্রমাণীকরণ ও রেট লিমিটিং](#৭-নিরাপত্তা-প্রমাণীকরণ-ও-রেট-লিমিটিং)
   - ৭.১ API Key Authentication
   - ৭.২ JWT (JSON Web Token) ও OAuth2
   - ৭.৩ রেট লিমিটিং (Token Bucket / SlowAPI)
   - ৭.৪ প্রম্পট ইনজেকশন ও PII ডেটা স্যানিটাইজেশন
8. [মনিটরিং, হেলথ প্রোব ও মেট্রিক্স](#৮-মনিটরিং-হেলথ-প্রোব-ও-মেট্রিক্স)
   - ৮.১ কুবারনেটিস Liveness ও Readiness প্রোব
   - ৮.২ Prometheus মেট্রিক্স স্ক্র্যাপিং (`/metrics`)
9. [ইউনিট ও ইন্টিগ্রেশন টেস্টিং (Pytest & TestClient)](#৯-ইউনিট-ও-ইন্টিগ্রেশন-টেস্টিং)
10. [প্রোডাকশন কন্টেইনারাইজেশন (Docker & Compose)](#১০-প্রোডাকশন-কন্টেইনারাইজেশন)
11. [পূর্ণাঙ্গ এন্ড-টু-এন্ড প্রোডাকশন প্রজেক্ট কোড](#১১-পূর্ণাঙ্গ-এন্ড-টু-এন্ড-প্রোডাকশন-প্রজেক্ট-কোড)

---

# ১. FastAPI পরিচিতি ও কেন এটি Flask/Django-কে প্রতিস্থাপন করেছে?

মেশিন লার্নিং ও এআই জগতে ঐতিহ্যবাহী ফ্রেমওয়ার্কগুলোর (Flask, Django) প্রধান সমস্যা ছিল তাদের সিনক্রোনাস আর্কিটেকচার এবং টাইপ ভ্যালিডেশনের অভাব।

```
    Traditional Flask (WSGI):
    Request 1 ──────► [ Worker Thread 1 (Blocked during I/O) ] ──────► Response 1
    Request 2 ──────► [ Worker Thread 2 (Blocked during I/O) ] ──────► Response 2
    (প্রতি রিকোয়েস্টে ১টি ভারী ওএস থ্রেড, উচ্চ মেমরি খরচ, লো কনকারেন্সি)

    Modern FastAPI (ASGI + Asyncio Event Loop):
    Request 1 ──┐
    Request 2 ──┼──► [ Single Main Thread + Async Event Loop ] ──► Async I/O (Database, S3)
    Request 3 ──┤            │ (During model CPU computation)
    Request 4 ──┘            ▼
                 [ Worker Thread Pool (Auto-managed) ]
```

---

### ১.১ ASGI বনাম WSGI
- **WSGI (Web Server Gateway Interface):** সিঙ্ক্রোনাস। একটি রিকোয়েস্ট ডেটাবেস বা বাহ্যিক API-এর জন্য অপেক্ষা করলে পুরো থ্রেডটি ব্লক হয়ে বসে থাকে।
- **ASGI (Asynchronous Server Gateway Interface):** নন-ব্লকিং। পাইথনের `asyncio` ইভেন্ট লুপ ব্যবহার করে একটি একক থ্রেডেই হাজার হাজার কনকারেন্ট নেটওয়ার্ক কানেকশন হ্যান্ডেল করতে পারে।

### ১.২ স্বয়ংক্রিয় OpenAPI ডকুমেন্টেশন (Swagger UI)
FastAPI-তে কোড লেখার সাথে সাথে টাইপ হিন্টসের ভিত্তিতে স্বয়ংক্রিয়ভাবে দুটি ইন্টারেক্টিভ ডকুমেন্টেশন তৈরি হয়:
- **Swagger UI:** `http://localhost:8000/docs` (ব্রাউজার থেকেই সরাসরি মডেল টেস্ট ও ফাইল আপলোড করা যায়!)
- **ReDoc:** `http://localhost:8000/redoc` (ক্লিন ও প্রফেশনাল ডকুমেন্টেশন ভিউ)

### ১.৩ ফ্রেমওয়ার্ক বেঞ্চমার্ক তুলনা (QPS & Latency):
| ফ্রেমওয়ার্ক | আর্কিটেকচার | Requests / Sec (QPS) | ইনপুট ভ্যালিডেশন | এপিআই ডক্স |
| :--- | :---: | :---: | :---: | :---: |
| **Django** | WSGI | $\approx 2,100$ | ম্যানুয়াল ফর্ম | প্লাগইন প্রয়োজন |
| **Flask** | WSGI | $\approx 3,200$ | ম্যানুয়াল / Marshmallow | প্লাগইন প্রয়োজন |
| **FastAPI + Uvicorn** | **ASGI (uvloop)** | **$\approx 18,500+$** | **Pydantic বিল্ট-ইন** | **স্বয়ংক্রিয় Swagger** |

---

# ২. FastAPI কোর ফান্ডামেন্টালস ও রিকোয়েস্ট লাইফসাইকেল

```python
from fastapi import FastAPI, Path, Query, status
from pydantic import BaseModel, Field

app = FastAPI(title="Core ML Service", version="1.0.0")

class PredictionPayload(BaseModel):
    feature_a: float = Field(..., gt=0.0, description="Positive float feature")
    feature_b: int = Field(..., ge=0, le=100, description="Integer percentage between 0 and 100")

@app.post("/predict/{model_name}", status_code=status.HTTP_200_OK)
def predict(
    model_name: str = Path(..., description="Target model identifier"),
    version: str = Query("v1", description="Model version tag"),
    payload: PredictionPayload = ...
):
    # পাথ প্যারামিটার: model_name
    # কুয়েরি প্যারামিটার: version (e.g., /predict/xgboost?version=v2)
    # রিকোয়েস্ট বডি: payload (স্বয়ংক্রিয় ভ্যালিডেশন)
    return {
        "model": model_name,
        "version": version,
        "received_features": payload.model_dump()
    }
```

---

### ২.৩ Pydantic v2 ইন-ডেপথ: কাস্টম ভ্যালিডেটর
Pydantic v2 (Rust কোর ভিত্তিক) ইনপুট ডেটা ভ্যালিডেশন এবং টাইপ কাস্টিংকে বিদ্যুতগতির করেছে।

```python
from pydantic import BaseModel, Field, field_validator, model_validator
from typing import List, Optional

class CreditApplication(BaseModel):
    applicant_id: str
    annual_income: float = Field(..., gt=0, description="Annual income in USD")
    debt_amount: float = Field(..., ge=0)
    credit_score: int = Field(..., ge=300, le=850)
    past_defaults: Optional[int] = 0

    # ১. নির্দিষ্ট ফিল্ড ভ্যালিডেটর
    @field_validator("applicant_id")
    @classmethod
    def validate_id_prefix(cls, v: str) -> str:
        if not v.startswith("APP-"):
            raise ValueError("Applicant ID অবশ্যই 'APP-' দিয়ে শুরু হতে হবে!")
        return v

    # ২. সমগ্র মডেল লেভেল ভ্যালিডেটর (Cross-field validation)
    @model_validator(mode="after")
    def check_debt_to_income_ratio(self):
        if self.debt_amount > (self.annual_income * 2.0):
            raise ValueError("ঋণের পরিমাণ বার্ষিক আয়ের দ্বিগুণের বেশি হতে পারে না!")
        return self
```

---

### ২.৪ ডিপেন্ডেন্সি ইনজেকশন সিস্টেম (`Depends`)
ডিপেন্ডেন্সি ইনজেকশন কোড রিইউজেবিলিটি, প্রমাণীকরণ ও রিসোর্স শেয়ারিংয়ের মেরুদণ্ড।

```python
from fastapi import Depends, Header, HTTPException, status

# রিইউজেবল ডিপেন্ডেন্সি
def verify_api_key(x_api_key: str = Header(..., description="Secret Gateway API Key")):
    VALID_KEYS = {"secret-token-123", "enterprise-key-999"}
    if x_api_key not in VALID_KEYS:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="অবৈধ বা মেয়াদোত্তীর্ণ API Key!"
        )
    return x_api_key

# এন্ডপয়েন্টে ডিপেন্ডেন্সি ইনজেক্ট করা
@app.post("/v1/secure-predict")
def secure_predict(payload: dict, token: str = Depends(verify_api_key)):
    return {"message": "প্রমাণীকরণ সফল!", "authorized_token": token}
```

---

# ৩. FastAPI-তে AI/ML মডেল লোডিং ও মেমরি ম্যানেজমেন্ট

একটি মারাত্মক ভুল হলো প্রেডিকশন এন্ডপয়েন্টের ভেতরে প্রতিবার মডেল লোড করা (`joblib.load('model.pkl')`)! এটি করলে প্রতি রিকোয়েস্টে কয়েক সেকেন্ড সময় নষ্ট হবে এবং সার্ভার কয়েক মুহূর্তেই OOM (Out Of Memory) হয়ে ক্র্যাশ করবে।

মডেল লোড করার আধুনিক ও একমাত্র সঠিক উপায় হলো **Lifespan Context Manager**।

```
                       Uvicorn Server Starts
                                 │
                                 ▼
                     [ Lifespan Context Manager ]
                     Load ML Models into Memory (RAM / GPU VRAM)
                     Open Database Connection Pools
                                 │
                                 ▼
                   Server is Ready to Serve Traffic
                   (Incoming Requests share the SAME Loaded Model in Memory)
                                 │
                                 ▼
                       Uvicorn Server Shuts Down
                     [ Clean up GPU VRAM & Close DB Pools ]
```

---

### ৩.১ আধুনিক Lifespan Context Manager ইমপ্লিমেন্টেশন

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
import joblib
import torch
import onnxruntime as ort

# গ্লোবাল স্টেট ডিকশনারি
ml_models = {}

@asynccontextmanager
async def lifespan(app: FastAPI):
    # ==========================================
    # ১. স্টার্টআপ: সার্ভার চালু হওয়ার সময় একবারই চলবে
    # ==========================================
    print("🚀 [Startup] এআই মডেলসমূহ মেমরিতে লোড করা হচ্ছে...")
    
    # স্কিট-লার্ন / XGBoost মডেল লোড
    # ml_models["tabular_model"] = joblib.load("fraud_model.joblib")
    
    # ONNX Runtime সেশন লোড (CPU/GPU)
    # ml_models["onnx_session"] = ort.InferenceSession("resnet50.onnx")
    
    # ডামি মডেল সিমুলেশন
    class FastClassifier:
        def predict(self, features):
            return [1 if x[0] > 0.5 else 0 for x in features]
            
    ml_models["fraud_detector"] = FastClassifier()
    print("✅ [Startup] সকল মডেল সফলভাবে মেমরিতে প্রস্তুত!")

    yield # এখানে সার্ভার রানিং থাকবে এবং রিকোয়েস্ট প্রসেস করবে

    # ==========================================
    # ২. শাটডাউন: সার্ভার বন্ধ হওয়ার সময় চলবে
    # ==========================================
    print("🛑 [Shutdown] মেমরি ও জিপিইউ ভি-র‍্যাম খালি করা হচ্ছে...")
    ml_models.clear()
    if torch.cuda.is_available():
        torch.cuda.empty_cache()
    print("👋 [Shutdown] ক্লিনআপ সম্পন্ন!")

app = FastAPI(lifespan=lifespan)

@app.post("/predict")
def predict_fraud(features: list[float]):
    model = ml_models.get("fraud_detector")
    if not model:
        raise HTTPException(status_code=503, detail="মডেল মেমরিতে পাওয়া যায়নি!")
    prediction = model.predict([features])
    return {"prediction": prediction[0]}
```

---

# ৪. FastAPI-তে ফাইল, ইমেজ ও AI মডেল আপলোড মেকানিক্স

AI অ্যাপ্লিকেশনগুলোতে ক্লায়েন্ট প্রায়ই ছবি (Computer Vision), অডিও/ডকুমেন্ট (Whisper/RAG), অথবা সরাসরি নতুন মডেল ওয়েটস (`.pt` বা `.onnx`) আপলোড করে।

### ৪.১ `UploadFile` বনাম `bytes`: পার্থক্য কী?
- **`bytes` (e.g., `file: bytes = File(...)`):** ফাইলটি সরাসরি র‍্যামে (RAM) সম্পূর্ণ লোড হয়। যদি কোনো ইউজার ২ জিবি ওজনের ফাইল আপলোড করে, তবে সার্ভারের মেমরি এক নিমিষেই স্পাইক করবে।
- **`UploadFile` (ইন্ডাস্ট্রিয়াল স্ট্যান্ডার্ড):** এটি একটি স্পুলড ফাইল (`SpooledTemporaryFile`)। নির্দিষ্ট সাইজ (যেমন ১ মেগাবাইট) পর্যন্ত মেমরিতে থাকে, এরপর স্বয়ংক্রিয়ভাবে ডিস্কে টেম্পোরারি ফাইলে স্ট্রিম হয়। এটি মেমরি ক্র্যাশ হওয়া থেকে সার্ভারকে রক্ষা করে।

---

### ৪.২ কম্পিউটার ভিশন: ইমেজ আপলোড ও টেনসর প্রসেসিং

```python
from fastapi import FastAPI, UploadFile, File, HTTPException
import io
from PIL import Image
import numpy as np

app = FastAPI()

@app.post("/v1/vision/classify-image")
async def classify_image(file: UploadFile = File(..., description="Input image (JPG, PNG)")):
    # ১. ফাইল এক্সটেনশন ও MIME টাইপ ভ্যালিডেশন
    if file.content_type not in ["image/jpeg", "image/png", "image/webp"]:
        raise HTTPException(
            status_code=400, 
            detail="অবৈধ ফাইল ফরম্যাট! কেবল JPEG, PNG বা WebP ছবি গ্রহণযোগ্য।"
        )

    # ২. নন-ব্লকিং অ্যাসিনক্রোনাস রিডিং
    contents = await file.read()
    
    # ৩. PIL দিয়ে ইমেজ ওপেন ও টেনসর প্রি-প্রসেসিং
    try:
        image = Image.open(io.BytesIO(contents)).convert("RGB")
        image = image.resize((224, 224))
        img_array = np.array(image, dtype=np.float32) / 255.0 # নরমালাইজেশন
        
        # চ্যানেল ফার্স্ট ফরম্যাট: [H, W, C] -> [C, H, W] -> Batch Dimension [1, C, H, W]
        img_tensor = np.transpose(img_array, (2, 0, 1))
        img_batch = np.expand_dims(img_tensor, axis=0)
    except Exception as e:
        raise HTTPException(status_code=422, detail=f"ছবি প্রসেস করতে ব্যর্থ: {str(e)}")

    # ৪. ডামি মডেল প্রেডিকশন
    predicted_class = "Industrial_Defect_Crack"
    confidence = 0.942

    return {
        "filename": file.filename,
        "content_type": file.content_type,
        "image_shape": list(img_batch.shape),
        "prediction": predicted_class,
        "confidence": confidence
    }
```

---

### ৪.৩ ডাইনামিক মডেল আপলোড ও হট-সোয়াপিং (Hot-Swapping AI Models via API)
প্রোডাকশন সার্ভার রিস্টার্ট না করেই লাইভ ট্রাফিকের মধ্যে নতুন ভার্সনের মডেল আপলোড ও সক্রিয় করার কৌশল:

```python
import os
import shutil
import hashlib
from fastapi import FastAPI, UploadFile, File, Form, HTTPException

app = FastAPI()
MODELS_DIR = "./saved_models"
os.makedirs(MODELS_DIR, exist_ok=True)

loaded_registry = {"current_version": "v1.0", "weights_path": "./saved_models/model_v1.0.onnx"}

@app.post("/v1/models/upload-weights")
async def upload_model_weights(
    version: str = Form(..., description="New model version tag (e.g. v2.0)"),
    file: UploadFile = File(..., description="Model binary file (.onnx, .pt, .joblib)")
):
    # ১. এক্সটেনশন যাচাই
    allowed_extensions = {".onnx", ".pt", ".joblib", ".safetensors"}
    _, ext = os.path.splitext(file.filename)
    if ext not in allowed_extensions:
        raise HTTPException(status_code=400, detail="অননুমোদিত মডেল ফাইল ফরম্যাট!")

    target_path = os.path.join(MODELS_DIR, f"model_{version}{ext}")

    # ২. চাঙ্ক আকারে স্ট্রিমিং রাইট ও SHA-256 চেকসাম গণনা (মেমরি সেভ করে)
    hasher = hashlib.sha256()
    with open(target_path, "wb") as buffer:
        while chunk := await file.read(1024 * 1024): # ১ মেগাবাইট চাঙ্ক
            buffer.write(chunk)
            hasher.update(chunk)

    checksum = hasher.hexdigest()

    # ৩. হট-সোয়াপিং: মেমরিতে রেজিস্ট্রি পয়েন্টার পরিবর্তন
    loaded_registry["current_version"] = version
    loaded_registry["weights_path"] = target_path

    return {
        "status": "success",
        "message": f"মডেল {version} সফলভাবে আপলোড ও হট-সোয়াপ করা হয়েছে!",
        "file_path": target_path,
        "sha256_checksum": checksum
    }
```

---

# ৫. স্ট্রিমিং রেসপন্স ও জেনারেটিভ AI (LLM Serving)

একটি লার্জ ল্যাঙ্গুয়েজ মডেলের পুরো উত্তর তৈরি হতে ২০-৩০ সেকেন্ড সময় লাগতে পারে। ব্যবহারকারীকে এতক্ষণ ব্ল্যাঙ্ক স্ক্রিন না দেখিয়ে টোকেন জেনারেট হওয়ার সাথে সাথে টাইপরাইটার ইফেক্টের মতো ব্রাউজারে পাঠানোকে **Streaming Response (Server-Sent Events)** বলে।

```python
import asyncio
from fastapi import FastAPI
from fastapi.responses import StreamingResponse

app = FastAPI()

async def mock_llm_token_generator(prompt: str):
    """সিমুলেটেড এলএলএম টোকেন জেনারেটর (বাস্তবে vLLM বা Ollama/OpenAI স্ট্রিমিং হবে)"""
    sample_response = f"আপনার প্রশ্ন: '{prompt}' এর উত্তর হলো: মেশিন লার্নিং মডেল প্রোডাকশনে সফলভাবে ডেপ্লয় করা একটি বড় ইঞ্জিনিয়ারিং দক্ষতা।"
    words = sample_response.split()
    
    for word in words:
        await asyncio.sleep(0.08) # ৫০-৮০ ms টোকেন জেনারেশন লেটেন্সি
        yield f"data: {word} \n\n" # SSE ফরম্যাট

@app.get("/v1/llm/stream-chat")
async def stream_chat(prompt: str):
    return StreamingResponse(
        mock_llm_token_generator(prompt), 
        media_type="text/event-stream"
    )
```

---

# ৬. কনকারেন্সি, থ্রেডিং ও পারফরম্যান্স টিউনিং (The Golden Rules)

FastAPI-তে সবচেয়ে মারাত্মক পারফরম্যান্স বিপর্যয় ঘটে `async def` বনাম সাধারণ `def` ভুল ব্যবহারের কারণে!

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                             THE FASTAPI GOLDEN RULE                              │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1. Non-Blocking I/O (Database async query, S3 download, Redis fetch):            │
│    👉 USE `async def` (Event loop stays blazing fast)                            │
│                                                                                  │
│ 2. CPU-Bound Heavy Computation (Scikit-Learn .predict(), PyTorch tensor ops):   │
│    👉 USE NORMAL `def` (FastAPI automatically pushes it to a Worker Thread Pool)│
│                                                                                  │
│ ⚠️ CRITICAL WARNING: If you write CPU-heavy PyTorch inference inside `async def`,│
│ you will FREEZE the entire ASGI event loop! Every other user request will stall! │
└──────────────────────────────────────────────────────────────────────────────────┘
```

---

### ৬.১ ইভেন্ট লুপ ব্লকিংয়ের বাস্তব উদাহরণ ও সমাধান

#### ❌ ভুল কোড (সার্ভার জ্যাম হয়ে যাবে):
```python
# মারাত্মক ভুল: async def এর ভেতর ব্লকিং সিপিইউ প্রেডিকশন!
@app.post("/bad-predict")
async def bad_predict(features: list[float]):
    # এটি পুরো ইভেন্ট লুপকে ২ সেকেন্ডের জন্য ফ্রিজ করে দেবে!
    result = heavy_sklearn_model.predict([features]) 
    return {"res": result}
```

#### ✅ সঠিক কোড (FastAPI স্বয়ংক্রিয় থ্রেডপুলে চালাবে):
```python
# সঠিক: সাধারণ def লিখলে FastAPI এটিকে ব্যাকগ্রাউন্ড ThreadPool-এ পাঠায়
@app.post("/good-predict")
def good_predict(features: list[float]):
    result = heavy_sklearn_model.predict([features])
    return {"res": result}
```

#### ✅ বিকল্প সঠিক কোড (যদি কোনো কারণে ফাংশনটি `async` রাখতেই হয়):
```python
from starlette.concurrency import run_in_threadpool

@app.post("/async-safe-predict")
async def async_safe_predict(features: list[float]):
    # পৃথক থ্রেডে পাঠিয়ে ইভেন্ট লুপ মুক্ত রাখা
    result = await run_in_threadpool(heavy_sklearn_model.predict, [features])
    return {"res": result}
```

---

### ৬.২ Uvicorn ও Gunicorn মাল্টি-ওয়ার্কার প্রোডাকশন টিউনিং
একটি একক পাইথন প্রসেস শুধুমাত্র একটি CPU কোর ব্যবহার করতে পারে (GIL সীমাবদ্ধতা)। ৪-কোর বা ৮-কোরের সার্ভারে সমস্ত কোর ব্যবহার করতে **Uvicorn Workers** চালাতে হয়:
```bash
# অপটিমাল ওয়ার্কার সূত্র: (2 x CPU Cores) + 1
# ৪-কোরের সার্ভারের জন্য ৯টি ওয়ার্কার:
uvicorn app:app --host 0.0.0.0 --port 8000 --workers 9 --loop uvloop
```

---

# ৭. নিরাপত্তা, প্রমাণীকরণ ও রেট লিমিটিং

```python
from fastapi import FastAPI, Depends, HTTPException, Security, status
from fastapi.security import APIKeyHeader
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.util import get_remote_address
from slowapi.errors import RateLimitExceeded

# ১. রেট লিমিটার (আইপি প্রতি মিনিটে সর্বোচ্চ ১০টি রিকোয়েস্ট)
limiter = Limiter(key_func=get_remote_address)
app = FastAPI()
app.state.limiter = limiter
app.add_exception_handler(RateLimitExceeded, _rate_limit_exceeded_handler)

# ২. সিকিউর API Key হেডার
api_key_header = APIKeyHeader(name="X-API-KEY", auto_error=False)

def require_api_key(api_key: str = Security(api_key_header)):
    if api_key != "production-secret-token-xyz":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN, 
            detail="প্রবেশাধিকার সংরক্ষিত: অবৈধ API Key!"
        )
    return api_key

@app.post("/v1/predict-protected")
@limiter.limit("10/minute") # রেট লিমিট গার্ড
def protected_prediction(request: Request, user: str = Depends(require_api_key)):
    return {"status": "authorized", "data": "Inference Result"}
```

---

# ৮. মনিটরিং, হেলথ প্রোব ও মেট্রিক্স

কুবারনেটিসে পড রিস্টার্ট ও ট্রাফিক রাউটিং নিয়ন্ত্রণের জন্য দুটি প্রোব থাকা বাধ্যতামূলক:
1. **Liveness Probe (`/health/live`):** পড জীবিত আছে কিনা (ক্র্যাশ হলে কুবারনেটিস রিস্টার্ট করবে)।
2. **Readiness Probe (`/health/ready`):** মডেল মেমরিতে সফলভাবে লোড হয়ে প্রেডিকশনের জন্য প্রস্তুত কিনা (প্রস্তুত না হলে লোড ব্যালান্সার ট্রাফিক পাঠাবে না)।

```python
@app.get("/health/live", status_code=200)
def liveness():
    return {"status": "alive"}

@app.get("/health/ready")
def readiness():
    if "fraud_detector" not in ml_models:
        raise HTTPException(status_code=503, detail="মডেল এখনও মেমরিতে প্রস্তুত নয়!")
    return {"status": "ready", "loaded_models": list(ml_models.keys())}
```

---

# ৯. ইউনিট ও ইন্টিগ্রেশন টেস্টিং (Pytest & TestClient)

```python
"""
test_api.py
Run via: pytest test_api.py -v
"""
from fastapi.testclient import TestClient
from app import app

client = TestClient(app)

def test_liveness():
    response = client.get("/health/live")
    assert response.status_code == 200
    assert response.json() == {"status": "alive"}

def test_successful_prediction():
    payload = {"feature_a": 12.5, "feature_b": 50}
    response = client.post("/predict/fraud", json=payload)
    assert response.status_code == 200
    assert "prediction" in response.json()

def test_input_validation_failure():
    # feature_b যদি ১০০ এর বেশি হয়, তবে ভ্যালিডেশন এরর (422) আসবে
    invalid_payload = {"feature_a": 12.5, "feature_b": 150}
    response = client.post("/predict/fraud", json=invalid_payload)
    assert response.status_code == 422
```

---

# ১০. প্রোডাকশন কন্টেইনারাইজেশন (Docker & Compose)

### প্রোডাকশন মাল্টি-স্টেজ ডকারফাইল (`Dockerfile`)

```dockerfile
# Stage 1: Build & Dependencies
FROM python:3.11-slim as builder
WORKDIR /build

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# Stage 2: Runtime Image (Minimal & Secure)
FROM python:3.11-slim as runner
WORKDIR /app

# কপি কম্পাইল্ড প্যাকেজ
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

# অ্যাপ্লিকেশন কোড ও মডেল কপি
COPY . .

# নন-রুট সিকিউর ইউজার
RUN useradd -m -u 10001 mluser && chown -R mluser /app
USER mluser

EXPOSE 8000

# বিল্ট-ইন হেলথচেক
HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health/ready')"

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4", "--loop", "uvloop"]
```

---

# ১১. পূর্ণাঙ্গ এন্ড-টু-এন্ড প্রোডাকশন প্রজেক্ট কোড (`production_ml_api.py`)

নিচের পাইথন কোডটি একটি সম্পূর্ণ, এন্টারপ্রাইজ-রেডি FastAPI সার্ভিস যাতে Lifespan মডেল লোডিং, Pydantic v2 ভ্যালিডেশন, ইমেজ আপলোড, সিঙ্গেল ও ব্যাচ প্রেডিকশন, কোরিলেশন আইডি মিডলওয়্যার এবং হেলথ প্রোব সমন্বিত রয়েছে:

```python
"""
production_ml_api.py
Enterprise AI Model Serving Backend with FastAPI, Pydantic v2 & Image Upload
Run via: uvicorn production_ml_api:app --reload --port 8000
"""
from contextlib import asynccontextmanager
from fastapi import FastAPI, HTTPException, Request, UploadFile, File, Depends, Header, status
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field, field_validator
import time
import uuid
import logging
import json
import io
import numpy as np
from PIL import Image

# ১. স্ট্রাকচার্ড JSON লগিং
class JsonFormatter(logging.Formatter):
    def format(self, record):
        log_obj = {
            "timestamp": self.formatTime(record, self.datefmt),
            "level": record.levelname,
            "message": record.getMessage()
        }
        if hasattr(record, "extra_info"):
            log_obj.update(record.extra_info)
        return json.dumps(log_obj)

logger = logging.getLogger("ai_serving")
handler = logging.StreamHandler()
handler.setFormatter(JsonFormatter())
logger.addHandler(handler)
logger.setLevel(logging.INFO)

# ২. গ্লোবাল মডেল স্টেট
ml_state = {}

@asynccontextmanager
async def lifespan(app: FastAPI):
    # স্টার্টআপে মডেল লোড
    logger.info("🚀 [Startup] এআই মডেল লোড হচ্ছে...")
    
    # ডামি ক্লাসিফায়ার সিমুলেশন (বাস্তবে joblib বা onnx runtime)
    class RealtimeFraudModel:
        def predict_proba(self, X: np.ndarray) -> np.ndarray:
            # ইনপুটের উপর ভিত্তি করে রিস্ক স্কোর
            return np.array([min(0.99, max(0.01, (row[0]/1000.0) * 0.4 + row[1] * 0.3)) for row in X])

    ml_state["fraud_model"] = RealtimeFraudModel()
    ml_state["version"] = "v2.3.1"
    logger.info("✅ [Startup] মডেল মেমরিতে প্রস্তুত!")
    yield
    # শাটডাউন ক্লিনআপ
    logger.info("🛑 [Shutdown] মেমরি খালি করা হচ্ছে...")
    ml_state.clear()

app = FastAPI(
    title="Enterprise AI Serving Platform",
    description="High-Throughput, Low-Latency Model Serving API",
    version="2.3.1",
    lifespan=lifespan
)

# ৩. CORS মিডলওয়্যার
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# ৪. রিকোয়েস্ট কোরিলেশন আইডি ও লেটেন্সি মিডলওয়্যার
@app.middleware("http")
async def correlation_and_perf_middleware(request: Request, call_next):
    corr_id = request.headers.get("X-Correlation-ID", str(uuid.uuid4()))
    start_time = time.perf_counter()
    
    response = await call_next(request)
    
    latency_ms = (time.perf_counter() - start_time) * 1000.0
    response.headers["X-Correlation-ID"] = corr_id
    response.headers["X-Response-Time-Ms"] = f"{latency_ms:.2f}"
    
    logger.info(
        f"API Call: {request.method} {request.url.path}",
        extra={"extra_info": {
            "correlation_id": corr_id,
            "status_code": response.status_code,
            "latency_ms": round(latency_ms, 2)
        }}
    )
    return response

# ৫. Pydantic v2 স্কিমা
class TransactionInput(BaseModel):
    transaction_id: str = Field(..., description="Unique UUID")
    amount: float = Field(..., gt=0.0, le=100000.0, description="USD amount")
    foreign_tx: int = Field(..., ge=0, le=1, description="1 if foreign, 0 if domestic")

    @field_validator("transaction_id")
    @classmethod
    def id_not_blank(cls, v: str) -> str:
        if not v.strip():
            raise ValueError("transaction_id cannot be blank")
        return v

class BatchTransactionInput(BaseModel):
    transactions: list[TransactionInput] = Field(..., max_length=500)

class PredictionResponse(BaseModel):
    transaction_id: str
    risk_score: float
    decision: str
    model_version: str

# ৬. এন্ডপয়েন্টস

@app.post("/v1/predict", response_model=PredictionResponse)
def predict_single(tx: TransactionInput):
    """সিঙ্গেল লো-লেটেন্সি রিয়েল-টাইম ইনফারেন্স"""
    model = ml_state.get("fraud_model")
    if not model:
        raise HTTPException(status_code=503, detail="মডেল সক্রিয় নেই!")
        
    features = np.array([[tx.amount, tx.foreign_tx]])
    score = float(model.predict_proba(features)[0])
    
    return PredictionResponse(
        transaction_id=tx.transaction_id,
        risk_score=round(score, 4),
        decision="BLOCK" if score >= 0.20 else "ALLOW",
        model_version=ml_state["version"]
    )

@app.post("/v1/batch-predict", response_model=list[PredictionResponse])
def predict_batch(batch: BatchTransactionInput):
    """উচ্চ-থ্রুপুট ভেক্টরাইজড ব্যাচ ইনফারেন্স"""
    model = ml_state.get("fraud_model")
    if not model:
        raise HTTPException(status_code=503, detail="মডেল সক্রিয় নেই!")
        
    features = np.array([[t.amount, t.foreign_tx] for t in batch.transactions])
    scores = model.predict_proba(features)
    
    results = []
    for t, score in zip(batch.transactions, scores):
        s = float(score)
        results.append(PredictionResponse(
            transaction_id=t.transaction_id,
            risk_score=round(s, 4),
            decision="BLOCK" if s >= 0.20 else "ALLOW",
            model_version=ml_state["version"]
        ))
    return results

@app.post("/v1/vision/defect-detect")
async def detect_defect(image_file: UploadFile = File(...)):
    """ইমেজ আপলোড ও কম্পিউটার ভিশন ইনফারেন্স"""
    if image_file.content_type not in ["image/jpeg", "image/png"]:
        raise HTTPException(status_code=400, detail="শুধু JPEG/PNG ছবি সমর্থিত!")
        
    contents = await image_file.read()
    image = Image.open(io.BytesIO(contents)).convert("RGB").resize((224, 224))
    img_np = np.array(image, dtype=np.float32) / 255.0
    
    # সিমুলেটেড ভিশন রেজাল্ট
    return {
        "filename": image_file.filename,
        "image_size": list(image.size),
        "detected_defect": "Surface_Crack",
        "confidence": 0.965
    }

@app.get("/health/live", status_code=200)
def liveness():
    return {"status": "alive"}

@app.get("/health/ready", status_code=200)
def readiness():
    if "fraud_model" not in ml_state:
        raise HTTPException(status_code=503, detail="Model is still loading...")
    return {"status": "ready", "model_version": ml_state.get("version")}
```

---

## 🎯 সংক্ষেপ ও সারসংক্ষেপ
1. **কখনই এন্ডপয়েন্টে মডেল লোড করবেন না:** সর্বদা `@asynccontextmanager` (Lifespan) ব্যবহার করে স্টার্টআপে একবার মডেল মেমরিতে লোড করুন।
2. **ভ্যালিডেশন:** Pydantic v2 দিয়ে ইনপুট ডেটা কড়াভাবে স্যানিটাইজ করুন যাতে মডেল পর্যন্ত কোনো ভুল টাইপ বা নাল ভ্যালু না পৌঁছায়।
3. **কনকারেন্সি গোল্ডেন রুল:** CPU-বাউন্ড মডেল ইনফারেন্সে সাধারণ `def` লিখুন অথবা `run_in_threadpool` ব্যবহার করুন যাতে ইভেন্ট লুপ ফ্রিজ না হয়।
4. **ফাইল আপলোড:** মেমরি সুরক্ষিত রাখতে সর্বদা `UploadFile` ব্যবহার করুন।
5. **মনিটরিং:** কুবারনেটিসের জন্য `/health/live` এবং `/health/ready` প্রোভাইড করুন।

---
*মাস্টার হ্যান্ডবুক:* [📚 সম্পূর্ণ মেশিন লার্নিং হ্যান্ডবুক সূচিপত্রে ফিরে যান](file:///g:/MNC/ML/README.md)
