# Project 3: Real-time 2D Object Recognition

## Project Description
This project aims to recognize 2D objects in real-time in a translation, scale, and rotation invariant manner. It uses thresholding, morphological filtering, region segmentation, and moment computations in order to extract identifying features from each object. The translation, scale, and rotation invariant features are then extracted into a database (csv file) within a training mode that can be accessed via a keypress (see "Keypress Definitions" below). The data extracted from the objects in the training mode then can be used to identify unknown objects by comparing the unknown object features against the features for various known objects documented in the database. ***The 5 features extracted in this specific program are 3 central moment values {µ11, µ20, µ02}, percent filled of the oriented bounding box, and the aspect ratio of the oriented bounding box.*** The extracted features are then used to compute distances between an unknown object and the known database objects via a distance metric, such as scaled Euclidean distance. The K Nearest Neighbor algorithm can then further be applied in order to more accurately classify unknown objects. When classifying unknown objects in real-time, the label or name of the object's closest match is displayed on the video output. 


## Instructions for running executables:
1. Place all .cpp and .h files along with a CMakeLists.txt file into a directory (i.e. called "project")
2. Open "project" in Visual Studio Code
3. Build "project" using the "CMake Tools" extension
4. cd into the build folder
5. Run the program by first entering "./project" followed by the path to a newly created empty csv file that **must be called "features.csv"**
* **Note:** Every time you need to rebuild the program, the csv file needs to be manually deleted and recreated for it to build correctly.
* Please see the requirements.txt file for necessary plug-ins

### Keypress Definitions
```
q = quit
s = save .png of image
spacebar = original image

image clean-up and region segmentation required prior to feature extraction:
t = thresholding
m = morphological filtering (clean up thresholded image)
c = connected components region segmentation

feature computation:
f = 
* displays the major and minor axes and the oriented bounding box in real time on the video output 
* computes 5 features, the first 3 of which are central moment values -> {µ11, µ20, µ02, percent filled of the oriented bounding box, aspect ratio of the oriented bounding box}

training mode:
n = compute and extract 5 features from the object and record data in "features.csv" file 

classify the object:
d = classify by computing the scaled euclidean distance
k = classify by using k nearest neighbor (pluarlity vote)
```

### Video Capture Set-Up
* Install the EpocCam app to your mobile device in order to use it as a webcam 
* Use a phone tripod or DIY your own set-up to keep your phone in place while you extract object features
* It is important that your video capture device does not move throughout the entire training process 
* Good lighting
* Noiseless background

## Image Thresholding and Region Segmentation

### Image Thresholding
#### Thresholding: Keypress 't'
This function separates the object from the background. By setting pixels in the image black if the color-channel value is greater than a designated threshold or white if the color-channel falls below the designated threshold.
| Original | Thresholding |
|---|---|
| <img src="/readme-images/pencil-og.png" width=100%> | <img src="/readme-images/pencil-thresh.png" width=100%> |

#### Morphological Filtering: Keypress 'm'
This function cleans up the thresholded binary image by using OpenCV's built-in ```cv::morphologyEx```, which executes growing and shrinking (or opening and closing morphological filtering). 
| Thresholding | Morphological Filtering |
|---|---|
| <img src="/readme-images/pencil-thresh.png" width=100%> | <img src="/readme-images/pencil-morph.png" width=100%> |

### Connected Components Region Segmentation: Keypress 'c'
This function computes connected components using OpenCV's built-in function, ```cv::connectedComponentsWithStats``` and displays the detected segmented regions by color.
| Pencil Region | Region Segmentation |
|---|---|
| <img src="/readme-images/pencil-cc.png" width=100%> | <img src="/readme-images/cc.png" width=100%> |


## Feature Computation, Training System, and Classification
### Feature Computation: Keypress 'f'

Upon pressing ```f```, you should be able to observe the connected components regions showing the major and minor axes of least central moments and the oriented bounding box. 

