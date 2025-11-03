# Principles of Robot Autonomy I HW3
Homework 3 code for Principles of Robot Autonomy I

## Problem 1: Chessboard Camera Extrinsics 
### Goal: compute position of a camera relative to a known marker arrangement (in this example, the corners of the checkerboard), or equivalently the pose of the chessboard in the camera's coordinate frame. 
We're given the camera instrinsics matrix K (hardcoded to describe how 3D rays map to image pixels) and the physical geometry of the board (the distance between each corner). 

In our first step of feature extraction, we used openCV's findChessboardcorners function to get the cross-junction pixel coordinates. 
Then, since the chessboard lies on a flat plane (Z=0), we can represent its 3D world points as 2D coordinates and use a homography H to map these world coordinates into 2D image coordinates. H captures how the plane projects onto the image. 
```
How we do that: compute H by forming a linear system and solving for its nullspace (the set of vectors that map to zero). In practice, this is done using Singular Value Decomposition (SVD) — np.linalg.svd(M) — and taking the last column of V.T as the flattened form of H. (In short, the nullspace gives the one direction in which the linear system balances perfectly — i.e., the coefficients that best satisfy all point correspondences.)
```
Then, to go from the mapping to the pose, we remove the camera's internal effects by taking inverse K and solving for rotation and translation, which describe how to transform points from the chessboard's coordinate system into camera's coordinate system. 
```
P_camera = R P_board + t
```



## Dependencies
Python package dependencies for this assignment are listed in the `requirements.txt` file and can be automatically installed by
```
pip install -r requirements.txt
```
Please note the additional dependence on opencv for this assignment.