# Caffe++☕️
Assemble new features to enhance Caffe.  

The Caffe revision: ```99bd99795dcdf0b1d3086a8d67ab1782a8a08383```(commit [SHA](https://github.com/BVLC/caffe/tree/99bd99795dcdf0b1d3086a8d67ab1782a8a08383))

# New Layers
> common: you can use directly   
> special: need special net structure  
> experienced: need to adjust parameters or try some times
### common
- [CenterLoss](https://github.com/ydwen/caffe-face)
```prototxt
layer {
  name: "center_loss"
  type: "CenterLoss"
  bottom: "fc5"
  bottom: "label"
  top: "center_loss"
  param {
    lr_mult: 1
    decay_mult: 2 
  }
  center_loss_param {
    num_output: 10572
    center_filler {
      type: "xavier"
    }
  }
  loss_weight: 0.008
}
```
- [Upsample](https://github.com/TimoSaemann/caffe-segnet-cudnn5)
```
layer {
  name: "upsample5"
  type: "Upsample"
  bottom: "pool5"
  top: "pool5_D"
  bottom: "pool5_mask"
  upsample_param {
    scale: 2
    upsample_w: 30
    upsample_h: 23
  }
}
```

- [ConvolutionDepthwise](https://github.com/yonghenglh6/DepthwiseConvolution)
```prototxt
layer {
  name: "branch1_1_conv1"
  type: "ConvolutionDepthwise"
  bottom: "pool1"
  top: "branch1_1_conv1"
  convolution_param {
    num_output: 24
    kernel_size: 3
    stride: 2
    pad: 1
    bias_term: false
    weight_filler {
      type: "msra"
    }
  }
}
```

- [ShuffleChannel](https://github.com/farmingyard/ShuffleNet)
```prototxt
layer {
  name: "shuffle1"
  type: "ShuffleChannel"
  bottom: "concat1"
  top: "shuffle1"
  shuffle_channel_param {
    group: 2
  }
}
```
- [WeightedSoftmaxWithLoss](https://github.com/silver-rush/Weighted_Softmax_Loss)
```prototxt
layer {
  name: "loss"
  type: "WeightedSoftmaxWithLoss"
  bottom: "fc_end"
  bottom: "label"
  top: "loss"
  softmax_param {
    pos_cid: 1
    pos_mult: 2.0
  }
}
```
### special
- [Axpy](https://github.com/hujie-frank/SENet) (SENet)
```prototxt
layer {
  name: "conv2_1"
  type: "Axpy"
  bottom: "conv2_1_1x1_up"
  bottom: "conv2_1_1x1_increase"
  bottom: "conv2_1_1x1_proj"
  top: "conv2_1"
}
```
### experienced
- [MarginInnerProduct](https://github.com/KaleidoZhouYN/Angular-Triplet-Loss) (Angular-Triplet-Loss based A-Softmax-Loss)
```prototxt
layer {
  name: "fc6"
  type: "MarginInnerProduct"
  bottom: "fc5"
  bottom: "label"
  top: "fc6"
  top: "lambda"
  param {
    lr_mult: 1
    decay_mult: 1
  }
  margin_inner_product_param {
    num_output: 10572
    type: SINGLE
    weight_filler {
      type: "xavier"
    }
    base: 0
    gamma: 0.12
    power: 1
    lambda_min: 3
    iteration: 0
    triplet: true
    semihard: true
  }
}
```
- [InterClass](https://github.com/wy1iu/sphereface-plus)
```prototxt
layer {
  name: "fc6"
  type: "MarginInnerProduct"
  bottom: "fc5"
  bottom: "label"
  top: "fc6"
  top: "lambda"
  param {
    name: "fc6_w"
    lr_mult: 1
    decay_mult: 1
  }
  margin_inner_product_param {
    num_output: 10572
    type: QUADRUPLE
    weight_filler {
      type: "xavier"
    }
    base: 1000
    gamma: 0.12
    power: 1
    lambda_min: 5
    iteration: 2000
  }
}
layer {
  name: "inter_class_dist_output"
  type: "InterClass"
  bottom: "fc5"
  bottom: "label"
  top: "maximize_inter_class_dist"
  param {
    name: "fc6_w"
    lr_mult: 1
    decay_mult: 1
  }
  inter_class_param {
    num_output: 10572
    type: AMONG
    iteration: 16000
    alpha_start_iter: 16001
    alpha_start_value: 10 #among
  }
}
layer {
  name: "softmax_loss"
  type: "SoftmaxWithLoss"
  bottom: "fc6"
  bottom: "label"
  top: "softmax_loss"
}
```

## Examples
New layer examples are [here](https://github.com/becauseofAI/caffe-plus-plus/tree/master/examples).

# New Functions
### Multi-Label for Multi-Task
- ImageData  

train.txt
```txt
image/00001.jpg 0 5 1
image/00002.jpg 0 9 -1
image/00003.jpg 1 9 2
image/00004.jpg 1 -1 0
image/00005.jpg 1 0 2
```
train.prototxt
```prototxt
layer {
  name: "data"
  type: "ImageData"
  top: "data"
  top: "label"
  include {
    phase: TRAIN
  }
  transform_param {
    scale: 0.00390625
    mirror: true
  }
  image_data_param {
    source: "/your/path/train.txt"  
    root_folder: "/your/image/data/path"  
    new_height: 224 
    new_width: 224  
    batch_size: 64 
    is_color: true  
    shuffle: true
    label_dim: 3
   }
}

layer {
  name: "slice"
  type: "Slice"
  bottom: "label"
  top: "label_1"
  top: "label_2"
  top: "label_3"
  slice_param {
    axis: 1
    slice_point:1
    slice_point:2
  }
}

layer {
  name: "fc7_1"
  type: "InnerProduct"
  bottom: "conv7_1"
  top: "fc7_1"
  param {
    lr_mult: 1
  }
  param {
    lr_mult: 2
  }
  inner_product_param {
    num_output: 5
    weight_filler {
      type: "xavier"
    }
    bias_filler {
      type: "constant"
    }
  }
} 

layer {
  name: "fc7_2"
  type: "InnerProduct"
  bottom: "conv7_2"
  top: "fc7_2"
  param {
    lr_mult: 1
  }
  param {
    lr_mult: 2
  }
  inner_product_param {
    num_output: 10
    weight_filler {
      type: "xavier"
    }
    bias_filler {
      type: "constant"
    }
  }
} 

layer {
  name: "fc7_3"
  type: "InnerProduct"
  bottom: "conv7_3"
  top: "fc7_3"
  param {
    lr_mult: 1
  }
  param {
    lr_mult: 2
  }
  inner_product_param {
    num_output: 3
    weight_filler {
      type: "xavier"
    }
    bias_filler {
      type: "constant"
    }
  }
} 

layer {
  name: "loss1"
  type: "SoftmaxWithLoss"
  bottom: "fc7_1"
  bottom: "label_1"
  loss_param {
    ignore_label: -1
  }
}

layer {
  name: "loss2"
  type: "SoftmaxWithLoss"
  bottom: "fc7_2"
  bottom: "label_2"
  loss_param {
    ignore_label: -1
  }
}

layer {
  name: "loss3"
  type: "SoftmaxWithLoss"
  bottom: "fc7_3"
  bottom: "label_3"
  loss_param {
    ignore_label: -1
  }
}
```

# New Performance
- [pooling layer](https://github.com/hujie-frank/SENet)  

The implementation for global average pooling on GPU supported by cuDNN and BVLC/caffe is less efficient. In this regard, the author re-implement the operation which achieves significant acceleration.
```
layer {
  name: "conv2_1_global_pool"
  type: "Pooling"
  bottom: "conv2_1_1x1_increase"
  top: "conv2_1_global_pool"
  pooling_param {
    pool: AVE
    engine: CAFFE
    global_pooling: true
  }
}
```

---

# Caffe

[![Build Status](https://travis-ci.org/BVLC/caffe.svg?branch=master)](https://travis-ci.org/BVLC/caffe)
[![License](https://img.shields.io/badge/license-BSD-blue.svg)](LICENSE)

Caffe is a deep learning framework made with expression, speed, and modularity in mind.
It is developed by Berkeley AI Research ([BAIR](http://bair.berkeley.edu))/The Berkeley Vision and Learning Center (BVLC) and community contributors.

Check out the [project site](http://caffe.berkeleyvision.org) for all the details like

- [DIY Deep Learning for Vision with Caffe](https://docs.google.com/presentation/d/1UeKXVgRvvxg9OUdh_UiC5G71UMscNPlvArsWER41PsU/edit#slide=id.p)
- [Tutorial Documentation](http://caffe.berkeleyvision.org/tutorial/)
- [BAIR reference models](http://caffe.berkeleyvision.org/model_zoo.html) and the [community model zoo](https://github.com/BVLC/caffe/wiki/Model-Zoo)
- [Installation instructions](http://caffe.berkeleyvision.org/installation.html)

and step-by-step examples.

## Custom distributions

 - [Intel Caffe](https://github.com/BVLC/caffe/tree/intel) (Optimized for CPU and support for multi-node), in particular Xeon processors (HSW, BDW, SKX, Xeon Phi).
- [OpenCL Caffe](https://github.com/BVLC/caffe/tree/opencl) e.g. for AMD or Intel devices.
- [Windows Caffe](https://github.com/BVLC/caffe/tree/windows)

## Community

[![Join the chat at https://gitter.im/BVLC/caffe](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/BVLC/caffe?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Please join the [caffe-users group](https://groups.google.com/forum/#!forum/caffe-users) or [gitter chat](https://gitter.im/BVLC/caffe) to ask questions and talk about methods and models.
Framework development discussions and thorough bug reports are collected on [Issues](https://github.com/BVLC/caffe/issues).

Happy brewing!

## License and Citation

Caffe is released under the [BSD 2-Clause license](https://github.com/BVLC/caffe/blob/master/LICENSE).
The BAIR/BVLC reference models are released for unrestricted use.

Please cite Caffe in your publications if it helps your research:

    @article{jia2014caffe,
      Author = {Jia, Yangqing and Shelhamer, Evan and Donahue, Jeff and Karayev, Sergey and Long, Jonathan and Girshick, Ross and Guadarrama, Sergio and Darrell, Trevor},
      Journal = {arXiv preprint arXiv:1408.5093},
      Title = {Caffe: Convolutional Architecture for Fast Feature Embedding},
      Year = {2014}
    }
