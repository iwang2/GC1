# Computer Graphics, Spring 2018
### Table of Contents
DATE | AIM
:---:| ---
1/31 | [Peering into the depths of color](#13118---peering-into-the-depths-of-color) (color depth, file formats)
2/2 | [Utilities](#2218---utilities) (emacs, imagemagick)
2/5 | [Bresenham's Line Algorithm](#2518---bresenhams-line-algorithm)
2/7 | [Line Algorithm in other octants](#2718---moving-into-other-octants)
2/12 | [Representing Image Data](#21218---representing-image-data)
2/13 | [Matrix Math in Graphics](#21318---matrix-math-in-graphics)
2/26 | [Transformations](#22618---transformations) ([translation](#translation), [dilation](#dilation), [rotation](#rotation))
3/5 | [Parametrics](#3518---parametric-equations)
3/6 | [Hermite Curves](#3618---hermite-curves)
3/7 | [Bezier Curves](#3718---bezier-curves)
3/13 | [3D Shapes](#31318---3d-shapes) ([cube](#box), [sphere](#sphere), [torus](#torus))
3/20 | [Vector Math](#32018---vector-math-review) ([dot product](#dot-product-a--b), [cross product](#cross-product-a-x-b))
3/23 | [Rendering 3D Objects](#32318---rendering-3d-objects) ([wireframe](#wireframe-mesh), [polygon](#polygon-mesh))
3/28 | [Backface Culling](#32818---backface-culling)
4/11 | [Relative Coordinate System](#41118---relative-coordinate-system) ([CS stack](#coordinate-system-stack))
4/17 | [Filling in Triangles](#41718---filling-in-triangles) ([scanline conversion](#scanline-conversion))
4/19 | [Z-Buffering](#41918---z-buffering)
4/26 | [Lighting]

---
# 4.26.18 - Lighting

### Colors are modeled based on:
1. Color, intensity, and location of light.
2. Reflective properties of objects. 

### Types of Light
**Ambient Light:** general lighting of an image
- so single source - comes from all locations equally
- represented by a normal color value

**Point Light:** comes from a specific location
- can have many sources for an image
- we will assume the light is from very far away
- represented by a *color* and *location*

---
# 4.19.18 - Z-Buffering
Keeping a separate set of z values corresponding to each point the color grid to draw only the most front-facing side of an object.

**Z-BUFFER:** 2D array of *floating point* values that directly correspond to the screen
- check the z-buffer each time before updating the screen
- initialize each z-value to the smallest possible value

### Functions That Must Be Significantly Modified
- **`plot`** must check/modify the z-buffer 
- **`draw_line`** must compute z-values
- **`scanline`** must compute z-values

### Phong Reflection Model
Mlodels real world reflections by breaking them into 3 parts.
- Ambient, Diffuse, spectacular

I = ambient + diffuse + specular  
I: color, normalization
- diffuse and specular are point light

### Ambient Light
A: Ambient Light (0->255)
K<sub>a</sub>: constatnt of ambient reflection(0->1)
Ambient = AK<sub>a</sub>

### Diffuse Reflection
Relfection of a point ight source. 
Reflected evenly in all directions. Things that have a matte/dull finish.

---
# 4.17.18 - Filling in Triangles
Given a triangle, how can we fill in its area?
1. Fill with smaller and smaller triangles.
2. Edge line fill (blending an edge to its opposite point; parallel lines to an edge)
3. Scanline fill
4. Flood fill (a single point, and plot its adjacent points; recursive)

Things that are good about scanline:
- no double coverage
- no weird spacing (horizontal lines only)
- full coverage
- takes advantage of the optimized draw_line 

### Scanline Conversion
Filling in a polygon by drawing consecutive horizontal (or vertical) lines.
- Need to order vertices vertically for bottom, middle, and top, and find the endpoints of each scanline.  
```
     top (T)
       /\
      /  \
  x0 /    \
    /    __\  middle
   /__---
bottom (B)
```

Value | Pseudocode / Equivalent
--- | ---
y | y<sub>B</sub> -> y<sub>T</sub><br>y++
x<sub>0</sub> | on the line **BT**<br>x<sub>B</sub> -> x<sub>T</sub><br>x<sub>0</sub> += Δ0
Δ0 | (x<sub>T</sub> - x<sub>B</sub>) / (y<sub>T</sub> - y<sub>B</sub>)<br>*or*<br>Δx / # of scanlines, aka Δy
x<sub>1</sub> | on the line **BM** until y = y<sub>M</sub>, then on **MT**<br>x<sub>1</sub> += Δ1
Δ1 | (x<sub>M</sub> - x<sub>B</sub>) / (y<sub>M</sub> - y<sub>B</sub>)<br>*or*<br>(x<sub>T</sub> - x<sub>M</sub>) / (y<sub>T</sub> - y<sub>M</sub>)

### But...
What happens with a triangle where the middle has the same y-value as the top or bottom point?
```
    T
    /\
   /  \
  /    \
 /______\
B        M
```
Could possibly be dividing by 0, in which case you switch the order you draw each scanline. For example, instead of going from top to bottom, go from bottom to top (or vice versa).

If you flip, make sure to set x = x<sub>M</sub> to ensure that you cover the middle value. 

---
# 4.11.18 - Relative Coordinate System
Currently transformations are applied to all shapes in the polygon/edge matrix.  
In a RCS, we use transformations to modify the world in which we draw our shapes, rather than the shapes themselves.  
Each shape can be drawn in a different world.

```
| 1 0 0 0 |   |  2  0  0  50  |
| 0 1 0 0 | · |  0  2  0  100 |
| 0 0 1 0 |   |  0  0  2  0   |
| 0 0 0 1 |   |  0  0  0  1   |
     A               B
```

### Drawing
1. Generate polygons/edges.
2. Apply the current coordinate system to these points. 
3. Draw the polygons/edges to the image. 

#### For Example: Drawing a sphere in the upper right.
```
sphere        rotate
rotate  ===>  move
move          sphere
apply
```
Everything must be done in reverse.

### Coordinate System Stack
We will maintain a stack of coordinate systems.  
The top will always be the most current system.  
The bottom will always be the identity matrix (the global system).  
All transformations modify the top, but none of the other matrices.  
Push will push a **copy** of the top.

transformation · top vs. **top · transformation**

Command | Stack
:---:| ---
push | I<br>I
move | I<br>I · M<sub>1</sub>
box | ""
push | M<sub>1</sub> · I<br>M<sub>1</sub> · I<br>I
move | I·M<sub>1</sub>·M<sub>2</sub><br>I·M<sub>1</sub><br>I
rotate | I·M<sub>1</sub>·M<sub>2</sub>·R<br>I·M<sub>1</sub><br>I
sphere | ""
pop | I·M<sub>1</sub><br>I

---
# 3.28.18 - Backface Culling
Only drawing the polygons that are forward-facing.

Given a surface, take the surface normal (**N**), and a vector from the surface to the observer (**V**).  
The angle between the two will tell us which way the surface is facing. 

### Step 1: Calculate **N**.
Taking 3 points (a triangle P0, P1, P2), two vectors **A** and **B** point away from the same point.
- **N** = **A** x **B**
- **A** = < P<sub>1</sub> - P<sub>0</sub> >
- **B** = < P<sub>2</sub> - P<sub>0</sub> >

### Step 2: Find θ.
- **N** · **V** = || **N** || · || **V** || · cosθ
- **V** = < 0, 0, 1 >
- **N** = < x, y, z >
- **N** · **V** = 0 + 0 + z

### Step 3: If `-90° < θ < 90°`, draw.
Cosine is positive from -90 to 90, so if **N** · **V** > 0, draw. 

---
# 3.23.18 - Rendering 3D Objects

### Wireframe Mesh
Shape is defined by the *edges* that connect points on the surface.

`add_box` -> `add_edge` -> `add_point`  
`draw_lines` -> `draw_line`

#### Disadvantages: 
- edges cannot be filled in
- perspective is difficult to discern just by looking at the screen

### Polygon Mesh
Shape is defined by the *polygons* that connect points on the surface (triangles).

`add_box` -> `add_polygon` -> `add_point`  
new function (because `draw_lines` is no longer suitable): `draw_polygons` -> `draw_line`

```
    P0
    /\
P1 /__\ P2
      /\
     /__\
   P3    P4
```
#### Edge Matrix:
[ P<sub>0</sub>P<sub>1</sub>, P<sub>0</sub>P<sub>2</sub>, P<sub>1</sub>P<sub>2</sub>, ... ]
- six edges

#### Polygon Matrix
[ P<sub>0</sub>P<sub>1</sub>P<sub>2</sub>, P<sub>2</sub>P<sub>3</sub>P<sub>4</sub> ]
- two triangles
- points must be added in *counter-clockwise* order

---
# 3.20.18 - Vector Math "Review"
Vectors have direction and magnitude. 
```
| ( x, y, z )     |  ^   < x, y, z >
|    ·            | /
|____________     |/____________
```
Given vector **A**, `< x,y,z >`, the magnitude || A || = sqrt( x<sup>2</sup>, y<sup>2</sup>, z<sup>2</sup> ).

A normalized vector is a unit vector that preserves the direction of another vector.  
A' = 1 / || A || * < A<sub>x</sub>, A<sub>y</sub>, A<sub>z</sub> >

Given vectors **A** and **B**, and angle θ between them:  
### Dot Product: **A** · **B**
- || A || · || B || · cosθ
- A<sub>x</sub> · B<sub>x</sub> + A<sub>y</sub> · B<sub>y</sub> + A<sub>z</sub> · B<sub>z</sub>

### Cross Product: **A** x **B**
Perpendicular to **A** and **B**.  
Magnitude = area of the parallelogram formed by **A** and **B**.  
- || A || · || B || · sinθ
- < 
	A<sub>y</sub>B<sub>z</sub> - A<sub>z</sub>B<sub>y</sub> , 
	A<sub>z</sub>B<sub>x</sub> - A<sub>x</sub>B<sub>z</sub> , 
	A<sub>x</sub>B<sub>y</sub> - A<sub>y</sub>B<sub>y</sub> >

### Finding a Vector between 2 Points
**PQ**: < Q-P >  
**QP**: < P-Q >

---
# 3.13.18 - 3D Shapes

### Box
**Defining points:** vertices  
**Given information:** P<sub>0</sub> (top, left, front), width (x), height (y), depth (z)  

### Sphere
**Defining points:** points on the surface  
**Given information:** center, radius

Generate the defining points by drawing a circle and rotating it.  
If you rotate about the z-axis, there is no 3D shape. Rotate about the x or y-axis.  
```
| 1  0      0  |   | rcosθ |   | x |
| 0 cosϕ -sinϕ | · | rsinθ | = | y |
| 0 sinϕ  cosϕ |   |   0   |   | z |
```
θ = angle of circle creation  
ϕ = angle of rotation (0 -> 2π)

There are a few ways to draw a sphere:
1. Draw a circle and rotate it 180 degrees.
2. Draw a semi-circle and rotate it 360 degrees (the better option). 

#### Pseudocode
```
for (ϕ: 0 -> 2π) {
	for (θ: 0 -> π) { // Semicircle
		x = rcosθ + cx
		y = rsinθcosϕ + cy
		z = rsinθsinϕ + cz
	}
}
```

### Torus
**Defining points:** points on the surface  
**Given information:** R (radius of the whole torus), r (radius of cross section), center

Generate the image by translating a circle and rotating it.  
If we move over x, rotate about y.  
If we move over y, rotate about x. 

```
    y-rotation          circle               torus
|  cosϕ  0  sinϕ |  | rcosθ + R |   |  cosϕ(rcosθ + R) + cx |   | x |
|   0    1    0  |  |   rsinθ   | = |      rsinθ + cy       | = | y |
| -sinϕ  0  cosϕ |  |     0     |   | -sinϕ(rcosθ + R) + cz |   | z |
```
θ = 0 -> 2π  
ϕ = 0 -> 2π

---
# 3.7.18 - Bezier Curves
Defined by `n + 1` points (n = degree of the equation).

### Line
We will start with a line.
```
      . P1
     /
    /
   . Pt
  / 
 /
. P0
```
- P<sub>t</sub> moves along the line P<sub>0</sub>P<sub>1</sub>
- P<sub>t</sub> = (1 - t) · P<sub>0</sub> + t · P<sub>1</sub>

### Quadratic
Three points: P<sub>0</sub>, P<sub>1</sub>, P<sub>2</sub>.

Q<sub>0</sub> moves along the line P<sub>0</sub>P<sub>1</sub>.   
Q<sub>1</sub> moves along the line P<sub>1</sub>P<sub>2</sub>.  
Q<sub>t</sub> moves along the line Q<sub>0</sub>Q<sub>1</sub>.

Q<sub>t</sub> = (1 - t) · Q<sub>0</sub> + t · Q<sub>1</sub>  
= (1 - t)<sup>2</sup> · P<sub>0</sub> + 2t · (1 - t) · P<sub>1</sub> + t<sup>2</sup> · P<sub>2</sub>

### Cubic
Rt moves along the line R0, R1.  
R0 moves along the quadratic Q0, Q1.  
R1 moves along the quadratc Q1, Q2.

#### R<sub>t</sub>
= (1 - t) · R<sub>0</sub> + t · R<sub>1</sub>  
= (1 - t)<sup>2</sup> · P<sub>0</sub> + 3t · (1 - t)<sup>2</sup> · P<sub>1</sub> + 3 · t<sup>2</sup> · (1 - t) · P<sub>2</sub> + t<sup>3</sup> · P<sub>3</sub>  
= ( -P<sub>0</sub> + 3P<sub>1</sub> - 3P<sub>2</sub> + P<sub>3</sub> ) t<sup>3</sup> + ( 3P<sub>0</sub> - 6P<sub>1</sub> + 3P<sub>2</sub> ) t<sup>2</sup> + ( -3P<sub>0</sub> + 3P<sub>1</sub> ) t + P<sub>0</sub>

#### a·t<sup>3</sup> + b·t<sup>2</sup> + c·t + d
- a = -P<sub>0</sub> + 3P<sub>1</sub> - 3P<sub>2</sub> + P<sub>3</sub>
- b = 3P<sub>0</sub> - 6P<sub>1</sub> + 3P<sub>2</sub>
- c = -3P<sub>0</sub> + 3P<sub>1</sub>
- d = 3P<sub>2</sub>

```
| -1  3 -3  1 |   | P0 |   | a |
|  3 -6  3  0 |   | P1 |   | b |
| -3  3  0  0 | · | P2 | = | c |
|  1  0  0  0 |   | P3 |   | d |
```

---
# 3.6.18 - Hermite Curves

### Splines
Curves that can be combined smoothly.  
We will only draw cubics, and combine them to form these curves.

for t: 0->1, t += step
- x = a<sub>x</sub> · t<sup>3</sup> + b<sub>x</sub> · t<sup>2</sup> + c · t + d<sub>x</sub>
- y = a<sub>y</sub> · t<sup>3</sup> + b<sub>y</sub> · t<sup>2</sup> + c · t + d<sub>y</sub>

### Given Information
- endpoints: P<sub>0</sub>, P<sub>1</sub>
- rate of change at each endpoint: R<sub>0</sub>, R<sub>1</sub>
- points on curve: f(t) = a · t<sup>3</sup> + b · t<sup>2</sup> + c · t + d
- rates of change: f'(t) = 3a · t<sup>2</sup> + 2b · t + c
