# Camera_Based_2D_Feature_Tracking
2D Feature Tracking using camera input

=============================================================================
MP.1 Data Buffer Optimization
The size of the DataFrame object is limited to the current value of the variable dataBufferSize,
previously initialized to 2, as requested. By using an if-else structure, I fill this DataFrame object until it stores 
2 images by using the method push_back. Once it allocates 2 images, the first element is erased thanks to the 
method erase() and a new element is introduced by using the method push_back.
File MidTermProject_Camera_Student.cpp
Line 39: Definition and initialization of the variable dataBufferSize.
Lines 66- 74: Management of the DataFrame object and its content.
=============================================================================
MP.2 Keypoint Detection
A string called detectorType is used in the code to allocate the detector to be used. In case ofwanting 
to use a different one, it is needed to change the value of this string by the one corresponding to the detector 
to be used. Once it is defined, an if-else structure will call the corresponding function according to the detector
that has been selected to be used:
✓ For SHITOMASI, the function detKeypointsShiTomasi is called.
✓ For HARRIS, the function detKeypointsHarris is called.
✓ For all the rest of descriptors, the function detKeypointsModern is called.
Detector to be selected are: SHITOMASI, HARRIS, FAST, BRISK, ORB, AKAZE and SIFT. If a non-valid 
value is assigned to the variable detectorType, an error is thrown and the program is stopped.
File MidTermProject_Camera_Student.cpp
Line 83: The variable detectorType is defined and initialzed with the selected detector.
Lines 89 - 109: Management of the function to call depending on the value of variable detectorType.
Error thrown in these lines (105-109).
File matching2D_Student.cpp
Implementation is done in the 3 functions detKeypointsShiTomasi, detKeypointsHarris and 
detKeypointsModern. Each detector has been implemented as done in the previous exercises and following 
the OpenCV documentation for all of them that is available in the official page of the library.
=============================================================================
MP.3 Keypoint Removal
It is only executed if the boolean variable is passed to TRUE in line 117.Inside a for loop the code goes over all the elements inside the vector with all the detected keypoints.
In case that a keypoint is located inside the desired window (which is centered in the preceding car to enclose 
it), this is stored in a temporal vector. In any other case, nothing is done with the keypoint.
Once the vector is totally analyzed, we empty it and copy into it the content of the temporal vector. 
In this way, only the keypoint inside this window are conserved.
The position of the keypoints is compared to the position of the corners of the selected box by using 
the following statement in the if condition:
keypoints[i].pt.x > vehicleRect.x && keypoints[i].pt.x < (vehicleRect.x+vehicleRect.width) &&
keypoints[i].pt.y > vehicleRect.y && keypoints[i].pt.y < (vehicleRect.y+vehicleRect.height)
The variable vehicleRect is defined in line 118 as a cv::Rect object and initialized with a size (535, 180, 
180, 150).
File MidTermProject_Camera_Student.cpp
Lines 119 - 141: Code to perform the removal of keypoints.
Lines 143 – 149: Commented code to know the keypoints corresponding to the preceding vehicle area 
of the image. It is needed to comment lines 140 & 141 to properly run this code fragment.
=============================================================================
MP.4 Keypoint Descriptors
In the line 170 the variable descriptorType is initialized with the descriptor to be used. Its value shall 
be manually changed in case of wanting to use another one. The descriptors to use are BRISK, BRIEF, ORB, 
FREAK, AKAZE and SIFT.
In the line 179, we enter in the if structure only from the second frame onwards. When we have 
processed just one frame, we are not able to compare anything to extract matched keypoints. The first done 
thing is to initialize some parameters:
  ✓ matcherType: It will always be MAT_BF, except for descriptorType = SIFT, when it takes a value 
MAT_FLANN.
  ✓ descriptorClass: It is automatically selected depending on the value of variable descriptorType:
      ▪ It is set to DES_HOG for descriptorType = SIFT.
    ▪ It is set to DES_BINARY for descriptorType different of SIFT.
  ✓ selectorType: After some tries, I decided to fix it to SEL_KNN, but it can take the vale SEL_NN, which 
is less exact.
Finally, the function matchDescriptors is called to run the adequate descriptor. This function is written 
in the filematching2D_Student.cpp. First thing that is done, is to create the matcher according to matcherType 
variable that is passed.For a BF approcah, we will use a Hamming distance calculation for Binary descriptors 
and L2 distance for HOG ones. This isnot done for FLANN descriptors. In case the matcherType is neither of 
these two, an error is thrown, and the program stopped.
Afterwards, the matching is done according to the selectorType that has been passed to the function 
(KNN in my case), making the same implementation than in previous exercises and according to the official 
OpenCV documentation. Deletion of keypoints is done here.File MidTermProject_Camera_Student.cpp
Lines 169 - 213: Full implementation of the code except for the inside code of each descriptor. 
Selection of descriptor, descriptorClass automated selection and call to the descriptor itself are done in these 
lines.
File matching2D_Student.cpp
All the implementation is in the function matchDescriptors.
=============================================================================
MP.5 Descriptor Matching
MP.6 Descriptor Distance Ratio
This is done in the function matchDescriptors inside the file matching2D_Student.cpp.
The matcher is created as stated in the official OpenCV documentation:
matcher = cv::DescriptorMatcher::create(cv::DescriptorMatcher::FLANNBASED);
The matching is done as follows:
matcher->knnMatch(descSource, descRef, knn_matches, 2);
The matches are stored in the variable knn_matches. Then, by calculating the distance between best 
matches, I store only the good ones in the vector called matches. Then, the difference in size between 
knn_matches and matches gives the number of deleted keypoints.
File matching2D_Student.cpp
All the implementation is in the function matchDescriptors.
=============================================================================
