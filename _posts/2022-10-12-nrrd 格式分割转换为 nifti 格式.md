---
layout: mypost
title: nrrd 格式分割转换为 nifti 格式.md
categories: [python, medical image]
---

```
import nrrd
import SimpleITK as sitk
import numpy as np
import os
from os.path import join
import gaomi

def ConvertNrrd2Nii(nrrd_file, ref_file, save_file):
    """Save .nrrd format segmentation As .nii.gz format.

    Parameters
    ----------
    nrrd_path:

      file path of .nrrd format segmentation file

    image_path:

      file path of the base image of the segmentation above

    save_path:
    
      the path you want to save the .nii.gz format segmentation
      
    Make sure all paths do not contain any Chinese Characters!
    """
    img_nrrd = sitk.ReadImage(nrrd_file)
    arr_nrrd = sitk.GetArrayFromImage(img_nrrd)
    print(arr_nrrd.shape)
    
    img_ref = sitk.ReadImage(ref_file)
    arr_ref = sitk.GetArrayFromImage(img_ref)
    
    params = gaomi.GetParams(img_ref)
    
    _, info = nrrd.read(nrrd_file)
    # print(info['Segmentation_ReferenceImageExtentOffset'])
    offset = info['Segmentation_ReferenceImageExtentOffset']
    offset = offset.split(' ')
    
    # print(offset)
    offset = [int(offset[2]), int(offset[1]), int(offset[0])]
    
    print(offset)
    # print(arr_ref.shape)
    # print(arr_nrrd.shape)
    
    offset_last = [arr_ref.shape[0] - offset[0] - arr_nrrd.shape[0], 
                   arr_ref.shape[1] - offset[1] - arr_nrrd.shape[1],
                   arr_ref.shape[2] - offset[2] - arr_nrrd.shape[2]]
    print(offset_last)
    
    arr_save = np.pad(arr_nrrd, ((offset[0], offset_last[0]),
                                 (offset[1], offset_last[1]),
                                 (offset[2], offset_last[2])))
    
    print(arr_save.shape)
    img_save = sitk.GetImageFromArray(arr_save)
    img_save = gaomi.SetParams(img_save, params)
    
    sitk.WriteImage(img_save, save_file)
```
