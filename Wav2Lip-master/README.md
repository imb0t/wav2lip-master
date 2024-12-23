# 项目名称
Wav2lip-master
## 1. 项目简介
这个项目是基于Wav2lip-master项目（https://github.com/Rudrabha/Wav2lip/)的复现项目，在原项目的基础上通过更加简单的操作来实现从生成视频到评测完毕这一流程。

## 2. 系统要求
- 操作系统：Windows / Linux
- Python 版本：3.8

## 3. 环境配置

### 3.1 安装 Python
请确保系统上安装了 Python 3.8。可以从 [Python 官网](https://www.python.org/downloads/) 下载并安装。

### 3.2 创建虚拟环境
建议使用虚拟环境来管理项目依赖。使用conda创建虚拟环境。

```bash
# 使用 conda
conda create --name myenv python=3.8
conda activate myenv
```
在Windows下如果要使用gpu运行，请输入命令：
```BASH
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
```
### 3.3 安装依赖
在项目根目录下，使用 pip 或 conda 安装项目所需的依赖。
```BASH
# 使用 pip 安装
pip install -r requirements.txt
```
### 3.4 docker镜像安装
我已经将运行此项目docker打包并上传至该网址，读者可以自行下载使用：
```BASH
https://share.weiyun.com/sI9Hlbbb
```
```bash
docker run ... --shm-size=3g --gpus all  --name=xxxx -t -d name
#docker运行
docker exec -it yuyinshibie /bin/bash
#进入docker
conda activate xuni
#进入进行工作的虚拟环境
```
### 3.5 模型安装
我把模型都放在一起，方便在windows下的读者能够直接下载模型安装到对应位置。
链接: https://pan.baidu.com/s/1xIi0HJKMs7_V8mt5JVSsgw 提取码: kgmg 
```BASH
syncnet_v2.model和example.avi需要放置到Wav2Lip-master\evaluation\syncnet_python-master\data下，无data文件夹请自行创建
sfd_face.pth请放置在Wav2Lip-master\evaluation\syncnet_python-master\detectors\s3fd\weights下
s3fd-619a316812.pth请放置在Wav2Lip-master\face_detection\detection\sfd下
wav2lip.pth和wav2lip_gan.pth文件请放在Wav2Lip-master\checkpoints下
```
##  4. 项目结构
这里只显示操作涉及的文件夹和文件，没有提到的文件或文件夹不代表不需要
```bash
Wav2Lip-master/
│
├── checkpoints/            # wav2lip模型存放地
│   ├── .pth      #预训练的模型文件
│   ├── README
│
├── evaluation/           #评测文件夹
│   ├── inputframes/      # 由输入视频提取的图像
│   ├── outputframes/     # 由输出视频提取的图像
│   ├── scores_LSE/      # 评测文件，请保证该文件下的py文件复制到syncnet_python-master中
│   ├── syncnet_python-master/     # LSE-C LSE-D评估请在该文件夹下进行
│   ├── pytorch-fid-master/      #这里存放pytorch-fid项目 
│   ├── .../
│
├── inputmp4/            # 可以将作为人脸输入的jpg，mp4放在这个文件夹下
│   ├── ...      #
│
├── inputwav/            # 可以将作为音频输入的wav放在这个文件夹下
│   ├── ...
│
├── face_detection/            # 请将下载到的s3fd-619a316812.pth文件放置在face_detection\detection\sfd下
│   ├── ...
│
├── results/            # 生成的输出视频会出现在这个文件夹下
│   ├── ...
├── requirements.txt # 依赖项
├── README.md        # 项目说明
```
## 5. 运行项目 
确保inputmp4文件夹中和inputwav中存在格式正确的文件
```bash
运行下列命令：
python inference.py --checkpoint_path checkpoints\wav2lip_gan.pth --face inputmp4\video1.mp4 --audio inputwav\test1.wav#生成视频
```

## 6. 测试
### 6.1 LSE-C、LSE-D指数测试方法
```BASH
cd evaluation/synsyncnet_python-master
python run_pipeline.py --videofile path to\result_voice.mp4 --reference wav2lip --data_dir tmp_dir#将path to\result_voice.mp4改为你要检测的生成视频路径
python calculate_scores_real_videos.py --videofile path to\result_voice.mp4 --reference wav2lip --data_dir tmp_dir >> all_scores.txt
#将path to\result_voice.mp4改为你要检测的生成视频路径，结果会生成在all_scores.txt
#注意：在结束测试后，如果要进行下一次测试前请先删除产生的tmp_dir文件夹
```
### 6.2 FID指数测试方法
```BASH
python get_frames.py path/to/your/video.mp4 --output_folder evaluation\outputframes（inputframes） --frame_interval 1 --max_threads 4#用该命令将生成视频（输入视频）提取为图像存放在指定文件夹中
python -m pytorch_fid evaluation\inputframes evaluation\outputframes#生成结果
#注意：在结束测试后，如果要进行下一次测试前请先删除产生的inputframes和outputframes文件夹中的图片
```
## 7. 常见问题

问题 1 FID指数测试无法运行
检查被比对的文件夹中存放数据是否正确
在python -m pytorch_fid evaluation\inputframes evaluation\outputframes后加--num-workers 0 --device cuda:0

问题 2 linux下运行LSE-C测试在最后一步被KILLED
尝试缩短生成视频的长度，如果无法实现，请尝试在windows环境下运行


## 8. 联系信息

姓名：majunchen
邮箱：2406722613@qq.com
GitHub： GitHub 链接
