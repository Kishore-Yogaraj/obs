### Purpose
The purpose of this script is define a colour filter in the HSV space. We are telling open cv to only keep pixels whose Hue, Saturation and Value fall inside this range.

The camera will capture everything inside this range and make it white. Everything outside of this range becomes black. Contours are then drawn and detected from the range.

OpenCV uses this range:
- Hue: 0-179
- Saturation: 0-255
- Value (brightness): 0-255

### Hue Sliders (H low/ H high)
These control **which colour** we want to detect

**Hue** represents the actual color type:
![[Pasted image 20260322195705.png]]

In our current setup we have the following which keeps us in the blue range

```python 
H low = 100
H high = 130
```

If we increase H low: remove darker blues and shift toward cyan
If we decrease H low: include more cyan/greenish tones
If we increase H high: include more purple tones
If we decrease H high: narrows the blue band

Widening this gap between low and high makes the detection more tolerant


### Red Colour and Contour Detection
Red is a special case in open cv because the hue wraps around 
0  = red and 179 = red

This means that we have to specify two ranges when working with red:
In our case we set:
Range 1: 0-10
Range 2: 165 - 180

If we shrink this range then some red pixels may disappear

If we widen this range we might see more orange or magenta start appearing


### Saturation
Saturation controls how **pure** the colour must be

Low saturation = grayish colour
High saturation = vivid color

For example:
If we set our S min = 100, this means we will ignore any washed out or dull colours

If we increase S:
- remove reflections
- removes faded colours
- removes shadowed colours
- keeps only strong colours

If we decrease S:
- We detect more faded and shadowed colours
- Detect more pastel colours
- May include more background noise

We start around 6-120 and adjust upward until the noise completely disappears

### Value slider
Controls the brightness threshold

Low value = dark pixels
High value = bright pixels

For example:
V min = 60

This means to ignore very dark pixels

If we increase V min:
- Removes shadows
- Removes dim lighting artifacts 
- May lose object edges

If we decrease V min:
- detects darker regions
- Improves detection in low ligh
- increases noise of shadow

Increase until shadow noise disappears but object remains visible

### Tuning Workflow
- Set S min = 0, V min = 0
- Adjust Hue sliders until object appears
- Increase S min to remove background
- Increase V min to remove shadows

### Contour Filtering Parameters
**MIN_AREA** - removes contours that are too small to be real objects
- Good for removing sensor noise
- Reflections
- Shadows
- Background texture

**MAX_AREA** - removes contours that are too large to be real objects
- Large object detected accidently
- Camera exposure issues
- Lighting saturation

**MIN_SOLIDITY**- removes objects that are too narrow or thing (spindly)
- Thin reflections
- Edge fragments
- Partial detections
- Streak like shapes
- Contour left overs from morphology

![[Pasted image 20260322195301.png]]

![[Pasted image 20260322195314.png]]