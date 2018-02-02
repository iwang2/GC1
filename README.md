# Computer Graphics, Spring 2018

DATE | AIM
--- | ---
1.31 | Color depth, file formats: Peering into the depths of color
2.2 | Utilities
<br>

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

Example File:
```
P3    // file type
4 3   // dimensions
255   // maximum color value
// each group of 3 numbers is a single pixel
255 0 0  255 0 0  255 0 0  255 0 0
0 255 0  0 255 0  0 255 0  0 255 0
0 0 255  0 0 255  0 0 255  0 0 255
```

##### Number 1 Rule of Graphics: 
***DO NOT UPLOAD PPM FILES TO GITHUB***

### Terminal conversion
`$ convert pic.ppm pic.png`
