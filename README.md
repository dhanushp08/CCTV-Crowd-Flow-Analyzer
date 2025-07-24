# CCTV-Crowd-Flow-Analyzer

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

## Built With

  * **Python**
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

The system provides real-time human detection and counting. System is quite accurate given there is a limited number of people either entering or exiting the location. But the code does not work well when it is SUPER-CROWDED. It performs better than a human if you exactly want to know the performance of the code. Goal is to improve it so it can work well in congested locations as well. 

## How to Run 🚀

This project is designed to be run in a **Jupyter Notebook** environment, such as **Google Colab**, as it handles file paths related to Google Drive.

1.  **Get the Code:**

      * Clone this repository: `git clone https://github.com/dhanushp08/CCTV-Crowd-Flow-Analyzer.git`
      * Navigate into the project directory: `cd CCTV-Crowd-Flow-Analyzer`

2.  **Google Drive Setup:**

      * The notebook expects your input video and your mask image to be in a specific Google Drive folder.
      * **You must mount your Google Drive** at the beginning of your Colab session.
      * **Ensure your video and mask files are placed in the correct paths within your Google Drive**.
      * The output video will be saved to whatever `output_path` is set to in your code.

3.  **Run the Notebook:**

      * Open `[YourNotebookName].ipynb` (e.g., `restaurant_people_counter.ipynb`) in your Jupyter/Colab environment.
      * Execute all cells sequentially. The notebook will:
          * Load the YOLOv8 model.
          * Process the video frame-by-frame.
          * Apply detection, tracking, and counting logic.
          * Generate and save an output video with annotations.

## Demo

Input video and output video both are uploaded within the same repository.


## How the project can be enhanced in near future

  * **Live Stream Integration:** Adapt the code to directly process live RTSP streams from IP cameras.
  * **Dashboard/UI:** Create a simple web-based dashboard to display real-time counts and potentially the live processed feed.
-----
