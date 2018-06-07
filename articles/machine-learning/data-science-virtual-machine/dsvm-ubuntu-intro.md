---
title: Azure에서 Linux(Ubuntu) 데이터 과학 Virtual Machine 프로비전 | Microsoft Docs
description: 분석 및 기계 학습을 수행하기 위해 Azure에서 Linux(Ubuntu)용 데이터 과학 Virtual Machine을 구성하고 만듭니다.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: bradsev
ms.openlocfilehash: 18465463e924c10ddc35d619992655773e12cc82
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Azure에서 Linux(Ubuntu)용 데이터 과학 Virtual Machine 프로비전

Linux용 데이터 과학 Virtual Machine은 Azure에서 딥 러닝을 포함한 가상 학습을 쉽게 시작할 수 있도록 하는 Ubuntu 기반 가상 머신 이미지입니다. 심층 학습 도구에는 다음이 포함됩니다.

  * [Caffe](http://caffe.berkeleyvision.org/): 속도, 표현도 및 모듈화를 위해 구축된 심층 학습 프레임워크
  * [Caffe2](https://github.com/caffe2/caffe2): Caffe의 플랫폼 간 버전
  * [Microsoft Cognitive Toolki](https://github.com/Microsoft/CNTK): Microsoft Research의 심화 학습 소프트웨어 도구 키트
  * [H2O](https://www.h2o.ai/): 오픈 소스 빅 데이터 플랫폼 및 그래픽 사용자 인터페이스
  * [Keras](https://keras.io/): Python의 Theano 및 TensorFlow용 고급 신경망 네트워크 API
  * [MXNet](http://mxnet.io/): 많은 언어 바인딩을 사용하는 유연하고 효율적인 심층 학습 라이브러리
  * [NVIDIA DIGITS](https://developer.nvidia.com/digits): 일반적인 심층 학습 작업을 단순화하는 그래픽 시스템
  * [PyTorch](http://pytorch.org/): 동적 네트워크를 지원하는 고급 Python 라이브러리
  * [TensorFlow](https://www.tensorflow.org/): Google의 컴퓨터 인텔리전스를 위한 오픈 소스 라이브러리
  * [Theano](http://deeplearning.net/software/theano/): 다차원 배열과 관련된 수학 식을 정의하고, 최적화하고, 효율적으로 계산하기 위한 Python 라이브러리
  * [Torch](http://torch.ch/): 기계 학습 알고리즘을 광범위하게 지원하는 공학용 계산 프레임워크
  * CUDA, cuDNN 및 NVIDIA 드라이버
  * 많은 샘플 Jupyter 노트북

모든 라이브러리는 GPU 버전이지만 CPU에서도 실행됩니다.

Linux용 데이터 과학 Virtual Machine에는 다음을 포함하여 데이터 과학 및 개발 활동을 위한 인기 있는 도구도 포함되어 있습니다.

* Microsoft R Open을 포함하는 Microsoft R Server Developer Edition
* 널리 사용되는 데이터 분석 라이브러리를 포함한 Anaconda Python 배포(버전 2.7 및 3.5)
* JuliaPro - 인기 있는 과학 및 데이터 분석 라이브러리와 함께 Julia 언어의 큐레이트 배포
* 독립 실행형 Spark 인스턴스 및 단일 노드 Hadoop(HDFS, Yarn)
* JupyterHub - R, Python, PySpark, Julia 커널을 지원하는 다중 사용자 Jupyter Notebook 서버
* Azure Storage 탐색기
* Azure 리소스 관리를 위한 Azure CLI(명령줄 인터페이스)
* 기계 학습 도구
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 지원하는 속성 기계 학습 시스템
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): 기능이 향상된 빠르고 정확한 트리 구현을 제공하는 도구
  * [Rattle](http://rattle.togaware.com/): R에서 데이터 분석 및 기계 학습을 쉽게 시작할 수 있도록 하는 그래픽 도구
  * [LightGBM](https://github.com/Microsoft/LightGBM): 빠른 분산형 고성능 그라데이션 향상 프레임워크
* Java, Python, node.js, Ruby, PHP의 Azure SDK
* Azure Machine Learning 및 기타 Azure 서비스에서 사용하기 위한 R 및 Python의 라이브러리
* 개발 도구 및 편집자(RStudio, PyCharm, IntelliJ, Emacs, vim)


데이터 과학을 수행하려면 일련의 작업에 대해 다음 작업을 반복합니다.

1. 데이터 찾기, 로드 및 전처리
2. 모델 빌드 및 테스트
3. 지능형 응용 프로그램에서 사용하기 위해 모델 배포

데이터 과학자는 다양한 도구를 사용하여 이러한 태스크를 완료합니다. 소프트웨어의 적합한 버전을 찾은 다음 다운로드하여 컴파일하고 설치하는 데 시간이 상당히 걸릴 수 있습니다.

Linux용 데이터 과학 Virtual Machine을 사용하면 이러한 부담을 상당히 줄일 수 있습니다. Linux 데이터 과학 가상 컴퓨터를 사용하여 분석 프로젝트를 바로 시작합니다. R, Python, SQL, Java 및 C++ 등의 다양한 언어로 작업을 수행할 수 있습니다. VM에 포함된 Azure SDK를 통해 Linux의 다양한 서비스를 사용하여 Microsoft 클라우드 플랫폼용으로 응용 프로그램을 빌드할 수 있습니다. 또한 Ruby, Perl, PHP 및 node.js와 같이 미리 설치된 다른 언어에 액세스해야 합니다.

이 데이터 과학 VM 이미지에 대한 소프트웨어 요금은 부과되지 않습니다. 프로비전하는 가상 머신의 크기를 기반으로 평가되는 Azure 하드웨어 사용 요금만 지불합니다. 계산 요금에 대한 자세한 내용은 [Azure Marketplace의 VM 목록 페이지](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)를 참조하세요.

## <a name="other-versions-of-the-data-science-virtual-machine"></a>다른 버전의 데이터 과학 Virtual Machine
[CentOS](linux-dsvm-intro.md) 이미지도 Ubuntu 이미지와 같은 많은 도구와 함께 사용할 수 있습니다. [Windows](provision-vm.md) 이미지도 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
Linux용 데이터 과학 Virtual Machine을 만들려면 먼저 Azure 구독이 있어야 합니다. 다운로드하려면 [Azure 평가판 받기](https://azure.microsoft.com/free/)를 참조하세요.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Linux용 데이터 과학 Virtual Machine 만들기
Linux용 데이터 과학 Virtual Machine의 인스턴스를 만드는 단계는 다음과 같습니다.

1. [Azure 포털](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)에서 가상 머신 목록으로 이동합니다.
2. 아래의 **만들기**를 클릭하여 마법사를 실행합니다.![configure-data-science-vm](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
3. 다음 섹션에서는 Microsoft 데이터 과학 Virtual Machine을 만드는 데 사용되는 마법사의 각 단계(위 그림의 오른쪽에 열거되어 있음)에 필요한 내용을 입력합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.
   
   a. **기본 사항**:
   
   * **이름**: 만들려는 데이터 과학 서버 이름
   * **사용자 이름**: 첫 번째 계정 로그인 ID입니다.
   * **암호**: 첫 번째 계정 암호입니다. 암호 대신 SSH 공개 키를 사용할 수 있습니다.
   * **구독**: 둘 이상의 구독을 보유한 경우, 컴퓨터를 만들고 요금을 청구할 구독 하나를 선택합니다. 이 구독에 대한 리소스 만들기 권한이 있어야 합니다.
   * **리소스 그룹**: 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
   * **위치**: 가장 적합한 데이터 센터를 선택합니다. 이 위치는 대개 대부분의 데이터가 저장되어 있는 위치이거나 사용자의 실제 위치에 가장 가까운 위치(이 경우 네트워크에 가장 빠르게 액세스할 수 있음)입니다.
   
   나. **크기**:
   
   * 기능 요구 사항과 비용 제약 조건에 부합하는 서버 유형 중 하나를 선택합니다. **모두 보기** 를 선택하면 더 많은 VM 크기 선택 항목을 확인할 수 있습니다. GPU 학습을 위한 NC 또는 ND 클래스 VM을 선택합니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 페이지에는 GPU가 있는 영역이 나열됩니다.
   
   다. **설정**:
   
   * **디스크 유형**: SSD(반도체 드라이브)를 선호하는 경우 **프리미엄**을 선택합니다. 그렇지 않은 경우에는 **표준**을 선택합니다. GPU VM에는 표준 디스크가 필요합니다.
   * **저장소 계정**: 구독에서 새 Azure Storage 계정을 만들거나, 마법사의 **기본 사항** 단계에서 선택했던 동일 위치의 기존 계정을 사용할 수 있습니다.
   * **기타 매개 변수**: 대부분의 경우에는 기본값만 사용합니다. 기본값 이외의 값을 살펴보려면 정보 링크를 가리켜 특정 필드에 대한 도움말을 확인합니다.
   
   d. **요약**:
   
   * 입력한 모든 정보가 올바른지 확인합니다.
   
   e. **구입**:
   
   * 프로비전을 시작하려면 **구입**을 클릭합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다.

프로비전에는 5-10분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Linux용 데이터 과학 Virtual Machine에 액세스하는 방법

세 가지 메서드를 사용하여 Ubuntu DSVM에 액세스할 수 있습니다.
1. 터미널 세션에 대한 SSH
2. 그래픽 세션에 대한 X2Go
3. Jupyter 노트북에 대한 JupyterHub 및 JupyterLab

### <a name="ssh"></a>SSH

VM을 만든 후 SSH를 사용하여 해당 VM에 로그인할 수 있습니다. 3단계의 **기본 사항** 섹션에서 만든 계정 자격 증명을 텍스트 셸 인터페이스용으로 사용합니다. Windows에서는 [Putty](http://www.putty.org)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다. 그래픽 데스크톱(X Windows 시스템)을 사용하려는 경우 Putty에서 X11 전달을 사용하거나 X2Go 클라이언트를 설치할 수 있습니다.

> [!NOTE]
> 테스트 결과 X2Go 클라이언트의 성능이 X11 전달보다 더 우수했습니다. 그래픽 데스크톱 인터페이스에서는 X2Go 클라이언트를 사용하는 것이 좋습니다.
> 
> 

### <a name="x2go"></a>X2Go
Linux VM은 이미 X2Go 서버에 프로비전되어 있어 클라이언트 연결을 사용할 수 있습니다. Linux VM 그래픽 데스크톱에 연결하려면 클라이언트에서 다음 절차를 완료합니다.

1. 사용 중인 클라이언트 플랫폼용 X2Go 클라이언트를 [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)에서 다운로드하여 설치합니다.    
2. X2Go 클라이언트를 실행하고 **새 세션**을 선택합니다. 여러 탭이 있는 구성 창이 열립니다. 다음 구성 매개 변수를 입력합니다.
   * **세션 탭**:
     * **호스트**: Linux 데이터 과학 VM의 호스트 이름 또는 프로그램 IP 주소입니다.
     * **로그인**: Linux VM의 사용자 이름입니다.
     * **SSH 포트**: 기본값 22를 그대로 사용합니다.
     * **세션 유형**: 값을 XFCE로 변경합니다. 현재 Linux VM은 XFCE 데스크톱만 지원합니다.
   * **미디어 탭**: 사운드 지원 및 클라이언트 인쇄를 사용하지 않으려면 해제할 수 있습니다.
   * **공유 폴더**: 클라이언트 컴퓨터의 디렉터리를 Linux VM에 탑재하려면 이 탭에서 VM과 공유하려는 클라이언트 컴퓨터 디렉터리를 추가합니다.

X2Go 클라이언트를 통해 XFCE 그래픽 데스크톱 또는 SSH 클라이언트를 사용하여 VM에 로그인하고 나면 VM에 설치 및 구성된 도구를 사용할 수 있습니다. XFCE에는 다양한 도구에 대한 응용 프로그램 메뉴 바로 가기와 바탕 화면 아이콘이 표시됩니다.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub 및 JupyterLab

Ubuntu DSVM은 [JupyterHub](https://github.com/jupyterhub/jupyterhub), 다중 사용자 Jupyter 서버를 실행합니다. 연결하려면 랩톱 또는 데스크톱에서 https://your-vm-ip:8000으로 이동하고, VM을 만들고 로그인하는 데 사용한 사용자 이름 및 암호를 입력합니다. 다양한 샘플 노트북을 사용하여 찾아보고 체험할 수 있습니다.

Jupyter 노트북의 차세대 JupyterLab 및 JupyterHub도 제공됩니다. 액세스하려면 JupyterHub에 로그인한 다음, https://your-vm-ip:8000/lab URL로 이동합니다. /etc/jupyterhub/jupyterhub_config.py에 이 줄을 추가하여 기본 노트북 서버로 JupyterLab을 설정할 수 있습니다.

    c.Spawner.default_url = '/lab'

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Linux용 데이터 과학 Virtual Machine에 설치된 도구
### <a name="deep-learning-libraries"></a>심층 학습 라이브러리

#### <a name="cntk"></a>CNTK
Microsoft Cognitive Toolkit는 오픈 소스, 심층 학습 도구 키트입니다. Python 바인딩은 루트 및 py35 Conda 환경에서 사용할 수 있습니다. 이미 PATH에 포함된 명령줄 도구(cntk)도 있습니다.

샘플 Python 노트북은 JupyterHub에서 사용할 수 있습니다. 명령줄에서 기본 샘플을 실행하려면 셸에서 다음 명령을 실행합니다.

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

자세한 내용은 [GitHub](https://github.com/Microsoft/CNTK)의 CNTK 섹션과 [CNTK 위키](https://github.com/Microsoft/CNTK/wiki)를 참조하세요.

#### <a name="caffe"></a>Caffe
Caffe는 Berkeley Vision and Learning Center의 심층 학습 프레임워크입니다. /opt/caffe에서 사용할 수 있습니다. 예제는 /opt/caffe/examples에서 찾을 수 있습니다.

#### <a name="caffe2"></a>Caffe2
Caffe2는 Caffe를 기반으로 제작된 Facebook의 심층 학습 프레임워크입니다. Python 2.7의 Conda 루트 환경에서 Caffe2를 사용할 수 있습니다. Caffe2를 활성화하려면 셸에서 다음을 실행합니다.

    source /anaconda/bin/activate root

JupyterHub에서 몇 가지 예제 Notebook이 제공됩니다.

#### <a name="h2o"></a>H2O
H2O는 빠른 메모리 내 분산형 기계 학습 및 예측 분석 플랫폼입니다. Python 패키지는 루트 및 py35 Anaconda 환경 둘 다에 설치됩니다. R 패키지도 설치됩니다. 실행 명령줄에서 H2O 를 시작하려면 `java -jar /dsvm/tools/h2o/current/h2o.jar`을 실행합니다. 다양한 [명령줄 옵션](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line)을 구성할 수 있습니다. 흐름 웹 UI는 시작할 http://localhost:54321로 이동하여 액세스할 수 있습니다. 샘플 노트북은 JupyterHub에서도 사용할 수 있습니다.

#### <a name="keras"></a>Keras
Keras는 TensorFlow 또는 Theano에서 실행될 수 있는 Python의 고급 신경망 네트워크 API입니다. 루트 및 py35 Python 환경에서 사용할 수 있습니다. 

#### <a name="mxnet"></a>MXNet
MXNet은 효율성과 유연성을 위해 디자인된 심층 학습 프레임워크입니다. DSVM에 R 및 Python 바인딩이 포함되어 있습니다. 샘플 노트북은 JupyterHub에 포함되어 있고 샘플 코드는 /dsvm/samples/mxnet에서 사용할 수 있습니다.

#### <a name="nvidia-digits"></a>NVIDIA DIGITS
DIGITS라고도 하는 NVIDIA 심층 학습 GPU 교육 시스템은 데이터 관리, GPU 시스템에서의 신경망 네트워크 설계 및 교육, 고급 시각화를 사용한 실시간 성능 모니터링 등의 일반적인 심층 학습 작업을 간소화하기 위한 시스템입니다. 

DIGITS는 digits라는 서비스로 사용할 수 있습니다. 이 서비스를 시작하고 http://localhost:5000으로 이동하여 시작합니다.

DIGITS는 Conda 루트 환경에서 Python 모듈로도 설치됩니다.

#### <a name="tensorflow"></a>TensorFlow
TensorFlow는 Google의 심층 학습 라이브러리입니다. 데이터 흐름 그래프를 사용한 숫자 계산을 위한 오픈 소스 소프트웨어 라이브러리입니다. TensorFlow는 py35 Python 환경에서 사용할 수 있고, 일부 샘플 노트북이 JupyterHub에 포함되어 있습니다.

#### <a name="theano"></a>Theano
Theano는 효율적인 숫자 계산을 위한 Python 라이브러리입니다. 루트 및 py35 Python 환경에서 사용할 수 있습니다. 

#### <a name="torch"></a>Torch
Torch는 기계 학습 알고리즘을 광범위하게 지원하는 공학용 계산 프레임워크입니다. /dsvm/tools/torch에서 사용할 수 있으며 대화형 세션 및 luarocks 패키지 관리자는 명령줄에서 사용할 수 있습니다. 예제는 /dsvm/samples/torch에서 사용할 수 있습니다.

PyTorch는 루트 Anaconda 환경에서도 사용할 수 있습니다. 예제는 /dsvm/samples/pytorch에 있습니다.

### <a name="microsoft-r-server"></a>Microsoft R 서버
R은 가장 많이 사용되는 데이터 분석 및 Machine Learning 언어 중 하나입니다. 분석에 R을 사용하려는 경우 VM에서 MRO(Microsoft R Open) 및 MKL(Math Kernel Library)이 포함된 MRS(Microsoft R Server)를 사용합니다. MKL은 분석 알고리즘에 공통되는 수학 연산을 최적화합니다. MRO는 CRAN-R과 완벽하게 호환되며 CRAN에 게시된 모든 R 라이브러리를 MRO에 설치할 수 있습니다. MRS는 R 모델의 크기 조정 및 운영을 웹 서비스에 제공합니다. RStudio, vi 또는 Emacs 등의 기본 편집기 중 하나에서 R 프로그램을 편집할 수 있습니다. Emacs 편집기를 사용하려는 사전 설치되어 있으므로 사용할 수 있습니다. Emacs 패키지 ESS(Emacs Speaks Statistics)는 Emacs 편집기 내에서 R 파일을 편리하게 사용할 수 있도록 합니다.

R 콘솔을 시작하려면 셸에서 **R**만 입력하면 됩니다. 이렇게 하면 대화형 환경으로 이동됩니다. R 프로그램을 개발하려면 일반적으로 Emacs, vi 등의 편집기를 사용한 다음 R 내에서 스크립트를 실행합니다. RStudio를 통해 R 프로그램을 개발하기 위한 전체 그래픽 IDE 환경을 사용할 수 있습니다.

또한 원하는 경우 [상위 20개 R 패키지](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) 를 설치하기 위해 R 스크립트를 사용할 수도 있습니다. 앞에서 설명한 것처럼 R 대화형 인터페이스를 표시한 다음 셸에 **R** 을 입력하여 이 스크립트를 실행할 수 있습니다.  

### <a name="python"></a>파이썬
Anaconda Python은 Python 2.7 및 3.5 환경과 함께 설치됩니다. 2.7 환경을 _루트_라고 하며, 3.5 환경을 _py35_라고 합니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다. 

py35 환경은 기본값입니다. 루트(2.7) 환경을 활성화하려면:

    source activate root

py35 환경을 다시 활성화하려면:

    source activate py35

Python 대화형 세션을 호출하려는 경우 셸에 **python** 만 입력하면 됩니다. 

```conda``` 또는 ````pip````를 사용하여 추가 Python 라이브러리를 설치합니다. pip의 경우 기본값을 원하지 않는 경우 적절한 환경을 먼저 활성화합니다.

    source activate root
    pip install <package>

또는 전체 경로를 pip로 지정합니다.

    /anaconda/bin/pip install <package>
    
conda의 경우 환경 이름을 항상 지정해야 합니다(_py35_ 또는 _루트_).

    conda install <package> -n py35

그래픽 인터페이스를 사용 중이거나 X11 전달이 설정된 경우 **pycharm**을 입력하면 PyCharm Python IDE를 시작할 수 있습니다. 기본 텍스트 편집기를 사용할 수 있습니다. 또한 Anaconda Python 배포에 번들로 포함된 Python IDE인 Spyder를 사용할 수도 있습니다. Spyder를 사용하려면 그래픽 데스크톱 또는 X11 전달이 필요합니다. 그래픽 데스크톱에 Spyder에 대한 바로 가기가 제공됩니다.

### <a name="jupyter-notebook"></a>Jupyter Notebook
Anaconda 배포는 코드 및 분석을 공유하는 환경인 Jupyter Notebook도 제공됩니다. JupyterHub을 통해 Jupyter Notebook에 액세스합니다. 로컬 Linux 사용자 이름 및 암호를 사용하여 로그인합니다.

Jupyter Notebook 서버는 Python 2, Python 3 및 R 커널을 사용하여 미리 구성되어 있습니다. 브라우저를 시작하여 노트북 서버에 액세스하는 데 사용할 수 있는 "Jupyter Notebook"이라는 바탕 화면 아이콘이 있습니다. SSH 또는 X2Go 클라이언트를 통해 VM을 사용하는 경우 [https://localhost:8000/](https://localhost:8000/)을 방문하여 Jupyter Notebook 서버에 액세스할 수도 있습니다.

> [!NOTE]
> 인증서 경고가 나타나는 경우 계속 진행하세요.
> 
> 

모든 호스트에서 Jupyter Notebook 서버에 액세스할 수 있습니다. *https://\<VM DNS 이름 또는 IP 주소\>:8000/* 만 입력하면 됩니다.

> [!NOTE]
> VM이 프로비전될 때 포트 8000이 방화벽에 기본적으로 열립니다.
> 
> 

Python과 R로 각각 작성된 샘플 노트북이 패키지로 제공됩니다. 로컬 Linux 사용자 이름 및 암호를 사용하여 Jupyter Notebook에 인증하고 나면 노트북 홈페이지에서 샘플 링크를 확인할 수 있습니다. **새로 만들기**를 선택하고 적절한 언어 커널을 선택하여 새 Notebook을 만들 수 있습니다. **새로 만들기** 단추가 표시되지 않으면 왼쪽 위에서 **Jupyter** 아이콘을 클릭하여 Notebook 서버의 홈페이지로 이동합니다.

### <a name="apache-spark-standalone"></a>Apache Spark 독립 실행형 
Apache Spark의 독립 실행형 인스턴스는 Linux DSVM에 미리 설치되어 대형 클러스터에서 테스트 및 배포하기 전에 먼저 Spark 응용 프로그램을 로컬로 개발하는 데 도움을 줍니다. Jupyter 커널을 통해 PySpark 프로그램을 실행할 수 있습니다. Jupyter를 열고 **New** 단추를 클릭하면 사용할 수 있는 커널의 목록이 표시됩니다. "Spark - Python"은 Python 언어를 사용하여 Spark 응용 프로그램을 빌드할 수 있도록 하는 PySpark 커널입니다. 또한 PyCharm 또는 Spyder와 같은 Python IDE를 사용하여 Spark 프로그램을 빌드할 수도 있습니다. 이는 독립 실행형 인스턴스이므로 Spark 스택은 호출 클라이언트 프로그램 내에서 실행합니다. 이렇게 하면 Spark 클러스터에서 개발에 비해 문제를 더 빠르고 쉽게 해결할 수 있습니다. 

샘플 PySpark Notebook은 Jupyter($HOME/notebooks/SparkML/pySpark)의 홈 디렉터리에 있는 "SparkML" 디렉터리에서 찾을 수 있는 Jupyter에서 제공됩니다. 

Spark용 R에서 프로그래밍하는 경우 Microsoft R Server, SparkR 또는 sparklyr을 사용할 수 있습니다. 

Microsoft R Server에서 Spark 컨텍스트를 실행하기 전에 로컬 단일 노드 Hadoop HDFS 및 Yarn 인스턴스를 사용하도록 설정하려면 일회성 설정 단계를 수행해야 합니다. 기본적으로 Hadoop 서비스는 설치되지만 DSVM에서 사용하지 않도록 설정됩니다. 활성화하기 위해 처음으로 루트로 다음 명령을 실행해야 합니다.

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

필요하지 않을 때 ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn````을 실행하여 서비스와 관련된 Hadoop을 중지할 수 있습니다. 원격 Spark 컨텍스트(DSVM의 독립 실행형 Spark 인스턴스)에서 MRS를 개발 및 테스트하는 방법을 보여주는 샘플은 `/dsvm/samples/MRS` 디렉터리에서 제공되고 사용할 수 있습니다. 

### <a name="ides-and-editors"></a>IDE 및 편집기
여러 코드 편집기 중에서 선택할 수 있습니다. vi/VIM, Emacs, PyCharm, RStudio 및 IntelliJ를 포함합니다. IntelliJ, RStudio 및 PyCharm은 그래픽 편집기이며 그래픽 데스크톱에 로그인해야 사용할 수 있습니다. 이러한 편집기는 데스크톱 및 응용 프로그램 바로 가기 메뉴를 사용하여 시작할 수 있습니다.

**VIM** 및 **Emacs**는 텍스트 기반 편집기입니다. Emacs의 경우 Emacs 편집기 내에서 R 작업을 쉽게 수행할 수 있도록 해주는 ESS(Emacs Speaks Statistics)라는 추가 기능 패키지가 설치되어 있습니다. 자세한 내용은 [ESS](http://ess.r-project.org/)를 참조하세요.

**LaTex** 는 texlive 패키지를 통해 Emacs 추가 기능 [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) 패키지와 함께 설치되며 Emacs 내에서의 LaTex 문서 작성을 단순화합니다.  

### <a name="databases"></a>데이터베이스

#### <a name="graphical-sql-client"></a>그래픽 SQL 클라이언트
Microsoft SQL Server, MySQL 등의 다양한 데이터베이스에 연결하고 SQL 쿼리를 실행할 수 있도록 그래픽 SQL 클라이언트인 **SQuirrel SQL**이 제공됩니다. SQuirrel SQL은 그래픽 데스크톱 세션(예: X2Go 클라이언트 사용)에서 실행할 수 있습니다. SQuirrel SQL을 호출하려는 경우 바탕 화면의 아이콘에서 시작하거나 셸에서 다음 명령을 실행하면 됩니다.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

SQuirrel SQL을 처음 사용하기 전에 드라이버 및 데이터베이스 별칭을 설정합니다. JDBC 드라이버는 다음 위치에 있습니다.

*/usr/share/java/jdbcdrivers*

자세한 내용은 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)을 참조하세요.

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Microsoft SQL Server에 액세스하기 위한 명령줄 도구
SQL Server용 ODBC 드라이버 패키지에서는 다음의 두 가지 명령줄 도구도 제공됩니다.

**bcp**: bcp 유틸리티는 Microsoft SQL Server 인스턴스와 사용자 지정 형식의 데이터 파일 간에 데이터를 대량 복사합니다. bcp 유틸리티를 사용하여 SQL Server 테이블에 많은 수의 새 행을 가져오거나 테이블에서 데이터 파일로 데이터를 내보낼 수 있습니다. 데이터를 테이블로 가져오려면 해당 테이블용으로 만들어진 형식 파일을 사용하거나, 테이블의 구조 및 테이블의 열에 사용 가능한 데이터 형식을 파악해야 합니다.

자세한 내용은 [bcp를 사용하여 연결](https://msdn.microsoft.com/library/hh568446.aspx)을 참조하세요.

**sqlcmd**: sqlcmd 유틸리티를 사용하면 명령 프롬프트에 Transact-SQL 문을 비롯하여 시스템 프로시저와 스크립트 파일을 입력할 수 있습니다. 이 유틸리티는 ODBC를 사용하여 TRANSACT-SQL 일괄 처리를 실행합니다.

자세한 내용은 [sqlcmd를 사용하여 연결](https://msdn.microsoft.com/library/hh568447.aspx)을 참조하세요.

> [!NOTE]
> 이 유틸리티는 Linux 플랫폼과 Windows 플랫폼에서 다소 다릅니다. 자세한 내용은 (영문) 설명서를 참조하세요.
> 
> 

#### <a name="database-access-libraries"></a>데이터베이스 액세스 라이브러리
R 및 Python에는 데이터베이스에 액세스하는 데 사용할 수 있는 라이브러리가 있습니다.

* R에서 **RODBC** 패키지 또는 **dplyr** 패키지를 사용하면 데이터베이스 서버에서 SQL 문을 쿼리 또는 실행할 수 있습니다.
* Python에서 **pyodbc** 라이브러리는 ODBC를 사용한 데이터베이스 액세스를 기본 계층으로 제공합니다.  

### <a name="azure-tools"></a>Azure 도구
다음 Azure 도구가 VM에 설치됩니다.

* **Azure 명령줄 인터페이스**: Azure CLI를 사용하여 셸 명령을 통해 Azure 리소스를 만들고 관리할 수 있습니다. Azure 도구를 호출하려는 경우 **azure help**만 입력하면 됩니다. 자세한 내용은 [Azure CLI 설명서 페이지](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)를 참조하세요.
* **Microsoft Azure Storage 탐색기**: Microsoft Azure Storage 탐색기는 Azure Storage 계정에 저장한 개체를 찾아보고 Azure Blob에서 데이터를 업로드 및 다운로드하는 데 사용되는 그래픽 도구입니다. 바탕 화면 바로 가기 아이콘을 사용하여 Storage Explorer에 액세스할 수 있습니다. **StorageExplorer**를 입력하면 셸 프롬프트에서 Storage Explorer를 호출할 수 있습니다. 이렇게 하려면 X2Go 클라이언트에서 로그인하거나 X11 전달을 설정해야 합니다.
* **Azure 라이브러리**: 아래에는 미리 설치된 라이브러리 중 몇 가지가 나와 있습니다.
  
  * **Python**: Python으로 작성된 Azure 관련 라이브러리 **azure**, **azureml**, **pydocumentdb** 및 **pyodbc**가 설치되어 있습니다. 처음 세 개의 라이브러리를 사용하면 Azure Storage 서비스, Azure Machine Learning 및 Azure Cosmos DB(Azure의 NoSQL 데이터베이스)에 액세스할 수 있습니다. 네 번째 라이브러리인 pyodbc를 SQL Serve용 Microsoft ODBC 드라이버와 함께 사용하면 ODBC 인터페이스를 사용하여 Python에서 SQL Server, Azure SQL Database 및 Azure SQL Data Warehouse에 액세스할 수 있습니다. **pip list** 를 입력하면 나열된 라이브러리를 모두 확인할 수 있습니다. Python 2.7 및 3.5 환경 모두에서 이 명령을 실행해야 합니다.
  * **R**: R로 작성된 Azure 관련 라이브러리 **AzureML** 및 **RODBC**가 설치되어 있습니다.
  * **Java**: VM의 **/dsvm/sdk/AzureSDKJava** 디렉터리에서 Azure Java 라이브러리 목록을 찾을 수 있습니다. 핵심 라이브러리는 Azure Storage 및 관리 API, Azure Cosmos DB, SQL Server용 JDBC 드라이버입니다.  

미리 설치된 Firefox 브라우저에서 [Azure 포털](https://portal.azure.com) 에 액세스할 수 있습니다. Azure 포털에서 Azure 리소스를 작성, 관리 및 모니터링할 수 있습니다.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning은 예측 분석 솔루션을 빌드, 배포 및 공유할 수 있는 완전 관리형 클라우드 서비스입니다. Azure Machine Learning Studio에서 실험 및 모델을 빌드합니다. 데이터 과학 Virtual Machine의 웹 브라우저에서 [Microsoft Azure Machine Learning](https://studio.azureml.net)을 방문하여 액세스할 수도 있습니다.

Azure Machine Learning Studio에 로그인하고 나면 Machine Learning 알고리즘용 논리적 흐름을 빌드할 수 있는 실험 캔버스에 액세스할 수 있습니다. 또한 Azure Machine Learning에서 호스트되는 Jupyter Notebook에도 액세스할 수 있으며, Machine Learning Studio의 실험을 원활하게 진행할 수 있습니다. 빌드한 기계 학습 모델은 웹 서비스 인터페이스에 래핑하여 운용 가능하도록 설정합니다. 이렇게 하면 모든 언어로 작성된 클라이언트가 기계 학습 모델에서 예측을 호출할 수 있습니다. 자세한 내용은 [Machine Learning 설명서](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

VM에서 R 또는 Python으로 모델을 빌드한 다음 Azure Machine Learning에서 프로덕션 환경으로 배포할 수도 있습니다. R 및 Python에는 이 기능을 사용하는 데 필요한 라이브러리(각각 **AzureML**과 **azureml**)가 설치되어 있습니다.

R 및 Python으로 작성된 모델을 Azure Machine Learning에 배포하는 방법에 대한 자세한 내용은 [데이터 과학 Virtual Machine에서 수행할 수 있는 10가지 작업](vm-do-ten-things.md) , 특히 "R 또는 Python을 사용하여 모델을 빌드하고 Azure Machine Learning을 사용하여 운용 가능하도록 설정" 섹션을 참조하세요.

> [!NOTE]
> 이러한 지침은 데이터 과학 VM의 Windows 버전에 대해 작성되었습니다. 하지만 Azure Machine Learning에 모델을 배포하는 방법에 대해 제공되는 정보는 Linux VM에도 적용할 수 있습니다.
> 
> 

### <a name="machine-learning-tools"></a>기계 학습 도구
VM에는 로컬에서 미리 컴파일되어 미리 설치된 몇 가지 기계 학습 도구 및 알고리즘이 포함되어 있습니다. 내용은 다음과 같습니다.

* **Vowpal Wabbit**: 속성 온라인 학습 알고리즘입니다.
* **xgboost**: 최적화되고 향상된 트리 알고리즘을 제공하는 도구입니다.
* **Rattle**: 쉬운 데이터 탐색 및 모델링을 위한 R 기반 그래픽 도구입니다.
* **Python**: Anaconda Python에서는 Scikit-learn 등의 라이브러리가 포함된 기계 학습 알고리즘이 번들로 제공됩니다. `pip install` 하여 다른 라이브러리를 설치할 수 있습니다.
* **LightGBM**: 의사 결정 트리 알고리즘을 기준으로 하는 빠른 분산형 고성능 그라데이션 향상 프레임워크입니다.
* **R**: R에서는 다양한 기계 학습 기능이 포함된 라이브러리를 사용할 수 있습니다. 미리 설치된 라이브러리에는 lm, glm, randomForest, rpart 등이 있습니다. 다음 명령을 실행하면 다른 라이브러리를 설치할 수 있습니다.
  
        install.packages(<lib name>)

아래에서는 목록의 처음 3개 기계 학습 도구에 대해 추가로 설명합니다.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit은 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 사용하는 기계 학습 시스템입니다.

기본적인 예제에서 도구를 실행하려면 다음 명령을 사용합니다.

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

해당 디렉터리에는 더 큰 다른 데모도 있습니다. VW에 대한 자세한 내용은 [GitHub의 이 섹션](https://github.com/JohnLangford/vowpal_wabbit)과 [Vowpal Wabbit 위키](https://github.com/JohnLangford/vowpal_wabbit/wiki)를 참조하세요.

#### <a name="xgboost"></a>XGBoost
향상된 (트리) 알고리즘을 위해 디자인되고 최적화된 라이브러리입니다. 이 라이브러리는 확장, 이동성 및 정확도가 높은 대규모 트리 향상 기능을 제공하는 데 필요한 최대 한계까지 기계의 계산 제한을 높이는 데 사용되며

R 라이브러리뿐만 아니라 명령줄로도 제공됩니다.

R에서 이 라이브러리를 사용하려면 셸에서 **R** 을 입력하여 대화형 R 세션을 시작하고 라이브러리를 로드합니다.

R 프롬프트에서 실행할 수 있는 간단한 예제는 다음과 같습니다.

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

xgboost 명령줄을 실행하려면 셸에서 다음 명령을 실행합니다.

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


.model 파일이 지정된 디렉터리에 기록됩니다. 이 데모 예제에 대한 정보는 [GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)에서 찾을 수 있습니다.

xgboost에 대한 자세한 내용은 [xgboost 설명서 페이지](https://xgboost.readthedocs.org/en/latest/) 및 해당 [GitHub 리포지토리](https://github.com/dmlc/xgboost)를 참조하세요.

#### <a name="rattle"></a>Rattle
Rattle(**R** **A**nalytical **T**ool **T**o **L**earn **E**asily)은 GUI 기반 데이터 탐색 및 모델링 기능을 사용합니다. Rattle은 통계 및 시각적 데이터 요약을 표시하고, 즉시 모델링할 수 있도록 데이터를 변환하고, 해당 데이터에서 감독되거나 감독되지 않는 모델을 빌드하고, 모델의 성능을 그래픽으로 표시하고, 새 데이터 집합의 점수를 매깁니다. 또한 UI에서 작업을 복제하여 R에서 직접 실행하거나 추가 분석을 위한 시작점으로 사용할 수 있는 R 코드를 생성할 수도 있습니다.

Rattle을 실행하려면 그래픽 데스크톱 로그인 세션을 사용 중이어야 합니다. 터미널에서 ```R``` 을 입력하여 R 환경을 설정합니다. R 프롬프트에서 다음 명령을 입력합니다.

    library(rattle)
    rattle()

이제 그래픽 인터페이스가 열리고 일련의 탭이 표시됩니다. 아래에서는 샘플 날씨 데이터 집합을 사용하여 모델을 빌드하는 데 필요한 Rattle의 빠른 시작 단계를 설명합니다. 아래 일부 단계에서 아직 시스템에 없는 필수 R 패키지를 자동으로 설치 및 로드할 것인지 묻는 메시지가 나타납니다.

> [!NOTE]
> 시스템 디렉터리(기본값)에서 패키지를 설치하는 액세스 권한이 없는 경우 R 콘솔 창에 패키지를 개인 라이브러리에 설치할지 여부를 묻는 메시지가 나타납니다. 이러한 메시지가 표시되면 *y* 로 응답합니다.
> 
> 

1. **실행**을 클릭합니다.
2. 예제 날씨 데이터 집합을 사용할지 묻는 팝업 대화 상자가 표시됩니다. **예** 를 클릭하여 예제를 로드합니다.
3. **모델** 탭을 클릭합니다.
4. **실행** 을 클릭하여 의사 결정 트리를 빌드합니다.
5. **그리기** 를 클릭하여 의사 결정 트리를 표시합니다.
6. **포리스트** 라디오 단추를 클릭하고 **실행**을 클릭하여 임의 포리스트를 빌드합니다.
7. **평가** 탭을 클릭합니다.
8. **위험** 라디오 단추를 클릭하고 **실행**을 클릭하여 두 개의 위험(누적) 성능 도표를 표시합니다.
9. **로그** 탭을 클릭하여 위 작업에 해당하는 generate R 코드를 표시합니다.
   현재 Rattle 릴리스의 버그로 인해, 로그 텍스트의 *Export this log ...* 앞에 *#* 문자를 삽입해야 합니다.
10. **내보내기** 단추를 클릭하여 R 스크립트 파일 *weather_script.R*을 홈 폴더에 저장합니다.

이제 Rattle 및 R을 종료할 수 있습니다. 그러면 생성된 R 스크립트를 수정할 수도 있고, 언제든지 그대로 사용하여 Rattle UI 내에서 실행한 모든 작업을 반복할 수도 있습니다. 특히 R 초보자의 경우 이 손쉬운 방법을 사용하면 그래픽 인터페이스에서 분석 및 기계 학습을 신속하게 수행하는 동시에 R에서 코드를 자동으로 생성하여 수정 및/또는 학습할 수 있습니다.

## <a name="next-steps"></a>다음 단계
학습과 탐색을 계속하는 방법은 다음과 같습니다.

* [Linux용 데이터 과학 Virtual Machine의 데이터 과학](linux-dsvm-walkthrough.md) 연습은 여기에 프로비전된 Linux 데이터 과학을 사용하여 여러 가지 일반적인 데이터 과학 작업을 수행하는 방법을 보여 줍니다. 
* 이 문서에 설명된 도구를 사용하여 데이터 과학 VM에서 다양한 데이터 과학 도구를 살펴봅니다. 가상 머신 내의 셸에서 *dsvm-more-info* 를 실행하여 VM에 설치된 도구에 대한 기본 소개 및 자세한 내용을 참조할 수도 있습니다.  
* [팀 데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)를 사용하여 종단 간 분석 솔루션을 체계적으로 구축하는 방법을 알아봅니다.
* [Cortana 분석 갤러리](http://gallery.cortanaanalytics.com) 를 방문하여 Cortana 분석 Suite을 사용하는 기계 학습 및 데이터 분석 샘플을 확인합니다.

