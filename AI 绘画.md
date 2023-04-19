# Stable Diffusion

## 介绍
Stable Diffusion是**2022年发布的深度学习文生图模型**
以 Stable Diffusion、Midjourney、NovelAI 等为代表的文本生成图像的跨模态应用相继涌现

## 代码地址

[AUTOMATIC1111](https://github.com/AUTOMATIC1111)/**[stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui)**

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

## 文生图（text2img） 与 图生图（img2img）

| 参数 | 说明 |
| ---- | ---- |
|Prompt| 提示词（正向）|
|Negative prompt| 消极的提示词（反向）|
|Width & Height| 要生成的图片尺寸。尺寸越大，越耗性能，耗时越久。|
|CFG scale| AI 对描述参数（Prompt）的倾向程度。值越小生成的图片越偏离你的描述，但越符合逻辑；值越大则生成的图片越符合你的描述，但可能不符合逻辑。|
|Sampling method| 采样方法。有很多种，但只是采样算法上有差别，没有好坏之分，选用适合的即可。|
|Sampling steps| 采样步长。太小的话采样的随机性会很高，太大的话采样的效率会很低，拒绝概率高(可以理解为没有采样到,采样的结果被舍弃了)。|
|Seed| 随机数种子。生成每张图片时的随机种子，这个种子是用来作为确定扩散初始状态的基础。不懂的话，用随机的即可。|

参数示例

```text
Prompt：a cute cat, cyberpunk art, by Adam Marczyński, cyber steampunk 8 k 3 d, kerem beyit, very cute robot zen, beeple
Negative prompt：(deformed, distorted, disfigured:1.3), poorly drawn, bad anatomy, wrong anatomy, extra limb, missing limb, floating limbs, (mutated hands and fingers:1.4), disconnected limbs, mutation, mutated, ugly, disgusting, blurry, amputation, flowers, human, man, woman
CFG scale：6.5
Sampling method：Euler a
Sampling steps：26
Seed：1791574510
```

## 模型版本

-   stable-diffusion-v1-4 擅长绘制风景类画，整体偏欧美风，具有划时代意义；
-   stable-diffusion-v1-5 同上，但生成的作品更具艺术性；
-   stable-diffusion-2 图像生成质量大幅提升，原生支持768x768等；
-   waifu-diffusion 设定随机种子后，每次将生成相同的图像，无随机性，可方便复现；
-   Taiyi-Stable-Diffusion-1B-Chinese-v0.1 擅长中文古诗词绘画，整体绘画风格更偏中国风；
-   Taiyi-Stable-Diffusion-1B-Chinese-EN-v0.1 同上，但额外支持英文输入；
-   Stable_Diffusion_PaperCut_Model 擅长剪纸画；
-   trinart_characters_19.2m_stable_diffusion_v1 擅长动漫角色绘制；
-   trinart_derrida_characters_v2_stable_diffusion 擅长动漫角色绘制，出图效果更稳定。

### 模型文件下载的网站

[Civitai | Stable Diffusion models, embeddings, hypernetworks and more](https://civitai.com/)

## 模型使用方法

将下载好的模型文件放到 `stable-diffusion-webui\models\Stable-diffusion` 目录下
重启 `stable-diffusion-webui`


## TAG生成器


## xformers


## 其他问题

问题：Something went wrong Expecting value: line 1 column 1 (char 0)
解决方法：关闭代理
