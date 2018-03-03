# Computer Graphics, Spring 2018
### Table of Contents
DATE | AIM
:---:| ---
1/31 | [Peering into the depths of color](#13118---peering-into-the-depths-of-color) (color depth, file formats)
2/2 | [Utilities](#2218---utilities) (emacs, imagemagick)
2/5 | [Bresenham's Line Algorithm](#2518---bresenhams-line-algorithm)
2/7 | [Line Algorithm in other octants](#2718---moving-into-other-octants)
2/12 | [Representing Image Data](#21218---representing-image-data)
2/13 | [Matrix Math in Graphics](#21318---matrix-math-in-graphics) (matrices, transformations)
2/26 | [Transformations](#22618---transformations) ([translation](#translation), [dilation](#dilation), [rotation](#rotation))

---
# 2.26.18 - Transformations
Translation, dilation, rotation (*affine* transformations; preserves orientation and vertices).
- E: edge matrix
- T: transformation matrix
- E · T or **T · E** ( i.e. 3x3 · 3xN = 3xN )

### Translation
( x, y, z ) -> T<sub>(a, b, c)</sub> -> ( x+a, y+b, z+c );
```
| 1 0 0 a |   | x |   | x+a |
| 0 1 0 b | · | y | = | y+b |
| 0 0 1 c |   | z |   | z+c |
| 0 0 0 1 |   | 1 |   |  1  |
    4x4        4xN      4xN
```

### Dilation
( x, y, z ) -> D<sub>(a, b, c)</sub> -> ( ax, by, cz )
```
| a 0 0 0 |   | x |   | ax |
| 0 b 0 0 | · | y | = | by |
| 0 0 c 0 |   | z |   | cz |
| 0 0 0 1 |   | 1 |   |  1 |
  D(a,b,c)
```
With this formula, the size of the object being drawn will be scaled, but so will the distance from the origin. 
To avoid this, you can translate the object to the origin, dilate, and then translate back. 

### Rotation
```
y
|  · (x1,y1)
|
|      · (x,y)
|
|______________ x
```
( x, y ) -> R<sub>θ</sub> -> ( ? , ? )  
θ is the angle between the two points  
Φ is the angle between (x,y) and the original x-axis

#### Polar Coordinates
- **x = r·cos(Φ)**
- **y = r·sin(Φ)**
- x1 = r·cos(Φ + θ)  
  x1 = r·cos(Φ)cos(θ) - r·sin(Φ)sin(θ)  
  **x1 = x·cos(θ) - y·sin(θ)**
- y1 = r·sin(Φ + θ)  
  **y1 = y·cos(θ) + x·sin(θ)**

```
| cos -sin  0  0 |   | x |   | xcos - ysin |
| sin  cos  0  0 |   | y |   | ycos + xsin |
|  0    0   1  0 | · | z | = |      z      |
|  0    0   0  1 |   | 1 |   |      1      |
     R(θ,z)
```

#### R<sub>θ,x</sub>
```
    z
    |  · (y1,z1)
    |
    |      · (y,z)
    |
    |______________ y
   /
  /
 /
x
```
y = rcosθ, z = rsinθ  
y1 = ycosθ - zsinθ  
z1 = zcosθ + ysinθ

#### R<sub>θ,y</sub>
```
    x
    |  · (z1,x1)
    |
    |      · (z,x)
    |
    |______________ z
   /
  /
 /
y
```
z1 = zcosθ - xsinθ  
x1 = xcosθ + zsinθ

### Combining Transformations
E<sub>0</sub> (edges), T (translate), R (rotate), S (scale)
- T · E<sub>0</sub> = E<sub>1</sub>
- R · E<sub>1</sub> = E<sub>2</sub>
- S · E<sub>2</sub> = E<sub>3</sub>
- **E<sub>3</sub> = ( S · R · T ) · E<sub>0</sub>**

***THIS IS ASSOCIATIVE***  
Read from right to left. Translate first, then rotate, then scale.

---
# 2.13.18 - Matrix Math in Graphics
[ P<sub>0</sub> , P<sub>1</sub> , P<sub>2</sub> , P<sub>3</sub> ... P<sub>n</sub> ]

| x<sub>0</sub> , x<sub>1</sub> ... x<sub>n</sub> |  
| y<sub>0</sub> , y<sub>1</sub> ... y<sub>n</sub> |  
| z<sub>0</sub> , z<sub>1</sub> ... z<sub>n</sub> |

3 x N  (3 rows, N columns)

### Matrix multiplication: M·N
- \# of columns in M = # of rows in N  
- A x B · B x C = A x C
- Non-commutative: M·N ≠ N·M

#### Some Matrix Multiplication Examples
```
            | a |
| 1 2 3 | · | b | = | 1a + 2b + 3c |
    1x3     | c |         1x1
             3x1
```
```
| 1 2 3 |   | a b |   | 1a+2c+3a 1b+2d+3f |
| 4 5 6 | · | c d | = | 4a+5c+6e 4b+5d+6f |
| 7 8 9 |   | e f |   | 7a+8c+9e 7b+8d+9f |
   3x3        3x2             3x2
```

### Multiplicative Identity Matrix: M · I = M
- square, diagonal of 1's, and 0's everywhere else
```
| 1 0 | . | a | = | a |
| 0 1 |   | b |   | b |
```

---
# 2.12.18 - Representing Image Data
Say you have a triangle. Each time you create the triangle, `draw_line()` must be called 3 times.  
To store this triangle, you could create a list of the pairs of points.
- [ p0, p1, p2 ]      --> edge list
- [ p0  p1  p2 ]
  [ p1, p2, p0 ]
- [ p0, p1, p2, p0 ]
- one list for points, another list for connections between points

### Edge List 
List of points in the image where every 2 points determines a line.

[ p<sub>0</sub> , p<sub>1</sub> , p<sub>2</sub> , p<sub>3</sub> ... p<sub>n</sub> ]
--> 
( p<sub>0</sub> , p<sub>1</sub> ), (p<sub>2</sub> , p<sub>3</sub>) , (p<sub>n-1</sub> , p<sub>n</sub> )  
Triangle ABC --> [ A, B, B, C, C, A ]

This is essentially a two dimensional array.

---
# 2.7.18 - Moving into Other Octants

### Octant II
```
 __ __ __ __ __ __
|__|__|__|██|__|__|
|__|__|__|__|__|__|
|__|__|__|__|__|__|   //  1. ( x , y + 1 )
|1_|2_|__|__|__|__|   //  2. ( x + 1 , y + 1 )
|██|__|__|__|__|__|

```
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
1. `( x + 1 , y + 1 )`
2. `( x + 1 , y )`

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
