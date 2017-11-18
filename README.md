# 使用 gluon 实现狗品种分类

本项目是为了熟悉 gluon 操作的练手作，可以在测试集上获得0.28228的分数。

环境参考：

* mxnet 0.12.1
* numpy 1.13.3
* tqdm 4.11.2
* pandas 0.20.3
* sklearn 0.19.0

# 思路

复现步骤分为以下几步：

* 下载并解压数据集：[https://www.kaggle.com/c/dog-breed-identification/data](https://www.kaggle.com/c/dog-breed-identification/data)
* 运行 [preprocessing.ipynb](preprocessing.ipynb)，构建 for_train 和 for_test 的文件夹结构，为 [ImageFolderDataset](https://mxnet.incubator.apache.org/api/python/gluon/data.html?highlight=imagefolderdataset#mxnet.gluon.data.vision.ImageFolderDataset) 做准备
* 运行 [get_features.ipynb](get_features.ipynb)，导出 resnet152_v1, densenet161 和 inception_v3 等模型输出的特征，参考[model_zoo](https://mxnet.incubator.apache.org/versions/master/api/python/gluon/model_zoo.html)
* 运行 [model.ipynb](model.ipynb)，拼接特征，训练一个2层的神经网络，然后在测试集上进行预测

# Update

为了确定使用的预训练模型是否是最好的，我对所有预训练模型进行了迁移学习：

* [get_features_v3.ipynb](get_features_v3.ipynb) 导出所有预训练模型输出的特征
* [transfer_learning_all_pretrained_models.ipynb](transfer_learning_all_pretrained_models.ipynb) 对所有预训练模型分别进行迁移学习，按 val_loss 进行排序

model | val_loss
----|----
inceptionv3 | 0.296050225385
resnet152_v1 | 0.399359531701
resnet101_v1 | 0.410383010283
densenet161 | 0.418100789189
densenet201 | 0.453403010964
resnet50_v2 | 0.484435886145
resnet50_v1 | 0.496179759502
densenet169 | 0.512498702854
resnet34_v2 | 0.536734519526
vgg19_bn | 0.557294445112
vgg16_bn | 0.586511127651
resnet34_v1 | 0.591432901099
densenet121 | 0.591716498137
vgg19 | 0.619780953974
vgg16 | 0.669267293066
vgg13_bn | 0.702507363632
vgg11_bn | 0.708396691829
vgg13 | 0.756541173905
resnet18_v2 | 0.761708110571
vgg11 | 0.789955694228
resnet18_v1 | 0.832537706941
squeezenet1.1 | 1.6066500321
squeezenet1.0 | 1.62178872526
alexnet | 1.77026221156

可以看到 densenet 并没有那么好，于是我只使用 inceptionv3 和 resnet152_v1 的特征，进行了融合迁移学习，获得了 0.27143 的分数。