# Vehicle-Detecting-and-Tracking
Used computer vision techniques and Histogram of Oriented Gradients to track vehicles across frames in a video.

In this project, your goal is to write a software pipeline to detect vehicles in a video (start with the test_video.mp4 and later implement on full project_video.mp4), but the main output or product we want you to create is a detailed writeup of the project.  Check out the [writeup template](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup.  


The project is completed in the following stages:

Step 1: Create a function to draw bounding rectangles based on detection of vehicles.
Step 2: Create a function to compute Histogram of Oriented Gradients on image dataset.
Step 3: Extract HOG features from training images and build car and non-car datasets to train a classifier.
Step 4: Train a classifier to identify images of vehicles.
Step 5: Identify vehicles within images of highway driving.
Step 6: Track images across frames in a video stream.
Code:

This project requires python 3.5 and the following dependencies:

NumPy
Pandas
Scikit-learn
Scikit-image
matplotlib
OpenCV
MoviePy

#Step 1: Drawing Bounding Rectangles

For this step I defined a function draw_boxes which takes as input a list of bounding rectangle coordinates and uses the OpenCV function cv2.rectangle() to draw the bounding rectangles on an image.
![alt tag](https://github.com/RSavage2/Vehicle-Detecting-and-Tracking/blob/master/Screen%20Shot%202017-03-10%20at%208.11.28%20PM.png)

#Step 2: Compute Histogram of Oriented Gradients

For this step I defined a function get_hog_features which uses the Scikit-image function hog() to get Histogram of Oriented features from an image. The functions computes the HOG features from each of the 3 color channels in an image and concatenates them to gether in to a single feature vector.

HOG images
![alt tag](https://github.com/RSavage2/Vehicle-Detecting-and-Tracking/blob/master/Screen%20Shot%202017-03-10%20at%208.11.46%20PM.png)
#Step 3: Extract HOG features and build training datasets

For this step I defined a function extract_features which calls the function get_hog_features and appends the HOG features from each image to a dataset of training features. After building the datsets of car and noncar images and labelling them appropriately I normalized the features to have zero mean and unit variance using the Scikit-learn function StandardScaler, and used train_test_splitto split them in to training and testing datasets.

I found the YUV color space to have the best performance for identifying vehicles and I chose to use that space for feature extraction.

#Step4: Training a classifier

The next step is to train a classifier which will be used to identify vehicles within images. I chose to evaluate the performance of the following three classifiers: Linear Support Vector Machine, Logistic Regression Classifier, Multi-layer Perceptron
Based on the above results I decided to move forward with the Multi-layer Perceptron as my classifier to identify vehicles in images and in the video stream. 

#Step 5: Detect Vehicles in images

To search for vehicles within images I chose to implement a sliding window approach where I looked at one slice of the image at a time and made predictions on the HOG features from that particular window. In order to minimize the search area and speed up the pipeline I only searched for cars in the lower half of the image. Additionally, my algorithm searches for vehicles in windows of multiple scales, with an 80% overlap, in order to identify vehicles which can be either near or far in the image and will appear to have different sizes.

Next I use the OpenCV function cv2.findContours to find all of the objects in the mask image and once the contours are found I used the OpenCV function cv2.boundingRect on each contour to get the coordinates of the bounding rect for each vehicle.

Finally, I create a copy of the original image, called result, on which I draw the bounding rectangles. Below you can see the process on an example image:

![alt tag](https://github.com/RSavage2/Vehicle-Detecting-and-Tracking/blob/master/Screen%20Shot%202017-03-10%20at%208.12.28%20PM.png)
#Step 6: Tracking images across frames in a video

Finally, to track vehicles across frames in a video stream, I decided to create a class called boxes to store all of the bounding rectangles from each of the previous 8 frames in class variables. In each frame I then concatenate the lists of bounding rectangles from current and previous frames, and then use the OpenCV function cv2.groupRectangles to combine overlapping rectangles in to consolidated bounding boxes. Within the group rectangles function I set the parameter groupThreshold equal to 6 which means that it will only look for places where there are greater than 6 overlapping boxes and it will ignore everything else.



#Discussion:

The most difficult part of this project, and possibly the most important, is the elimination of false positives. Any time a non-vehicle object is falsely identified as a vehicle it can influence the car to make driving decisions which are potentially hazardous such as swerving or slamming on the brakes.
I notice my pipeline is running very slowly, at several seconds per frame.  In the future I would like to to reduce the number of features and reduce the number of windows searched in an attempt to speed up the prediction process.
