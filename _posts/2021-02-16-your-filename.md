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
    - Translation
    - Affine types

## What is SimpleITK?

SimpleITK (SITK) is a simplified programming interface to the algorithms and data structures of the Insight Toolkit (ITK). This means its a library that helps you load images and perform transformations (which are mentioned in the ITK) on them. 

## Images in SimpleITK

Images in SITK are different from those we are used to seeing everyday. Each pixel or [voxel](https://en.wikipedia.org/wiki/Voxel) \[3D pixel basically\] is not isotropically spaced. The image is defined as a physical entity in 3D space. That means, that not only does each pixel have coordinates, there are direction vectors for the axis as well. Simply put, this means,

1. Images can have different spacing between pixels along each axis
2. The axes are not necessarily orthogonal.

But what will happen if we use something like numpy to read the image data and directly plot it instead?

![Isotropic VS non-isotropic]({{site.baseurl}}/images/isotropic.svg)
<p style="text-align:center"><i> Left. Image displayed with a viewer that is not aware of spatial meta-data (eg. matplotlib). Right. Image displayed with a viewer that is aware of spatial meta-
data (eg. Fiji) Image authors: <a href="https://youtu.be/2JNTJKaz-aM?t=616">here</a> </i></p>

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
x = 10                        # X-coordinate of the pixel
y = 10                        # Y-coordinate of the pixel
value = image.GetPixel((x,y)) # May throw error if (x,y) is out of bounds of the physical space of the image.

print(value)
```
### Image attributes
![ImageDetails]({{site.baseurl}}/images/SITK_image_details.png)
Now, let's get into the details of the attributes of these images

**Origin**: This is the starting point of the image. Identified by the left-bottom corner of the image as shown in the figure. Use ```image.GetOrigin()``` to get the spacing of an SITK image. To change the origin of an image, read the transforms section.

**Spacing**: This is the distance between pixels along each of the dimensions. The picture below showsUse ```image.GetSpacing()``` to get the spacing of an SITK image.










   



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
