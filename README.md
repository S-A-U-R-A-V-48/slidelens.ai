# 🎓 LectureLens AI

**Turn video lectures into clean, distraction-free PDF notes.**

LectureLens AI automatically detects slide transitions in lecture
videos, uses AI to erase the presenter, and stitches the background
together to create unobstructed PDF notes.

## ✨ Features

- **Smart Detection:** Auto-detects slide changes using SSIM.
- **AI Eraser:** Removes the presenter using DeepLabV3.
- **Auto-Healing:** Fills \"holes\" where the person stood by merging
  frames.
- **PDF Output:** Compiles clean slides into a ready-to-study PDF.

## 🚀 Quick Start

### 1. Install Dependencies

pip install opencv-python numpy torch torchvision scikit-image pillow

### 2. Required Files

Ensure these three files are in the same folder:

- `main.py` (Run this one)
- `slide_change_detector.py`
- `create_composite_image.py`

### 3. Run

python main.py

## 🎮 How to Use

1. **Run the script** and select your video file (`.mp4`, `.avi`, etc.)
   via the popup.
2. **Adjust settings** if needed (defaults are usually fine):
   - **Min Slide Duration:** Increase (e.g., 40s) if you see
     duplicate slides.
   - **Sampling Interval:** Decrease (e.g., 1s) for better accuracy.
3. **Wait for processing.** The first run downloads the AI model
   (\~100MB).
4. **Enter a name** for your output PDF when prompted.

now let go

--first the pdf which will be generated will have one image for each slides => the number of slides used in the video = number of slides in the pdf.

--now the question is how to know the number of slides in the pdf => we can simply detect the changes in the slide , if except the obstacle(i.e instructor in this case) there  any significant changes , we will mark the time at which the slide has changed.

This code is a Slide Change Detector. It analyzes a video (like a recording of a lecture or presentation) to automatically find the timestamps where the slide changes. It uses image processing techniques to compare frames and determines if a significant change has occurred, indicating a new slide.

Core Workflow
User Input: It asks the user to select a video file and define a "minimum slide duration" (how long a slide must be shown to be counted).

Sampling: Instead of checking every single millisecond, it checks one frame every few seconds to be efficient.

Comparison: It compares the current frame with the previous one using a similarity score.

Filtering: If the frames are different enough and enough time has passed, it marks a slide change.

Output: It prints the start and end times for each detected slide.

Detailed Breakdown by Function

1. get_video_details(video_path)

Purpose: Extracts basic metadata from the video.

How: It opens the video using OpenCV (cv2) and retrieves the FPS (Frames Per Second) and total frame count.

Why: We need FPS to convert frame numbers (computer time) into seconds (human time).

2. frame_to_gray(frame)
   Purpose: Converts a colored image frame to grayscale.

Why: Comparing color images is computationally expensive and sensitive to minor lighting changes. Grayscale focuses on structure and contrast, which is perfect for slides.

3. compute_frame_difference(frame1, frame2)
   Purpose: Calculates how similar two frames are.

Method: It uses SSIM (Structural Similarity Index), a metric from the skimage library.

SSIM Score: Returns a value between -1 and 1.

1.0: Identical images.

Low score: The images are very different (indicating a slide change).

4. detect_slide_changes(...) (The Brains of the Operation)
   This is the most complex function. Here is the logic flow:

Sampling: It skips through the video, grabbing a frame every sample_interval_sec (default is 2 seconds). This makes the script run much faster than playing the video in real-time.

Comparison Loop: It iterates through these sampled frames.

Threshold Check: It compares the current sampled frame to the previous one.

If the SSIM score is below 0.75 (hardcoded threshold), it assumes the visual content has changed significantly.

Time Check: It checks if the time since the last recorded slide change is greater than the user's min_slide_duration_sec. This prevents the system from detecting animations or mouse movements as new slides.

Recording: If both checks pass, it records the frame number as a new slide start point.

5. main()
   Purpose: The entry point that ties everything together with a User Interface (UI).

UI: Uses tkinter to open a file explorer dialog so you can click to select a video file instead of typing the path.

Input: Asks you to type the minimum duration (e.g., "30" for 30 seconds) via a popup box.

Reporting: Finally, it calculates the minutes and seconds for every detected interval and prints them to the console in a readable format (e.g., [0 min 0 sec, 5 min 20 sec]).

--now for each slide we have to remove the obstacle

Image Compositor (or "Patcher"). It takes a folder full of images that have transparent holes (like the ones created by the previous script where the person was removed) and stitches them together into one single, complete image.

Think of it like a puzzle. If you have 10 photos of the same background, but each one has a different piece missing (where the person was standing), this script combines them to fill in all the gaps.

Core Workflow
Input: You select a folder containing the transparent .png images you created earlier.

Base Image: It starts with the first image as the "base." This base has a hole in it.

Filling Holes: It looks at the next image. If that image has pixels where the "base" is currently transparent (empty), it copies those pixels over.

Loop: It repeats this for every image in the folder.

Result: By the end, hopefully, every "hole" has been filled by at least one of the images, resulting in a clean, full scenery without any person.

for example

when you visit this link the images with name similar to framebg_only_210 , are the images with holes and the image named composite_image.png is the final image we get .
isn't that awesome .

--repeat the process for all the slides, find images with holes and then combine those images to get the final composite_image without any hole and free from obsticle

-----------------------------------only add pictures no link
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/original_img2.jpeg" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/user-attachments/assets/ce68fd01-113b-41db-918d-2d1650faa38f" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_30.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_60.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_90.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_120.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_150.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_180.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_210.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_240.png" />
<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/frame_bgonly_270.png" />


final image without any obstacle and holes!

<img width="1156" height="576" alt="frame_bgonly_0" src="https://github.com/S-A-U-R-A-V-48/slidelens.ai/blob/main/composite_image.png" />

![alt text](composite_image-1.png)

## ⚙️ Troubleshooting

- **Process too slow?** Increase *Sampling Interval* or *Frame Step*.
- **Presenter ghosting?** Increase *Person Mask Dilation* (e.g., to 20
  or 25).
- **No slides found?** Your *Min Slide Duration* might be longer than
  the actual slides. Lower it.
