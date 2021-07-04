# Granular-Material-Packing-Analysis

## Description 

Repository containing code for a discrete Morse theory based method to analyse 3-D CT scans of granular material packings. 

(e-mail: karran13 [AT] gmail.com)

---

## Requirements

To compile and execute this code, you will need Cmake > 3.1.8, Boost > 1.58, OpenCL 1.1 (implicitly available along with CUDA > 10.1), OpenMP and python with the following packages: 

1. vtk
2. scikit-image
3. SimpleITK
4. ITK
5. numpy/scipy

---

## Instructions

### Prerequisite installation
* Install Cmake, Boost using your software package manager
* If your gpu is Cuda compatible, follow the detailed installation instructions [here](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html). Or install opencl from your software package manager. Check if opencl is working with clinfo. If you get "number of platform 0", it means opencl is not working correctly. Fix the installation of opencl.
* OpenMP should be normally installed if not use your package manager to install openmp libraries.
* Install python packages, specifically numpy as it will be used for building the pyms3d module.
* Install cmake-gui for building pyms3d.

### Installing pyms3d

The MS-Complex computation is done using pyms3d. The installation instruction of pyms3d are as follows:

* Clone the repository

```sh
git clone https://github.com/karran13/Granular-Material-Packing-Analysis 
```

* Navigate to mscomplex-3d, create build and install directories

```sh
cd Granular-Material-Packing-Analysis/ms\ complex\ software/mscomplex-3d/
mkdir build install
cd build
```

* Build pyms3d using cmake-gui

```sh
cmake-gui
```

1. In the cmake-gui, provide the path for
 source code ---> absolute path to mscomplex-3d 
 build directory ---> absolute path to mscomplex-3d/build

2. Press configure to see the default values cmake-gui picked up
3. Use advance option and change the default values to something similar to the following

![cmake-gui options](./Extras/cmake-gui.png)

4. Important:
 Check BUILD_PYMS3D, BUILD_TOOL. Press configure to update and display new values.
 Provide path for MSCOMPLEX3D_INSTALL_DIR, opencl cuda paths, libboost paths, python3 paths.
 The path to PYTHON_SITE_PACKAGE_DIR should be "Python Routines" in the repository folder (in Granular-Material-Packing-Analysis).
 Click generate and close cmake-gui.

* From the build directory, execute the following commands:

```sh
make -j8
make -j8 install
```

where 8 is number of processes used to build the pyms3d.

* If everything went alright, you should see 'pyms3d.so' in 'Python Routines' directory.
* To check whether your installation works, import pyms3d in ipython or jupyter-lab. If import is successful, your installation works.

### Description: Running the pipeline

The python scripts to run the pipeline can be found in the Python Routines folder. You should have all the python packages specified above to run the pipeline successfully. The scripts and their input formats are described below:

#### bd_extraction.py 

This script takes as input the raw CT image and outputs the distance field based on the extracted boundary. It takes as input a MetaImage file (.mhd + .raw). To run the script, execute the following command in the terminal: 

`python bd_extraction.py [Path to .mhd file]` 

This will store the computed distance field in MetaImage format (.mhd + .raw) in the 'Outputs' folder in the repository.

#### downsample_skimage.py 

This script is a useful downsampling routine if the original CT image doesn't fit in memory for the MS-Complex computation. It takes as input the raw CT image and downsamples it based on the given factor. It takes as input a MetaImage file (.mhd + .raw). To run the script, execute the following command in the terminal: 

`python downsample_skimage.py [Path to .mhd file] [factor]` 

This will store the downsampled CT image in MetaImage format (.mhd + .raw) in the 'Outputs' folder in the repository.

#### main.py 

This script is the main interface to run the Morse-Smale Complex computation and extract relevant geometric and topological structures for analysis. It takes the computed distance field as input and returns the structures selected from the in-program menu. The program allows for the visualization of the persistence curve, computation/simplification of the MS-Complex and extraction of the segmentation, connectivity network and contact regions in the granular material packing.  

`python main.py [Path to .raw file of distance field] [dimension 1] [dimension 2] [dimension 3]` 

Running this will store the selected structures in '.vtp' format (accessible through VTK/ParaView) in the 'Outputs' folder in the repository.

Few notes here: Use the 'knee' in the persistence curve to select a simplification threshold. Secondly, Make sure the dimension order is as per the mhd file description of the distance field.

---

## Tutorial: How to run the pipeline 

We provide a test data set of a spherical bead packing to get the user started off with an easy-to-run example. This tutorial will illustrate the data and the expected outputs from the pipeline, all while providing a step-by-step description on how to precisely run the pipeline. 

### Dataset

The test dataset, located in the 'Test Data' folder in the repository, is an mhd file of the CT scan of a spherical bead packing. When visualized, the scan looks as follows:

![Data Volume Render](/Tutorial%20Images/raw%20data%20vol%20render.png)

### Boundary extraction

First, we run the boundary extraction script as described in the previous section. Say, we are in the 'Python Routines' folder in the repository, we run the following command: 

![Boundary extraction terminal](/Tutorial%20Images/bd_extraction_terminal.png)

This writes an output distance field file called 'chamf_distance_Steel_Deposition_181_176_251_downsampled.mhd/raw'. When visualized in ParaView, the 0-isocontour looks as follows:

![Boundary Surface](/Tutorial%20Images/bd_surface.png)

### Geometric and Topological Analysis

Next, we go for the MS-Complex computation and simplification. To do this we run main.py, as described earlier:

![Main Terminal Command](/Tutorial%20Images/main_terminal_run.png)

We see the following options:

![Terminal Options](/Tutorial%20Images/terminal_options.png)

We now press 1 to show the persistence curve. We then see the following window pop up:

![Persistence Curve](/Tutorial%20Images/pers_curve.png)

We identify the precise persistence value for the knee, by hovering the cursor over the graph at the location of the knee and seeing the displayed x-co-ordinate in the persistence curve window. In this case, it turns out to be 0.2.

We then close the window and press 2 at the terminal. When the prompt asks for the persistence threshold, we enter 0.2 as identified. We then use the other options to store the outputs for the segmentation and connectivity network.

These files, along with information about the grain centres, contact regions and points will be stored in the outputs folder. When visualized, the segmentation, network, contacts and grain centres will look as follows:

![Segmentation](/Tutorial%20Images/segmentation.png)

![Connectivity Network](/Tutorial%20Images/contact_network.png)

---




