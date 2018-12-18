# Caffe++☕️
Assemble new layers to enhance Caffe.  
The Caffe revision: ```99bd99795dcdf0b1d3086a8d67ab1782a8a08383```(commit [SHA](https://github.com/BVLC/caffe/tree/99bd99795dcdf0b1d3086a8d67ab1782a8a08383))

# New Layers
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
# Examples
New layer examples are [here](https://github.com/becauseofAI/caffe-plus-plus/tree/master/examples).

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
