---
title: Azure Machine Learning Workbench에서 Jupyter 노트북을 사용하는 방법 | Microsoft Docs
description: Azure Machine Learning Workbench에서 Jupyter 노트북 기능 사용 가이드
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 11d295734fd6d9180468709f0a25cca48b13df4d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench에서 Jupyter 노트북 사용

Azure Machine Learning Workbench는 Jupyter 노트북과의 통합을 통해 대화형 데이터 과학 실험을 지원합니다. 이 문서에서는 이 기능을 효과적으로 사용하여 대화형 데이터 과학 실험의 속도와 품질을 높이는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
- [Azure Machine Learning 계정을 만들고 Azure Machine Learning Workbench를 설치합니다](../service/quickstart-installation.md).
- [Jupyter 노트북](http://jupyter.org/)에 익숙해집니다. 이 문서는 Jupyter 사용 방법을 학습하는 내용을 포함하지 않습니다.

## <a name="jupyter-notebook-architecture"></a>Jupyter 노트북 아키텍처
전체적으로 볼 때, Jupyter Notebook 아키텍처에는 세 가지 구성 요소가 포함되어 있습니다. 각각은 여러 계산 환경에서 실행할 수 있습니다.

- **클라이언트**: 사용자 입력을 수신하고 렌더링된 출력을 표시합니다.
- **서버**: 노트북 파일(.ipynb 파일)을 호스트하는 웹 서버입니다.
- **커널**: 노트북 셀이 실행되는 런타임 환경입니다.

자세한 내용은 공식적인 [Jupyter 설명서](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html)를 참조하세요. 다음은 이 클라이언트, 서버 및 커널 아키텍처가 Azure Machine Learning의 구성 요소에 매핑되는 방식을 설명하는 다이어그램입니다.

![Jupyter 노트북 아키텍처](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Azure Machine Learning Workbench 노트북의 커널
프로젝트의 `aml_config` 폴더에서 실행 구성 및 계산 대상을 정의하여 Azure Machine Learning Workbench의 여러 커널에 액세스할 수 있습니다. `az ml computetarget attach` 명령을 실행하여 새로운 계산 대상을 추가하는 것은 새 커널을 추가하는 것과 동일합니다.

>[!NOTE]
>실행 구성 및 계산 대상에 대한 자세한 내용은 [Azure Machine Learning 실험 서비스 구성](experimentation-service-configuration.md)을 검토하세요.

### <a name="kernel-naming-convention"></a>커널 명명 규칙
Azure Machine Learning Workbench는 사용자 지정 Jupyter 커널을 생성합니다. 이러한 커널의 이름은 *\<프로젝트 이름> \<실행 구성 이름>*입니다. 예를 들어, _myIris_라는 프로젝트에 _docker-python_이라는 실행 구성이 있는 경우 Azure Machine Learning에서 *myIris docker-python*이라는 커널을 사용할 수 있습니다. Jupyter 노트북 **커널** 메뉴, **커널 변경** 하위 메뉴에서 실행 커널을 설정합니다. 실행 커널 이름이 메뉴 모음의 오른쪽 끝에 나타납니다.
 
현재 Azure Machine Learning에서 다음과 같은 종류의 커널을 지원합니다.

### <a name="local-python-kernel"></a>로컬 Python 커널
이 Python 커널은 로컬 컴퓨터에서 실행할 수 있습니다. 또한 Azure Machine Learning의 실행 기록 지원과 통합됩니다. 커널 이름은 일반적으로 *my_project_name local*입니다.

>[!NOTE]
>Python 3 커널은 사용하지 마세요. 기본적으로 Jupyter에서 제공하는 독립 실행형 커널로, Azure Machine Learning 기능과 통합되지 않습니다. 예를 들어, `%azureml` Jupyter 매직 함수는 "찾을 수 없음" 오류를 반환합니다. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Docker의 Python 커널(로컬 또는 원격)
이 Python 커널은 로컬 컴퓨터 또는 원격 Linux VM(가상 머신)의 Docker 컨테이너에서 실행됩니다. 커널 이름은 일반적으로 *my_project docker.*입니다. 연결된 `docker.runconfig` 파일에는 `Python`로 설정된 `Framework` 필드가 있습니다.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Docker의 PySpark 커널(로컬 또는 원격)
이 PySpark 커널은 로컬 컴퓨터 또는 원격 Linux VM의 Docker 컨테이너 내부에서 실행 중인 Spark 컨텍스트에서 스크립트를 실행합니다. 커널 이름은 일반적으로 *my_project docker*입니다. 연결된 `docker.runconfig` 파일에는 `PySpark`로 설정된 `Framework` 필드가 있습니다.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Azure HDInsight 클러스터의 PySpark 커널
이 커널은 프로젝트의 계산 대상으로 연결된 원격 Azure HDInsight 클러스터에서 실행됩니다. 커널 이름은 일반적으로 *my_project my_hdi*입니다. 

>[!IMPORTANT]
>이 커널을 사용하려면 HDI 계산 대상의 `.compute` 파일에서 `yarnDeployMode` 필드를 `client`로 변경해야 합니다(기본값은 `cluster`). 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench에서 Jupyter 서버 시작
Azure Machine Learning Workbench에서 **노트북** 탭을 통해 노트북에 액세스할 수 있습니다. _조리개 분류_ 샘플 프로젝트에는 `iris.ipynb` 샘플 Notebook이 포함되어 있습니다.

![노트북 탭](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Azure Machine Learning Workbench에서 노트북을 열면 **미리 보기 모드**의 자체 문서 탭에 노트북이 표시됩니다. 이 보기는 Jupyter 서버와 커널을 실행하지 않아도 되는 읽기 전용 보기입니다.

![노트북 미리 보기](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

**Notebook 서버 시작** 단추를 선택하면 Jupyter 서버가 시작되고 노트북이 **편집 모드**로 전환됩니다. 익숙한 Jupyter 노트북 사용자 인터페이스가 Workbench에 포함되어 나타납니다. 이제 **커널** 메뉴에서 커널을 설정하고 대화형 Notebook 세션을 시작할 수 있습니다. 

>[!NOTE]
>로컬이 아닌 커널을 처음으로 사용하는 경우 시작하는 데 1-2분 정도 걸릴 수 있습니다. 계산 대상이 준비되면 커널을 훨씬 빨리 시작할 수 있으므로 CLI 창에서 `az ml experiment prepare` 명령을 실행하여 계산 대상을 준비할 수 있습니다.

![편집 모드](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

완전한 대화형 Jupyter 노트북 환경입니다. Workbench **노트북** 탭 및 **파일** 탭에서 실행할 수 있는 일부 파일 작업을 제외한 모든 일반 노트북 작업 및 키보드 바로 가기는 이 창에서 지원됩니다.

## <a name="start-a-jupyter-server-from-the-command-line"></a>명령줄에서 Jupyter 서버 시작
명령줄 창에서 `az ml notebook start` 명령을 실행하여 노트북 세션을 시작할 수도 있습니다.
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
기본 브라우저가 자동으로 열리고 Jupyter 서버는 프로젝트 홈 디렉터리를 가리킵니다. CLI 창에 표시된 URL 및 토큰을 사용하여 다른 브라우저 창을 로컬로 열 수도 있습니다. 

![프로젝트 대시보드](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

이제 `.ipynb` 노트북 파일을 선택하여 열고, 커널을 설정하고(아직 설정되지 않은 경우), 대화형 세션을 시작할 수 있습니다.

![프로젝트 대시보드](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>매직 명령을 사용하여 실험 관리

노트북 셀에서 [매직 명령](http://ipython.readthedocs.io/en/stable/interactive/magics.html)을 사용하여 실행 기록을 추적하고 모델 또는 데이터 집합과 같은 출력을 저장할 수 있습니다.

개별 노트북 셀 실행을 추적하려면 `%azureml history on` 매직 명령을 사용합니다. 기록을 켜면 각 셀 실행이 실행 기록의 항목으로 표시됩니다.

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

셀 실행 추적을 끄려면 `%azureml history off` 매직 명령을 사용합니다.

`%azureml upload` 매직 명령을 사용하여 실행 중인 모델 및 데이터 파일을 저장할 수 있습니다. 저장된 개체는 실행 기록 보기에 출력으로 나타납니다.

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>출력은 *outputs* 폴더에 저장해야 합니다.

## <a name="next-steps"></a>다음 단계
- Jupyter 노트북 사용 방법은 [Jupyter 공식 설명서](http://jupyter-notebook.readthedocs.io/en/latest/)를 참조하세요.    
- Azure Machine Learning 실험 실행 환경을 보다 깊이 있게 이해하려면 [Azure Machine Learning 실험 서비스 구성](experimentation-service-configuration.md)을 참조하세요.

