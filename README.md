# SFND 2D Feature Tracking

<img src="images/keypoints.png" width="820" height="248" />

The idea of the camera course is to build a collision detection system - that's the overall goal for the Final Project. As a preparation for this, you will now build the feature tracking part and test various detector / descriptor combinations to see which ones perform best. This mid-term project consists of four parts:

* First, you will focus on loading images, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, you will integrate several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and compare them with regard to number of keypoints and speed. 
* In the next part, you will then focus on descriptor extraction and matching using brute force and also the FLANN approach we discussed in the previous lesson. 
* In the last part, once the code framework is complete, you will test the various algorithms in different combinations and compare them with regard to some performance measures. 

See the classroom instruction and code comments for more details on each of these parts. Once you are finished with this project, the keypoint matching part will be set up and you can proceed to the next lesson, where the focus is on integrating Lidar points and on object detection using deep-learning. 

## Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.

## Reflections

### MP.1 Data Buffer Optimization
Implement a vector for dataBuffer objects whose size does not exceed a limit for 2 elements in my case.

### MP.2 Keypoint Detection
Implement keypoint detect methods using SHITOMASI, HARRIS, FAST, BRISK, ORB, AKAZE, and SIFT.

### MP.3 Keypoint Removal
Remove all keypoints outside of a pre-defined rectangle and only use the keypoints within the rectangle for further processing.

### MP.4 Keypoint Descriptors
Implement keypoint descriptor with mehtods like BRISK, BRIEF, ORB, FREAK, AKAZE and SIFT.

### MP.5 Descriptor Matching
Implement BruteForce and FLANN matching as well as k-nearest neighbor selection.

### MP.6 Descriptor Distance Ratio
Use the K-Nearest-Neighbor matching to implement the descriptor distance ratio test (k = 2), which looks at the ratio of best vs. second-best match to decide whether to keep an associated pair of keypoints.

### MP.7 Performance Evaluation 1
Count the number of keypoints on the preceding vehicle (within the rectangle) for all 10 images and take note of the distribution of their neighborhood size. Do this for all the detectors you have implemented.

| Detector  | Avg Keypoints (#) | Avg Time (ms) |
|-----------|-------------------|---------------|
| SHITOMASI | 117               | 15.4644       |
| HARRIS    | 24                | 17.223        |
| FAST      | 149               | 0.738557      |
| BRISK     | 276               | 35.4729       |
| ORB       | 116               | 6.26023       |
| AKAZE     | 167               | 66.2857       |
| SIFT      | 138               | 107.554       |

### MP.8 Performance Evaluation 2
Count the average number of matched keypoints for all 10 images using all possible combinations of detectors and descriptors. In the matching step, the BF approach is used with the descriptor distance ratio set to 0.8.

| Detector\Descriptor | BRISK | BRIEF | ORB           | FREAK | AKAZE | SIFT |
|---------------------|-------|-------|---------------|-------|-------|------|
| SHITOMASI           | 545   | 739   | 629           | 455   | N/A   | 1051 |
| HARRIS              | 70    | 88    | 79            | 56    | N/A   | 111  |
| FAST                | 734   | 952   | 846           | 579   | N/A   | 1386 |
| BRISK               | 1070  | 1267  | 777           | 863   | N/A   | 1694 |
| ORB                 | 270   | 204   | 221           | 102   | N/A   | 373  |
| AKAZE               | N/A   | N/A   | N/A           | N/A   | 893   | N/A  |
| SIFT                | 477   | 503   | Out of memory | 364   | N/A   | 780  |

### MP.9 Performance Evaluation 3
Log the average time (ms) it takes for keypoint detection and descriptor extraction.

| Detector\Descriptor | BRISK   | BRIEF   | ORB           | FREAK   | AKAZE  | SIFT    |
|---------------------|---------|---------|---------------|---------|--------|---------|
| SHITOMASI           | 27.655  | 21.3493 | 22.4298       | 61.971  | N/A    | 42.2856 |
| HARRIS              | 16.7741 | 15.4487 | 18.8217       | 55.6307 | N/A    | 33.7848 |
| FAST                | 16.2924 | 6.29149 | 7.14353       | 50.4669 | N/A    | 69.3814 |
| BRISK               | 60.413  | 43.4915 | 51.5373       | 87.0826 | N/A    | 187.733 |
| ORB                 | 10.6498 | 8.77507 | 20.8755       | 47.8608 | N/A    | 112.535 |
| AKAZE               | N/A     | N/A     | N/A           | N/A     | 128.69 | N/A     |
| SIFT                | 124.775 | 124.775 | Out of memory | 163.798 | N/A    | 192.179 |

Not taking the correctness or accuracy of the matched keypoints. We can alternatively take the matched-keypoint number and time expense to rank these pairs of detectors and descriptors. With less time used and more points matched get higher ranking, I would give these the following pairs as the top-3 combination of detector and descriptor:

| rank | combination (det, desc) | matched keypoints | time    |
|------|-------------------------|-------------------|---------|
| 1    | FAST, BRIEF             | 952               | 6.29149 |
| 2    | FAST, ORB               | 846               | 7.14353 |
| 3    | FAST, BRISK             | 734               | 16.2924 |
