# Real-Time Webcam Object Detection

This is a browser-based web application that uses your webcam to perform real-time object detection. It identifies various objects in the video feed, draws bounding boxes around them, and triggers a visual and audible alert when a specific object class (in this case, a 'person') is detected.

This project serves as a web-based equivalent to a Python/OpenCV desktop application, using modern web technologies like TensorFlow.js to run machine learning models directly in the browser.

## Features

* **Live Webcam Feed**: Directly processes your webcam stream.
* **Real-Time Detection**: Uses the COCO-SSD model via TensorFlow.js to identify 80 common object classes in real-time.
* **Visual Feedback**: Draws colored bounding boxes around all detected objects.
* **Custom Alerts**: Highlights the target object ('person') with a red bounding box and plays a continuous alert sound.
* **User Controls**: Simple interface with a button to start and stop the detection process.
* **No Backend Needed**: The entire application runs on the client-side; no data is sent to a server.

## How It Works

The application is built with a single HTML file and relies on several powerful JavaScript libraries:

* **HTML5/CSS (Tailwind CSS)**: Provides the structure and modern styling for the user interface.
* **JavaScript**: Handles all the application logic, including webcam access, canvas drawing, and user interactions.
* **TensorFlow.js**: A library for running machine learning models in JavaScript. It's used to load the object detection model and process the video frames.
* **COCO-SSD Model**: A pre-trained object detection model that can recognize 80 different classes of objects. This is loaded via TensorFlow.js.
* **Tone.js**: A framework for creating interactive music and sounds in the browser. It's used here to generate the alert beep, replacing the need for local audio files.

The core logic follows these steps:
1.  The application loads the pre-trained COCO-SSD model.
2.  When the user clicks "Start Webcam," it requests access to the camera.
3.  A loop begins that continuously captures a frame from the webcam video.
4.  Each frame is passed to the TensorFlow.js model for prediction.
5.  The application draws the video frame onto a `<canvas>` element.
6.  It then iterates through the model's predictions and draws a bounding box and label for each detected object.
7.  If a 'person' is detected, the bounding box is colored red, and an alert sound is played using Tone.js. Otherwise, the sound is stopped.

## How to Use

1.  Save the code as an `index.html` file.
2.  Open the `index.html` file in a modern web browser (like Chrome, Firefox, or Edge).
3.  When the browser prompts you, grant permission for the page to access your webcam.
4.  Click the **"Start Webcam"** button to begin the detection.

## Customization: Using Your Own Model

This application is designed to be a template. You can adapt it to use your own custom-trained model (like the original `.h5` model).

#### 1. Convert Your Model
First, you must convert your Keras (`.h5`) or TensorFlow (`SavedModel`) model into the TensorFlow.js web format. This will produce a `model.json` file and one or more binary `groupX-shardYofZ.bin` weight files.

You can do this using the `tensorflowjs_converter` command-line tool:
```bash
# Install the converter
pip install tensorflowjs

# Run the converter on your Keras .h5 file
tensorflowjs_converter --input_format keras \
                       /path/to/your/CustomisedCNNModel.h5 \
                       /path/to/your/web_model_directory
```

#### 2. Load Your Custom Model
Place the converted `model.json` and `.bin` files into your project directory. Then, modify the JavaScript code to load your model instead of COCO-SSD.

Find this line in the `initialize()` function:
```javascript
// Current model loading
model = await cocoSsd.load();
```
And replace it with the function to load your graph or layers model:
```javascript
// Replace with your model loading
// Make sure the path to your 'model.json' is correct
model = await tf.loadLayersModel('path/to/your/web_model_directory/model.json');
```

#### 3. Adapt the Prediction Loop
Your custom model will have a different prediction function than COCO-SSD's `model.detect()`. You will need to replace the `predictLoop` logic to match your model's input and output.

This will involve:
* **Preprocessing the image:** Resizing the video frame and normalizing pixel values to match your model's training requirements (e.g., using `tf.image.resizeBilinear` and `tf.div`).
* **Running prediction:** Calling `model.predict()` with the preprocessed tensor.
* **Post-processing the output:** Interpreting the model's output tensor to get the class label ("violent" / "notviolent") and probability.

By following these steps, you can transform this generic object detection app into a specialized tool that runs your custom violence detection model directly in the browser.
