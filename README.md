한국어 | [English](README_en.md) | [简体中文](README_zh.md)

<p align="center"><img src="https://github.com/YaoFANGUK/video-subtitle-extractor/raw/main/design/icon_1024.png" alt="VSE 로고" width="128"/></p>

# Video Subtitle Extractor (VSE)

![라이선스](https://img.shields.io/badge/License-Apache%202-red.svg)
![Python 버전](https://img.shields.io/badge/Python-3.12+-blue.svg)
![지원 운영체제](https://img.shields.io/badge/OS-Windows/macOS/Linux-green.svg)

영상에 직접 삽입된 자막(하드 자막)을 OCR로 인식하여 SRT 자막 파일이나 TXT 텍스트 파일로 추출하는 프로그램이다. 음성을 받아쓰거나 자막을 번역하는 프로그램은 아니다.

- **현재 저장소**: [workingdad365/video-subtitle-extractor](https://github.com/workingdad365/video-subtitle-extractor)
- **원본 프로젝트**: [YaoFANGUK/video-subtitle-extractor](https://github.com/YaoFANGUK/video-subtitle-extractor)
- **원본 배포판 다운로드**: [Releases](https://github.com/YaoFANGUK/video-subtitle-extractor/releases)

> 원본 배포판에는 이 저장소의 추가 수정 사항이 포함되지 않을 수 있다. 이 저장소의 코드를 사용하려면 아래 소스 코드 설치 방법을 따른다.

## 주요 기능

- 영상의 주요 프레임 추출 및 프레임 내 문자 위치 검출
- OCR을 이용한 자막 인식과 자막 영역 외 텍스트 제외
- 중복 자막 제거 및 SRT/TXT 파일 생성
- 여러 영상의 자막 일괄 추출
- 한국어, 영어, 일본어, 중국어 간체·번체, 베트남어, 아랍어, 프랑스어, 독일어, 러시아어, 스페인어, 포르투갈어, 이탈리아어 등 87개 언어 지원
- 로컬 OCR 사용으로 외부 OCR API 키나 온라인 OCR 서비스 연결 불필요
- CUDA 환경에서 GPU 가속 지원

영상에서 워터마크, 방송사 로고 또는 기존 하드 자막 자체를 지우려면 별도 프로젝트인 [Video Subtitle Remover (VSR)](https://github.com/YaoFANGUK/video-subtitle-remover/tree/main)를 사용한다.

<p align="center"><img src="design/demo.png" alt="VSE 실행 화면"/></p>

## 사용 방법

1. 프로그램에서 **열기(Open)**를 눌러 영상 파일을 선택한다.
2. 미리보기에서 자막이 표시되는 영역을 지정한다.
3. 영상의 자막 언어와 인식 모드를 설정한다.
4. **실행(Run)**을 눌러 자막을 추출한다.

한 파일만 선택하면 단일 추출, 여러 파일을 선택하면 일괄 추출을 수행한다. 일괄 추출 시에는 영상의 해상도와 자막 영역이 동일한지 확인한다.

### 인식 모드

| 모드 | 설명 | 권장 용도 |
|------|------|-----------|
| Fast | 경량 모델을 사용하여 빠르게 추출한다. 일부 자막 누락이나 오인식이 발생할 수 있다. | 빠른 추출 |
| Auto | 사용 가능한 하드웨어에 따라 모델을 자동 선택한다. CPU에서는 경량 모델, CUDA GPU에서는 정밀 모델을 사용한다. | 일반적인 사용 |
| Precise | 정밀 모델을 사용하는 모드이다. CUDA 환경에서는 프레임별 검출을 수행하므로 매우 느릴 수 있다. | 다른 모드의 자막 누락이 많은 경우 |

먼저 **Fast 또는 Auto**로 실행한 뒤, 자막 누락이 많을 때 Precise를 검토한다. 인식 결과는 영상 해상도, 글꼴, 배경 등에 영향을 받으므로 검수가 필요하다.

Fast/Auto의 자막 검출에는 Windows, Linux, macOS에서 VideoSubFinder를 사용한다. 현재 코드에서는 CUDA를 사용할 수 있을 때만 Precise의 프레임별 검출 경로를 사용한다.

### 특정 문구 교체 또는 제외

인식된 텍스트를 교정하거나 특정 문구를 출력에서 제외하려면 [backend/configs/typoMap.json](backend/configs/typoMap.json)에 치환 규칙을 추가한다.

```json
{
  "l'm": "I'm",
  "l just": "I just",
  "Let'sqo": "Let's go",
  "Iife": "life",
  "제외할 문구": ""
}
```

키에 해당하는 문구를 값으로 교체하며, 빈 문자열을 지정하면 해당 문구를 제거한다. 이 설정은 추출된 텍스트에 적용되며 영상 자체를 수정하지 않는다.

### 경로 주의사항

원본 안내에서는 프로그램과 영상 경로에 중국어 문자나 공백을 사용하지 않도록 권장한다. 호환성 문제를 피하려면 영문·숫자로 구성된 공백 없는 경로를 사용하는 것이 좋다.

```text
D:\tools\video-subtitle-extractor
D:\videos\sample.mp4
```

## 소스 코드 설치 및 실행

### 1. Python 설치

Python **3.12 이상**이 필요하다. 실제 설치 가능 버전은 PaddlePaddle 등 의존 패키지의 운영체제별 지원 범위에 따라 달라질 수 있다.

- Windows: [Python 공식 다운로드](https://www.python.org/downloads/windows/)
- macOS: Homebrew 사용

  ```shell
  brew install python@3.12
  ```

- Ubuntu/Debian: 해당 버전 패키지가 제공되는 환경에서 설치

  ```shell
  sudo apt update && sudo apt install python3.12 python3.12-venv python3.12-dev
  ```

### 2. 저장소 다운로드 및 가상환경 생성

Git이 설치된 환경에서 실행한다. 이미 저장소를 내려받았다면 해당 폴더로 이동하고 복제 단계는 생략한다.

```shell
git clone https://github.com/workingdad365/video-subtitle-extractor.git
cd video-subtitle-extractor
```

Windows PowerShell:

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

PowerShell 실행 정책으로 활성화가 차단되는 경우, 현재 터미널에만 정책을 적용한 뒤 다시 활성화한다.

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
.\venv\Scripts\Activate.ps1
```

macOS/Linux:

```shell
python3.12 -m venv venv
source venv/bin/activate
```

### 3. 실행 환경에 맞는 의존성 설치

아래에서 사용할 환경에 해당하는 설치 방법을 선택한다. CPU용과 GPU용 PaddlePaddle을 같은 가상환경에 중복 설치하지 않도록 한다.

#### CUDA: NVIDIA GPU

이 저장소의 GPU 의존성은 **PaddlePaddle GPU 3.3.1 / CUDA 11.8**을 기준으로 한다. 원본에서 권장하는 조합은 CUDA 11.8과 cuDNN 8.6.0이다. GPU와 드라이버가 해당 조합을 지원하는지 먼저 확인한다.

- [GPU별 CUDA 연산 능력 확인](https://developer.nvidia.com/cuda-gpus)
- [Windows CUDA 11.8 설치 프로그램](https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_522.06_windows.exe)
- [Windows cuDNN 8.6.0](https://developer.download.nvidia.cn/compute/redist/cudnn/v8.6.0/local_installers/11.8/cudnn-windows-x86_64-8.6.0.163_cuda11-archive.zip)
- [Linux cuDNN 8.6.0](https://developer.download.nvidia.cn/compute/redist/cudnn/v8.6.0/local_installers/11.8/cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz)

Linux CUDA 설치 예시:

```shell
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_520.61.05_linux.run
sudo sh cuda_11.8.0_520.61.05_linux.run
```

CUDA와 cuDNN의 상세 설치 절차는 NVIDIA 공식 문서를 참고한다. macOS에서는 CUDA를 지원하지 않는다.

가상환경에서 GPU 의존성을 설치한다.

```shell
python -m pip install paddlepaddle-gpu==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cu118/
python -m pip install -r requirements.txt
```

> NVIDIA RTX 50 시리즈 등 CUDA 11.8과 호환되지 않는 GPU에는 위 설치 구성을 그대로 적용하지 않는다. 해당 하드웨어를 지원하는 PaddlePaddle/CUDA 조합을 별도로 확인하거나 CPU 환경을 사용한다.

#### CPU: GPU 가속 없이 실행

GPU가 없거나 CUDA 환경을 사용할 수 없는 경우의 설치 방법이다.

```shell
python -m pip install paddlepaddle==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
python -m pip install -r requirements_cpu.txt
```

#### DirectML: Windows의 AMD/NVIDIA/Intel GPU 환경

ONNX Runtime DirectML 관련 패키지 설치 방법이다.

```shell
python -m pip install paddlepaddle==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
python -m pip install -r requirements_cpu.txt
python -m pip install -r requirements_directml.txt
```

> **현재 OCR 실행 경로는 PaddleOCR를 사용하며, CUDA를 사용할 수 없으면 CPU에서 실행된다.** DirectML 실행 공급자가 감지되더라도 이 설치만으로 OCR의 GPU 가속이 활성화되는 것은 아니다.

#### 기타 ONNX 환경: macOS, AMD ROCm 등

원본에서 제공하는 **미검증 구성 안내**이다. 운영체제와 하드웨어에 맞는 ONNX Runtime 실행 공급자를 직접 구성해야 한다.

```shell
python -m pip install paddlepaddle==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
python -m pip install -r requirements_cpu.txt
```

[ONNX Runtime 실행 공급자 문서](https://onnxruntime.ai/docs/execution-providers/)와 [requirements_directml.txt](requirements_directml.txt)를 참고하여 환경에 맞는 패키지를 선택한다. ONNX 패키지 설치와 현재 PaddleOCR 경로의 GPU 가속 지원은 별개이며, 이 구성의 동작이나 가속을 보장하지 않는다. 원본 안내에서는 이 미검증 배포 방식에 대한 이슈 제보를 받지 않는다고 명시한다.

### 4. 프로그램 실행

가상환경을 활성화한 상태에서 프로젝트 루트 폴더에서 실행한다.

GUI 버전:

```shell
python gui.py
```

CLI 버전:

```shell
python ./backend/main.py
```

Windows에서는 가상환경을 활성화하지 않고도 다음과 같이 GUI를 실행할 수 있다.

```powershell
.\venv\Scripts\python.exe .\gui.py
```

## 자주 묻는 질문

### 프로그램이 실행되지 않거나 추출 결과가 없는 경우

터미널의 오류 메시지를 확인한다. GPU를 사용하는 경우 GPU 모델과 드라이버에 맞는 CUDA/cuDNN이 설치되어 있는지 확인한다. 프로그램과 영상 경로에 공백이나 중국어 문자가 없는지도 확인한다.

### GPU가 있는데 OCR이 CPU로 실행되는 경우

현재 OCR 경로는 PaddlePaddle에서 CUDA를 사용할 수 있어야 GPU로 실행된다. CPU용 PaddlePaddle 설치 여부와 GPU/드라이버/CUDA 호환성을 확인한다. DirectML 또는 다른 ONNX 실행 공급자의 존재만으로는 PaddleOCR의 GPU 실행을 보장하지 않는다.

### 터미널에서 Ctrl+C로 종료되지 않는 경우

Qt GUI 이벤트 루프 때문에 Python의 종료 신호 처리가 지연될 수 있다. 프로그램 창의 닫기 버튼이나 Windows의 `Alt+F4`로 종료한다.

### 7z 압축 해제 오류가 발생하는 경우

7-Zip을 최신 버전으로 업데이트한 뒤 다시 압축을 해제한다.

## 데모 및 문의

- [GPU 버전 소스 코드 설치 영상](https://www.bilibili.com/video/bv11L4y1Y7Tj)
- [현재 저장소 이슈](https://github.com/workingdad365/video-subtitle-extractor/issues)
- [원본 프로젝트 이슈](https://github.com/YaoFANGUK/video-subtitle-extractor/issues) 및 [토론](https://github.com/YaoFANGUK/video-subtitle-extractor/discussions)
- 원본 QQ 문의 그룹: 210150985, 806152575, 816881808(앞의 세 그룹은 원본 안내 기준 정원 마감), 295894827

<p align="center"><img src="https://github.com/YaoFANGUK/video-subtitle-extractor/raw/main/design/demo.gif" alt="VSE 사용 데모"/></p>

## 원본 프로젝트 후원

<img src="https://github.com/YaoFANGUK/video-subtitle-extractor/raw/main/design/sponsor.png" alt="원본 프로젝트 후원 안내" width="600"/>

## 라이선스

[Apache License 2.0](LICENSE) 적용.简体中文 | [English](README_en.md)

<p align="center"><img src="https://github.com/YaoFANGUK/video-subtitle-extractor/raw/main/design/icon_1024.png" alt="VSE Logo" width="128"/></p>

## 项目简介

![License](https://img.shields.io/badge/License-Apache%202-red.svg)
![python version](https://img.shields.io/badge/Python-3.12+-blue.svg)
![support os](https://img.shields.io/badge/OS-Windows/macOS/Linux-green.svg)  

Video-subtitle-extractor (VSE) 是一款将视频中的硬字幕提取为外挂字幕文件(srt格式)的软件
主要实现了以下功能：

- 提取视频中的关键帧
- 检测视频帧中文本的所在位置
- 识别视频帧中文本的内容
- 过滤非字幕区域的文本
- 去除水印、台标文本、原视频硬字幕，可配合：[video-subtitle-remover (VSR) ](https://github.com/YaoFANGUK/video-subtitle-remover/tree/main)
- 去除重复字幕行，生成srt字幕文件/txt文本文件
- 支持视频字幕**批量提取**
- 多语言：支持**简体中文（中英双语）**、**繁体中文**、**英文**、**日语**、**韩语**、**越南语**、**阿拉伯语**、**法语**、**德语**、**俄语**、**西班牙语**、**葡萄牙语**、**意大利语**等**87种**语言的字幕提取
- 多模式：
  - **快速**：（推荐）使用轻量模型，快速提取字幕，可能丢少量字幕、存在少量错别字
  - **自动**：（推荐）自动判断模型，CPU下使用轻量模型；GPU下使用精准模型，提取字幕速度较慢，可能丢少量字幕、几乎不存在错别字
  - **精准**：（不推荐）使用精准模型，GPU下逐帧检测，不丢字幕，几乎不存在错别字，但速度**非常慢**

> 请优先使用快速/自动模式，如果前两种模式存在较多丢字幕轴情况时，再使用精准模式
 
<p style="text-align:center;"><img src="design/demo.png" alt="demo.png"/></p>

**项目特色**：

- 采用本地进行OCR识别，无需设置调用任何API，不需要接入百度、阿里等在线OCR服务即可本地完成文本识别
- 支持GPU加速，GPU加速后可以获得更高的准确率与更快的提取速度

**使用说明**：

- 有使用问题请加群讨论，QQ群：210150985（已满）、806152575（已满）、816881808（已满）、295894827

- 点击【打开】后选择视频文件，调整字幕区域，点击【运行】
  - 单文件提取：打开文件的时候选择**单个**视频
  - **批量提取**：打开文件的时候选择**多个**视频，确保每个视频的分辨率、字幕区域保持一致

- 去除水印文本/替换特定文本：
> 如果视频中出现特定的文本需要删除，或者特定的文本需要替换，可以编辑 ``backend/configs/typoMap.json``文件，加入你要替换或去除的内容

```json
{
	"l'm": "I'm",
	"l just": "I just",
	"Let'sqo": "Let's go",
	"Iife": "life",
	"威筋": "威胁",
  	"性感荷官在线发牌": ""
}
```

> 这样就可以把文本中出现的所有“威筋”替换为“威胁”，所有的“性感荷官在线发牌”文本删除

- 视频以及程序路径请**不要带中文和空格**，否则可能出现未知错误！！！

 > 如：以下存放视频和代码的路径都不行
 >
 > D:\下载\vse\运行程序.exe（路径含中文）
 >
 > E:\study\kaoyan\sanshang youya.mp4 （路径含空格） 

- 直接下载压缩包解压运行，如果不能运行再按照下面的教程，尝试源码安装conda环境运行

**下载地址**：<a href="https://github.com/YaoFANGUK/video-subtitle-extractor/releases"> Release </a>

> **有任何改进意见请在ISSUES和DISCUSSION中提出**

> NVIDIA官方提供了各GPU型号的计算能力列表，您可以参考链接: [CUDA GPUs](https://developer.nvidia.com/cuda-gpus) 查看你的GPU适合哪个CUDA版本

> NVIDIA 50系显卡需要使用cuda12.8.0及以上版本, 但Paddle3.3.1目前仍未支持，所以建议使用Directml通用版本

**识别模式选择说明**：
|    模式名称    | GPU | OCR模型尺寸 | 字幕检测引擎 | 备注 |
|---------------|-----|---------|------|------|
|    快速        | 有/无 | 迷你  | VideoSubFinder | |
|    自动  | 有| 大  | VideoSubFinder |  推荐   |
|    自动  | 无| 迷你  | VideoSubFinder |  推荐   |
|    精准        | 有/无| 大  | VSE | 非常慢 |
> Windows/Linux/MacOS环境下字幕检测引擎都是VideoSubFinder

## 演示

- GUI版：[点击查看GPU版本源码运行的安装教程 👈](https://www.bilibili.com/video/bv11L4y1Y7Tj)

<p style="text-align:center;"><img src="https://github.com/YaoFANGUK/video-subtitle-extractor/raw/main/design/demo.gif" alt="demo.gif"/></p>


## 源码使用说明

#### 1. 安装 Python

请确保您已经安装了 Python 3.12+

- Windows 用户可以前往 [Python 官网](https://www.python.org/downloads/windows/) 下载并安装 Python
- MacOS 用户可以使用 Homebrew 安装：
  ```shell
  brew install python@3.12
  ```
- Linux 用户可以使用包管理器安装，例如 Ubuntu/Debian：
  ```shell
  sudo apt update && sudo apt install python3.12 python3.12-venv python3.12-dev
  ```

#### 2. 安装依赖文件

请使用虚拟环境来管理项目依赖，避免与系统环境冲突

（1）创建虚拟环境并激活
```shell
python -m venv videoEnv
```

- Windows：
```shell
videoEnv\\Scripts\\activate
```
- MacOS/Linux：
```shell
source videoEnv/bin/activate
```

#### 3. 创建并激活项目目录

切换到源码所在目录：
```shell
cd <源码所在目录>
```
> 例如：如果您的源代码放在 D 盘的 tools 文件夹下，并且源代码的文件夹名为 video-subtitle-extractor，则输入：
> ```shell
> cd D:/tools/video-subtitle-extractor-main
> ```

#### 4. 安装合适的运行环境

本项目支持 CUDA（NVIDIA显卡加速）、CPU（无 GPU）、DirectML（AMD、Intel等GPU/APU加速）、ONNX四种运行模式

##### (1) CUDA（NVIDIA 显卡用户）

> 请确保您的 NVIDIA 显卡驱动支持所选 CUDA 版本

- 推荐 CUDA 11.8，对应 cuDNN 8.6.0

- 安装 CUDA：
  - Windows：[CUDA 11.8 下载](https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_522.06_windows.exe)
  - Linux：
    ```shell
    wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_520.61.05_linux.run
    sudo sh cuda_11.8.0_520.61.05_linux.run
    ```
  - MacOS 不支持 CUDA

- 安装 cuDNN（CUDA 11.8 对应 cuDNN 8.6.0）：
  - [Windows cuDNN 8.6.0 下载](https://developer.download.nvidia.cn/compute/redist/cudnn/v8.6.0/local_installers/11.8/cudnn-windows-x86_64-8.6.0.163_cuda11-archive.zip)
  - [Linux cuDNN 8.6.0 下载](https://developer.download.nvidia.cn/compute/redist/cudnn/v8.6.0/local_installers/11.8/cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz)
  - 安装方法请参考 NVIDIA 官方文档

- 安装 PaddlePaddle GPU 版本（CUDA 11.8）：
  ```shell
  pip install paddlepaddle-gpu==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cu118/
  pip install -r requirements.txt
  ```

##### (2) DirectML（AMD、Intel等GPU/APU加速卡用户）

- 适用于 Windows 设备的 AMD/NVIDIA/Intel GPU
- 安装 ONNX Runtime DirectML 版本：
  ```shell
  pip install paddlepaddle==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
  pip install -r requirements_cpu.txt
  pip install -r requirements_directml.txt
  ```

##### (3) ONNX (适合macOS、AMD ROCm等环境加速用户, 基础环境与DirectML方式一致，未测试！)

- 使用这个方式部署请勿反馈Issues
- 适用于 Linux 或 macOS 设备的 AMD/Metal GPU/Apple Silicon GPU
- 安装 ONNX Runtime DirectML 版本：
  ```shell
  pip install paddlepaddle==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
  pip install -r requirements_cpu.txt

  # 阅读文档 https://onnxruntime.ai/docs/execution-providers/
  # 根据你的设备选择合适的执行后端, 参考requirements_directml.txt文件修改成合适你环境的依赖

  # 例如:
  # requirements_coreml.txt
  #   paddle2onnx==1.3.1
  #   onnxruntime-coreml==1.13.1

  pip install -r requirements_coreml.txt
  ```

##### (4) CPU 运行（无 GPU 加速）

- 适用于没有 GPU 或不希望使用 GPU 的情况
- 直接安装 CPU 版本 PaddlePaddle：
  ```shell
  pip install paddlepaddle==3.3.1 -i https://www.paddlepaddle.org.cn/packages/stable/cpu/
  pip install -r requirements_cpu.txt
  ```

#### 5. 运行程序

- 运行图形化界面版本（GUI）

```shell
python gui.py
```

- 运行命令行版本（CLI）

```shell
python ./backend/main.py
```

## 常见问题与解决方案

#### 1. 运行不正常/没有结果/cuda及cudnn问题

解决方案：根据自己的显卡型号、显卡驱动版本，安装对应的cuda与cudnn

#### 2. 7z文件解压错误

解决方案：升级7-zip解压程序到最新版本

## 赞助
<img src="https://github.com/YaoFANGUK/video-subtitle-extractor/raw/main/design/sponsor.png" width="600">

