# YouTube Video Summarizer – Detailed README

## 📋 Table of Contents
- Project Overview
- Features
- Project Structure
- Installation
- Configuration
- Usage
- API Endpoints
- Architecture
- Dependencies
- Docker Deployment
- Troubleshooting
- Future Enhancements
- Performance Metrics
- License
- Contact

---

## 🎯 Project Overview
**YouTube Video Summarizer** is a Flask-based web application that extracts YouTube captions and generates concise summaries using transformer models like BART, T5, and Pegasus. It includes a clean web UI and a backend API.

---

## ✨ Features
### Core
- Automatic YouTube caption extraction  
- Abstractive summarization  
- Model comparison  
- ROUGE/BLEU scoring  

### UI
- Responsive web interface  
- Embedded YouTube player  
- AJAX-based summarization  
- Error handling  

### Advanced
- Intelligent chunking  
- Preprocessing + tokenization  
- Topic & keyword extraction  

---

## 📁 Project Structure
```
GenAI_Project_updated/
├── app.py
├── youtube_utils.py
├── test.py
├── requirements.txt
├── Dockerfile
├── prompts.txt
├── templates/index.html
├── static/images/test.png
└── GenAI_Project_Comparison.ipynb
```

---

## 🚀 Installation

### 1. Clone Repo
```
git clone <repo-url>
cd GenAI_Project_updated
```

### 2. Setup Virtual Environment
```
python -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```
pip install -r requirements.txt
```

### 4. Download NLTK Data
```
python -c "import nltk; nltk.download('punkt'); nltk.download('stopwords'); nltk.download('wordnet')"
```

---

## ⚙️ Configuration
Modify parameters inside **youtube_utils.py**:
```
max_chunk_length = 1024
overlap = 2
desired_min_length = 0.28 * len(input)
desired_max_length = 0.40 * len(input)
```

Device auto-selection:
```
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
```

---

## 📖 Usage

### Start Server
```
python app.py
```
Open: **http://localhost:5000**

### API Usage
```
POST /summarize
{
  "video_url": "https://youtu.be/VIDEO_ID"
}
```

Response:
```
{
  "status": "success",
  "summary": "...",
  "video_id": "...",
  "transcript_length": 2500
}
```

---

## 🔌 API Endpoints
| Endpoint | Method | Description |
|---------|--------|-------------|
| `/` | GET | Web UI |
| `/summarize` | POST | Summarize YouTube video |

---

## 🏗️ Architecture
```
User → Flask → youtube_utils.py → YouTube Transcript API
                  ↓
          Transformers (BART/T5/Pegasus)
                  ↓
             Summary Output
```

---

## 📦 Dependencies
- Flask  
- transformers  
- torch  
- youtube-transcript-api  
- nltk  
- numpy  
- rouge-score  

---

## 🐳 Docker Deployment

### Build Image
```
docker build -t youtube-summarizer .
```

### Run Container
```
docker run -p 5000:5000 youtube-summarizer
```

GPU:
```
docker run --gpus all -p 5000:5000 youtube-summarizer
```

---

## 🔧 Troubleshooting
### Captions not found  
Video may not have captions → try another.

### CUDA memory error  
Use CPU mode or reduce chunk size.

### NLTK error  
Download missing datasets.

---

## 🚀 Future Enhancements
- Multi-language summaries  
- Batch video processing  
- PDF/DOCX export  
- User accounts + history  
- BERTScore integration  

---

## 📊 Performance Metrics
| Video Length | Time | Chunks |
|--------------|------|--------|
| 5 min | 30–45 sec | 1 |
| 15 min | 1–2 min | 2–3 |
| 30 min | 3–5 min | 4–6 |
| 60 min | 5–8 min | 8–12 |

---

## 📝 License
Free for educational and research use.

---

## 📧 Contact
For help, contact project maintainers or open an issue.