The built-in OpenCV function ```cv::moments``` was used in order to compute the 3 central moment feature values for each object. Moments characterize the shape and properties of each region and the standard moments are computed as ```Mpq = Σ xpyq``` where the sum of all pixels in a region are ```xpyq```. These are the transform coordinates where the origin is at (0,0). After we compute the standard moments, we can obtain a region's area (M00) and compute a region's centroid where ```x = M10/M00 and y =M01/M00```. The central moments can then be computed relative to the centroid of a region: ```µpq = Σ (x - x̄ )p(y - ȳ )q``` . With the built-in ```cv::moments``` function, we can simply call the central moments, such as µ11, µ20, and µ02. Central moments are translation invariant but not rotation invariant, but we can use the central moments to calculate the rotation invariant orientation of major axis of a region. The orientation is calculated as follows: ```orientation = 0.5*atan2(2*µ11, (µ20 - µ02))```. The major axis is (cosα, sinα) and the minor axis is (-sinα, cosα). To get new x and y values in a rotated coordinate system we can calculate x' and y' as follows: ```x' = (x- x̄)cosα + (y - ȳ)sinα``` and ```y' = (x- x̄)sinα + (y - ȳ)cosα```. With the rotation invariant (x', y') coordinates, we can then obtain the oriented bounding box points by finding the max/min for x' and y'. 

The 5 features chosen to be extracted for each object include **three central moment values (µ11, µ20, and µ02), the percent filled of the oriented bounding box, and the aspect ratio of the oriented bounding box**. These specific features are pushed into a feature vector that is called in as a parameter to this feature computation function. The extracted feature vector is then used in the training mode outlined in _"Training System"_ below. 

| Original | Minor + Major Axes and Oriented Bounding Box |
|---|---|
| <img src="/readme-images/pencil-og.png" width=100%> | <img src="/readme-images/pencil-axes-box.png" width=100%> |

### Training System: Keypress 'n'

Upon a keypress, 5 features: ```{µ11, µ20, µ02, percent filled of the oriented bounding box, aspect ratio of the oriented bounding box}``` will be extracted from an object via the features function described in **"Feature Computation"** above. The user will then be prompted to assign a label to the object by entering a label in the command line. The object's feature vector and its label will then be written together into a csv file that is passed in as a command line argument. The user can then replace the object with a new object and press the keypress again in order to extract the new object's feature vector, assign a label to it, then push the paired information into the csv file. The user can continue adding as many objects as they want to the csv file or database by repeating the process of replacing the object and pressing the keypress. The user may decide to extract multiple feature vectors from the same object by rotating the object with each keypress, and this is advised especially for the object classification via the KNN functionality outlined under **"Classification"** below. **NOTE: It is important that the objects do not change in scale, and it is strongly advised to create a stable video capture set-up in order to ensure more accurate classification results. Please refer to the **"Video Capture Set-Up"** section for more detailed suggestions.**

### Classification: Keypresses 'd' and 'k'

#### Unknown Object Classification via Scaled Euclidean Distance: Keypress 'd'
An unknown object's feature vector is compared with the feature vectors of the known objects in the database through the use of a scaled Euclidean distance metric. The unknown object is identified according to the closest matching feature vector in the object DB (nearest-neighbor recognition). The matched label of the object is displayed on the middle left of the video output.

| Original | Classification |
|---|---|
| <img src="/readme-images/camera-og.png" width=100%> | <img src="/readme-images/camera-classify.png" width=100%> |

#### Unknown Object Classification via KNN: Keypress 'k'
While the euclidean distance classifying method used a nearest neighbor algorithm that returns the object with the least distance from the unknown object (essentially K=1), the K Nearest Neighbor algorithm looks at K>1 nearest neighbors of each class. With KNN, an object is classified by a plurality vote of its neighbors, where the object is matched to the class that appears in greater frequency among its K nearest neighbors. The nearest neighbor algorithm is essentially the same as KNN with K=1 because in this case an object is matched to the class of the single nearest neighbor.

KNN may alternatively be implemented (not via a plurality vote of its neighbors), but by obtaining K nearest neighbors of each object class, computing the sum of distances of each class, then matching the unknown object to the class with the smallest distance sum. In this program, however, it should be noted that the plurality vote of K nearest neighbors was implemented. 

For this method, **objects are classified in the command line** and users define the value of K that will be used to classify each object.

<img src="/readme-images/k-means.png" width="50%">

## Video Demo
[<img src="/readme-images/demo-image.png" width="50%">]


## Further Notes
Objects that were mistaken for each other were very similar in size and shape. Some objects cast shadows that were taken into account when extracting their features. As a result, for more accurate results, it is suggested to prepare a more controlled lighting situation and to follow the previous suggestions for video capture set-up. It is also suggested to extract multiple feature sets of each object in various rotations and translations in order to ensure improved accuracy.


###### Wiki Report
https://wiki.khoury.northeastern.edu/x/1qN5Bg


