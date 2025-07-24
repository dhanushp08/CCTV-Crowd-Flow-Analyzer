# CCTV-Crowd-Flow-Analyzer

Okay, here is the complete README file for your CCTV human detection and line crossing project, incorporating all the details we discussed, including the manual line setup and the use of a pre-trained model.

-----

# Restaurant Occupancy Monitor & People Counter (DineTrack AI) 🍽️🚶‍♂️

## Project Overview

This project implements an intelligent computer vision system designed to monitor and count human traffic within a restaurant environment using CCTV footage. It leverages a state-of-the-art object detection model (YOLOv8) to identify individuals and tracks their movement across a predefined virtual line, providing real-time "in" and "out" counts.

This system can be invaluable for understanding customer flow, managing capacity, or optimizing staffing based on foot traffic patterns.

## Features

  * **Human Detection:** Accurately identifies people in video streams using a powerful deep learning model.
  * **Virtual Line Crossing:** Defines a configurable virtual line within the video frame.
  * **Directional Counting:** Distinguishes between people entering ("IN") and exiting ("OUT") the monitored area.
  * **Persistent Tracking:** Utilizes a custom centroid-based tracking mechanism to maintain individual identities across frames and prevent double-counting.
  * **Region of Interest (ROI) Masking:** Employs an image mask to focus detection only on relevant areas of the CCTV footage, improving accuracy and efficiency.
  * **Video Output:** Generates an annotated output video visualizing detections, the virtual line, and real-time counts.

## Technologies Used

  * **Python 3.x**
  * **OpenCV (`cv2`)**: For video processing, drawing, and image manipulation.
  * **`ultralytics` (YOLOv8)**: For efficient and accurate object detection.
  * **`cvzone`**: A helper library for simplified computer vision drawing tasks.
  * **NumPy**
  * **Google Colab / Jupyter Notebook**: Development environment.

## Model & Detection

At the core of the human detection is a **pre-trained YOLOv8n (nano) model**. This model has been trained on a vast dataset (like COCO) and is highly optimized for real-time object detection across 80 different classes, including `person`.

By using a pre-trained model, the system benefits from robust and generalized human detection capabilities without requiring extensive custom training. Detections are filtered by a confidence threshold to ensure accuracy.

## How It Works

1.  **Video Input & Masking:**
      * The system takes an input video stream (either a file or a live CCTV feed).
      * A custom **mask image** is applied to the video frames. This mask defines a specific "Region of Interest" (ROI), ensuring that the YOLO model only processes pixels within the relevant area (e.g., a doorway or entrance), ignoring irrelevant background noise.
2.  **Object Detection (YOLOv8):**
      * The pre-trained YOLOv8 model analyzes the masked video frames to detect all instances of `person`.
3.  **Virtual Line Definition:**
      * A virtual "slant line" is defined by two pixel coordinates (`slant_line_start` and `slant_line_end`). **These specific pixel locations were determined manually through trial and error** by observing the video stream and adjusting the coordinates to best represent the desired counting threshold in the CCTV footage.
4.  **Custom Tracking:**
      * For each detected person, its centroid (center point) is calculated.
      * A simple centroid-based tracking mechanism (`defaultdict`) assigns a unique ID to each individual across frames. It updates the position of existing IDs and assigns new IDs to newly detected individuals based on proximity.
5.  **Line Crossing Logic:**
      * The `is_left_of_line` function mathematically determines which side of the virtual line each tracked person's centroid is currently on.
      * By comparing a person's current side to their previously recorded side, the system detects a line crossing.
      * Counters (`people_in`, `people_out`) are incremented based on the direction of the crossing. Flags are used for each tracked person to prevent multiple counts for a single crossing event (e.g., if someone lingers around the line).
6.  **Output Generation:**
      * The system overlays bounding boxes, confidence scores, the virtual line, and the "IN" / "OUT" counts directly onto the video frames.
      * The processed frames are then saved as an output video file.

## Performance

The system provides real-time human detection and counting. Performance can vary based on:

  * The complexity of the video scene.
  * The resolution of the input video.
  * The hardware running the code (CPU vs. GPU).
  * The chosen YOLOv8 model size (YOLOv8n is lightweight).

The current setup effectively tracks and counts individuals, providing an accurate representation of people flow within the defined ROI.

## How to Run 🚀

This project is designed to be run in a **Jupyter Notebook** environment, such as **Google Colab**, as it handles file paths related to Google Drive.

1.  **Get the Code:**

      * Clone this repository: `git clone https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO_NAME.git`
      * Navigate into the project directory: `cd YOUR_REPO_NAME`

2.  **Google Drive Setup:**

      * The notebook expects your input video (e.g., `D01_20240927125959.mp4`) and your mask image (`newmask.png`) to be in a specific Google Drive folder (e.g., `/content/drive/MyDrive/final/`).
      * **You must mount your Google Drive** at the beginning of your Colab session using `from google.colab import drive; drive.mount('/content/drive')`.
      * **Ensure your video and mask files are placed in the correct paths within your Google Drive** as specified in the Python code (`cap = cv2.VideoCapture(...)`, `mask = cv2.imread(...)`).
      * The output video will be saved to `/content/drive/MyDrive/final/count.mp4` (or whatever `output_path` is set to in your code).

3.  **Run the Notebook:**

      * Open `[YourNotebookName].ipynb` (e.g., `restaurant_people_counter.ipynb`) in your Jupyter/Colab environment.
      * Execute all cells sequentially. The notebook will:
          * Load the YOLOv8 model.
          * Process the video frame-by-frame.
          * Apply detection, tracking, and counting logic.
          * Generate and save an output video with annotations.

## Demo

Watch a short demonstration video of the system in action:

[](https://www.google.com/search?q=YOUR_YOUTUBE_OR_DRIVE_VIDEO_LINK_HERE)

*(Click the image above to view the demo)*

## Future Enhancements

  * **Robust Tracking:** Integrate more advanced object tracking algorithms (e.g., SORT, DeepSORT) for improved ID persistence and accuracy in complex crowd scenarios.
  * **Live Stream Integration:** Adapt the code to directly process live RTSP streams from IP cameras.
  * **Data Logging:** Store "IN" and "OUT" counts to a database (e.g., SQLite, CSV) with timestamps for historical analysis.
  * **Dashboard/UI:** Create a simple web-based dashboard (e.g., using Flask) to display real-time counts and potentially the live processed feed.
  * **Alerting:** Implement alerts (e.g., email, notification) if capacity thresholds are exceeded.
  * **Parameter Configuration:** Externalize line coordinates, thresholds, and video paths into a configuration file for easier setup without modifying code.

-----
