---
title: A multi-thread media stream in C++ based on OpenCV and OpenGL
date: 2023-10-24 10:57:48
tags: 
- C++
- FFmpeg
- OpenCV
- OpenGL
categories: Tech
---

#### **Video Display**
We did the video capture using OpenCV, and the video display .
<!--more-->
##### **FPS Modification**
- Built-in function for FPS modification
In OpenCV, there is a built-in function set() aimed to change the frame rate. However, when you set a lower fps, OpenCV will not add or remove frames from the video stream. Instead, it may internally drop frames as needed to achieve the desired fps. The mechanism depends on the backend and video source. 
``` C++
bool glcvCanvas::setVideo(const std::string& videoPath) {
    cv::VideoCapture cap(videoPath);
    if (!cap.isOpened()) 
        return false;
    LOGI("Number of frames in the video file: %d\tVideo FPS: %f\n",cap.get(CAP_PROP_FRAME_COUNT), cap.get(cv::CAP_PROP_FPS));
    LOGI("Video Capture Backend: %s\n", cap.getBackendName());
    //cap.set(cv::CAP_PROP_FPS, 5);
```
- Re-encode the video
In our case, the backend for video capture is [FFmpeg](https://trac.ffmpeg.org/wiki/ChangingFrameRate). Although the FFmpeg supports frame rate modification by inserting command like

``` bash
ffmpeg -i <input> -filter:v fps=30 <output>
``` 
it will also drop or duplicate frames as necessary. That is an undesired side effect in our case.

Therefore, in order to achieve the target frame rate without dropping frames, a dynamic delay is introduced for each frame. The multi-threading section will be discussed in the next part.
The LOGI() macro function is defined as the wxLogInfo() (exactly the same as wxLogVerbose()) from wxWidgets libarary. 
``` C++
    double target_fps = cap.get(cv::CAP_PROP_FPS);
    double frame_time = 1000.0 / target_fps;
    LOGI("Number of frames: %f\tVideo FPS: %f\n", cap.get(cv::CAP_PROP_FRAME_COUNT), target_fps);
    {
        std::unique_lock<std::mutex> medialock(mp.mediaMutex);
        mp.thread_running = true;
    }
    cv::Mat frame;
    while (!stopThread) {
        auto start = std::chrono::high_resolution_clock::now();
        cap >> frame;
        if (frame.empty())
            break;
        set(frame);

        auto end = std::chrono::high_resolution_clock::now();
        auto elapsedMilliseconds = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();

        if (elapsedMilliseconds < frame_time) {
            std::this_thread::sleep_for(std::chrono::milliseconds(static_cast<long long>(frame_time - elapsedMilliseconds)));
        }
    }
    cap.release();
    {
        std::unique_lock<std::mutex> medialock(mp.mediaMutex);
        mp.thread_running = false;
    }
    c_v.notify_one();
```
##### **Muti-thread media sources selection**
In most cases, if there's a thread currently running which is manipulating the media, we expect it would be safely terminated before proceeding with new operations. Therefore, this section will check for a running thread, request the video to stop, wait for the thread to stop, reset the video playing flag, and unlock the mutex. 
- Detachable or joinable:
Detachable threads are typically used when the parent thread does not need to wait for or synchronize with the detached thread's completion. 
Joining a thread involves waiting for the thread to finish executing and obtaining its return value or status. This can be useful when the parent thread relies on the results of the child thread or needs to ensure proper synchronization before proceeding. 
In our case, we expect the previous video process to be terminated properly as we selecting a new source to display. 

Note that the logging information for the running thread is probably blocked on the log view, but this part of multi-thread code does it functionality properly.
``` C++
void targetCanvas::selectSource(int src)
{
    image_source = (image_source_t)(src % TARGET_LAST);
    if (image_source == TARGET_FILE) {
        std::unique_lock<std::mutex> medialock(mediaMutex);
        if (thread_running) {
            stopThread = true;
            c_v.wait(medialock, []{return !thread_running;  });
            stopThread = false;
        }

        std::string fileExtension = getFileExtension(imageFile.ToStdString());
        std::transform(fileExtension.begin(), fileExtension.end(), fileExtension.begin(), ::tolower);
        medialock.unlock();

        if (fileExtension == "mp4" || fileExtension == "avi" || fileExtension == "mov") {
            setVideo(imageFile.ToStdString());
        } else {
            cv::Mat image = cv::imread(imageFile.ToStdString(), cv::IMREAD_ANYCOLOR);
            if (image.empty()) {
                // ... handle issues
            }
            else {
                set(image);
            }
        }
        
    }
}
```