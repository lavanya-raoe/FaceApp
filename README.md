# Face Verification App

This is a mobile face verification application built with **React Native** and **Python** (via **Chaquopy**) for Android.

---

## 🧠 Face Add (Enrollment)

The app allows a user to **enroll their face** using the camera. It detects and aligns the face, generates a 512-dimension embedding using a lightweight deep learning model (MobileFaceNet), and stores it **locally** on the device.

---

## 📦 Storage

- The face embeddings are stored in JSON format at:

  ```
  /data/user/0/com.myfaceapp/files/face_db.json
  ```

- This is Android’s private app storage path, accessible only to the app.

- Each enrolled user is identified by a **UUID** (e.g., `8b6f0f16-9c24-43a3-a6c7-55d9606b53a8`) generated during enrollment.

- The data is stored as:

  ```json
  {
    "8b6f0f16-9c24-43a3-a6c7-55d9606b53a8": {
      "name": "John Doe",
      "embedding": [0.123, 0.456, ...]
    }
  }
  ```

---

## 🏧 Python Location

All face logic (alignment, embedding, verification, storage) is handled inside:

```
MyfaceApp/android/pyengine/src/main/python/face_module.py
```

---

## 📸 Screenshot



---

## 🧪 Functions (Python Backend)

> ℹ️ **Note:** The input image is aligned and resized to **112×112 pixels** before generating a 512-dimensional face embedding using MobileFaceNet. This ensures accuracy and compatibility with the model.

### 🔧 Detailed Functionalities

1. **enroll(user\_id, name, image\_b64)**

   - Accepts a unique `user_id`, a name, and a base64-encoded image string.
   - Aligns and normalizes the face image.
   - Generates a 512-d vector using the MobileFaceNet model.
   - Stores the embedding with the user ID and name in a local JSON file.
   - Returns a success JSON response with the user ID.

2. **verify(image\_b64, threshold=0.3)**

   - Accepts a base64-encoded image and a similarity threshold.
   - Aligns and generates an embedding for the input image.
   - Compares this against every enrolled face using cosine similarity.
   - Returns the highest similarity match, its ID and name, and whether the match passes the threshold (i.e., is verified).

3. **clear\_all()**

   - Deletes the face database JSON file from internal storage.
   - Useful for resetting or cleaning the app's state.

4. **list\_all()**

   - Returns a list of all enrolled users with their IDs and names.
   - Helps in debugging or managing users from the frontend.

### 🧠 Backend Flow Summary

- Face is first **aligned** using MTCNN for consistency.
- Then it's resized to **112×112 pixels**.
- Processed using a PyTorch-based **MobileFaceNet** model to extract the embedding.
- Embeddings are stored and later compared using **cosine similarity**.



> ℹ️ **Note:** The input image is aligned and resized to **112×112 pixels** before generating a 512-dimensional face embedding using MobileFaceNet. This ensures accuracy and compatibility with the model.

- `enroll(user_id, name, image_b64)` – Adds a new user face to the database.
- `verify(image_b64)` – Verifies a face against enrolled users.
- `clear_all()` – Clears the database.
- `list_all()` – Lists all enrolled users.

---

## ✅ Run the App

1. Start the React Native server:

   ```bash
   npm start
   ```

2. Run the Android app:

   ```bash
   npx react-native run-android
   ```

Make sure to place your model file in:

```
MyfaceApp/android/pyengine/src/main/python/models/MFN_AdaArcDistill_backbone.pth
```

---

## 🏗️ Architecture

The architecture of the Face Verification App integrates React Native (frontend) and Python (backend) using Chaquopy on Android:

### 🔄 System Overview

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

### 🔹 Key Components

- **React Native UI:**

  - Captures user name and face image.
  - Generates UUID for each user.
  - Sends base64-encoded image to native backend.

- **Chaquopy (Android-Python bridge):**

  - Enables direct calls from React Native to Python functions.
  - Communicates via NativeModules in JS and Chaquopy plugin in Android.

- **Python Backend (face\_module.py):**

  - Aligns the face using MTCNN.
  - Resizes to 112×112 and passes through MobileFaceNet model.
  - Stores or compares face embeddings.
  - Manages a local JSON database of enrolled users.

- **Model & Embedding:**

  - MobileFaceNet is used for lightweight, fast embedding generation.
  - Embeddings are 512-dimension vectors.

- **Data Storage:**

  - Stored locally in JSON format in app-private Android storage path.

---

## 🛠 Requirements

- **Node.js** (LTS version recommended)
- **Java 1.8** (required for Android builds)

Ensure Chaquopy is configured in `build.gradle` and `Python` packages like `opencv-python`, `numpy`, `facenet-pytorch`, `torch`, and `Pillow` are installed via:

```groovy
python {
    pip {
        install "opencv-python"
        install "numpy"
        install "facenet-pytorch"
        install "torch"
        install "Pillow"
    }
}
```

---

