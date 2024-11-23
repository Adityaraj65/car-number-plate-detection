# car-number-plate-detection
This MATLAB code implements Basic License Plate Detection without using the Computer Vision Toolbox. It is designed to detect and extract a license plate from an image. Below is an explanation of the code step-by-step:

1. Setting Up the Environment:
clc;
clear all;
close all;
•	clc: Clears the MATLAB command window.
•	clear all: Clears the workspace, removing all variables.
•	close all: Closes all figure windows that may be open.

3. File Selection and Image Loading:
[filename, pathname] = uigetfile({'.jpg;.png;.bmp', 'Image Files (.jpg,.png,.bmp)'});
if filename == 0
    error('No file selected');
end
img = imread(fullfile(pathname, filename));
•	uigetfile: Opens a file selection dialog, allowing the user to select an image file from their computer.
•	imread: Loads the selected image into the workspace as img.


5. Displaying the Original Image:
figure('Name', 'License Plate Detection');
subplot(2,2,1);
imshow(img);
title('Original Image');
•	A figure window is created for displaying results.
•	The original image is shown in the first subplot with the title 'Original Image'.
6. Image Preprocessing (Grayscale Conversion):
if size(img,3) == 3
    gray = rgb2gray(img);
else
    gray = img;
end
•	This checks if the image is in color (3 channels). If true, it converts the image to grayscale using rgb2gray. If the image is already grayscale (1 channel), it is used as is.
7. Focusing on the Bottom Half of the Image:
[rows, cols] = size(gray);
bottomHalf = gray(round(rows/2):end, :);
•	The size of the image is obtained using size(), which gives the number of rows and columns.
•	The bottom half of the image is extracted (round(rows/2):end), which is often where the license plate is located.
8. Displaying the Bottom Half of the Image:
subplot(2,2,2);
imshow(bottomHalf);
title('Bottom Half');
•	The bottom half of the image is displayed in the second subplot, titled 'Bottom Half'.
9. Thresholding and Noise Removal:
level = graythresh(bottomHalf);
binary = im2bw(bottomHalf, level);
se = strel('square', 3);
binary = imopen(binary, se);
binary = imclose(binary, se);
•	graythresh: Automatically determines an optimal threshold value for converting the image to binary (black and white).
•	im2bw: Converts the image to a binary image using the computed threshold.
•	strel: Creates a structural element (a square of size 3x3).
•	imopen and imclose: Apply morphological operations to remove small noise and close gaps in the binary image. These operations help clean up the image and make the plate region more distinct.
10. Displaying Processed Image (Binary Image):
subplot(2,2,3);
imshow(binary);
title('Processed Image');
•	The binary (processed) image is displayed in the third subplot, titled 'Processed Image'.
11. Finding Potential License Plate Regions:
[labeledImage, numRegions] = bwlabel(binary);
regionMeasurements = regionprops(labeledImage, 'BoundingBox', 'Area');
•	bwlabel: Labels connected regions in the binary image, assigning a unique label to each connected component.
•	regionprops: Measures properties of the labeled regions, such as the bounding box (coordinates of the region) and the area.
12. Filtering Potential Plate Regions:
possiblePlates = false(size(regionMeasurements));
for i = 1:numRegions
    bbox = regionMeasurements(i).BoundingBox;
    area = regionMeasurements(i).Area;
    
    aspectRatio = bbox(3) / bbox(4);
    
    if aspectRatio > 2 && aspectRatio < 5 && area > 2000
        possiblePlates(i) = true;
    end
end
•	A loop goes through each detected region.
•	The aspect ratio (width/height) of each region is calculated. License plates typically have an aspect ratio between 2 and 5.
•	Only regions with an appropriate aspect ratio and an area greater than 2000 pixels are considered as potential license plates.
•	possiblePlates(i) = true marks regions that could be license plates.
13. Finding the Most Likely License Plate Region:
validRegions = regionMeasurements(possiblePlates);
if ~isempty(validRegions)
    [~, maxIdx] = max([validRegions.Area]);
    plateBox = validRegions(maxIdx).BoundingBox;
    
    plateRegion = imcrop(bottomHalf, plateBox);
    
    subplot(2,2,4);
    imshow(plateRegion);
    title('Detected Plate');
•	From the valid regions, the region with the largest area is selected as the most likely license plate.
•	imcrop: Crops the image to extract the detected license plate using the bounding box (plateBox).
•	The detected plate is displayed in the fourth subplot, titled 'Detected Plate'.
14. Enhancing the License Plate Image:
plateBW = im2bw(plateRegion, graythresh(plateRegion));
figure('Name', 'Enhanced Plate');
imshow(plateBW);
title('Enhanced Plate Image');
•	The extracted license plate is further processed into a binary image using im2bw.
•	The enhanced version of the license plate is displayed in a new figure.
15. Saving Detected Plate Images:
imwrite(plateRegion, 'detected_plate.jpg');
imwrite(plateBW, 'enhanced_plate.jpg');
fprintf('Plate images have been saved as "detected_plate.jpg" and "enhanced_plate.jpg"\n');
•	The detected license plate image (plateRegion) and the enhanced binary plate image (plateBW) are saved as separate JPEG files on disk.
16. Error Handling:
catch err
    errordlg(['Error: ' err.message]);
end
•	If an error occurs anywhere in the process, it is caught by the catch block, and an error message is displayed using errordlg.
Summary of the Code's Functionality:
This MATLAB code performs the following steps for License Plate Detection:
1.	Image Input: The user selects an image of a vehicle.
2.	Preprocessing: The image is converted to grayscale, and the bottom half is extracted.
3.	Thresholding and Noise Removal: The image is converted to a binary form, and noise is removed using morphological operations.
4.	Region Detection: Connected regions are labeled, and those with an appropriate aspect ratio and size are considered potential license plates.
5.	Plate Detection: The largest potential region is identified as the license plate, and it is extracted from the image.
6.	Enhancement: The license plate image is enhanced and displayed in a new figure.
7.	Saving Output: The detected and enhanced plate images are saved as files.
This code demonstrates basic license plate detection without relying on specialized toolboxes, making it suitable for simple applications. However, for more accurate and reliable results, you might want to use more advanced methods such as Haar cascades or deep learning-based approaches.

