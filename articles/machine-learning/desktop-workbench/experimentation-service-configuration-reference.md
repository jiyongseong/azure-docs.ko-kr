---
title: Azure Machine Learning 실험 서비스 구성 파일
description: 이 문서에서는 Azure ML 실험 서비스를 위한 구성 설정을 자세히 설명합니다.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 8ea7e7453ba49b17560b7c2147dd6c71c6c1a7a0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Azure Machine Learning 실험 서비스 구성 파일

Azure ML(Machine Learning) Workbench용 스크립트를 제출할 때 실행 동작은 **aml_config** 폴더의 파일을 통해 제어됩니다. 이 폴더는 프로젝트 폴더 루트 아래에 있습니다. 최적의 방법으로 실행에 대해 원하는 결과를 얻으려면 이 파일의 내용을 이해해야 합니다.

다음은 이 폴더 아래의 관련 파일입니다.
- conda_dependencies.yml
- spark_dependencies.yml
- 계산 대상 파일
    - \<compute target name>.compute
- 실행 구성 파일
    - \<run configuration name>.runconfig

>[!NOTE]
>일반적으로 만드는 각 계산 대상에 대한 계산 대상 파일 및 실행 구성 파일이 있습니다. 하지만 이러한 파일을 독립적으로 만들고 여러 실행 구성 파일이 같은 계산 대상을 가리키도록 할 수 있습니다.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
이 파일은 코드가 의존하는 패키지Python 런타임 버전 및 패키지를 지정하는 [conda 환경 파일](https://conda.io/docs/using/envs.html#create-environment-file-by-hand)입니다. Azure ML Workbench는 Docker 컨테이너 또는 HDInsight 클러스터에서 스크립트를 실행할 때 사용자 스크립트를 실행할 [conda 환경](https://conda.io/docs/using/envs.html)을 만듭니다. 

실행을 위해 스크립트에 필요한 Python 패키지를 이 파일에 지정합니다. Azure ML 실험 서비스는 종속성 목록에 따라 conda 환경을 만듭니다. 여기에 나열된 패키지는 다음과 같은 채널을 통해 실행 엔진이 도달할 수 있어야 합니다.

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* 공개적으로 액세스할 수 있는 끝점(URL)
* 또는 로컬 파일 경로
* 실행 엔진을 통해 도달할 수 있는 기타 대상

>[!NOTE]
>HDInsight 클러스터에서 실행할 때 Azure ML Workbench는 특정 실행을 위한 conda 환경을 만듭니다. 이를 통해 서로 다른 Python 환경에서 실행하는 여러 사용자들이 같은 클러스터에서 실행할 수 있습니다.  

다음은 일반적인 **conda_dependencies.yml** 파일의 예입니다.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local Docker target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench는 **conda_dependencies.yml**이 그대로 유지되는 한 다시 빌드하지 않고 같은 conda 환경을 사용합니다. 종속성이 변경되면 환경을 다시 빌드합니다.

>[!NOTE]
>실행이 _로컬_ 계산 컨텍스트를 대상으로 하는 경우 **conda_dependencies.yml** 파일이 사용되지 **않습니다**. 로컬 Azure ML Workbench Python 환경에 대한 패키지 종속성은 수동으로 설치해야 합니다.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
이 파일은 설치해야 하는 PySpark 스크립트 및 Spark 패키지를 제출할 때 Spark 응용 프로그램 이름을 지정합니다. 또한 Maven 리포지토리에서 찾을 수 있는 공용 Maven 리포지토리 및 Spark 패키지를 지정할 수도 있습니다.

다음은 예제입니다.

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>작업자 크기 및 코어와 같은 클러스터 튜닝 매개 변수는 spark_dependecies.yml 파일의 "configuration" 섹션으로 들어가야 함 

>[!NOTE]
>Python 환경에서 스크립트를 실행하는 경우 *spark_dependencies.yml* 파일은 무시됩니다. Spark(Docker 또는 HDInsight 클러스터)에서 실행중인 경우에만 사용됩니다.

## <a name="run-configuration"></a>실행 구성
특정 실행 구성을 지정하려면 .compute 파일과 .runconfig 파일이 필요합니다. 이들은 일반적으로 CLI 명령을 사용하여 생성됩니다. 기존 명령을 복제하고 이름을 변경하고 편집할 수도 있습니다.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

이 명령은 지정된 계산 대상을 기반으로 파일 쌍을 만듭니다. 계산 대상을 _foo_로 지정했다고 하겠습니다. 이 명령은 사용자의**aml_config** 폴더에 _foo.compute_ 및 _foo.runconfig_를 생성합니다.

>[!NOTE]
> 실행 구성 파일에 대한 _로컬_ 또는 _docker_ 이름은 임의로 지정할 수 있습니다. Azure ML Workbench는 편의상 공백 프로젝트를 만들 때 이 두 실행 구성을 추가합니다. 프로젝트 템플릿에 제공되는 "<run configuration name>.runconfig" 파일을 이름 변경하거나 원하는 이름의 새 파일을 만들 수 있습니다.

### <a name="compute-target-namecompute"></a>\<compute target name>.compute
_\<compute target name>.compute_ 파일은 계산 대상에 대한 연결 및 구성 정보를 지정합니다. 이는 이름-값 쌍의 목록입니다. 다음은 지원되는 설정입니다.

**type**: 계산 환경의 유형입니다. 지원되는 값은 다음과 같습니다.
  - local
  - remote
  - docker
  - remotedocker
  - cluster

**baseDockerImage**: Python/PySpark 스크립트를 실행하는 데 사용하는 Docker 이미지입니다. 기본값은 _microsoft/mmlspark:plus-0.7.91_입니다. 또한 다른 이미지 한 개, _microsoft/mmlspark:plus-gpu-0.7.91_도 지원하며, 이는 호스트 컴퓨터에 대한 GPU 액세스 권한을 부여합니다(GPU가 존재하는 경우).

**address**: 가상 머신 또는 HDInsight 클러스터 헤드-노드의 IP 주소 또는 FQDN(정규화된 도메인 이름)입니다.

**username**: 가상 머신 또는 HDInsight 헤드-노드에 액세스하기 위한 SSH 사용자 이름입니다.

**password**: SSH 연결을 위해 암호화된 암호입니다.

**sharedVolumes**: 실행 엔진이 Docker 공유 볼륨 기능을 사용하여 프로젝트 파일을 앞뒤로 이동해야 한다는 것을 나타내는 플래그입니다. 이 플래그를 켜면 Docker가 프로젝트를 복사할 필요 없이 직접 액세스할 수 있으므로 실행 속도를 높일 수 있습니다. Docker 엔진이 Windows에서 실행하는 경우 Windows에서 Docker를 공유하기 위한 볼륨은 색다를 수 있기 때문에 _False_로 설정하는 것이 가장 좋습니다. macOS 또는 Linux에서 실행하는 경우 _True_로 설정합니다.

**nvidiaDocker**: 이 플래그는 _True_로 설정하면 Azure ML 실험 서비스에 대해 Docker 이미지를 시작할 때 일반적인 _docker_ 명령을 사용하지 말고 _nvidia-docker_ 명령을 사용하도록 지정합니다. _nvidia-docker_ 엔진을 사용하면 Docker 컨테이너가 GPU 하드웨어에 액세스할 수 있습니다. 이 설정은 Docker 컨테이너에서 GPU 실행을 수행하려는 경우에 필요합니다. Linux 호스트만이 _nvidia-docker_를 지원합니다. 예를 들어 Azure의 Linux 기반 DSVM은 _nvidia-docker_를 제공합니다. 현재 Windows에서는 _nvidia-docker_를 지원하지 않습니다.

**nativeSharedDirectory**: 이 속성은 파일을 같은 계산 대상에서 실행 간에 공유하기 위해 저장할 수 있는 기본 디렉터리(예: _~/.azureml/share/_)를 지정합니다. Docker 컨테이너에서 실행할 때 이 설정을 사용하는 경우 _sharedVolumes_를 True로 설정해야 한다. 그렇지 않으면 실행되지 않습니다.

**userManagedEnvironment**: 이 속성은 계산 대상을 사용자가 직접 관리할지 아니면 실험 서비스를 통해 관리할지 지정합니다.  

**pythonLocation**: 컴퓨터 대상에서 사용자의 프로그램을 실행하는 데 사용할 Python 런타임의 위치를 지정합니다. 

### <a name="run-configuration-namerunconfig"></a>\<run configuration name>.runconfig
_\<run configuration name>.runconfig_는 Azure ML 실험 실행 동작을 지정합니다. 실행 기록 추적 또는 많은 다른 대상과 함께 사용할 계산 대상 등과 같은 실행 구성 동작을 구성할 수 있습니다. 실행 구성 파일의 이름은 Azure ML Workbench 데스크톱 응용 프로그램에서 실행 컨텍스트 드롭다운을 채우는 데 사용됩니다.

**ArgumentVector**: 이 섹션은 이 실행의 일부로 실행할 스크립트 및 해당 스크립트에 대한 매개 변수를 지정합니다. 예를 들어 "<run configuration name>.runconfig" 파일에 다음과 같은 코드 조각이 있는 경우 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml experiment submit foo.runconfig"_는 234를 매개 변수로 전달하는 _myscript.py_ 파일을 자동으로 실행하고 --verbose 플래그를 설정합니다.

**Target**: 이 매개 변수는 _runconfig_ 파일이 참조하는 _.compute_ 파일의 이름입니다. 이는 일반적으로 _foo.compute_ 파일을 가리키지만 다른 계산 대상을 가리키도록 편집할 수 있습니다.

**Environment Variables**: 이 섹션을 통해 사용자는 실행의 일부로 환경 변수를 설정할 수 있습니다. 사용자는 다음과 같은 형식의 이름-값 쌍을 사용하여 환경 변수를 지정할 수 있습니다.
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

이러한 환경 변수는 사용자의 코드에서 액세스할 수 있습니다. 예를 들어 이 Python 코드는 "EXAMPLE_ENV_VAR"이라는 환경 변수를 인쇄함
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: 이 속성은 Azure ML Workbench가 스크립트를 실행하기 위해 Spark 세션을 시작해야 하는지 여부를 지정합니다. 기본값은 _PySpark_입니다. PySpark 코드를 실행하지 않는 경우 _Python_으로 설정하며, 이렇게 하면 적은 오버헤드로 작업을 더 빨리 시작하는 데 도움이 될 수 있습니다.

**CondaDependenciesFile**: 이 속성은 *aml_config* 폴더의 conda 환경 종속성을 지정하는 파일을 가리킵니다. _Null_로 설정하면 기본값 **conda_dependencies.yml** 파일을 가리킵니다.

**SparkDependenciesFile**: 이 속성은 **aml_config** 폴더의 Spark 종속성을 지정하는 파일을 가리킵니다. 기본적으로 _Null_로 설정되며 기본값 **spark_dependencies.yml** 파일을 가리킵니다.

**PrepareEnvironment**: 이 속성은 _True_로 설정하면 실험 서비스에 대해 초기 실행의 일부로 지정된 conda 종속성을 기반으로 conda 환경을 준비할 것을 지정합니다. 이 속성은 Docker 환경에 대해 실행할 경우에만 유효합니다. _로컬_ 환경을 대상으로 실행하는 경우 이 설정은 효과가 없습니다. 

**TrackedRun**: 이 플래그는 실험 서비스에 대해 Azure ML Workbench 실행 기록 인프라에서 실행을 추적할지 여부를 지정합니다. 기본값은 _true_입니다. 

**UseSampling**: _UseSampling_은 실행을 위해 데이터 소스에 대한 활성 샘플 데이터 집합을 사용하는지 여부를 지정합니다. _False_로 설정하면 데이터 소스는 데이터 소스에서 읽은 전체 데이터를 수집하고 사용합니다. _True_로 설정하면 활성 샘플을 사용합니다. 사용자는 **DataSourceSettings**를 사용하여 활성 샘플을 재정의하고자 하는 경우 사용할 특정 샘플 데이터 집합을 지정할 수 있습니다. 

**DataSourceSettings**: 이 구성 섹션은 데이터 소스 설정을 지정합니다. 이 섹션에서 사용자는 실행의 일부로 사용하는 특정 데이터 소스의 기존 데이터 샘플을 지정합니다. 

다음 구성 설정은 "MyDataSource"라는 데이터 소스에 대해 "MySample"이라는 샘플을 사용한다는 것을 지정함
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: 사용자가 코드를 변경하지 않고 한 데이터 소스를 다른 데이터 소스로 전환하려는 경우 데이터 소스 대체를 사용할 수 있습니다. 예를 들어 사용자는 데이터 소스 참조를 변경하여 샘플링한 로컬 파일에서 Azure Blob에 저장된 원래의 더 큰 데이터 집합으로 전환할 수 있습니다. 대체를 사용하면 Azure ML Workbench는 대체 데이터 소스를 참조하여 사용자의 데이터 소스 및 데이터 준비 패키지를 실행합니다.

다음 예제는 Azure ML 데이터 소스 및 데이터 준비 패키지의 "mylocal.datasource" 참조를 "myremote.dsource"로 바꿉니다. 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

위의 대체에 따라, 다음 코드 샘플은 이제 사용자가 코드를 변경할 필요 없이 "mylocal.dsource" 대신에 "myremote.dsource"에서 읽습니다.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>다음 단계
[실험 서비스 구성](experimentation-service-configuration.md)에 대해 자세히 알아봅니다.
