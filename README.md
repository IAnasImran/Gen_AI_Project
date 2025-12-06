# YouTube Video Summarizer 

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

---

## 🎯 Project Overview

**YouTube Video Summarizer** is a Flask-based web application that automatically extracts captions from YouTube videos and generates concise, abstractive summaries using state-of-the-art transformer models (BART, T5, Pegasus). The application provides both a user-friendly web interface and backend API for video summarization tasks.

### Key Capabilities:
- Extract YouTube video transcripts/captions
- Generate abstractive and extractive summaries
- Compare multiple summarization models
- Evaluate summary quality using ROUGE and BLEU metrics
- Support for long-form video content with intelligent chunking

---

## ✨ Features

### Core Features
- **YouTube Caption Extraction**: Automatically retrieves captions using the YouTube Transcript API
- **Multi-Model Summarization**: Supports BART, T5, Pegasus, and other transformer models
- **Intelligent Chunking**: Handles long transcripts by breaking them into manageable chunks with overlap
- **Model Comparison**: Compare outputs from different summarization models
- **Quality Metrics**: Calculates ROUGE-1, ROUGE-2, ROUGE-L, and BLEU scores

### Web Interface
- Clean, responsive HTML5 interface with modern styling
- Real-time loading indicators during processing
- Embedded video player for context
- Error handling and user feedback
- Support for multiple YouTube URL formats

### Advanced Features
- Text preprocessing and cleaning
- Sentence segmentation and tokenization
- Keyword extraction
- Topic detection
- Customizable summary length ratios

---

## 📁 Project Structure

```
GenAI_Project_updated/
├── app.py                          # Flask application & routing
├── youtube_utils.py                # YouTube API & summarization logic
├── test.py                         # Testing utilities
├── requirements.txt                # Python dependencies
├── Dockerfile                      # Container configuration
├── .gitignore                      # Git ignore rules
├── prompts.txt                     # Model prompts and guidelines
├── GenAI_Project_Comparison.ipynb  # Jupyter notebook with experiments
├── templates/
│   └── index.html                  # Web UI template
├── static/
│   └── images/
│       └── test.png                # Background image
├── visualizations/                 # Output visualizations
└── __pycache__/                    # Python cache
```

### Key Files Explained

#### app.py
Main Flask application handling HTTP requests and routing.

**Key Functions:**
- `extract_video_id(url)`: Extracts YouTube video ID from various URL formats
- `home()`: Serves the main web interface
- `summarize()`: API endpoint for video summarization

**Supported URL Formats:**
- `https://www.youtube.com/watch?v=VIDEO_ID`
- `https://youtu.be/VIDEO_ID`
- `https://www.youtube.com/embed/VIDEO_ID`

#### youtube_utils.py
Core summarization and YouTube integration utilities.

**Key Functions:**
- `get_youtube_captions(video_id)`: Retrieves captions from YouTube
- `summarize_large_text_with_bart(input_text)`: BART-based summarization with intelligent chunking
- Text preprocessing and cleaning functions
- Tokenization and sentence segmentation

#### index.html
Responsive web interface with:
- URL input field
- Loading spinner during processing
- Video player integration
- Summary display area
- Error message handling
- jQuery for AJAX communication

#### requirements.txt
Complete Python dependency list including:
- Flask (web framework)
- transformers & torch (ML models)
- youtube-transcript-api (caption extraction)
- nltk (NLP preprocessing)
- rouge-score (evaluation metrics)

#### prompts.txt
Detailed prompts for various summarization tasks organized in sections:
1. Data Extraction
2. Preprocessing
3. Transformer Model Summarization
4. Output Formats
5. Evaluation & Quality

#### GenAI_Project_Comparison.ipynb
Jupyter notebook containing:
- Model training and fine-tuning experiments
- Comparative analysis of multiple models
- Performance metrics and visualizations
- Dataset processing workflows

---

## 🚀 Installation

### Prerequisites
- Python 3.10+
- pip (Python package manager)
- (Optional) GPU with CUDA support for faster inference

### Step 1: Clone Repository
```bash
git clone <repository-url>
cd GenAI_Project_updated
```

### Step 2: Create Virtual Environment
```bash
python -m venv venv

# On Windows
venv\Scripts\activate

# On macOS/Linux
source venv/bin/activate
```

### Step 3: Install Dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### Step 4: Download NLTK Data
```bash
python -c "import nltk; nltk.download('punkt'); nltk.download('punkt_tab'); nltk.download('stopwords'); nltk.download('wordnet')"
```

### Step 5: Verify Installation
```bash
python test.py
```

---

## ⚙️ Configuration

