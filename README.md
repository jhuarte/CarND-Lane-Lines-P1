# **Project 1: Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

## Overview

## Setup and run

**Step 1:** Install Docker on your computer or a remote system. Review the documentation [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md).

**Step 2:** Pull the Precompiled Docker Image from Docker Hub. A precompiled image with all dependencies required for the first term is available on [Docker Hub.](https://hub.docker.com/r/udacity/carnd-term1-starter-kit/). Once you have docker working, pull the image using the following command:

`docker pull udacity/carnd-term1-starter-kit`

**Step 3:** In your shell, navigate to the directory of a project, e.g.

`$ cd ~/src/CarND-LaneLines-P1`

**Step 3:** Run The Image as a New Container

`docker run -it --rm --entrypoint "/run.sh" -p 8888:8888 -v 'pwd':/src udacity/carnd-term1-starter-kit`

**Step 4:** Open the code in a Jupyter Notebook. Use the ip address or the reference of the container to connect to Jupyter notebook, this can be found by opening another docker terminal and running this command: `docker-machine ip default`. Jupyter server can then be accessed by going to this page from your browser: `http://[ip-of-container]:8888`.


## Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline developed during the project has six main steps and is embedded on the funtion `lane_line_detection(frame,debug)`. A varaible `param` has been defined as a dictionary to store all the parameters that must be tunned during the development.


* **STEP 1: Applies the Grayscale transform**: 

    The first step the converts a color image to a grayscale image using the [OpenCV](https://opencv.org/) funtion `cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)`. This conversion let us to work using only one channel of color instead 3 channels on a color image (RGB,R=red,G=Green,B=Blue).

<img src="img/solidWhiteRight.jpg" width="240"/> <img src="img/grayscale_solidWhiteRight.jpg" width="240"/>
    
    
* **STEP 2: Applies a Gaussian Noise kernel**: 

    In the second step, a [gaussian](https://docs.opencv.org/2.4/modules/imgproc/doc/filtering.html?highlight=gaussianblur#gaussianblur) filter `cv2.GaussianBlur` is applied before running Canny detector, which is essentially a way of suppressing noise and spurious gradients by averaging (`blur_kernel` is the parameter to be tunned and larger value implies averaging, or smoothing, over a larger area). `cv2.Canny()` actually applies Gaussian smoothing internally, but it's not a changeable parameter.
    
    
        - `blur_kernel: 5`
    
    
* **STEP 3: Applies the Canny transform**: 

    After having the filtered imaged (and in grayscale), the [Canny detector](https://docs.opencv.org/2.4/doc/tutorials/imgproc/imgtrans/canny_detector/canny_detector.html) `cv2.Canny`  is applied to the image to detect the edges. The algorithm will first detect strong edge (strong gradient) pixels above the `high_threshold`, and reject pixels below the `low_threshold`. The recomendation to adjust this two parameters is to use a relation 1:2 or 1:3. Next, pixels with values between the low_threshold and high_threshold will be included as long as they are connected to strong edges. The output edges is a binary image with white pixels tracing out the detected edges and black everywhere else.
     
    
        - `high_threshold: 50`
        - `low_threshold: 150`
        
* **STEP 4: Applies an image mask**:

    Then a mask filter is applied to the binary image with the edges detected to obtain the region of interest (where the lane lines are suppoused to be. In this case, in order to make the pipeline more robust we create two ROI (the function `region_of_interest` is appplied twice with difetent parameters), one for the left lane lines and other for the right lane lines, as it's represented on the next figure:
    
                      Left          Right
         ymin     (lx2)__ (lx3)   (rx2)__ (rx3)
                      / /             \ \
                     / /               \ \
         ymax  (lx1)/ / (lx4)       (rx1\ \ (rx4)
    
 

* **STEP 5: Applies hough transform and draw lines**:

    Then the Hough line algorithm (a specific function that use lines as the model) is used to compute the lines segments `cv2.HoughLinesP`. 
    
    
        - `rho: 2`
        - `theta: 1`
        - `threshol: 15`
        - `min_line_le: 30`
        - `max_line_gap: 20`

* **STEP 6: `draw_lines` function modifications**:


    The `draw_lines` function provided, has been modified to select the line segments that correspond to the left lane lines (negative slopes) or to the right lane lines (positive slope), delete the lines segments with a very high slope (positve and negative, for example horizontal line segments that correspond with road defects or other issues), average the slopes and paint then with a differente color.
<img src="examples/laneLines_thirdPass.jpg" width="240"/>


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...

## Licence



## About me


# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project you will detect lane lines in images using Python and OpenCV.  OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.  

To complete the project, two files will be submitted: a file containing project code and a file containing a brief write up explaining your solution. We have included template files to be used both for the [code](https://github.com/udacity/CarND-LaneLines-P1/blob/master/P1.ipynb) and the [writeup](https://github.com/udacity/CarND-LaneLines-P1/blob/master/writeup_template.md).The code file is called P1.ipynb and the writeup template is writeup_template.md 

To meet specifications in the project, take a look at the requirements in the [project rubric](https://review.udacity.com/#!/rubrics/322/view)


Creating a Great Writeup
---
For this project, a great writeup should provide a detailed response to the "Reflection" section of the [project rubric](https://review.udacity.com/#!/rubrics/322/view). There are three parts to the reflection:

1. Describe the pipeline

2. Identify any shortcomings

3. Suggest possible improvements

We encourage using images in your writeup to demonstrate how your pipeline works.  

All that said, please be concise!  We're not looking for you to write a book here: just a brief description.

You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup. Here is a link to a [writeup template file](https://github.com/udacity/CarND-LaneLines-P1/blob/master/writeup_template.md). 


The Project
---

## If you have already installed the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) you should be good to go!   If not, you should install the starter kit to get started on this project. ##

**Step 1:** Set up the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) if you haven't already.

**Step 2:** Open the code in a Jupyter Notebook

You will complete the project code in a Jupyter notebook.  If you are unfamiliar with Jupyter Notebooks, check out [Udacity's free course on Anaconda and Jupyter Notebooks](https://classroom.udacity.com/courses/ud1111) to get started.

Jupyter is an Ipython notebook where you can run blocks of code and see results interactively.  All the code for this project is contained in a Jupyter notebook. To start Jupyter in your browser, use terminal to navigate to your project directory and then run the following command at the terminal prompt (be sure you've activated your Python 3 carnd-term1 environment as described in the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) installation instructions!):

`> jupyter notebook`

A browser window will appear showing the contents of the current directory.  Click on the file called "P1.ipynb".  Another browser window will appear displaying the notebook.  Follow the instructions in the notebook to complete the project.  

**Step 3:** Complete the project and submit both the Ipython notebook and the project writeup

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).

