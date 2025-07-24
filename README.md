# Face Verification App

## Project Overview

This is an on-device face verification application built with **React Native** and **Python** (via **Chaquopy**) for Android. The app uses deep learning models to detect, align, and verify faces entirely on-device, without sending biometric data to external servers.

## Features

- **Face Enrollment**: Register users by capturing their facial biometrics
- **Face Verification**: Authenticate users by comparing live face captures against stored templates
- **Local Storage**: All biometric data stored securely on the device
- **Real-time Processing**: Fast on-device face detection and verification
- **User Management**: List and manage enrolled users
- **Privacy-focused**: No cloud dependencies, all processing happens locally

## Technical Architecture

### System Overview

```
[User Interaction via UI]
        ↓
[React Native (JavaScript/TypeScript)]
        ↓
[Native Bridge (Java <-> Python) via Chaquopy]
        ↓
[Python Backend: face_module.py]
        ↓
[Model Inference using MobileFaceNet + Torch]
        ↓
[Local JSON DB: face_db.json]
```

### Key Components

- **React Native UI**:
  - Captures user name and face image
  - Generates UUID for each user
  - Sends base64-encoded image to native backend

- **Chaquopy Bridge**:
  - Enables direct calls from React Native to Python functions
  - Communicates via NativeModules in JS and Chaquopy plugin in Android

- **Python Backend**:
  - Face alignment using MTCNN
  - Feature extraction with MobileFaceNet (512-d embeddings)
  - Face verification via cosine similarity
  - Local database management

- **Data Storage**:
  - JSON-based local storage at `/data/user/0/com.myfaceapp/files/face_db.json`
  - Each user identified by UUID with name and embedding vector

## Installation

### Prerequisites
- Node.js (LTS version)
- Java Development Kit 1.8+
- Android SDK
- Android device or emulator with minimum SDK version 24 (Android 7.0+)
- Kotlin version 2.1.20
- Python dependencies (installed via Chaquopy):
  - opencv-python
  - numpy
  - facenet-pytorch
  - torch
  - Pillow

### Setup Steps

1. Clone the repository:
   ```bash
   git clone https://github.com/lavanya-raoe/FaceApp.git
   cd FaceApp
   ```

2. Install JavaScript dependencies:
   ```bash
   npm install
   ```

3. Ensure the model file is placed at:
   ```
   MyfaceApp/android/pyengine/src/main/python/models/MFN_AdaArcDistill_backbone.pth
   ```

4. Run the app:
   ```bash
   npm start
   ```

5. In a separate terminal, deploy to your connected Android device:
   ```bash
   npm run android
   ```
   
   Alternatively, you can use:
   ```bash
   npx react-native run-android
   ```

### Android Device Requirements
- Android 7.0 (Nougat) or higher
- Camera access permission
- Sufficient storage space (approximately 150MB)
- At least 2GB of RAM for smooth operation

## Usage

### Enrolling a Face
1. Launch the app
2. Navigate to the "Enroll Face" section
3. Enter a user name
4. Align your face within the camera frame
5. Tap "Enroll" to register your face

### Verifying a Face
1. Navigate to the "Verify Identity" section
2. Position your face in front of the camera
3. The app will automatically detect and verify your face
4. Results will show the closest match and confidence score

## Implementation Details

### Face Processing Pipeline

1. **Face Detection & Alignment**:
   - MTCNN model detects facial landmarks
   - Face is aligned and cropped to 112×112 pixels

2. **Feature Extraction**:
   - MobileFaceNet generates a 512-dimension embedding vector
   - Model is optimized for mobile devices

3. **Verification**:
   - Cosine similarity measures distance between embeddings
   - Default threshold of 0.3 (configurable)
   - Higher similarity scores indicate closer match

### Security Considerations

- All biometric data stays on device
- Database is stored in app's private storage
- No network transmission of face data
- UUID-based identification for privacy

## Project Structure

```
MyfaceApp/
├── android/
│   └── pyengine/
│       └── src/
│           └── main/
│               └── python/
│                   ├── face_module.py  # Core Python backend
│                   ├── align.py        # Face alignment
│                   ├── embed.py        # Embedding generation
│                   ├── mobilefacenet.py # Neural network model
│                   └── models/
│                       └── MFN_AdaArcDistill_backbone.pth # Model weights
├── src/
│   └── ... # React Native frontend code
```

## Core Functions

### Python Backend (face_module.py)

- `enroll(user_id, name, image_b64)`: Registers a new user with face embedding
- `verify(image_b64, threshold=0.3)`: Compares face against database
- `clear_all()`: Deletes the entire enrollment database
- `list_all()`: Returns all enrolled users

### Data Format

```json
{
  "8b6f0f16-9c24-43a3-a6c7-55d9606b53a8": {
    "name": "Amrit Khadka",
    "embedding": [0.123, 0.456, ...]
  }
}
```

## UI Components

The application features a modern, user-friendly dark mode interface with:

1. **Home Screen**: Main navigation hub with options for enrollment and verification
2. **Enrollment Screen**: Three-step process for registering new users
3. **Verification Screen**: Camera capture and result display with confidence metrics

## Performance and Limitations

- **Speed**: Optimized for real-time performance on modern Android devices
- **Accuracy**: MobileFaceNet provides high accuracy with a small footprint
- **Limitations**: 
  - Requires good lighting conditions
  - Single-face verification only
  - Performance varies by device hardware

## Future Improvements

- iOS support
- Multi-face recognition
- Enhanced security measures
- User interface refinements
- Performance optimizations

## Team Members

- Amrit Khadka
- Lavanya Rao

## Acknowledgments

- MobileFaceNet implementation adapted from [cavalleria/cavaface.pytorch](https://github.com/cavalleria/cavaface.pytorch)
- Face alignment using [timesler/facenet-pytorch](https://github.com/timesler/facenet-pytorch)

## References

- Insightface project: [https://github.com/deepinsight/insightface](https://github.com/deepinsight/insightface)
- Chen, S., Liu, Y., Gao, X., & Han, Z. (2018). MobileFaceNets: Efficient CNNs for Accurate Real-Time Face Verification on Mobile Devices. [arXiv:1804.07573](https://arxiv.org/abs/1804.07573)
- Zhang, K., Zhang, Z., Li, Z., & Qiao, Y. (2016). Joint Face Detection and Alignment Using Multitask Cascaded Convolutional Networks. IEEE Signal Processing Letters, 23(10), 1499-1503.
