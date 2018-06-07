---
title: Azure Machine Learning에 GPU를 사용하는 방법 | Microsoft Docs
description: 이 문서에서는 GPU(그래픽 처리 장치)를 사용하여 Azure Machine Learning Workbench에서 심층 신경망을 학습하는 방법을 설명합니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 1bd8f28b472361470fa8020149a9af0c690fbc52
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Azure Machine Learning에서 GPU를 사용하는 방법
GPU(그래픽 처리 장치)는 일반적으로 특정 심층 신경망 모델을 교육할 때 발생할 수 있는 계산 집약적 작업을 처리하는 데 널리 사용됩니다. GPU를 사용하면 모델의 교육 시간을 크게 줄일 수 있습니다. 이 문서에서는 실행 대상으로 GPU가 장착된 [DSVM(Data Science Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)을 사용하도록 Azure ML Workbench를 구성하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건
- 이 방법 가이드를 실행하려면 먼저 [Azure ML Workbench를 설치](../service/quickstart-installation.md)해야 합니다.
- NVidia GPU가 장착된 컴퓨터에 액세스할 수 있어야 합니다.
    - GPU가 장착된 로컬 컴퓨터(Windows 또는 macOS)에서 직접 스크립트를 실행할 수 있습니다.
    - GPU가 장착된 컴퓨터의 Docker 컨테이너에서도 스크립트를 실행할 수 있습니다.

## <a name="execute-in-local-environment-with-gpus"></a>GPU가 장착된 _로컬_ 환경에서 실행
GPU가 장착된 컴퓨터에 Azure ML Workbench를 설치한 후 _로컬_ 환경에 대해 실행할 수 있습니다. 로컬 환경은 다음과 같은 시스템일 수 있습니다.
- 실제 Windows 또는 macOS 컴퓨터.
- Azure NC 시리즈 VM 템플릿을 사용하여 프로비전된 DSVM 같은 Windows VM(Virtual Machine).

이 경우 Azure ML Workbench에서 특별히 구성할 것이 없습니다. 필요한 모든 드라이버, 도구 키트 및 GPU를 지원하는 Machine Learning 라이브러리를 로컬에 설치하기만 하면 됩니다. Python 런타임이 로컬 GPU 하드웨어에 직접 액세스할 수 있는 _로컬_ 환경에 대해 실행합니다.

1. AML Workbench를 엽니다. **파일**, **명령 프롬프트 열기**로 이동합니다. 
2. 명령줄에서 Microsoft Cognitive 도구 키트, TensorFlow 등 GPU를 지원하는 심층 학습 프레임워크를 설치합니다. 예: 

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. 심층 학습 라이브러리를 활용하는 Python 코드를 작성합니다.
4. 계산 환경으로 _로컬_을 선택하고 Python 코드를 실행합니다.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>GPU가 장착된 Linux VM의 _docker_ 환경에서 실행
Azure ML Workbench는 Azure Linux VM의 Docker에서 실행하는 것도 지원합니다. 여기서는 계산이 많은 작업을 강력한 가상 하드웨어에서 실행하고 작업이 완료되면 해제하여 사용한 만큼만 비용을 지불하는 효율적인 옵션이 제공됩니다. 원칙적으로 모든 Linux 가상 머신에서 GPU를 사용할 수 있지만, Ubuntu 기반 DSVM은 필수 CUDA 드라이버와 라이브러리가 미리 설치된 상태로 제공되므로 설치 과정이 훨씬 간편합니다. 아래 단계를 수행하세요.

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Azure에서 Ubuntu 기반 Linux 데이터 과학 Virtual Machine 만들기
1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털 왼쪽에서 **+ 새로 만들기**를 선택합니다.

3. 마켓플레이스에서 "Linux용 데이터 과학 Virtual Machine(Ubuntu)"을 검색합니다.

4. **만들기**를 클릭하고 Ubuntu DSVM을 만듭니다.

5. **기본 사항** 양식에 필수 정보를 입력합니다.
VM의 위치를 선택할 때 GPU VM은 특정 Azure 지역(예: **미국 중남부**)에서만 사용할 수 있다는 점에 주의해야 합니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.
확인을 클릭하여 **기본 사항** 정보를 저장합니다.

6. 가상 컴퓨터의 크기를 선택합니다. NVidia GPU 칩이 탑재되었다는 뜻의 접두사 NC가 붙은 VM 크기 중 하나를 선택합니다.  필요에 따라 **모두 보기**를 클릭하여 전체 목록을 볼 수 있습니다. [GPU가 장착된 Azure VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu)에 대해 자세히 알아보세요.

7. 나머지 설정을 완료하고 구매 정보를 검토합니다. 구매를 클릭하여 VM을 만듭니다. 가상 컴퓨터에 할당된 IP 주소를 기록해 둡니다. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Azure ML Workbench에서 새 프로젝트 만들기 
_TensorFlow를 사용하여 MNIST 분류_ 예제 또는 _CNTK를 사용하여 MNIST 데이터 집합 분류_ 예제를 사용할 수 있습니다.

### <a name="create-a-new-compute-target"></a>새 계산 대상 만들기
Azure ML Workbench에서 명령줄을 시작합니다. 다음 명령을 입력합니다. 아래 예제의 자리 표시자 텍스트를 사용자 고유의 이름, IP 주소, 사용자 이름 및 암호로 바꿉니다. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>GPU에 액세스하도록 Azure ML Workbench 구성
프로젝트로 돌아가서 **파일 보기**를 열고 **새로 고침** 단추를 누릅니다. 두 개의 새 구성 파일 `my_dsvm.compute` 및 `my_dsvm.runconfig`가 보일 것입니다.
 
`my_dsvm.compute` 파일을 엽니다. `baseDockerImage`를 `microsoft/mmlspark:plus-gpu-0.7.9`로 변경하고 새 줄 `nvidiaDocker: true`를 추가합니다. 그러면 파일에 다음 두 줄이 있을 것입니다.
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
이제 `my_dsvm.runconfig`를 열고, `Framework` 값을 `PySpark`에서 `Python`으로 변경합니다. 이 줄이 보이지 않으면 추가합니다. 기본값이 `PySpark`이기 때문입니다.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>참조 심층 학습 프레임워크 
`conda_dependencies.yml` 파일을 열고, 심층 학습 프레임워크 Python 패키지의 GPU 버전을 사용하고 있는지 확인합니다. 샘플 프로젝트에 CPU 버전이 나열되므로 이 변경 작업을 수행해야 합니다.

TensorFlow 예제: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Microsoft Cognitive 도구 키트 예제:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

다중 GPU VM의 성능을 개선하는 Microsoft Cognitive 도구 키트의 1비트 SGD 버전을 사용할 수도 있습니다. [1비트 SGD에 대한 라이선스 요구 사항](https://docs.microsoft.com/cognitive-toolkit/cntk-1bit-sgd-license)을 기록해 둡니다.

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>실행
이제 Python 스크립트를 실행할 준비가 되었습니다. `my_dsvm` 컨텍스트를 사용하여 Azure ML Workbench 내에서 스크립트를 실행할 수도 있고 명령줄에서 시작할 수도 있습니다.
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
GPU가 사용되고 있는지 확인하기 위해 실행 출력을 검사하여 다음과 같은 정보를 살펴봅니다.

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

축하합니다! 스크립트가 Docker 컨테이너를 통해 GPU의 강력한 성능을 활용했습니다!

## <a name="next-steps"></a>다음 단계
GPU를 사용하여 Azure ML 갤러리의 심층 신경망 교육을 가속화하는 예제를 살펴봅니다.
