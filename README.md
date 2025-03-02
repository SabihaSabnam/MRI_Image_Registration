# MRI Image Registration: Aligning Scans from Different Machines

## Project Goal
Medical images are often captured using different MRI machines (like 3T and 7T scanners), which can cause variations in size, resolution, and alignment. Our goal is to **align and compare MRI scans** from two different machines to analyze differences accurately.

---

## Project Workflow

### Step 1: Load the MRI Scans  
We have two MRI scans:  
- `vol7T` → High-resolution MRI scan (from a **7 Tesla scanner**)  
- `vol3T` → Lower-resolution MRI scan (from a **3 Tesla scanner**)
  
### Step 2: View the MRI Scans
Before proceeding with any further steps, it's important to visualize both scans and understand their differences in terms of size and orientation.
code : 
orthosliceViewer(vol3T); % View 3T MRI scan
orthosliceViewer(vol7T); % View 7T MRI scan

### Step 3:Resample (Resize) the 3T Image
The 3T scan needs to be resized to match the geometry of the 7T scan.
code :
resample3T = resample(vol3T, vol7T.VolumeGeometry); % Resize 3T MRI to match 7T

### Step 4: Extract a Specific Slice
For simplicity, we extract a middle slice from each scan to compare.
code :
img1 = extractSlice(resample3T, 64, "transverse"); % Extract slice from 3T scan
img2 = extractSlice(vol7T, 64, "transverse");     % Extract slice from 7T scan
imshowpair(img1, img2, 'montage');  % Display side by side for easy comparison

### Step 5: Configure the Image Registration Algorithm
Here we configure the registration setup to handle different image modalities.
code : 
[optimizer, metric] = imregconfig('multimodal'); % Set up for multimodal images

### Step 6: Register the 7T MRI Scan
We align the 7T scan with the resampled 3T scan to make them match.
code :
newVoxels = imregister(vol7T.Voxels, resample3T.Voxels, 'similarity', optimizer, metric);

### Step 7: Update the 7T Scan with the Registered Data
After registration, we update the 7T MRI scan with the new, aligned voxel data.

code :
registered7T = vol7T; % Copy original 7T scan
registered7T.Voxels = newVoxels; % Replace voxel data with the aligned version

### Step 8: Visualize the Registered Image
Finally, visualize the registered 7T scan to ensure it aligns correctly with the 3T scan.
code :
orthosliceViewer(registered7T); % View the aligned 7T scan

### MATLAB Code:
```matlab
vol7T = medicalVolume("T1_7T_2020"); % Load high-resolution MRI
vol3T = medicalVolume("T1_3T_2018"); % Load low-resolution MRI

This step helps us see the scans side by side, which will allow us to compare their alignment and identify differences before applying any transformations.
###Step 3: Resample (Resize) the 3T Image
The 3T scan is typically smaller and has a different resolution. To align the scans effectively, we resize the 3T scan to match the resolution and dimensions of the 7T scan.
orthosliceViewer(vol3T); % View 3T MRI scan
orthosliceViewer(vol7T); % View 7T MRI scan
resample3T = resample(vol3T, vol7T.VolumeGeometry); % Resize 3T MRI to match 7T
img1 = extractSlice(resample3T, 64, "transverse"); % Extract slice from resampled 3T scan
img2 = extractSlice(vol7T, 64, "transverse");     % Extract slice from 7T scan
imshowpair(img1, img2, 'montage');  % Show both side by side
[optimizer, metric] = imregconfig('multimodal'); % Set up for different image types
newVoxels = imregister(vol7T.Voxels, resample3T.Voxels, 'similarity', optimizer, metric);
registered7T = vol7T; % Copy original 7T scan
registered7T.Voxels = newVoxels; % Replace voxels with registered (aligned) version
