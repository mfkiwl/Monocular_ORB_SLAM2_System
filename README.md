# ORB-SLAM2

This is the GitHub repository of ORB-SLAM2 used for course COMP0130 at UCL. For original release of ORBSLAM2, please go to:
https://github.com/raulmur/ORB_SLAM2
# 1. License

ORB-SLAM2 is released under a [GPLv3 license](https://github.com/raulmur/ORB_SLAM2/blob/master/License-gpl.txt). For a list of all code/library dependencies (and associated licenses), please see [Dependencies.md](https://github.com/raulmur/ORB_SLAM2/blob/master/Dependencies.md).

For a closed-source version of ORB-SLAM2 for commercial purposes, please contact the authors: orbslam (at) unizar (dot) es.

If you use ORB-SLAM2 (Monocular) in an academic work, please cite:

    @article{murTRO2015,
      title={{ORB-SLAM}: a Versatile and Accurate Monocular {SLAM} System},
      author={Mur-Artal, Ra\'ul, Montiel, J. M. M. and Tard\'os, Juan D.},
      journal={IEEE Transactions on Robotics},
      volume={31},
      number={5},
      pages={1147--1163},
      doi = {10.1109/TRO.2015.2463671},
      year={2015}
     }

if you use ORB-SLAM2 (Stereo or RGB-D) in an academic work, please cite:

    @article{murORB2,
      title={{ORB-SLAM2}: an Open-Source {SLAM} System for Monocular, Stereo and {RGB-D} Cameras},
      author={Mur-Artal, Ra\'ul and Tard\'os, Juan D.},
      journal={IEEE Transactions on Robotics},
      volume={33},
      number={5},
      pages={1255--1262},
      doi = {10.1109/TRO.2017.2705103},
      year={2017}
     }

# 2. Prerequisites
We have tested the library in **Ubuntu 18.04**, but it should be easy to compile in other platforms. A powerful computer (e.g. i7) will ensure real-time performance and provide more stable and accurate results.

## C++11 or C++0x Compiler
We use the new thread and chrono functionalities of C++11. And please make sure you use g++-7 or lower version.

## Pangolin
We use [Pangolin](https://github.com/stevenlovegrove/Pangolin) for visualization and user interface. Dowload and install instructions can be found at: https://github.com/stevenlovegrove/Pangolin.

## OpenCV
We use [OpenCV](http://opencv.org) to manipulate images and features. Dowload and install instructions can be found at: http://opencv.org. **Required at leat 2.4.3. Tested with OpenCV 2.4.11 and OpenCV 3.2**.

## Eigen3
Required by g2o (see below). Download and install instructions can be found at: http://eigen.tuxfamily.org. **Required at least 3.1.0**.

## DBoW2 and g2o (Included in Thirdparty folder)
We use modified versions of the [DBoW2](https://github.com/dorian3d/DBoW2) library to perform place recognition and [g2o](https://github.com/RainerKuemmerle/g2o) library to perform non-linear optimizations. Both modified libraries (which are BSD) are included in the *Thirdparty* folder.

# 3. Building ORB-SLAM2 library and examples

Clone the repository:
```
git clone https://github.com/JingwenWang95/ORB_SLAM2.git
```

We provide an tutorial document on how to build all the dependencies and ORB-SLAM2: https://docs.google.com/document/d/1HXsPRtphYfZMvATXM8IZRQ9CnC_nSBfR5bW46QUYAjE/edit?usp=sharing 

It includes a brief introduction to the build process of a C++ project and how to use `cmake` and `make` to build C++ project on Ubuntu.
It's also got a short tutorial on how to install and keep multiple versions of a library, which will be useful for handling multiple OpenCV installations. Please make sure you read it carefully.

We also provide a script `install_orbslam2.sh` to build all the dependencies and ORB-SLAM2. You can simply execute:
```
cd ORB_SLAM2
chmod +x install_orbslam2.sh 
./install_orbslam2.sh
```
This script will download and install the first 3 dependencies (Pangolin, Eigen and OpenCV), and build the last two (DBoW and g2o) for you. Pangolin and Eigen are installed to the default location (`/usr/local`), whereas OpenCV will be installed under Thirdparty/. You can also install Pangolin and Eigen to different locations following the same procedure specified in the tutorial document. 

After successfully building ORB-SLAM2, you will have **libORB_SLAM2.so**  at *lib* folder and the executables **mono_tum**, **mono_kitti**, **rgbd_tum**, **stereo_kitti**, **mono_euroc** and **stereo_euroc** in *Examples* folder.

# 4. KITTI Dataset  
You only need to run ORB-SLAM on the KITTI sequences that we provided for you on moodle. If you are interested in running ORB-SLAM2 on other sequences, please refer to the original release.

1. Download the dataset (grayscale images) from http://www.cvlibs.net/datasets/kitti/eval_odometry.php 

2. Execute the following command. Change `KITTIX.yaml`by KITTI00-02.yaml, KITTI03.yaml or KITTI04-12.yaml for sequence 0 to 2, 3, and 4 to 12 respectively. Change `PATH_TO_DATASET_FOLDER` to the uncompressed dataset folder. Change `SEQUENCE_NUMBER` to 00, 01, 02,.., 11. 
```
./Examples/Monocular/mono_kitti Vocabulary/ORBvoc.txt Examples/Monocular/KITTIX.yaml PATH_TO_DATASET_FOLDER/dataset/sequences/SEQUENCE_NUMBER PATH_TO_SAVE_TRAJECTORY
```

# 5. Evaluation Tools
We recommend you to use EVO tool https://github.com/MichaelGrupp/evo for evaluating the performance of ORB-SLAM2. You can simply install it via `pip`. For more detailed instructions please refer to their GitHub page.

As you are running in monocular mode, the camera trajectory will suffer from scale ambiguity and scale drift. So you will need to do 7-DoF alignment between the estimated trajectory and the groundtruth. This can be done by specifying the option `--align` and `--correct_scale`, or simply `-as`. 

EVO can only do the scale alignment on sequences with timestamps, but KITTI poses file doesn't have timestamps. To solve this issue, we will save the camera trajectories in TUM format. To make it consistent with the groundtruth data format, you need to convert the groundtruth poses file to TUM format. We have provided a python script for you to do this under Evaluation/

```
python kitti_to_tum.py <groundtruth-file> <times-file> <output-file>
```