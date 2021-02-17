---
published: false
---
_A short article on the simpleITK library's handling of image data_

---

## Index

  - What is SimpleITK
  - Images in SimpleITK
    - Loading an image
    - Spacing
    - Direction corsines
    - other small attributes (size etc)
  - Transforms
    - Translation
    - Affine types

## What is SimpleITK?

SimpleITK (SITK) is a simplified programming interface to the algorithms and data structures of the Insight Toolkit (ITK). This means its a library that helps you load images and perform transformations (which are mentioned in the ITK) on them. 

Images in SITK are different from those we are used to seeing everyday. Each pixel or [voxel](https://en.wikipedia.org/wiki/Voxel) \[3D pixel basically\] is not isotropically spaced. The image is defined as a physical entity in 3D space. That means, that not only does each pixel have coordinates, there are direction vectors for the axis as well. Simply put, this means,

1. Images can have different spacing between pixels along each axis
2. The axes are not necessarily orthogonal.

## Images in SimpleITK

Let's start by loading a dicom image in SimpleITK.

```python
image_file_reader = sitk.ImageFileReader()
image_file_reader.SetFileName('/path/to/image/image.dcm')
image = image_file_reader.Execute()
```








   



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
