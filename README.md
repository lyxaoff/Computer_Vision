# Computer_Vision

#**SUMMARY**

The project aims at detecting an object like an hammer in X-ray baggage images. I started with the script given by the teacher and tried simple methods like Template matching and Edges detection. I then tried to follow the ideas given by the teacher by focusing on detecting hammer heads rather than the all hammer and then tried to use the SIFT features and bags of words approach. I then tried  to combine template matching and edges detection with other methods like Hough Transform and Morphological operations. Some of my trials resulted in failures due to unaccurate predictions or endless running times. My best trial remained the simplest approach I used: Template matching and edges detection.

#**SOLUTION DESIGN AND PIPELINE**

For the final pipeline,I kept the code provided by the teacher that follows a classical architecture:
- Contrast Limited Adaptive Histogram Equalization (CLAHE): it enhances contrast which helps define the edges of thin metal objects for Canny to detect the edges of the handle against a busy background.
- Preprocessing: To handle the noise, i used a 7x7 Median Blur to smooth out "salt-and-pepper" noise from the sensors while preserving the sharp structural edges of metallic tools.
- Region Proposals or Contour Analysis: I used the Canny algorithm to identify high-gradient boundaries. Thus, we get the outlines of everything in the bag and then use findContours to draw boxes around these outlines.
- Heuristic Filtering: We want to ignore any box with an area smaller than 150 pixels to get rid of small things that would have slowed down the template matching.
- Template Matching: I looped through my templates and compared them to the cropped boxes from the image. TM_CCOEFF_NORMED handles changes in brightness better than the other methods. I also added a check so the code wouldn't crash if a template was bigger than the detected box.

#**JUSTIFICATION OF MY APPROACHES**

I tried different approaches:
- SIFT and bag of words: While it is robust to rotation,SIFT produced very inaccurate predictions. X-ray images lack the rich texture that SIFT needs to create reliable keypoints, so it often matched random noise to the hammer. It was also far too slow for the size of the test set.
- Hough Transform: I tested Hough line detection to find the straight handle of the hammer. In practice, the internal wires of the bags and the edges of other items created too many "fake" lines, leading to a high false-positive rate.
- Morphology operations: It allowed to separate objects grouped together and get clean bounding boxes, which was simpler and faster than implementing complex segmentation algorithms.
- Hammer Head Focus: It made sens as the metallic density of the head is usually the most distinctive part of the hammer. However, the unique shape of the entire hammer was actually a more reliable feature for the template matcher. Focusing only on the head led to too many false matches with other metallic objects.

#**LIMITATIONS OF MY SOLUTION**

My script successfully detects hammers in clear orientations and handles changes in brightness levels due to the normalized correlation method. However, it has some flaws hence its score on Kaggle:
- Rotation: if a hammer is tilted at an angle that doesn't match my templates, it might be missed it. I tried to implement a loop of rotated templates but the code was running for hours.
- Dense Occlusion: If a hammer is hidden directly behind a very dense metallic object, the Canny edges merge them and the template match fails.
- Image Scale: I used a fixed resize(60, 60) for matching to improve the running time but hammers can appear at different distances or be different sizes.

Other possible approaches:
- HOG and SVM: However, it relies on consistent gradient orientations and in X-ray baggage, hammers can be rotated 360 degrees and the gradients of the suitcase create too much noise for a traditional ML classifier like SVM to handle without a massive training set.
- Background Subtraction: This is more useful for video where the camera is static to subtract the background.
- Genetic Algorithms: the search space for object detection is too large for Genetic Algorithms to be efficient in terms of running time.
- Color Histograms: X-ray images are essentially grayscale/density maps.
