# Principles of Robot Autonomy I HW3
Homework 3 code for Principles of Robot Autonomy I

## Problem 1: Chessboard Camera Extrinsics 

### Goal
Compute the position and orientation (pose) of a camera relative to a known marker arrangement (in this example, the corners of the checkerboard), or equivalently the pose of the chessboard in the camera's coordinate frame. 

We're given the camera intrinsics matrix K (hardcoded to describe how 3D rays map to image pixels) and the physical geometry of the board (the distance between each corner, `SQUARE_SIZE = 0.0205` meters).

### What We Generate

**Problem 1.1: Corner Detection**
- Uses OpenCV's `cv2.findChessboardCorners()` to detect 63 corner points (9×7 grid) in the checkerboard image
- Extracts pixel coordinates (u, v) for each detected corner
- Visualizes the detected corners on the image
![Coordinates of checkerboard corners extracted and plotted onto image](Problem1_1.png)

**Problem 1.2: Homography and Camera Pose Estimation**
- **Homography H**: Computes a 3×3 homography matrix H that maps world coordinates on the checkerboard plane (Z=0) to image pixel coordinates. Since the chessboard lies on a flat plane, we can represent 3D world points as 2D coordinates and use homography to map these world coordinates into 2D image coordinates.
- **Camera Pose (R, t)**: Decomposes the homography to extract the camera's rotation matrix R (3×3) and translation vector t (3×1) that describe how to transform points from the chessboard's world coordinate system into the camera's coordinate system:
```
P_camera = R @ P_world + t
```

The decomposition process:
1. Removes camera internal effects by computing `K_inv @ H`
2. Normalizes to extract rotation vectors r0, r1 (first two columns)
3. Computes r2 as the cross product of r0 and r1 to ensure orthogonality
4. Extracts translation t from the third column

**Problem 1.3: Validation and Projection Function**
- **`transform_world_to_camera()` function**: Implements the complete transformation pipeline:
  - Transforms 3D world coordinates to camera coordinates using R and t
  - Projects to image plane using camera intrinsics K
  - Normalizes homogeneous coordinates to get pixel coordinates (u, v)
- **Validation**: Projects the known world coordinates of the checkerboard corners back onto the image and compares them with the originally detected corners
- Visualizes both detected corners (red) and projected corners (blue/green) to verify calibration accuracy
![Validation plot overlaying detected corners (red) and projected corners (blue) to verify camera calibration accuracy](Problem1_3.png)

### What It Shows/Tells Us

The key validation is that when we project the world coordinates of the checkerboard corners using our computed camera pose (R, t) and intrinsics (K), the projected points should align with the originally detected corners. This demonstrates:

1. **Calibration Accuracy**: If the projected points align with detected corners, it confirms that:
   - The homography H was correctly computed
   - The camera pose (R, t) was correctly extracted from H
   - The transformation pipeline works correctly

2. **Camera Understanding**: The computed R and t tell us:
   - **R**: How the camera is oriented relative to the checkerboard (rotation)
   - **t**: Where the camera is positioned relative to the checkerboard (translation)
   - Together, they fully describe the camera's pose in 3D space

3. **Practical Applications**: This calibration process enables:
   - Augmented reality (overlaying virtual objects on real-world markers)
   - Robot navigation (knowing robot position relative to landmarks)
   - 3D reconstruction from 2D images
   - Projecting arbitrary 3D points onto the image plane (as shown in the "Project Anything!" section)





## Dependencies
Python package dependencies for this assignment are listed in the `requirements.txt` file and can be automatically installed by
```
pip install -r requirements.txt
```
Please note the additional dependence on opencv for this assignment.