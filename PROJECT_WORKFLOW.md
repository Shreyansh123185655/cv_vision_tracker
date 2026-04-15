# AI Vision Tracker - Complete Project Workflow

## Overview

This document provides a comprehensive workflow guide for the AI Vision Tracker project, which implements real-time object detection, tracking, and counting using YOLOv8 and DeepSORT algorithms with a Streamlit web interface.

---

## 1. Project Setup & Installation

### Prerequisites
- Python 3.7+
- Git
- Virtual environment support

### Installation Steps

```bash
# 1. Clone the repository
git clone https://github.com/Shreyansh123185655/ai-vision-tracker.git
cd ai-vision-tracker

# 2. Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Download YOLOv8 models (if not present)
mkdir -p weights/detection
# Models will be auto-downloaded by YOLOv8 on first run
```

---

## 2. Project Architecture

### File Structure
```
ai-vision-tracker/
|
|-- app.py                 # Main Streamlit application entry point
|-- config.py              # Configuration settings and paths
|-- utils.py               # Core utility functions for detection
|-- requirements.txt       # Python dependencies
|-- weights/detection/     # YOLOv8 model files
|   |-- yolov8n.pt        # Nano model (fastest)
|   |-- yolov8s.pt        # Small model (balanced)
|   |-- yolov8m.pt        # Medium model
|   |-- yolov8l.pt        # Large model
|   |-- yolov8x.pt        # Extra large model (most accurate)
|-- videos/               # Sample video files
|-- images/               # Sample image files
|-- venv/                 # Virtual environment
```

### Core Components

#### A. Streamlit Application (`app.py`)
- **Purpose**: Main web interface and application controller
- **Key Functions**:
  - Page configuration and layout setup
  - Sidebar controls for model selection
  - Confidence threshold adjustment
  - Input source selection (Image/Video/Webcam)
  - Model loading and error handling

#### B. Configuration Management (`config.py`)
- **Purpose**: Centralized configuration settings
- **Key Elements**:
  - Model paths and variants
  - Source type definitions
  - Object counter initialization
  - Directory structure handling

#### C. Utility Functions (`utils.py`)
- **Purpose**: Core detection and processing logic
- **Key Functions**:
  - `load_model()`: YOLOv8 model loading with caching
  - `infer_uploaded_image()`: Image processing pipeline
  - `infer_uploaded_video()`: Video processing pipeline
  - `infer_uploaded_webcam()`: Real-time webcam processing
  - `_display_detected_frames()`: Detection visualization

---

## 3. Application Workflow

### Phase 1: Application Startup
```
1. Streamlit initializes web server
2. Page configuration sets layout and title
3. Dependencies imported (YOLO, OpenCV, Streamlit)
4. Configuration loaded from config.py
5. UI components rendered (sidebar, main area)
```

### Phase 2: User Configuration
```
1. Model Selection
   - User selects YOLOv8 variant from dropdown
   - Options: yolov8n, yolov8s, yolov8m, yolov8l, yolov8x
   
2. Confidence Threshold
   - User adjusts slider (30-100%)
   - Higher confidence = fewer false positives
   - Lower confidence = more detections
   
3. Input Source Selection
   - Image: Single image processing
   - Video: File-based video analysis
   - Webcam: Real-time camera feed
```

### Phase 3: Model Loading
```
1. Model path constructed from config.py
2. YOLOv8 model loaded using ultralytics
3. Model cached for performance (@st.cache_resource)
4. Error handling for missing model files
```

### Phase 4: Media Processing Pipeline

#### A. Image Processing Workflow
```
1. User uploads image file
2. Image validation (jpg, jpeg, png, bmp, webp)
3. Original image displayed in left column
4. User clicks "Execution" button
5. YOLOv8 performs object detection
6. Results plotted with bounding boxes
7. Detected image displayed in right column
8. Detection results shown in expandable section
```

#### B. Video Processing Workflow
```
1. User uploads video file
2. Video preview displayed
3. User clicks "Execution" button
4. Temporary file created for processing
5. OpenCV reads video frame by frame
6. For each frame:
   a. YOLOv8 detects objects
   b. Object counters updated
   c. Results plotted and displayed
7. Process continues until video ends
8. Resources cleaned up (video release)
```

#### C. Webcam Processing Workflow
```
1. User selects webcam source
2. OpenCV accesses camera (index 0)
3. Continuous processing loop:
   a. Capture frame from camera
   b. YOLOv8 detects objects
   c. Results displayed in real-time
   d. Object counters updated
4. User clicks "Stop running" to terminate
5. Camera resources released
```

### Phase 5: Detection & Visualization
```
1. Frame preprocessing (if needed)
2. YOLOv8 inference with confidence threshold
3. Post-processing of detection results
4. Bounding box plotting with labels
5. Object counting logic execution
6. Results displayed in Streamlit interface
```

---

## 4. Technical Implementation Details

### Object Detection Process
```python
# Core detection pipeline
res = model.predict(image, conf=conf)
boxes = res[0].boxes
res_plotted = res[0].plot()[:, :, ::-1]  # BGR to RGB conversion
```

### Object Counting System
```python
# In/Out counting logic
inText = 'Vehicle In'
outText = 'Vehicle Out'
if config.OBJECT_COUNTER1 != None:
    for _, (key, value) in enumerate(config.OBJECT_COUNTER1.items()):
        inText += ' - ' + str(key) + ": " +str(value)
if config.OBJECT_COUNTER != None:
    for _, (key, value) in enumerate(config.OBJECT_COUNTER.items()):
        outText += ' - ' + str(key) + ": " +str(value)
```

