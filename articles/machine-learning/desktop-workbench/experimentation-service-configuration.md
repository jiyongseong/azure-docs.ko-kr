---
title: Azure Machine Learning 실험 서비스 구성 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 실험 서비스에 대한 대략적인 개요와 구성 방법에 관한 지침을 제공합니다.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 25f51618e906c2993ebf02795b9a50d4d7a0b464
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Azure Machine Learning 실험 서비스 구성

## <a name="overview"></a>개요
Azure Machine Learning 실험 서비스를 사용하면 데이터 과학자가 Azure Machine Learning 실행 및 실행 관리 기능을 사용하여 실험을 실행할 수 있습니다. 이는 빠른 반복과 함께 빠른 실험 프레임워크를 제공합니다. Azure Machine Learning Workbench를 사용하면 컴퓨터에서 로컬 실행을 시작할 수 있으며, Spark를 실행하는 GPU 또는 HDInsight 클러스터가 있는 원격 데이터 과학 VM과 같은 다른 환경으로 규모를 강화하거나 확장하는 쉬운 경로도 시작할 수 있습니다.

실험 서비스는 격리되고 재현 가능하고 일관성 있는 실험 실행을 제공하기 위한 기능입니다. 이는 계산 대상, 실행 환경 및 실행 구성을 관리하는 데 도움이 됩니다. Azure Machine Learning Workbench 실행 및 실행 관리 기능을 사용하면 서로 다른 환경 간에 쉽게 이동할 수 있습니다. 

Workbench 프로젝트에서 로컬로 또는 클라우드에서 크기에 따라 Python 또는 PySpark 스크립트를 실행할 수 있습니다. 

다음 환경에서 스크립트를 실행할 수 있습니다. 

