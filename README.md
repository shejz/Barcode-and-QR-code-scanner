## Barcode and QR Code Scanner
Barcode and QR code Scanner using ZBar and OpenCV

The **ZBar library** will be used together with OpenCV to scan and decode barcodes and QR codes.


### The structure of a barcode / QR code
1. **Type**: If the symbol detected by ZBar is a QR code, the type is QR-Code. If it is barcode, the type is one of the several kinds of barcodes ZBar is able to read. In our example, we have used a barcode of type CODE-128
2. **Data**: This is the data embedded inside the barcode / QR code. This data is usually alphanumeric, but other types ( numeric, byte/binary etc. ) are also valid.
3. **Location**: This is a collection of points that locate the code. For QR codes, it is a list of four points corresponding to the four corners of the QR code quad. For barcodes, location is a collection of points that mark the start and end of word boundaries in the barcode. The location points are plotted for a few different kinds of symbols below.

### Decoding barcodes and QR codes

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/qr-barcode.png)

## Detecting Barcodes in Images

The general outline of the algorithm is to:

1. Compute the Scharr gradient magnitude representations in both the x and y direction.
2. Subtract the y-gradient from the x-gradient to reveal the barcoded region.
3. Blur and threshold the image.
4. Apply a closing kernel to the thresholded image.
5. Perform a series of dilations and erosions.
6. Find the largest contour in the image, which is now presumably the barcode.

Load our image off disk and convert it to grayscale.then, we use the Scharr operator (specified using ksize = -1 ) to construct the gradient magnitude representation of the grayscale image in the horizontal and vertical directions. From there, we subtract the y-gradient of the Scharr operator from the x-gradient of the Scharr operator. By performing this subtraction we are left with regions of the image that have high horizontal gradients and low vertical gradients.

Our gradient representation of our original image looks like:

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/Detecting%20Barcodes%20in%20Images/gradient_barcode.jpg)

Notice how the barcoded region of the image has been detected by our gradient operations. The next steps will be to filter out the noise in the image and focus solely on the barcode region.

The first thing we’ll do is apply an average blur to the gradient image using a 9 x 9 kernel. This will help smooth out high frequency noise in the gradient representation of the image. We’ll then threshold the blurred image. Any pixel in the gradient image that is not greater than 225 is set to 0 (black). Otherwise, the pixel is set to 255 (white).

The output of the blurring and thresholding looks like this:

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/Detecting%20Barcodes%20in%20Images/threshold.jpg)

However, as you can see in the threshold image above, there are gaps between the vertical bars of the barcode. In order to close these gaps and make it easier for our algorithm to detect the “blob”-like region of the barcode, we’ll need to perform some basic morphological operations:

We’ll start by constructing a rectangular kernel. This kernel has a width that is larger than the height, thus allowing us to close the gaps between vertical stripes of the barcode. We then perform our morphological operation by applying our kernel to our thresholded image, thus attempting to close the the gaps between the bars.

You can now see that the gaps are substantially more closed, as compared to the thresholded image above:

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/Detecting%20Barcodes%20in%20Images/morphological.jpg)

Of course, now we have small blobs in the image that are not part of the actual barcode, but may interfere with our contour detection. Remove these small blobs:

All we are doing here is performing 4 iterations of erosions, followed by 4 iterations of dilations. An erosion will “erode” the white pixels in the image, thus removing the small blobs, whereas a dilation will “dilate” the remaining white pixels and grow the white regions back out. Provided that the small blobs were removed during the erosion, they will not reappear during the dilation.

After our series of erosions and dilations you can see that the small blobs have been successfully removed and we are left with the barcode region:

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/Detecting%20Barcodes%20in%20Images/blobs.jpg)

Finally, let’s find the contours of the barcoded region of the image:

Find the largest contour in the image, which if we have done on image processing, should correspond to the barcoded region. We then determine the minimum bounding box for the largest contour and finally display the detected barcode.

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/Detecting%20Barcodes%20in%20Images/detected_barcode_in_image.jpg)


### Limitations

It is important to note that since this method makes assumptions regarding the gradient representations of the image, and thus will only work for **horizontal barcodes**.

If you wanted to implement a more robust barcode detection algorithm, you would need to take the orientation of the image into consideration, or better yet, apply machine learning techniques such as Haar cascades or HOG + Linear SVM to “scan” the image for barcoded regions.
