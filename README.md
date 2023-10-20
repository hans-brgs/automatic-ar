# automatic-ar

**automatic-ar** is a specialized computer vision project focused on the calibration of extrinsic parameters of multiple cameras. Leveraging a multi-camera video sequence, the system employs a unique 3D calibration rig composed of ARUCO markers on each face. Through this calibration, the project successfully determines the relative positions of the cameras, either in reference to a primary camera or in relation to the calibration rig itself. 

## Table of Contents

1. [Reference Implementation](#reference-implementation)
2. [Prerequisites](#prerequisites)
3. [Installing Dependencies using vcpkg](#installing-dependencies-using-vcpkg)
4. [Configuring and Building the Project](#configuring-and-building-the-project)
5. [Sample data & Usage](#sample-data--usage)
6. [Visualization](#visualization)
7. [Dataset format](#dataset-format)

## Reference Implementation:

This code is the reference implementation of the following paper:
[H. Sarmadi, R. Muñoz-Salinas, M. A. Berbís and R. Medina-Carnicer, "Simultaneous Multi-View Camera Pose Estimation and Object Tracking With Squared Planar Markers," in IEEE Access, vol. 7, pp. 22927-22940, 2019.](https://ieeexplore.ieee.org/document/8631108)

The original code can be found on GitHub at this [link](https://github.com/HSarham/automatic-ar/tree/ae495352cc870464c08c263d6dd10e15cd365469).

This version has been updated to work with the **latest dependencies as of October 2023** and has been adapted for the **Windows system**.

## Prerequisites

### System
This project has been set up and tested on **Windows 11**.

### Software
- Visual Studio Build Tools 2022  with compiler MSVC 19.36.32537.0 (specifically the "Release - amd64" version)
- Visual Studio Code for code editing and building the project.

### Dependencies
The project has the following library dependencies:
- OpenCV (version 4.8.1)
- VTK (version 9.2.0)
- Point Cloud Library (PCL, version 1.13.1 ) 
- Qt (version 6.5.0)
- Eigen3 (version 3.4.0)

Additionally, the project includes the following packages:
- [ArUcO](https://sourceforge.net/projects/aruco/) (version 3.1.15)
- [SGL](https://github.com/rmsalinas/sgl)

## Installing Dependencies using vcpkg

We recommend using `vcpkg` for managing certain project dependencies, specifically for `vtk`, `pcl`, and `eigen3`.

### 1. Clone and install `vcpkg`:
```bash
git clone https://github.com/Microsoft/vcpkg.git
cd vcpkg
./bootstrap-vcpkg.bat
```

### 2. Set up vcpkg integration:

```bash
`./vcpkg integrate install`
```

This command allows Visual Studio to automatically recognize libraries installed by `vcpkg`.

### 3. Install the required packages:
```bash
./vcpkg install vtk pcl eigen3 [any other packages if necessary]
```
> **Note**: While libraries like `opencv` and `QT` can potentially be installed using `vcpkg`, in this project setup, we have installed them manually. If you prefer a unified dependency management solution, you might want to explore installing these libraries through `vcpkg` as well.

### 4. For CMake Projects in Visual Studio:
Add the following to your project's CMake settings:

```bash
-DCMAKE_TOOLCHAIN_FILE=[path to your vcpkg]/scripts/buildsystems/vcpkg.cmake
```
Replace [path to your vcpkg] with the actual path to your vcpkg installation.

## Configuring and Building the Project

1. Configure the project with CMake:
```bash
mkdir build
cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=[path to your vcpkg]/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Release
```

2. Open the project `build\automatic-ar.sln` in Visual Studio.

4. Build the project, ensuring you're in "Release" mode.


## Sample data
You can download sample data sets from [here](https://sarmadi.me/public_files/automatic-ar).

## Usage

### Processing a sequence

1. Unzip the desired data set.
```shell
unzip box.zip
```
2. Do the marker detection by giving the path of the data set to `detect_markers`:
```shell
detect_markers box
```
After unzipping you will find a file name `aruco.detections` in the data folder path.

3. Apply the algorithm by providing the data folder path and the physical size of the marker to `find_solution`:
```shell
find_solution box 0.05
```
Here `0.05` is the input size of each marker in meters.

#### Outputs

The output of `find_solutions` includes several files. Files with the name format 'initial*' store information of the solution after initialization and before optimization. The files with their names 'final*' store information resulted after doing the optimization.

There are also `*.yaml` files that have the relative transformation between cameras, relative transformation between markers, and the relative transformation from the reference marker to the reference camera for each frame stored in the [YAML](http://yaml.org/) format. You can use OpenCV's FileStorage class to read the data from the stored `*.yaml` files.

If you compile with PCL library you will also find `*.cameras.pcd` and `*.markers.pcd` which are point cloud based visualizations for cameras' configuration and markers' configuration respectively. These files could be viewed using `pcl_viewer` from the PCL library.

### Tracking a sequence

For tracking the marker detection is done live so you do not need to do the detection in separate step. However, you need a processed sequence with the same camera and object configuration as your tracking sequence. Let's assume that we want to do tracking in the `box_tracking` sequence using the already processed `box` sequence (you can find both of them in the sample data sets). You just run:
```shell
track box_tracking box/final.solution
```

## Visualization
If you compile with the PCL library you will have automatic 3D visualization when you run `find_solution`. However, if not, you can still visualize the solution using the overlay app:
```shell
overlay pentagonal
```
You can also save overlayed visualization in a video in the dataset folder by using an extra option:
```shell
overlay pentagonal -save-video
```
Also the `track` app has a live overlay visualization in the runtime that does not need PCL.

## Dataset format

Each dataset is defined within a folder. In that folder each camera has a directory with its index as its name. In the cameras folders there is a "calib.xml" file specifying the camera matrix, distortion coefficients and the image size in OpenCV calibration output style for that camera.
