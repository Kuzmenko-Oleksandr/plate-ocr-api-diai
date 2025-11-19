# Plate OCR API (FastAPI + YOLO)

This project provides a REST API for Ukrainian license plate recognition using:
- YOLOv8 segmentation model (`best_yolo_mask.pt`) for plate detection  
- YOLOv8 OCR model (`best_ocr.pt`) for character recognition  
- FastAPI for serving the API  
- Docker for deployment  
- AWS App Runner (via ECR image) for hosting  

---

## 🚀 Features
- Accepts **two images** in one request
- Returns detected license plate text for each image
- Fast, lightweight, production-ready
- Supports AWS App Runner deployment via container image

---

## 📦 Project Structure
```
.
├── app.py               # FastAPI backend
├── Dockerfile           # Container build file
├── requirements.txt     # Python dependencies
├── models/              # YOLO models folder
│   ├── best_yolo_mask.pt
│   └── best_ocr.pt
└── README.md
```

---

## ▶️ Run Locally

### 1. Create virtual environment
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install requirements
```bash
pip install -r requirements.txt
```

### 3. Start the API
```bash
uvicorn app:app --host 0.0.0.0 --port 8000 --reload
```

### 4. Open Swagger UI
```
http://localhost:8000/docs
```

---

## 🐳 Run with Docker

### Build image
```bash
docker build -t plate-ocr-api .
```

### Run container
```bash
docker run -p 8000:8000 plate-ocr-api
```

Swagger UI:
```
http://localhost:8000/docs
```

---

## ☁️ Deploy on AWS App Runner (via ECR)

### 1. Authenticate Docker with ECR
```bash
aws ecr get-login-password --region <region>   | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
```

### 2. Build and push multi-arch image (required for App Runner)
```bash
docker buildx build   --platform linux/amd64   -t <aws_account_id>.dkr.ecr.<region>.amazonaws.com/<repo>:latest   --push .
```

### 3. Create App Runner service
- In AWS Console → App Runner  
- Source: **Container registry**  
- Choose your ECR repo  
- Runtime port: **8000**  
- Deployment: **Automatic**  

Done 🎉

---

## 📷 API Example

### Endpoint:
```
POST /plates/recognize-two
```

### Form-Data fields:
| field | type | description |
|------|------|-------------|
| images | file | first plate photo |
| images | file | second plate photo |

### Example response:
```json
{
  "results": [
    {
      "index": 0,
      "filename": "image.jpg",
      "success": true,
      "plate": "AA7777AA",
      "error": null
    },
    {
      "index": 1,
      "filename": "image.jpg",
      "success": true,
      "plate": "AA7777AA",
      "error": null
    }
  ]
}


