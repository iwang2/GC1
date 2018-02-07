# Computer Graphics, Spring 2018
### Table of Contents
DATE | AIM
:---:| ---
1/31 | [Peering into the depths of color](#13118---peering-into-the-depths-of-color) (color depth, file formats)
2/2 | [Utilities](#2218---utilities) (emacs, imagemagick)
2/5 | [Bresenham's Line Algorithm](#2518---bresenhams-line-algorithm)

---
# 2.7.18 - Moving into other octants
### Octant II
```
 __ __ __ __ __ __
|__|__|__|██|__|__|
|__|__|__|__|__|__|
|__|__|__|__|__|__|
|1_|2_|__|__|__|__|
|██|__|__|__|__|__|

```
1. `( x , y + 1 )`
2. `( x + 1 , y + 1 )`  

MIDPOINT: `( x + 0.5 , y + 1 )`

```
d = 2A + B      --> d = A + 2B
while x <= x1   --> y <= y1
  plot(x,y)
    if d > 0    --> d < 0
    x++         // swapped with 
    d += 2A
  y++
  d += 2B
```

### Octant VIII
```
 __ __ 
|██|1_|  // ( x + 1 , y )
|__|2_|  // ( x + 1 , y - 1 )

```
MIDPOINT : `( x + 1 , y - 0.5 )`

```
d0 : f ( x0 + 1 , y0 - 0.5 )
     2A - B
if d > 0
```

---
# 2.5.18 - Bresenham's Line Algorithm 
`y = mx + b` - This is nice, but won't work in graphics because everything is in pixels, and therefore **INTEGERS**.  
When using a line algorithm, we are *approximating* the line (except with horizontal/vertical lines).

### Bresenham's Line Algorithm
Find the pixels that best approximate a target line.
#### Assume: 
- ( x<sub>0</sub> , y<sub>0</sub> ) -> ( x<sub>1</sub> , y<sub>1</sub> ) are all integers (endpoints exist)
- Only lines in octant I - `0 < m < 1`
- x<sub>0</sub> < x<sub>1</sub> - always start in the left and move to the right

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

To pick, use the midpoint `( x + 1 , y + 0.5 )`.  
- If the midpoint is **above** the line, draw the **lower** pixel.
- If the midpoint is **below** the line, draw the **upper** pixel.
- If the midpoint is on the line, draw one or the other. 

#### Testing the Midpoint
```
y = mx + b
0 = mx - y + b

m = Δy / Δx

0 = (Δy / Δx) x - y + b
  = xΔy -yΔx + bΔx
A = Δy, B = -Δx, C = bΔx
0 = Ax + By + C

f(x,y) = Ax + By + C
```
- When the midpoint is directly on the line, `f(x,y) = 0` (this is not really that important).
- When the midpoint is above the line, `f(x,y) < 0`.
- When the midpoint is below the line, `f(x,y) > 0`.

### ( x<sub>0</sub> , y<sub>0</sub> ) -> ( x<sub>1</sub> , y<sub>1</sub> )

#### First Draft Algorithm: 
```
x = x0 ,  y = y1
d = f ( x + 1 , y + 0.5)
while x <= x1
    plot ( x , y )
    x++
    if d > 0 : y++
    d = f ( x + 1 , y + 0.5 )
```

#### Second Draft Algorithm: 
```
if x++ : d += A
if y++ : d += B

d = f( x + 1 , y + 0.5)  
while x <= x1 
    plot ( x , y )  
    if d > 0  
        y++ 
        d += B
    x++  
    d += A
    
d = f ( x0 + 1 , y0 + 0.5 )  
  = A ( x0 + 1 ) + B ( y0 + 0.5 ) + C  
  = A x0 + B y0 + A + 0.5 B  
  = f (x0, y0) + A + 0.5B
  = A + 0.5B
```
#### Third Draft Algorithm
```
d = 2A + B
while x <= x1
 plot(x,y)
 if d > 0
   y++
   d += 2B
 x++
 d += 2A
 ```

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
