# Computer Graphics, Spring 2018
### Table of Contents
DATE | AIM
:---:| ---
1/31 | [Peering into the depths of color](#13118---peering-into-the-depths-of-color) (color depth, file formats)
2/2 | [Utilities](#2218---utilities) (emacs, imagemagick)
2/5 | [Bresenham's Line Algorithm](#2518---line-algorithms)
<br>
---
# 2.5.18 - Line Algorithms 
`y = mx + b` - This is nice, but won't work in graphics because everything is in pixels, and therefore **INTEGERS**.  
When using a line algorithm, we are *approximating* the line (except with horizontal/vertical lines).

### Bresenham's Line Algorithm
Find the pixels that best approximate a target line.
#### Assume: 
- `( x0 , y0 ) -> ( x1 , y1 )` are all integers (endpoints exist)
- Only lines in octant I - `0 < m < 1`
- `x0 < x1` - always start in the left and move to the right

#### For example:
```
 __ __ __ __ __ __
|__|__|__|__|__|__|
|__|__|__|__|__|██|
|__|__|__|__|__|__|
|__|1_|__|__|__|__|
|██|2_|__|__|__|__|

```
In picking the line between the two selected endpoints, we have 2 options for the next pixel:
1. `( x+1 , y+1 )`
2. `( x+1 , y )`

To pick, use the midpoint `( x+1 , y+0.5 )`.  
- If `( x+1 , y+0.5 )` is above the line, draw the lower pixel.
- If `( x+1 , y+0.5 )` is below the line, draw the upper pixel.
- If `( x+1 , y+0.5 )` is on the line, draw one or the other. 

---
# 2.2.18 - Utilities
### emacs
`ctrl-C` to convert between image and code

### imagemagick
Program thing for ppm.
- `$ display image.ppm`
- `$ convert image.ppm image.png`

---
# 1.31.18 - Peering into the depths of color.

### Color Depth
The amount of data used to represent a single pixel.

SIZE | COLOR OPTIONS
--- | ---
1 bit | 1 color; on/off
2 bit | 1 color; on/off, plus 1 bit for intensity
3 bit | Red, Green, Blue; on/off
4 bit | RGB with intensity
6 bit | RGB, each color with its own intensity (each one color has 2 bits)
3 byte | 1 byte for each color; each with 256 possible intensities (~16 million combinations)

### Other Color Spaces
We mostly just use RGB.
- **RGBA:** Red, Green, Blue + Alpha (transparency)
- **HSB:** Hue, Saturation, Brightness (cone diagram)

## Image File Formats

### Vector vs. Raster
**Vector:** represent images as a series of drawing instructions
- They are infinitely scalable.
- SVG (Scalable Vector Graphics)

**Raster:** represent images as a grid of pixels

### Uncompressed vs. Compressed (Raster)
**Uncompressed:** contain data for each pixel
- (i.e. BMP, TIFF, RAW)

**Compressed:** use a compression algorithm to minimize file size
1. *lossless* - contain enough information to exactly recreate the original image
   - PNG (Portable Network Graphics), GIF (Graphics Interchange Format)
2. *lossy* - do not retain all the details of the original image
   - JPEG (Joint Photographic Experts Group)

### PPM (Portable PixMap)
Uncompressed raster format.  
Pixel data is represented by RGB triplets in either ASCII or binary.  
All whitespace is equivalent.

#### Example File:
```
P3    // file type
4 3   // dimensions
255   // maximum color value
// each group of 3 numbers is a single pixel
255 0 0  255 0 0  255 0 0  255 0 0
0 255 0  0 255 0  0 255 0  0 255 0
0 0 255  0 0 255  0 0 255  0 0 255
```

Number 1 Rule of Graphics:  
***DO NOT UPLOAD PPM FILES TO GITHUB***