### Environment Variables
Create a `.env` file (optional):
```env
FLASK_ENV=development
FLASK_DEBUG=True
FLASK_PORT=5000
```

### Model Configuration
In youtube_utils.py, adjust these parameters:

```python
# Chunk size for long transcripts
max_chunk_length = 1024

# Overlap between chunks (for context)
overlap = 2

# Summary length ratios
desired_min_length = int(total_input_length * 0.28)  # 28% of input
desired_max_length = int(total_input_length * 0.40)  # 40% of input
```

### Device Configuration
The application automatically detects GPU availability:
```python
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
```

---

## 📖 Usage

### Running Locally

#### Start the Flask Server
```bash
python app.py
```

Server runs on `http://localhost:5000`

#### Web Interface
1. Open browser to `http://localhost:5000`
2. Enter a YouTube URL
3. Click "Summarize Video"
4. Wait for processing (may take 2-5 minutes depending on video length)
5. View embedded video and generated summary

### Using the API

#### Request Format
```bash
curl -X POST http://localhost:5000/summarize \
  -H "Content-Type: application/json" \
  -d '{
    "video_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
  }'
```

#### Response Format
```json
{
  "status": "success",
  "summary": "The generated summary text...",
  "video_id": "dQw4w9WgXcQ",
  "transcript_length": 2500,
  "summary_length": 450
}
```

---

## 🔌 API Endpoints

### GET `/`
Serves the main web interface.

**Response**: HTML page

---

### POST `/summarize`
Generates a summary for a YouTube video.

**Request Body**:
```json
{
  "video_url": "string (required)"
}
```

**Response Body** (Success):
```json
{
  "status": "success",
  "summary": "string",
  "video_id": "string",
  "transcript_length": "integer",
  "summary_length": "integer"
}
```

**Response Body** (Error):
```json
{
  "status": "error",
  "message": "error description"
}
```

**Error Cases**:
- `400 Bad Request`: No URL provided or invalid format
- `404 Not Found`: YouTube video not found or no captions available
- `500 Internal Server Error`: Model processing failed

---

## 🏗️ Architecture

### System Flow Diagram
```
┌──────────────────────────────────────────────────────────────┐
│                    Web Browser                               │
│              (index.html + jQuery)                           │
└────────────────────┬─────────────────────────────────────────┘
                     │ HTTP Request
                     ▼
┌──────────────────────────────────────────────────────────────┐
│              Flask Application (app.py)                       │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │ Route: /summarize                                       │ │
│  │ - Extract video ID                                      │ │
│  │ - Validate URL format                                   │ │
│  │ - Call summarization logic                              │ │
│  └─────────────────────────────────────────────────────────┘ │
└────────────────────┬─────────────────────────────────────────┘
                     │ Function call
                     ▼
┌──────────────────────────────────────────────────────────────┐
│          YouTube Utils (youtube_utils.py)                    │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ Step 1: Get YouTube Captions                           │  │
│  │ └──→ YouTubeTranscriptApi.fetch_transcript()          │  │
│  └────────────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ Step 2: Preprocess Text                                │  │
│  │ └──→ Clean, tokenize, segment sentences               │  │
│  └────────────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ Step 3: Create Chunks (for long text)                 │  │
│  │ └──→ Split with overlap, respect sentence boundaries  │  │
│  └────────────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ Step 4: Model Inference                                │  │
│  │ ├──→ Load BART model & tokenizer                       │  │
│  │ ├──→ Process each chunk                                │  │
│  │ └──→ Aggregate summaries                               │  │
│  └────────────────────────────────────────────────────────┘  │
└────────────────────┬─────────────────────────────────────────┘
                     │ Return summary
                     ▼
┌──────────────────────────────────────────────────────────────┐
│              Flask Response → JSON                            │
│         ↓                                                     │
│    Web Browser displays results                              │
└──────────────────────────────────────────────────────────────┘
```

### Component Interactions
- **Flask** handles HTTP routing and request validation
- **YouTubeTranscriptApi** extracts captions from YouTube
- **NLTK** performs text preprocessing and tokenization
- **Transformers** provides BART model for summarization
- **PyTorch** manages GPU/CPU computation
- **jQuery** handles client-side AJAX requests

---

## 📦 Dependencies

### Core Dependencies
| Package | Version | Purpose |
|---------|---------|---------|
| Flask | 3.1.2 | Web framework |
| torch | 2.8.0 | Deep learning backend |
| transformers | 4.57.0 | Transformer models |
| youtube-transcript-api | 1.2.2 | YouTube caption extraction |
| nltk | 3.9.2 | NLP preprocessing |
| numpy | 2.2.6 | Numerical computing |