### Model Caching Strategy
```python
@st.cache_resource
def load_model(model_path):
    model = YOLO(model_path)
    return model
```

---

## 5. Supported Models & Performance

### YOLOv8 Model Variants
| Model | Size | mAP50-95 | Speed (CPU) | Speed (GPU) | Parameters |
|-------|------|----------|-------------|-------------|------------|
| YOLOv8n | 640 | 37.3 | 80.4ms | 0.99ms | 3.2M |
| YOLOv8s | 640 | 44.9 | 128.4ms | 1.20ms | 11.2M |
| YOLOv8m | 640 | 50.2 | 234.7ms | 1.83ms | 25.9M |
| YOLOv8l | 640 | 52.9 | 375.2ms | 2.39ms | 43.7M |
| YOLOv8x | 640 | 53.9 | 479.1ms | 3.53ms | 68.2M |

### Performance Recommendations
- **Real-time applications**: Use YOLOv8n or YOLOv8s
- **High accuracy requirements**: Use YOLOv8l or YOLOv8x
- **Balanced performance**: Use YOLOv8m

---

## 6. Use Cases & Applications

### Traffic Management
- Vehicle counting and classification
- Traffic flow analysis
- Parking space monitoring
- Speed estimation

### Retail Analytics
- Customer counting and tracking
- Store layout optimization
- Heat map generation
- Dwell time analysis

### Industrial Monitoring
- Production line monitoring
- Safety compliance
- Equipment tracking
- Quality control

### Security & Surveillance
- Intrusion detection
- Perimeter monitoring
- Crowd analysis
- Anomaly detection

---

## 7. Deployment Options

### Local Development
```bash
# Run locally
streamlit run app.py
```

### Streamlit Cloud
- Connect GitHub repository
- Auto-deploy on changes
- Free hosting available

### Docker Deployment
```bash
# Build Docker image
docker build -t ai-vision-tracker .

# Run container
docker run -p 8501:8501 ai-vision-tracker
```

### Production Considerations
- GPU acceleration for better performance
- Load balancing for multiple users
- Model optimization for specific use cases
- Security measures for webcam access

---

## 8. Troubleshooting Guide

### Common Issues

#### Model Loading Errors
- **Problem**: Model file not found
- **Solution**: Ensure models are in `weights/detection/` directory
- **Alternative**: Models auto-download on first run

#### GPU Not Detected
- **Problem**: Slow processing on CPU
- **Solution**: Install CUDA-compatible PyTorch
- **Command**: `pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118`

#### Webcam Access Denied
- **Problem**: Browser blocks camera access
- **Solution**: Use HTTPS or localhost
- **Alternative**: Test with different browsers

#### Memory Issues
- **Problem**: Out of memory errors
- **Solution**: Use smaller models (YOLOv8n/s)
- **Alternative**: Reduce input resolution

### Performance Optimization
1. **Model Selection**: Choose appropriate model size
2. **Input Resolution**: Reduce for faster processing
3. **Confidence Threshold**: Adjust for accuracy/speed tradeoff
4. **Frame Skipping**: Skip frames in video processing
5. **GPU Acceleration**: Use CUDA-compatible PyTorch

---

## 9. Future Enhancements

### Planned Features
- [ ] DeepSORT integration for multi-object tracking
- [ ] Custom model training support
- [ ] Export functionality for results
- [ ] Batch processing capabilities
- [ ] API endpoint for integration
- [ ] Mobile app interface

### Technical Improvements
- [ ] Model quantization for edge devices
- [ ] Real-time streaming support
- [ ] Advanced counting algorithms
- [ ] Custom detection classes
- [ ] Performance monitoring dashboard

---

## 10. Development Guidelines

### Code Style
- Follow PEP 8 guidelines
- Add comprehensive docstrings
- Use type hints for better code clarity
- Implement error handling gracefully

### Testing Strategy
- Unit tests for utility functions
- Integration tests for model loading
- End-to-end tests for complete workflow
- Performance benchmarks

### Contribution Process
1. Fork the repository
2. Create feature branch
3. Implement changes with tests
4. Submit pull request
5. Code review and merge

---

## 11. Quick Start Guide

### For Immediate Use
```bash
# One-command setup and run
git clone https://github.com/Shreyansh123185655/ai-vision-tracker.git && \
cd ai-vision-tracker && \
python3 -m venv venv && \
source venv/bin/activate && \
pip install -r requirements.txt && \
streamlit run app.py
```

### First Time Usage
1. Open http://localhost:8501 in browser
2. Select YOLOv8s model (balanced performance)
3. Set confidence to 50%
4. Choose "Image" source
5. Upload a test image
6. Click "Execution" to see results

### Advanced Usage
1. Experiment with different models
2. Adjust confidence threshold
3. Try video processing
4. Test webcam functionality
5. Analyze counting results

---

## 12. Support & Resources

### Documentation
- [README.md](./README.md) - Basic project information
- [ML_CV_PIPELINE.md](./ML_CV_PIPELINE.md) - Technical pipeline details
- [DEPLOYMENT.md](./DEPLOYMENT.md) - Deployment instructions

### Community
- GitHub Issues: Report bugs and request features
- Discussions: Share ideas and ask questions
- Wiki: Additional documentation and tutorials

### External Resources
- [YOLOv8 Documentation](https://docs.ultralytics.com/)
- [Streamlit Documentation](https://docs.streamlit.io/)
- [OpenCV Documentation](https://docs.opencv.org/)

---

*This workflow document provides a comprehensive guide for understanding, using, and extending the AI Vision Tracker project. For specific implementation details, refer to the source code and inline documentation.*
