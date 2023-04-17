# Stable Diffusion

## 介绍
Stable Diffusion是**2022年发布的深度学习文生图模型**
以 Stable Diffusion、Midjourney、NovelAI 等为代表的文本生成图像的跨模态应用相继涌现

## 安装Python

不要使用超过3.10 版本的 Python 环境，有部分依赖不支持3.10 以上的 Python 版本

遇到问题
```text
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
ERROR: Could not find a version that satisfies the requirement torch==1.12.1 (from versions: 2.0.0)
ERROR: No matching distribution found for torch==1.12.1
```

torch 依赖根据处理工具的不同需要不同的版本，如果是nvida 显卡，需要安装NVIDIA的开发者工具进入官网https://developer.nvidia.com/，根据自己计算机的系统情况，选择合适的安装包下载安装。如果AMD 显卡

进入官网https://pytorch.org/，根据计算机配置选择合适的版本进行安装。这里需要注意的是CUDA的平台选择，先打开NVIDIA控制面板-帮助-系统信息-组件查看CUDA版本，官网上选择的计算平台需要低于计算机的NVIDIA版本。
当查到Pytorch官网推荐的CUDA版本跟你的显卡版本不匹配时，就需要根据官网的CUDA版本找到对应的显卡驱动版本并升级显卡驱动，对应关系可通过https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html查看。

目前 macos 最低都要 `torch==2.0.0` 的支持，所以直接进行2.0.0 版本的安装

```bash
pip install torch -f https://download.pytorch.org/whl/torch_stable.html
```


安装clip 等
安装open_clip
```bash
pip install git+https://gitee.com/ufhy/open_clip.git@bb6e834e9c70d9c27d0dc3ecedeebeaeb1ffad6b --prefer-binary
```



## xformers


## 其他问题

问题：Something went wrong Expecting value: line 1 column 1 (char 0)
解决方法：关闭代理
