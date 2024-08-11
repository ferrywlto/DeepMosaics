<div align="center">
  <img src="./imgs/logo.png" width="250"><br><br>
  <img src="https://badgen.net/github/stars/hypox64/deepmosaics?icon=github&color=4ab8a1">&emsp;<img src="https://badgen.net/github/forks/hypox64/deepmosaics?icon=github&color=4ab8a1">&emsp;<a href="https://github.com/HypoX64/DeepMosaics/releases"><img src=https://img.shields.io/github/downloads/hypox64/deepmosaics/total></a>&emsp;<a href="https://github.com/HypoX64/DeepMosaics/releases"><img src=https://img.shields.io/github/v/release/hypox64/DeepMosaics></a>&emsp;<img src=https://img.shields.io/github/license/hypox64/deepmosaics>
</div>

# DeepMosaics

**English | [中文](./README_CN.md)**<br>
You can use it to automatically remove the mosaics in images and videos, or add mosaics to them.<br>This project is based on "semantic segmentation" and "Image-to-Image Translation".<br>Try it at this [website](http://118.89.27.46:5000/)!<br>

This fork is for my own reference on how did I successfully running the source code.
Only this readme will get updated. I will list out the tricky part I had encountered.

My development environment:
- 16" Macbook Pro M3-Pro
- MacOS Sonoma 14.5
- Python 3.12

## Pre-requisites

### Model files
As the repo author suggest, you should download the pretrained_models from the links provided in this readme. You should have a directory named `pretrained_models` in your project directory with the model files in it:
```
./pretrained_models
├─ clean_face_HD.pth
├─ clean_youknow_resnet_9blocks.pth
├─ clean_youknow_video.pth
├─ mosaic_position.pth
├─ put_pretrained_model_here
``` 
### Python 3
Since Python 3 it will throw errors when you `pip install -r requirements.txt` or `python3 -m pip install -r requirements.txt`
```
error: externally-managed-environment

× This environment is externally managed
```
You will need to create a virtual environment first
then call the `source` commend to set python use the created virtual environemnt
```
// run under project directory
python3 -m venv any_name_you_like
source the_name_you_used/bin/activate
```
For example if your virtual environment named "moasic"
```
python3 -m venv moasic
source moasic/bin/activate
```
You will find that there is a `(moasic)` before your current working directory path afterwards.

You are all set and can test now:
```
// run under project directory
(moasic) DeepMosaics % python3 deepmosaic.py --media_path imgs/example/demosaic.jpg --model_path ./pretrained_models/clean_youknow_resnet_9blocks.pth --gpu_id 0
```
Result image will be located in `./result` folder.

### FFmpeg
To process video files, you will need ffmpeg.
1. Download the `ffmpeg` and `ffprobe` binaries form FFmpeg [download](https://www.ffmpeg.org/download.html) page
2. Upzip and extract the files to a location close to project directory. (I put them into `ffmpeg_bin`)

if the scripts cannot locate the ffmpeg binaries, it will throw the error below:
```
--------------------ERROR--------------------
--------------Environment--------------
DeepMosaics: 0.5.1
Python: 3.12.4 (main, Jun  6 2024, 18:26:44) [Clang 15.0.0 (clang-1500.3.9.4)]
Pytorch: 2.4.0
OpenCV: 4.10.0
Platform: macOS-14.5-arm64-arm-64bit
--------------BUG--------------
Error Type: <class 'json.decoder.JSONDecodeError'>
Expecting value: line 1 column 1 (char 0)
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/deepmosaic.py, line 77 in <module>>
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/deepmosaic.py, line 51 in main>
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/cores/clean.py, line 169 in cleanmosaic_video_fusion>
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/cores/init.py, line 8 in video_init>
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/util/ffmpeg.py, line 67 in get_video_infos>
<FrameSummary file /opt/homebrew/Cellar/python@3.12/3.12.4/Frameworks/Python.framework/Versions/3.12/lib/python3.12/json/__init__.py, line 346 in loads>
<FrameSummary file /opt/homebrew/Cellar/python@3.12/3.12.4/Frameworks/Python.framework/Versions/3.12/lib/python3.12/json/decoder.py, line 337 in decode>
<FrameSummary file /opt/homebrew/Cellar/python@3.12/3.12.4/Frameworks/Python.framework/Versions/3.12/lib/python3.12/json/decoder.py, line 355 in raw_decode>
```
3. Update `util/ffmpeg.py`, introduce variables to the path of ffmpeg binaries.
4. Replace all the hardcoded call to ffmpeg/ffprobe to those variables.
```
# Note that the path is relative to the project root directory, not the util folder
# Define the path to the ffmpeg binary
FFMPEG_PATH = 'ffmpeg_bin/ffmpeg'
FFPROBE_PATH = 'ffmpeg_bin/ffprobe'

# change L58
os.system('ffmpeg -y -r '+str(fps)+' -i '+imagepath+' -vcodec libx264 '+os.path.split(voicepath)[0]+'/video_tmp.mp4')
# to
os.system(f'{FFMPEG_PATH} -y -r '+str(fps)+' -i '+imagepath+' -vcodec libx264 '+os.path.split(voicepath)[0]+'/video_tmp.mp4')
```

### Other errors
It is suggested to turn off the moasic mask preview with the `--no_preview` flag.

One for saving computer resource to not opening a window and display those masks which I can't think of anything useful.
Second is to avoid the error I encounted below.
```
Step:3/4 -- Clean Mosaic:
--------------------ERROR--------------------
--------------Environment--------------
DeepMosaics: 0.5.1
Python: 3.12.4 (main, Jun  6 2024, 18:26:44) [Clang 15.0.0 (clang-1500.3.9.4)]
Pytorch: 2.4.0
OpenCV: 4.10.0
Platform: macOS-14.5-arm64-arm-64bit
--------------BUG--------------
Error Type: <class 'NotImplementedError'>

<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/deepmosaic.py, line 77 in <module>>
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/deepmosaic.py, line 51 in main>
<FrameSummary file /Users/ferrywlto/Documents/GitHub/3rdParty/DeepMosaics/cores/clean.py, line 211 in cleanmosaic_video_fusion>
<FrameSummary file /opt/homebrew/Cellar/python@3.12/3.12.4/Frameworks/Python.framework/Versions/3.12/lib/python3.12/multiprocessing/queues.py, line 126 in qsize>
```

### Examples

![image](./imgs/hand.gif)

|                origin                |             auto add mosaic              |             auto clean mosaic              |
| :----------------------------------: | :--------------------------------------: | :----------------------------------------: |
|  ![image](./imgs/example/lena.jpg)   |  ![image](./imgs/example/lena_add.jpg)   |  ![image](./imgs/example/lena_clean.jpg)   |
| ![image](./imgs/example/youknow.png) | ![image](./imgs/example/youknow_add.png) | ![image](./imgs/example/youknow_clean.png) |

- Compared with [DeepCreamPy](https://github.com/deeppomf/DeepCreamPy)

|                mosaic image                |            DeepCreamPy             |                   ours                    |
| :----------------------------------------: | :--------------------------------: | :---------------------------------------: |
| ![image](./imgs/example/face_a_mosaic.jpg) | ![image](./imgs/example/a_dcp.png) | ![image](./imgs/example/face_a_clean.jpg) |
| ![image](./imgs/example/face_b_mosaic.jpg) | ![image](./imgs/example/b_dcp.png) | ![image](./imgs/example/face_b_clean.jpg) |

- Style Transfer

|              origin              |               to Van Gogh                |                   to winter                    |
| :------------------------------: | :--------------------------------------: | :--------------------------------------------: |
| ![image](./imgs/example/SZU.jpg) | ![image](./imgs/example/SZU_vangogh.jpg) | ![image](./imgs/example/SZU_summer2winter.jpg) |

An interesting example:[Ricardo Milos to cat](https://www.bilibili.com/video/BV1Q7411W7n6)

## Run DeepMosaics

You can either run DeepMosaics via a pre-built binary package, or from source.<br>

### Try it on web

You can simply try to remove the mosaic on the **face** at this [website](http://118.89.27.46:5000/).<br>

### Pre-built binary package

For Windows, we bulid a GUI version for easy testing.<br>
Download this version, and a pre-trained model via [[Google Drive]](https://drive.google.com/open?id=1LTERcN33McoiztYEwBxMuRjjgxh4DEPs) [[百度云,提取码1x0a]](https://pan.baidu.com/s/10rN3U3zd5TmfGpO_PEShqQ) <br>

- [[Help document]](./docs/exe_help.md)<br>
- Video tutorial => [[youtube]](https://www.youtube.com/watch?v=1kEmYawJ_vk) [[bilibili]](https://www.bilibili.com/video/BV1QK4y1a7Av)

![image](./imgs/GUI.png)<br>
Attentions:<br>

- Requires Windows_x86_64, Windows10 is better.<br>
- Different pre-trained models are suitable for different effects.[[Introduction to pre-trained models]](./docs/pre-trained_models_introduction.md)<br>
- Run time depends on computers performance (GPU version has better performance but requires CUDA to be installed).<br>
- If output video cannot be played, you can try with [potplayer](https://daumpotplayer.com/download/).<br>
- GUI version updates slower than source.<br>

### Run From Source

#### Prerequisites

- Linux, Mac OS, Windows
- Python 3.6+
- [ffmpeg 3.4.6](http://ffmpeg.org/)
- [Pytorch 1.0+](https://pytorch.org/)
- CPU or NVIDIA GPU + CUDA CuDNN<br>

#### Dependencies

This code depends on opencv-python, torchvision available via pip install.

#### Clone this repo

```bash
git clone https://github.com/HypoX64/DeepMosaics.git
cd DeepMosaics
```

#### Get Pre-Trained Models

You can download pre_trained models and put them into './pretrained_models'.<br>
[[Google Drive]](https://drive.google.com/open?id=1LTERcN33McoiztYEwBxMuRjjgxh4DEPs) [[百度云,提取码1x0a]](https://pan.baidu.com/s/10rN3U3zd5TmfGpO_PEShqQ)<br>
[[Introduction to pre-trained models]](./docs/pre-trained_models_introduction.md)<br>

In order to add/remove mosaic, there must be a model file `mosaic_position.pth` at `./pretrained_models/mosaic/mosaic_position.pth`

#### Install dependencies

(Optional) Create a virtual environment

```bash
virtualenv mosaic
source mosaic/bin/activate
```

Then install the dependencies

```bash
pip install -r requirements.txt
```

If you can not build `scikit-image`, running `export CFLAGS='-Wno-implicit-function-declaration` then try to rebuild.

#### Simple Example

- Add Mosaic (output media will be saved in './result')<br>

```bash
python deepmosaic.py --media_path ./imgs/ruoruo.jpg --model_path ./pretrained_models/mosaic/add_face.pth --gpu_id 0
```

- Clean Mosaic (output media will save in './result')<br>

```bash
python deepmosaic.py --media_path ./result/ruoruo_add.jpg --model_path ./pretrained_models/mosaic/clean_face_HD.pth --gpu_id 0
```

If you see the error `Please check mosaic_position_model_path!`, check if there is a model file named `mosaic_position.pth` at `./pretrained_models/mosaic/mosaic_position.pth`

#### More Parameters

If you want to test other images or videos, please refer to this file.<br>
[[options_introduction.md]](./docs/options_introduction.md) <br>

## Training With Your Own Dataset

If you want to train with your own dataset, please refer to [training_with_your_own_dataset.md](./docs/training_with_your_own_dataset.md)

## Acknowledgements

This code borrows heavily from [[pytorch-CycleGAN-and-pix2pix]](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix) [[Pytorch-UNet]](https://github.com/milesial/Pytorch-UNet) [[pix2pixHD]](https://github.com/NVIDIA/pix2pixHD) [[BiSeNet]](https://github.com/ooooverflow/BiSeNet) [[DFDNet]](https://github.com/csxmli2016/DFDNet) [[GFRNet_pytorch_new]](https://github.com/sonack/GFRNet_pytorch_new).
