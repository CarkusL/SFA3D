# Super Fast and Accurate 3D Object Detection based on 3D LiDAR Point Clouds

[![python-image]][python-url]
[![pytorch-image]][pytorch-url]

---

## Features
- [x] Super fast and accurate 3D object detection based on LiDAR
- [x] Fast training, fast inference
- [x] An Anchor-free approach
- [x] No Non-Max-Suppression
- [x] Support [distributed data parallel training](https://github.com/pytorch/examples/tree/master/distributed/ddp)
- [x] Release pre-trained models 

**The technical details are described [here](./Technical_details.md)**

**Update 2020.09.06**: Add `ROS` source code. The great work has been done by [@AhmedARadwan](https://github.com/AhmedARadwan). 
The implementation is [here](https://github.com/maudzung/SFA3D/tree/ea0222c1b35489dc35d8452c989c4b014e20e0da)

## Demonstration (on a single GTX 1080Ti)

[![demo](http://img.youtube.com/vi/FI8mJIXkgX4/0.jpg)](http://www.youtube.com/watch?v=FI8mJIXkgX4)


**[Youtube link](https://youtu.be/FI8mJIXkgX4)**

## 2. Getting Started
### 2.1. Requirement

The instructions for setting up a virtual environment is [here](https://github.com/maudzung/virtual_environment_python3).

```shell script
git clone https://github.com/maudzung/SFA3D.git SFA3D
cd SFA3D/
pip install -r requirements.txt
```

### 2.2. Data Preparation
Download the 3D KITTI detection dataset from [here](http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d).

The downloaded data includes:

- Velodyne point clouds _**(29 GB)**_
- Training labels of object data set _**(5 MB)**_
- Camera calibration matrices of object data set _**(16 MB)**_
- **Left color images** of object data set _**(12 GB)**_ (For visualization purpose only)


Please make sure that you construct the source code & dataset directories structure as below.

### 2.3. How to run

#### 2.3.1. Visualize the dataset 

To visualize 3D point clouds with 3D boxes, let's execute:

```shell script
cd sfa/data_process/
python kitti_dataset.py
```


#### 2.3.2. Inference

The pre-trained model was pushed to this repo.

```
python test.py --gpu_idx 0 --peak_thresh 0.2
```

#### 2.3.3. Making demonstration

```
python demo_2_sides.py --gpu_idx 0 --peak_thresh 0.2
```

The data for the demonstration will be automatically downloaded by executing the above command.


#### 2.3.4. Training

##### 2.3.4.1. Single machine, single gpu

```shell script
python train.py --gpu_idx 0
```

##### 2.3.4.2. Distributed Data Parallel Training
- **Single machine (node), multiple GPUs**

```
python train.py --multiprocessing-distributed --world-size 1 --rank 0 --batch_size 64 --num_workers 8
```

- **Two machines (two nodes), multiple GPUs**

   - _**First machine**_
    ```
    python train.py --dist-url 'tcp://IP_OF_NODE1:FREEPORT' --multiprocessing-distributed --world-size 2 --rank 0 --batch_size 64 --num_workers 8
    ```

   - _**Second machine**_
    ```
    python train.py --dist-url 'tcp://IP_OF_NODE2:FREEPORT' --multiprocessing-distributed --world-size 2 --rank 1 --batch_size 64 --num_workers 8
    ```
#### 2.3.5 Evaluation
- **Do Evaluation on kitti val dataset**
```
python  train.py --evaluate  --gpu_idx 0 --pretrained_path=../checkpoints/fpn_resnet_18/fpn_resnet_18_epoch_300.pth
```
- **Evaluation result**
```
Car AP(Average Precision)@0.70, 0.70, 0.70:
bbox AP:96.57, 89.17, 89.41
bev  AP:97.52, 89.62, 89.78
3d   AP:88.09, 87.86, 88.09
aos  AP:60.28, 55.63, 55.04
Car AP(Average Precision)@0.70, 0.50, 0.50:
bbox AP:96.57, 89.17, 89.41
bev  AP:98.03, 89.94, 90.09
3d   AP:98.01, 89.94, 90.09
aos  AP:60.28, 55.63, 55.04
Pedestrian AP(Average Precision)@0.50, 0.50, 0.50:
bbox AP:65.38, 64.95, 63.96
bev  AP:68.14, 69.36, 65.41
3d   AP:66.44, 62.36, 62.77
aos  AP:32.13, 30.95, 30.21
Pedestrian AP(Average Precision)@0.50, 0.25, 0.25:
bbox AP:65.38, 64.95, 63.96
bev  AP:88.43, 88.61, 88.59
3d   AP:88.29, 88.45, 88.48
aos  AP:32.13, 30.95, 30.21
Cyclist AP(Average Precision)@0.50, 0.50, 0.50:
bbox AP:89.62, 87.64, 87.70
bev  AP:82.11, 75.41, 75.64
3d   AP:80.09, 74.31, 74.45
aos  AP:54.37, 52.01, 51.44
Cyclist AP(Average Precision)@0.50, 0.25, 0.25:
bbox AP:89.62, 87.64, 87.70
bev  AP:96.04, 88.67, 88.78
3d   AP:96.04, 88.67, 88.78
aos  AP:54.37, 52.01, 51.44
```
The original evaluation code is from [here](https://github.com/traveller59/kitti-object-eval-python).

#### Tensorboard

- To track the training progress, go to the `logs/` folder and 

```shell script
cd logs/<saved_fn>/tensorboard/
tensorboard --logdir=./
```

- Then go to [http://localhost:6006/](http://localhost:6006/)


## Contact

If you think this work is useful, please give me a star! <br>
If you find any errors or have any suggestions, please contact me (**Email:** `nguyenmaudung93.kstn@gmail.com`). <br>
Thank you!


## Citation

```bash
@misc{Super-Fast-Accurate-3D-Object-Detection-PyTorch,
  author =       {Nguyen Mau Dung},
  title =        {{Super-Fast-Accurate-3D-Object-Detection-PyTorch}},
  howpublished = {\url{https://github.com/maudzung/Super-Fast-Accurate-3D-Object-Detection}},
  year =         {2020}
}
```

## References

[1] CenterNet: [Objects as Points paper](https://arxiv.org/abs/1904.07850), [PyTorch Implementation](https://github.com/xingyizhou/CenterNet) <br>
[2] RTM3D: [PyTorch Implementation](https://github.com/maudzung/RTM3D) <br>
[3] Libra_R-CNN: [PyTorch Implementation](https://github.com/OceanPang/Libra_R-CNN)

_The YOLO-based models with the same BEV maps input:_ <br>
[4] Complex-YOLO: [v4](https://github.com/maudzung/Complex-YOLOv4-Pytorch), [v3](https://github.com/ghimiredhikura/Complex-YOLOv3), [v2](https://github.com/AI-liu/Complex-YOLO)

*3D LiDAR Point pre-processing:* <br>
[5] VoxelNet: [PyTorch Implementation](https://github.com/skyhehe123/VoxelNet-pytorch)

## Folder structure

```
${ROOT}
└── checkpoints/
    ├── fpn_resnet_18/    
        ├── fpn_resnet_18_epoch_300.pth
└── dataset/    
    └── kitti/
        ├──ImageSets/
        │   ├── test.txt
        │   ├── train.txt
        │   └── val.txt
        ├── training/
        │   ├── image_2/ (left color camera)
        │   ├── calib/
        │   ├── label_2/
        │   └── velodyne/
        └── testing/  
        │   ├── image_2/ (left color camera)
        │   ├── calib/
        │   └── velodyne/
        └── classes_names.txt
└── sfa/
    ├── config/
    │   ├── train_config.py
    │   └── kitti_config.py
    ├── data_process/
    │   ├── kitti_dataloader.py
    │   ├── kitti_dataset.py
    │   └── kitti_data_utils.py
    ├── models/
    │   ├── fpn_resnet.py
    │   ├── resnet.py
    │   └── model_utils.py
    └── utils/
    │   ├── demo_utils.py
    │   ├── evaluation_utils.py
    │   ├── logger.py
    │   ├── misc.py
    │   ├── torch_utils.py
    │   ├── train_utils.py
    │   └── visualization_utils.py
    ├── demo_2_sides.py
    ├── demo_front.py
    ├── test.py
    └── train.py
├── README.md 
└── requirements.txt
```



[python-image]: https://img.shields.io/badge/Python-3.6-ff69b4.svg
[python-url]: https://www.python.org/
[pytorch-image]: https://img.shields.io/badge/PyTorch-1.5-2BAF2B.svg
[pytorch-url]: https://pytorch.org/