* Workbench에서 로컬 컴퓨터에 설치한 Python(3.5.2) 환경
* 로컬 컴퓨터의 Docker 컨테이너 내부에 있는 conda Python 환경
* 원격 Linux 컴퓨터에서 소유하고 관리하는 Python 환경
* 원격 Linux 컴퓨터의 Docker 컨테이너 내부에 있는 conda Python 환경. 예를 들어 [Azure의 Ubuntu 기반 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure의 [Spark용 HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)

>[!IMPORTANT]
>Azure Machine Learning 실험 서비스는 현재 Python 및 Spark 런타임 버전으로 Python 3.5.2 및 Spark 2.1.11을 각각 지원합니다. 


### <a name="key-concepts-in-experimentation-service"></a>실험 서비스의 주요 개념
Azure Machine Learning 실험 실행의 다음과 같은 개념을 이해해야 합니다. 이후 섹션에서 이러한 개념을 사용 하는 방법을 자세히 설명합니다. 

#### <a name="compute-target"></a>계산 대상
_계산 대상_은 사용자의 데스크톱, VM의 원격 Docker 또는 클러스터 등과 같은 사용자의 프로그램을 실행하는 위치를 지정합니다. 계산 대상은 사용자가 주소를 지정하고 액세스할 수 있어야 합니다. Workbench에서는 Workbench 응용 프로그램 및 CLI를 사용하여 계산 대상을 만들고 관리할 수 있습니다. 

CLI의 _az ml computetarget attach_ 명령을 사용하면 실행에 사용할 수 있는 계산 대상을 만들 수 있습니다.

지원되는 계산 대상은 다음과 같습니다.
* Workbench가 사용자 컴퓨터에 설치한 로컬 Python(3.5.2) 환경.
* 사용자 컴퓨터의 로컬 Docker
* 원격 Linux-Ubuntu VM의 사용자 관리 Python 환경. 예를 들어 [Azure의 Ubuntu 기반 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Linux-Ubuntu VM의 원격 Docker. 예를 들어 [Azure의 Ubuntu 기반 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure의 [Spark 클러스터용 HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)

실험 서비스는 현재 Python 및 Spark 런타임 버전으로 Python 3.5.2 및 Spark 2.1.11을 각각 지원합니다. 

>[!IMPORTANT]
> Docker를 실행하는 Windows VM은 원격 계산 대상으로 지원되지 **않습니다**.

#### <a name="execution-environment"></a>실행 환경
_실행 환경_은 Workbench에서 프로그램을 실행하는 데 필요한 런타임 구성 및 종속성을 정의합니다.

Workbench 기본 런타임에서 실행하는 경우 자신이 즐겨 쓰는 도구 및 패키지 관리자를 사용하여 로컬 실행 환경을 관리합니다. 

Conda는 로컬 Docker와 원격 Docker 실행 및 HDInsight 기반 실행을 관리하는 데 사용됩니다. 이러한 계산 대상의 경우 실행 환경 구성은 **Conda_dependencies.yml** 및 **Spark_dependencies.yml 파일**을 통해 관리됩니다. 이 파일들은 프로젝트 내의 **aml_config** 폴더에 있습니다.

**실행 환경을 위해 지원되는 런타임:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>실행 구성
계산 대상과 실행 환경 외에도 Azure Machine Learning은 *실행 구성*을 정의 및 변경하기 위한 프레임워크를 제공합니다. 실험의 서로 다른 실행을 위해 반복 실험의 일부로 서로 다른 구성이 필요할 수 있습니다. 서로 다른 데이터 소스를 사용하여 서로 다른 매개 변수 범위를 비우고 Spark 매개 변수를 튜닝할 수 있습니다. 실험 서비스는 실행 구성을 관리하기 위한 프레임워크를 제공합니다.

_az ml computetarget attach_ 명령을 실행하면 _<your_computetarget_name>.compute_ 및 _<your_computetarget_name>.runconfig_ 규칙에 따라 프로젝트의 **aml_config** 폴더에 두 개의 파일, 즉 ".compute" 및 ".runconfig"가 생성됩니다. .runconfig 파일은 계산 대상을 만들 때 사용자의 편의를 위해 자동으로 생성됩니다. CLI에서 _az ml runconfigurations_ 명령을 사용하여 다른 실행 구성을 만들고 관리할 수 있습니다. 또한 사용자의 파일 시스템에서 이들을 만들고 편집할 수도 있습니다.

Workbench의 실행 구성을 사용하여 환경 변수를 지정할 수도 있습니다. .runconfig 파일에 다음 섹션을 추가하여 환경 변수를 지정하고 해당 환경 변수를 코드에 사용할 수 있습니다. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

이러한 환경 변수는 사용자의 코드에서 액세스할 수 있습니다. 예를 들어 이 Phyton 코드 조각은 "EXAMPLE_ENV_VAR1"이라는 환경 변수를 인쇄함
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**다음 그림은 초기 실험 실행에 대한 고급 흐름을 보여 줍니다.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>실험 실행 시나리오
이 시나리오에서는 실행 시나리오를 자세히 살펴보고 Azure Machine Learning이 실험을 실행하는 방법, 구체적으로 원격 VM 및 HDInsight 클러스터에서 실험을 로컬로 실행하는 방법을 알아봅니다. 이 섹션에서는 계산 대상 만들기에서 시작하여 실험 실행까지 훑어봅니다.

>[!NOTE]
>이 문서의 나머지 부분에서는 CLI(명령줄 인터페이스) 명령을 사용하여 개념과 기능을 보여 줍니다. 여기서 설명하는 기능은 Workbench에서도 사용할 수 있습니다.

## <a name="launching-the-cli"></a>CLI 시작
CLI를 시작하는 쉬운 방법은 Workbench에서 프로젝트를 열고 **파일-->명령 프롬프트 열기**로 이동하는 것입니다.

![](media/experimentation-service-configuration/opening-cli.png)

이 명령은 현재 프로젝트 폴더의 스크립트를 실행하는 명령을 입력할 수 있는 터미널 창을 시작합니다. 이 터미널 창은 Workbench가 설치하는 Python 3.5.2 환경을 사용하여 구성됩니다.

>[!NOTE]
> 명령 창에서 임의의 _az ml_ 명령을 실행하는 경우 Azure에 대해 인증해야 합니다. CLI는 독립적인 인증 캐시에 이어서 데스크톱 앱을 사용하므로 Workbench에 로그인한다고 해서 CLI 환경에서 인증되는 것은 아닙니다. 인증하려면 다음 단계를 사용합니다. 인증 토큰은 일정 시간 동안 로컬 컴퓨터에 캐시되므로 토큰이 만료할 때에만 이 단계를 반복하면 됩니다. 토큰이 만료되거나 인증 오류가 발견된 경우 다음 명령을 실행합니다.

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>프로젝트 폴더 내에서 _az ml_ 명령을 실행하는 경우 프로젝트가 _현재_ Azure 구독의 Azure Machine Learning 실험 계정에 속하는지 확인해야 합니다. 그렇지 않으면 실행 오류가 발생할 수 있습니다.


## <a name="running-scripts-and-experiments"></a>스크립트 및 실험 실행
Workbench를 사용하면 _az ml experiment submit_ 명령을 사용하여 다양한 컴퓨터 대상에서 Python 및 PySpark 스크립트를 실행할 수 있습니다. 이 명령을 사용하려면 실행 구성 정의가 필요합니다. 

Workbench는 계산 대상을 만들 때 해당 runconfig 파일을 만들지만, _az ml runconfiguration create_ 명령을 사용하여 추가 실행 구성을 만들 수 있습니다. 실행 구성 파일을 수동으로 편집할 수도 있습니다.

실행 구성은 Workbench에서 실험 실행 경험의 일부로 표시됩니다. 

>[!NOTE]
>[실험 실행 구성 참조](experimentation-service-configuration-reference.md) 섹션에서 실행 구성 파일에 대해 더 자세히 알아볼 수 있습니다.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Workbench가 설치된 런타임에 대해 로컬로 스크립트 실행
AWorkbench를 사용하면 Workbench가 설치된 Python 3.5.2 런타임에 대해 직접 스크립트를 실행할 수 있습니다. 이 기본 런타임은 Workbench 설치 시에 설치되며 Azure Machine Learning 라이브러리 및 종속성을 포함합니다. 로컬 실행에 대한 실행 결과 및 아티팩트는 여전히 클라우드의 실행 기록 서비스에 저장됩니다.

Docker 기반 실행과 달리 이 구성은 Conda에서 관리되지 _않습니다_. 로컬 Workbench Python 환경에 대한 패키지 종속성은 수동으로 프로비전해야 합니다.

다음 명령을 실행하여 Workbench가 설치된 Python 환경에서 로컬로 스크립트를 실행할 수 있습니다. 

```
$az ml experiment submit -c local myscript.py
```

Workbench CLI 창에 다음 명령을 입력하여 기본 Python 환경에 대한 경로를 찾을 수 있습니다.
```
$ conda env list
```

>[!NOTE]
>로컬 Spark 환경에 대해 PySpark를 로컬로 직접 실행하는 것은 현재 지원되지 **않습니다**. Workbench는 로컬 Docker에서 PySpark 스크립트 실행을 지원합니다. Azure Machine Learning 기본 Docker 이미지는 Spark 2.1.11이 미리 설치된 상태에서 제공됩니다. 

_**Python 스크립트에 대한 로컬 실행 개요:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>로컬 Docker에서 스크립트 실행
또한 실험 서비스를 통해 로컬 컴퓨터의 Docker 컨테이너에서 프로젝트를 실행할 수도 있습니다. Workbench는 로컬 Spark 실행을 쉽게 해주기 위해 Azure Machine Learning 및 Spark 2.1.11 런타임이 포함된 기본 Docker 이미지를 제공합니다. Docker는 이미 로컬 컴퓨터에서 실행하고 있어야 합니다.

로컬 Docker에서 Python 또는 PySpark 스크립트를 실행하려는 경우 CLI에서 다음 명령을 실행할 수 있습니다.

```
$az ml experiment submit -c docker myscript.py
```
또는
```
az ml experiment submit --run-configuration docker myscript.py
```

로컬 Docker의 실행 환경은 Azure Machine Learning 기본 Docker 이미지를 사용하여 준비됩니다. Workbench는 처음 실행할 때 이 이미지를 다운로드하고 사용자의 conda_dependencies.yml 파일에 지정된 패키지로 덮어씁니다. 이 작업은 초기 실행을 느리게 만들지만 이후 실행은 Workbench가 캐시된 레이어를 다시 사용하는 덕분에 훨씬 더 빨라집니다. 

>[!IMPORTANT]
>첫 번째 실행에 대해 Docker 이미지를 준비하려면 _az ml experiment prepare -c docker_ 명령을 먼저 실행해야 합니다. 또한 docker.runconfig 파일에서 **PrepareEnvironment** 매개 변수를 True로 설정할 수도 있습니다. 이 작업은 실행 수행의 일부로 환경을 자동으로 준비합니다.  

>[!NOTE]
>Spark에서 PySpark 스크립트를 실행하는 경우, conda_dependencies.yml에 더하여 spark_dependencies.yml도 사용됩니다.

Docker 이미지에서 스크립트를 실행하면 다음과 같은 이점이 있습니다.

1. 다른 실행 환경에서 스크립트를 안정적으로 실행될 수 있도록 보장합니다. Docker 컨테이너에서 실행하면 이식성에 영향을 미칠 수 있는 로컬 참조를 검색하여 방지하는 데 도움이 됩니다. 

2. 이렇게 하면 Apache Spark처럼 설치와 구성이 복잡한 런타임 및 프레임워크에서 코드를 직접 설치할 필요 없이 빨리 테스트할 수 있습니다.


_**Python 스크립트를 위한 로컬 Docker 실행 개요:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>원격 Docker에서 스크립트 실행
경우에 따라 사용자의 로컬 컴퓨터에서 사용할 수 있는 리소스가 원하는 모델을 학습하기에 부족할 수 있습니다. 이 경우 실험 서비스를 사용하면 원격 Docker 실행을 사용하여 더 강력한 VM에서 Python 또는 PySpark 스크립트를 실행하는 손쉬운 방법을 선택할 수 있습니다. 

원격 VM은 다음 요구 사항을 충족 해야 합니다.
* 원격 VM이 Linux-Ubuntu를 실행해야 하며 SSH를 통해 액세스할 수 있어야 합니다. 
* 원격 VM이 Docker를 실행하고 있어야 합니다.

>[!IMPORTANT]
> Docker를 실행하는 Windows VM은 원격 계산 대상으로 지원되지 **않습니다**.


다음 명령을 사용하여 원격 Docker 기반 실행을 위한 계산 대상 정의와 실행 구성을 모두 만들 수 있습니다.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

계산 대상을 구성한 후 다음 명령을 사용하여 스크립트를 실행할 수 있습니다.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>실행 환경이 conda_dependencies.yml의 사양을 사용하여 구성된다는 것을 명심하세요. spark_dependencies.yml은 .runconfig 파일에 PySpark 프레임워크를 지정한 경우에도 사용됩니다. 

원격 VM을 위한 Docker 구성 프로세스는 로컬 Docker 실행 프로세스와 정확히 같으므로 유사한 실행 경험을 예상해야 합니다.

>[!TIP]
>처음에 Docker 이미지를 만들어서 발생하는 대기 시간을 피하고 싶으면 스크립트를 실행하기 전에 다음 명령을 사용하여 계산 대상을 준비할 수 있습니다. az ml experiment prepare -c remotedocker

_**Python 스크립트를 위한 원격 VM 실행 개요:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>사용자 관리 환경을 대상으로 하는 원격 VM에서 스크립트 실행
실험 서비스는 원격 Ubuntu 가상 머신 내에 있는 사용자 고유의 Python 환경에서 스크립트를 실행하는 것도 지원합니다. 이렇게 하면 사용자 고유의 실행 환경을 관리하고 Azure Machine Learning 기능도 사용할 수 있습니다. 

다음 단계에 따라 사용자 고유의 환경에서 스크립트를 실행합니다.
* 종속성을 설치하는 원격 Ubuntu VM 또는 DSVM에 Python 환경을 준비합니다.
* 다음 명령을 사용하여 Azure Machine Learning 요구 사항을 설치합니다.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>어떤 경우에는 권한에 따라 sudo 모드에서 이 명령을 실행해야 할 수도 있습니다. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* 다음 명령을 사용하여 원격 VM 실행 시 사용자 관리 실행에 대한 계산 대상 정의 및 실행 구성을 모두 만듭니다.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>이렇게 하면 .computer 구성 파일의 "userManagedEnvironment" 매개 변수를 true로 설정합니다.

* .computer 파일에서 Python 런타임 실행 파일의 위치를 설정합니다. Python 실행 파일의 전체 경로를 참조해야 합니다. 
```
pythonLocation: python3
```

계산 대상을 구성한 후 다음 명령을 사용하여 스크립트를 실행할 수 있습니다.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> DSVM에서 실행하는 경우 다음 명령을 사용해야 합니다.

DSVM의 전역 Python 환경에서 직접 실행하려면 다음 명령을 실행합니다.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 스크립트 실행
HDInsight는 Apache Spark를 지원하는 인기 있는 빅 데이터 분석용 플랫폼입니다. Workbench를 사용하면 HDInsight Spark 클러스터를 사용하여 빅 데이터에 대해 실험할 수 있습니다. 

>[!NOTE]
>HDInsight 클러스터는 Azure Blob을 주 저장소로 사용해야 합니다. 아직 Azure Data Lake Storage를 사용하도록 지원되지 않습니다.

다음 명령을 사용하여 HDInsight Spark 클러스터에 대한 계산 대상 및 실행 구성을 만들 수 있습니다.

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>IP 주소 대신에 FQDN을 사용하고 HDI Spark 클러스터 이름이 _foo_인 경우, SSH 끝점은 _foo-ssh.azurehdinsight.net_이라는 드라이버 노드에 있습니다. _--address_ 매개 변수에 FQDN을 사용하는 경우 서버 이름의 접미사 **-ssh**를 잊지 마십시오.


계산 컨텍스트를 가지고 있는 경우 다음 명령을 실행하여 PySpark 스크립트를 실행할 수 있습니다.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench는 Conda를 사용하여 HDInsight 클러스터의 실행 환경을 준비 및 관리합니다. 구성은 _conda_dependencies.yml_ 및 _spark_dependencies.yml_ 구성 파일을 통해 관리됩니다. 

이 모드에서 실험을 실행하려면 HDInsight 클러스터에 대한 SSH 액세스 권한이 필요합니다. 

>[!NOTE]
>지원되는 구성은 Linux(Python/PySpark 3.5.2 및 Spark 2.1.11 포함 Ubuntu)를 실행하는 HDInsight Spark 클러스터입니다.

_**PySpark 스크립트를 위한 HDInsight 기반 실행 개요**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>GPU에서 스크립트 실행
GPU에서 스크립트를 실행하려면 [Azure Machine Learning에서 GPU를 사용하는 방법](how-to-use-gpu.md) 문서의 지침을 따를 수 있습니다.

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>SSH 키 기반 인증을 통해 계산 대상을 만들고 사용하기
Azure Machine Learning Workbench를 사용하면 사용자 이름/암호 기반 체계 외에도 SSH 키 기반 인증을 사용하여 계산 대상을 만들고 사용할 수 있습니다. remotedocker 또는 클러스터를 계산 대상으로 사용할 때 이 기능을 사용할 수 있습니다. 이 체계를 사용하면 Workbench에서 공개/개인 키 쌍을 만든 후 해당 공개 키를 알려줍니다. 사용자 이름에 해당하는 ~/.ssh/authorized_keys 파일에 이 공개 키를 추가합니다. 그러면 Azure Machine Learning Workbench에서 ssh 키 기반 인증을 통해 이 계산 대상에 액세스하여 작업을 실행합니다. 계산 대상에 대한 개인 키는 작업 영역을 위한 키 저장소에 저장되므로 해당 작업 영역의 다른 사용자가 계산 대상을 만들기 위해 제공한 사용자 이름을 제공하여 동일한 방식으로 계산 대상을 사용할 수 있습니다.  

이 기능을 사용하려면 다음 단계를 수행합니다. 

- 다음 명령 중 하나를 사용하여 계산 대상을 만듭니다.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
또는
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- 연결된 계산 대상의 ~/.ssh/authorized_keys 파일에 Workbench에서 생성한 공개 키를 추가합니다. 

>[!IMPORTANT]
>계산 대상을 만드는 데 사용한 것과 동일한 사용자 이름을 사용하여 계산 대상에 로그온해야 합니다. 

- 이제 SSH 키 기반 인증을 사용하여 계산 대상을 준비하고 사용할 수 있습니다.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>다음 단계
* [Azure Machine Learning 만들기 및 설치](../service/quickstart-installation.md)
* [모델 관리](model-management-overview.md)
