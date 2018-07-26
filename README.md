<p align="center"><img width="40%" src="./imgs/pytorch.png"></p>

# graph-cnn.pytorch
Pytorch implementation of Graph Convolution Networks.
This project is made by Bumsoo Kim.

Korea University, Master-Ph.D intergrated Course.
<p align="center"><img width="40%" src="./imgs/transfer-learning.jpeg"></p>


## Graph Convolutional Networks
Many important real-world datasets come in the form of graphs or networks: social networks, knowledge graphs, protein-interaction networks, the World Wide Web, etc. In this repository, we introduce a basic tutorial for generalizing neural netowrks to work on arbitrarily structured graphs, along with a proposal of a new structure that outperforms the state-of-the-art performance of current Graph Convolutional Networks([Attention GCN]()).

Currently, most graph neural network models have a somewhat universal architecture in common. They are referred as Graph Convoutional Networks(GCNs) since filter parameters are typically shared over all locations in the graph.

![alt_tag](./imgs/gcn_web.png)

For these models, the goal is to learn a function of signals/features on a graph G=(V, E), which takes as 

```bash
Input
- N x D feature matrix (N : Number of nodes, D : number of input features)
- representative description of the graph structure in matrix form; typically in the form of adjacency matrix A

Output
- N x F feature matrix (N : Number of nodes, F : number of output features)
```

Graph-level outputs can be modeled by introducing some form of pooling operation.

Every neural network layer can then be written as a non-linear function
```bash
H^(l+1) = f(H^(l), A),
```

with H^(0) = X and H^(L) = Z, L being the number of layers. The specific models then differ only in how f is chosen and parameterized.

## Requirements
See the [installation instruction](INSTALL.md) for a step-by-step installation guide.
See the [server instruction](SERVER.md) for server settup.
- Install [cuda-8.0](https://developer.nvidia.com/cuda-downloads)
- Install [cudnn v5.1](https://developer.nvidia.com/cudnn)
- Download [Pytorch for python-2.7](https://pytorch.org) and clone the repository.
```bash
pip install http://download.pytorch.org/whl/cu80/torch-0.1.12.post2-cp27-none-linux_x86_64.whl
pip install torchvision
git clone https://github.com/meliketoy/resnet-fine-tuning.pytorch
```

## Basic Setups
After you have cloned this repository into your file system, open [config.py](./config.py),
And edit the lines below to your data directory.
```bash
data_base = [:dir to your original dataset]
aug_base =  [:dir to your actually trained dataset]
```

For training, your data file system should be in the following hierarchy.
Organizing codes for your data into the given requirements will be provided [here](https://github.com/meliketoy/image-preprocessing)

```bash
[:data file name]

    |-train
        |-[:class 0]
        |-[:class 1]
        |-[:class 2]
        ...
        |-[:class n]
    |-val
        |-[:class 0]
        |-[:class 1]
        |-[:class 2]
        ...
        |-[:class n]
```

## How to run
After you have cloned the repository, you can train the dataset by running the script below.

You can set the dimension of the additional layer in [config.py](./config.py)

The resetClassifier option will automatically detect the number of classes in your data folder and reset the last classifier layer to the according number.

```bash
# zero-base training
python main.py --lr [:lr] --depth [:depth] --resetClassifier

# fine-tuning
python main.py --finetune --lr [:lr] --depth [:depth]

# fine-tuning with additional linear layers
python main.py --finetune --addlayer --lr [:lr] --depth [:depth]
```

## Train various networks

I have added fine-tuning & transfer learning script for alexnet, VGG(11, 13, 16, 19),
ResNet(18, 34, 50, 101, 152).

Please modify the [scripts](./train) and run the line below.

```bash

$ ./train/[:network].sh 

# For example, if you want to pretrain alexnet, just run
$ ./train/alexnet.sh

```

## Test (Inference) various networks

For testing out your fine-tuned model on alexnet, VGG(11, 13, 16, 19), ResNet(18, 34, 50, 101, 152),

First, set your data directory as test_dir in [config.py](./config.py).

Please modify the [scripts](./test) and run the line below.

```bash

$ ./test/[:network].sh

```
For example, if you have trained ResNet with 50 layers, first modify the [resnet test script](./test/resnet.sh)

```bash
$ vi ./test/resnet.sh

python main.py \
    --net_type resnet \
    --depth 50
    --testOnly

$ ./test/resnet.sh

```

The code above will automatically download weights from the given depth data, and train your dataset with a very small learning rate.

## Feature extraction
For various training mechanisms, extracted feature vectors are needed.

This repository will provide you not only feature extraction from pre-trained networks,

but also extractions from a model that was trained by yourself.

Just set the test directory in the [config.py](config.py) and run the code below.

```bash
python extract_features.py
```

This will automatically create pickles in a newly created 'vector' directory,

which will contain dictionary pickles which contains the below.

Currently, the 'score' will only cover 0~1 scores for binary classes.

Confidence scores for multiple class will be updated afterwards.

```bash
pickle_file [name : image base name]
    |- 'file_name' : file name of the test image
    |- 'features'  : extracted feature vector
    |- 'score'     : Score for binary classification
```

Enjoy :-)