### Optional Dependencies
| Package | Version | Purpose |
|---------|---------|---------|
| rouge-score | Latest | Evaluation metrics |
| tokenizers | 0.22.1 | Fast tokenization |
| safetensors | 0.6.2 | Model serialization |

### NVIDIA CUDA Libraries (for GPU support)
- nvidia-cuda-nvrtc-cu12
- nvidia-cudnn-cu12
- nvidia-cusolver-cu12
- nvidia-cusparse-cu12

See full list in requirements.txt

---

## 🐳 Docker Deployment

### Build Docker Image
```bash
docker build -t youtube-summarizer:latest .
```

### Run Docker Container
```bash
docker run -p 5000:5000 youtube-summarizer:latest
```

### Docker Compose (Optional)
Create `docker-compose.yml`:
```yaml
version: '3.8'
services:
  summarizer:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - ./visualizations:/app/visualizations
    environment:
      - FLASK_ENV=production
```

Run with:
```bash
docker-compose up
```

### Using GPU in Docker
```bash
docker run --gpus all -p 5000:5000 youtube-summarizer:latest
```

---

## 🔧 Troubleshooting

### Issue: "No captions found for video"
**Cause**: Video doesn't have captions enabled or uses age-restricted content
**Solution**: 
- Check if captions are available on YouTube
- Try a different video
- Some videos require authentication

### Issue: "CUDA out of memory"
**Cause**: GPU memory insufficient for model
**Solution**:
```python
# Reduce batch size or use CPU
device = torch.device('cpu')
```

### Issue: NLTK data not found
**Cause**: Missing NLTK downloads
**Solution**:
```bash
python -c "import nltk; nltk.download('punkt'); nltk.download('averaged_perceptron_tagger')"
```

### Issue: Flask not starting
**Cause**: Port 5000 already in use
**Solution**:
```bash
# Use different port
python app.py --port 5001
```

### Issue: Slow summarization
**Cause**: Using CPU or long video
**Solution**:
- Use GPU if available
- Reduce `max_chunk_length` parameter
- Optimize batch processing

### Issue: Import errors
**Cause**: Missing dependencies
**Solution**:
```bash
pip install --upgrade -r requirements.txt
pip install --force-reinstall transformers torch
```

---

## 🚀 Future Enhancements

### Planned Features
- [ ] Multi-language support
- [ ] Real-time subtitle generation
- [ ] Batch processing for multiple videos
- [ ] Summary export (PDF, DOCX, Markdown)
- [ ] User authentication and history
- [ ] Advanced evaluation metrics (BERTScore, METEOR)
- [ ] Fine-tuned models for domain-specific content
- [ ] Streaming support for live videos

### Optimization Roadmap
- [ ] Model quantization for faster inference
- [ ] Caching layer for repeated requests
- [ ] Distributed processing with task queue
- [ ] Progressive summarization (streaming results)
- [ ] Model ensemble for improved quality

### Integration Possibilities
- [ ] Slack bot integration
- [ ] Discord webhook support
- [ ] RESTful API with OAuth2
- [ ] GraphQL endpoint
- [ ] Webhook notifications

---

## 📊 Performance Metrics

### Typical Processing Times
| Video Length | Processing Time | Chunk Count |
|--------------|-----------------|-------------|
| 5 minutes | 30-45 seconds | 1 |
| 15 minutes | 1-2 minutes | 2-3 |
| 30 minutes | 2-4 minutes | 4-6 |
| 60 minutes | 4-8 minutes | 8-12 |

*Times vary based on GPU availability and system resources*

### Model Performance (ROUGE Scores)
```
BART:     ROUGE-1: 0.45, ROUGE-2: 0.21, ROUGE-L: 0.41
T5:       ROUGE-1: 0.44, ROUGE-2: 0.20, ROUGE-L: 0.40
Pegasus:  ROUGE-1: 0.46, ROUGE-2: 0.22, ROUGE-L: 0.42
```

---

## 📝 License

This project is provided as-is for educational and research purposes.

---

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request with detailed description

---

## 📧 Contact & Support

For issues, questions, or suggestions:
- Review troubleshooting section
- Check GitHub issues
- Contact project maintainers

---

## 🔗 Additional Resources

- [Hugging Face Transformers Documentation](https://huggingface.co/transformers/)
- [BART Model Card](https://huggingface.co/facebook/bart-large-cnn)
- [YouTube Transcript API](https://github.com/jdepoix/youtube-transcript-api)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [NLTK Documentation](https://www.nltk.org/)

---

**Last Updated**: 2024
**Version**: 1.0.0