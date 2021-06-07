## Barcode and QR code scanner
Barcode and QR code Scanner using ZBar and OpenCV

The **ZBar library** will be used together with OpenCV to scan and decode barcodes and QR codes.


### The structure of a barcode / QR code
1. **Type**: If the symbol detected by ZBar is a QR code, the type is QR-Code. If it is barcode, the type is one of the several kinds of barcodes ZBar is able to read. In our example, we have used a barcode of type CODE-128
2. **Data**: This is the data embedded inside the barcode / QR code. This data is usually alphanumeric, but other types ( numeric, byte/binary etc. ) are also valid.
3. **Location**: This is a collection of points that locate the code. For QR codes, it is a list of four points corresponding to the four corners of the QR code quad. For barcodes, location is a collection of points that mark the start and end of word boundaries in the barcode. The location points are plotted for a few different kinds of symbols below.

### Decoding barcodes and QR codes

![](https://github.com/shejz/Barcode-and-QR-code-scanner/blob/main/qr-barcode.png)
