# Vehicle Recognition and Speed Estimation from Video

A C# Windows Forms application that performs vehicle detection and speed estimation from traffic video using classical computer vision techniques with Emgu CV (OpenCV for .NET).

---

## 1. Overview

This project detects moving vehicles in a recorded traffic video and estimates their speed based on centroid displacement between consecutive frames.

The system uses:

- Background subtraction  
- Perspective transformation (Bird’s Eye View)  
- Binary thresholding  
- Morphological operations (Erosion and Dilation)  
- Connected component analysis  
- Centroid-based speed calculation  

The application provides a graphical interface for video playback and processing visualization.

---

## 2. Built With

- C# (.NET Framework)
- Windows Forms
- Emgu CV
- Microsoft Visual Studio

---

## 3. How It Works

### 3.1 Video Loading

- User imports a video file.
- FPS and total frame count are extracted.
- Frames are processed sequentially.

---

### 3.2 Background Subtraction

Foreground objects (vehicles) are detected using absolute difference:

```csharp
CvInvoke.AbsDiff(currentFrameGray, backgroundFrame, processedFrame);
```

This highlights moving vehicles while suppressing static background elements.

---

### 3.3 Perspective Transformation (Bird’s Eye View)

A homography transformation is applied using four manually selected points:

```csharp
Mat mat = CvInvoke.GetPerspectiveTransform(poin, dsts);
CvInvoke.WarpPerspective(data, output, mat, new Size(data.Rows, data.Cols));
```

This converts the road into a top-down view to reduce perspective distortion and improve measurement consistency.

---

### 3.4 Image Processing Pipeline

Each frame undergoes:

- Grayscale conversion  
- Binary thresholding  
- Erosion  
- Dilation  
- Canny edge detection  
- Connected component labeling  

Example thresholding:

```csharp
processedFrame = processedFrame
    .ToImage<Gray, Byte>()
    .ThresholdBinary(new Gray(39), new Gray(159))
    .Erode(5)
    .Dilate(20)
    .Mat;
```

---

### 3.5 Vehicle Detection

Vehicles are detected using:

```csharp
CvInvoke.ConnectedComponentsWithStats(processedFrame, labels, stats, centroids);
```

The centroid of detected components is extracted for motion tracking.

---

### 3.6 Speed Estimation

Speed is calculated using centroid displacement between frames:

```csharp
double distance = Math.Sqrt(Math.Pow(x2 - x1, 2) + Math.Pow(y2 - y1, 2));
double speed = distance * FPS * scaleFactor;
```

Note: The scale factor is empirical and not calibrated to real-world distance measurements.

---

## 4. User Interface Features

- Original video display  
- Processed binary image display  
- Canny edge result display  
- Speed output (km/h)  
- Frame navigation trackbar  
- Manual perspective point adjustment  

Users can toggle perspective point editing and click on the video to redefine the four transformation points.


## 5. Assumptions

- The camera is stationary  
- Lighting conditions are relatively stable  
- Vehicles move primarily in one direction  
- A predefined background image represents the empty road  

---

## 6. Limitations

- No real-world calibration for speed accuracy  
- Best performance with a single dominant vehicle  
- Sensitive to lighting changes  
- Not optimized for multi-object tracking  

---

## 7. Future Improvements

- Real-world calibration for accurate speed measurement  
- Multi-object tracking  
- Deep learning-based vehicle detection (YOLO, SSD)  
- Adaptive background modeling (MOG2)  
- Performance optimization for real-time processing  

---
