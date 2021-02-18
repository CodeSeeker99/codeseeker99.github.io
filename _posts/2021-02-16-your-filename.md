---
published: false
---
_A short article on the simpleITK library's handling of image data_

---

## Index

  - What is SimpleITK
  - Images in SimpleITK
    - Loading an image
    - Attributes
  - Transforms
    - Common transforms
    - Resampling

## What is SimpleITK?

SimpleITK (SITK) is a simplified programming interface to the algorithms and data structures of the Insight Toolkit (ITK). This means its a library that helps you load images and perform transformations (which are mentioned in the ITK) on them. 

![SITK logo]({{site.baseurl}}/images/sitk.png)

## Images in SimpleITK

Images in SITK are different from those we are used to seeing everyday. Each pixel or [voxel](https://en.wikipedia.org/wiki/Voxel) \[3D pixel basically\] is not isotropically spaced. The image is defined as a physical entity in 3D space. That means, that not only does each pixel have coordinates, there are direction vectors for the axis as well. Simply put, this means,

1. Images can have different spacing between pixels along each axis
2. The axes are not necessarily orthogonal.

But what will happen if we use something like numpy to read the image data and directly plot it instead?

![Isotropic VS non-isotropic]({{site.baseurl}}/images/isotropic.svg)
<p style="text-align:center"><i> Left. Image displayed with a viewer that is not aware of spatial meta-data. Right. Image displayed with a viewer that is aware of spatial meta-
data. Image authors: <a href="https://simpleitk.readthedocs.io/en/master/fundamentalConcepts.html#lbl-isotropy">here</a> </i></p>

### Loading an image

Let's start by loading a dicom image in SimpleITK.

```python
image = sitk.ReadImage('/path/to/image/image.dcm')
print("Type:", type(image), "Image size:", size[0], size[1], size[2])
```

Or if you wish to load an entire series all at once

```python
reader = sitk.ImageSeriesReader()
dicom_names = reader.GetGDCMSeriesFileNames("/path/to/series/directory")
reader.SetFileNames(dicom_names)

image_series = reader.Execute()
size = image_series.GetSize()
print("Type:", type(image_series), "Image size:", size[0], size[1], size[2])
```

This will give us an object of SITK Image class. **Now how do we view it?**
The recommended viewing library from SITK is [FIJI](https://fiji.sc/). You can however, also use [Matplotlib](https://matplotlib.org/stable/index.html) to view single images.

```python
## .. code to get image

## If you already have FIJI installed
sitk.show(image, "Title")

## Using matplotlib instead
nd_image = sitk.GetArrayViewFromImage(image)
nd_image = np.squeeze(nd_image)           # plt cannot plot (1,x,x) it needs to be (x,x) for a 2D image
plt.imshow(nd_image, cmap='gray')
print(f'Image shape {nd_image.shape}')
```

If you wish to access any particular pixel values, simply use:

```python

## .. code to get image
x = 10                          # X-coordinate of the pixel
y = 10                          # Y-coordinate of the pixel
z = 0                           # Z-coordinate of the pixel
value = image.GetPixel((x,y,z)) # May throw error if (x,y,z) is out of bounds of the physical space of the image.
value2 = image[x,y,z]           # Another way of accessing value

print(value)
print(value2)
```

### Image attributes

![ImageDetails]({{site.baseurl}}/images/SITK_image_details.png)
Now, let's get into the details of the attributes of these images

**Origin**: This is the starting point of the image. Identified by the left-bottom corner of the image as shown in the figure. Use ```image.GetOrigin()``` to get the spacing of an SITK image. To change the origin of an image, read the transforms section. This attribute is crucial when composing 2 images on top of each other. If their origins do not match, SITK does not allow super-position of 2 images.

**Spacing**: This is the distance between pixels along each of the dimensions. The picture below shows two images with different spacing values. As you can see, image stretching can be performed by changing this value. Use ```image.GetSpacing()``` to get the spacing of an SITK image.

**Size**: This defines the number of pixels in each dimension of the image. Use ```image.GetSize()``` to get the size of an SITK image.

**Direction Cosines**: A tuple containing 9 values, where first 3 represent the x-axis vector, the next 3 represent the y-axis vector and the last represent the z-axis vector.  Use ```image.GetDirection()``` to get the spacing of the SITK image.

![DimensionDifferences]({{site.baseurl}}/images/dimension_difference.png)

## Transforms

Spatial transformations in SITK convert a point x to T(x) in the following way (except for translation transform)

#### T(x) = A(x-c) + t + c

Here, 
  - Matrix: the matrix A
  - Center: the point c
  - Translation: the vector t
  - Offset: t + c - Ac
  
Translations can be applied directly to points in SITK. The code below performs simple translation on a point in 3D space.

```python
dimension = 2
offset = [1,2]

translation = sitk.TranslationTransform(dimension, offset)

point = [10, 10]
transformed_point = translation.TransformPoint(point)
translation_inverse = translation.GetInverse()

print(translation)
print('original point: {}\ntransformed point: {}\nback to original: {}'.format(point,transformed_point,translation_inverse.TransformPoint(transformed_point)))
```

![TranslationPointsImage]({{site.baseurl}}/images/translation_difference.png)
<p style="text-align:center"><i> Translation transform. Circle: Original point. Triangle: Shifted point</i></p>

### Some common transforms 

**Translation**: Simply moving a point from one place to another. y = x + t
```translation = sitk.TranslationTransform(dimension, offset)```

**Euler Transforms**: This transform applies a rotation and translation to the space given euler angles and a translation. May be 2D or 3D. Rotation is about a user specified center.
```euler2d = sitk.Euler2DTransform(centre, angle, offset)```
```euler3d = sitk.Euler3DTransform(centre, angleX, angleY, angleZ, translation)```

**Affine Transforms**: An affine transformation is defined mathematically as a linear transformation plus a constant offset. If A is a constant n x n matrix and b is a constant n-vector, then y = Ax+b defines an affine transformation from the n-vector x to the n-vector y.
```affine = sitk.AffineTransform(matrix.flatten(), translation, centre)```


### Resampling

We have applied transforms to points. When we do the same for an entire image, it is termed as resampling in SITK. 

```python
dimension = 2
offset = (3, 4)
translation = sitk.TranslationTransform(dimension, offset)

## Create a sample grid for demonstration
grid = sitk.GridSource(outputPixelType=sitk.sitkUInt16,size=(250, 250),sigma=(0.5, 0.5),gridSpacing=(5.0, 5.0),gridOffset=(0.0, 0.0),spacing=(0.2,0.2))

plt.imshow(sitk.GetArrayViewFromImage(grid), cmap='gray', origin ='lower')

resampled = sitk.Resample(grid, translation)
plt.imshow(sitk.GetArrayViewFromImage(resampled), cmap='gray', origin ='lower')
```

![TranslationDifferences]({{site.baseurl}}/images/translation_difference.png)
<p style="text-align:center"><i> Left. Our original grid image. Right. Grid image after applying translation. Notice how our translation is positive (3,4) but our image moved towards the left and bottom. This is because, translation is defined from output points to input points. i.e (3,4) of output image is mapped to (0,0) of input image. Therefore, the </i></p>

Likewise, if we apply Euler transforms on an image

```python
## Define image to be transformed
grid = sitk.GridSource(outputPixelType=sitk.sitkUInt16,size=(250, 250),sigma=(0.5, 0.5),gridSpacing=(5.0, 5.0),gridOffset=(0.0, 0.0),spacing=(0.2,0.2))

## Define transform
angle = np.pi/4
offset = (0, 0)
centre = grid.TransformContinuousIndexToPhysicalPoint(np.array(grid.GetSize())/2.0)
euler2d = sitk.Euler2DTransform(centre, angle, offset)

## Resample
resampled = sitk.Resample(grid, euler2d)

## Show
plt.imshow(sitk.GetArrayViewFromImage(grid), cmap='gray', origin ='lower')
plt.show()
plt.imshow(sitk.GetArrayViewFromImage(resampled), cmap='gray', origin ='lower')
plt.show()
```

In the example above we did not specify a separate reference image for our mapping. As a result, for many of the transformations the resulting image contained black pixels, pixels which were mapped outside the spatial domain of the original image and a partial view of the original image.

If we want the resulting image to contain all of the original image no matter the transformation, we will need to define the resampling grid using our knowledge of the original image's spatial domain and the inverse of the given transformation.


   



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
