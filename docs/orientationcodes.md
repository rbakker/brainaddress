---
layout: default
title: "Orientation codes"
---

# Anatomical orientation codes

Anatomical orientation codes describe the spatial orientation of the brain in its 
default position, i.e. when the subject is looking ahead.
There are three orientation axes for which single-letter orientation code-pairs are defined.
- The left-right axis (seen from the subject's perspective), with code-pair [L, R]
- The posterior-anterior axis, with code-pair [P, A]
- The inferior-superior axis, with code-pair [I, S]

In BAS, these orientation codes are used to describe the orientation of the 
data *after applying the transformation that converts voxel-indices to
real-world coordinates*. The x-, y- and z-axes are assigned
orientation codes in such a way that one code from each code-pair is used exactly 
once. Examples of valid orientation codes: RAS+, PIR+, LPI+, etc.
The + in RAS+ indicates that the codes refer to the direction of the positive axes.
It can be omitted, since this is already the case by default.
It should be noted however that some file formats, in particular those affiliated 
with the <a href="https://itk.org/ITK/project/about.html">ITK project</a>, 
use orientation codes that work in the opposite direction.

Assigning orientation codes to your data can be a confusing matter, and 
a lot of tutorials have been written on this subject.
- https://nipy.org/nibabel/coordinate_systems.html
- https://www.slicer.org/wiki/Coordinate_systems
- https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Orientation%20Explained

If you use Nifti volume files, then the Nifti standard requires that the anatomical
orientation of the data in real-world coordinates is RAS+. 
However, quite often this requirement gets ignored.
