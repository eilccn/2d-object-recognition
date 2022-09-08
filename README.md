# Project 3: Real-time Object 2D Recognition

## Project Description
This project aims to recognize 2D objects in real-time in a translation, scale, and rotation invariant manner. It uses thresholding, morphological filtering, region segmentation, and moment computations in order to extract identifying features from each object. The translation, scale, and rotation invariant features are then extracted into a database (csv file) within a training mode that can be accessed via a keypress (see "Keypress Definitions" below). The data extracted from the objects in the training mode then can be used to identify unknown objects by comparing the unknown object features against the features for various known objects documented in the database. The features extracted in this specific program are 3 central moment values, percent filled of the oriented bounding box, and the aspect ratio of the oriented bounding box. The extracted features are then used to compute distances between an unknown object and the known database objects via a distance metric, such as scaled Euclidean distance. The K Nearest Neighbor algorithm can then further be applied in order to more accurately classify unknown objects. When classifying unknown objects in real-time, the label or name of the object's closest match is displayed on the video output. 

## Instructions for running executables:
1. Place all .cpp and .h files along with a CMakeLists.txt file into a directory (i.e. called "project3")
2. Open "project3" in Visual Studio Code
3. Build "project3" using the "CMake Tools" extension
4. Run the program by first entering "./project3" followed by the path to a newly created empty csv file that **must be called "features.csv"**
5. **Note:** Every time you need to rebuild the program, the csv file needs to be manually deleted and recreated for it to build correctly.

### Keypress Definitions:
```
q = quit
s = save .png of image
spacebar = original image

for simply displaying methodologies to be used in feature extraction:
t = thresholding
m = morphological filtering
c = connected components region segmentation

enter training mode:
n = training mode

feature extraction:
f = extract features from object

classify the object:
d = classify by computing the scaled euclidean distance
k = classify by using k nearest neighbor (pluarlity vote)
y = classify by using k nearest neighbor (sums)
```

## Feature Computation, Training System, and Classification
### Feature Computation

### Training System

### Classification

## Video Demo
[<img src="/video_demo/demo-image.png" width="50%">](https://drive.google.com/file/d/1pK1d6vgmeVoRCaKcsgjpci0UV_i4_DI6/view?usp=sharing)

## Wiki Report
https://wiki.khoury.northeastern.edu/x/1qN5Bg


