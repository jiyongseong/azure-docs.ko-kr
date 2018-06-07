---
title: TB 단위의 데이터에 대한 서버 워크로드 예측 - Azure | Microsoft Docs
description: Azure Machine Learning Workbench를 사용하여 빅 데이터에서 기계 학습 모델을 교육하는 방법입니다.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: 424af2ffd1b7931701036aeb819cbb8879cb7a41
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>TB 단위의 데이터에 대한 서버 작업 예측

이 문서는 데이터 과학자가 Azure Machine Learning Workbench를 사용하여 빅 데이터를 사용해야 하는 솔루션을 개발하는 방법을 설명합니다. 큰 데이터 집합의 샘플에서 시작하고, 데이터 준비, 기능 엔지니어링 및 기계 학습을 통해 반복한 다음 전체 큰 데이터 집합으로 프로세스를 확장할 수 있습니다. 

Machine Learning Workbench의 다음 주요 기능에 대해 알아봅니다.
* 계산 대상 간 쉬운 전환 서로 다른 계산 대상을 설정하고 실험에서 사용할 수 있습니다. 이 예제에서는 Ubuntu DSVM과 Azure HDInsight 클러스터를 계산 대상으로 사용합니다. 또한 리소스 가용성에 따라 계산 대상을 구성할 수 있습니다. 특히, 더 많은 작업자 노드로 Spark 클러스터를 확장한 후 Machine Learning Workbench를 통해 리소스를 사용하여 실험 실행 속도를 높일 수 있습니다.
* 실행 기록 추적 Machine Learning Workbench를 사용하여 기계 학습 모델 및 관심 있는 기타 메트릭의 성능을 추적할 수 있습니다.
* 기계 학습 모델의 운영화 Machine Learning Workbench 내에서 기본 제공 도구를 사용하여 Azure Container Service의 웹 서비스로 학습된 기계 학습 모델을 배포할 수 있습니다. 또한 웹 서비스를 사용하여 REST API 호출을 통해 미니 일괄 처리 예측을 가져올 수도 있습니다. 
* TB 단위의 데이터를 지원합니다.

> [!NOTE]
> 이 예제와 관련된 코드 샘플 및 기타 자료는 [GitHub](https://github.com/Azure/MachineLearningSamples-BigData)를 참조하세요.
> 

## <a name="use-case-overview"></a>사용 사례 개요

서버에서 워크로드를 예측하는 것은 자체 인프라를 관리하는 기술 회사의 공통 비즈니스 요구 사항입니다. 인프라 비용을 줄이기 위해 충분히 활용되지 않는 서버에서 실행되는 서비스는 더 작은 수의 컴퓨터에서 실행되도록 함께 그룹화되어야 합니다. 과도하게 사용되는 서버에서 실행되는 서비스에는 실행할 더 많은 컴퓨터를 제공해야 합니다. 

이 시나리오에서는 각 컴퓨터(또는 서버)에 대한 워크로드 예측에 중점을 둡니다. 특히 각 서버의 세션 데이터를 사용하여 향후 서버의 워크로드 클래스를 예측합니다. [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html)의 임의 포리스트 분류자를 사용하여 각 서버의 로드를 낮음, 중간 및 높음 클래스로 분류합니다. 이 예제의 기계 학습 기술 및 워크플로는 다른 유사한 문제로 쉽게 확장될 수 있습니다. 


## <a name="prerequisites"></a>필수 조건

이 예제를 실행하기 위한 필수 조건은 다음과 같습니다.

* [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
* 설치된 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) 복사본. 프로그램을 설치하고 작업 영역을 만들려면 [빠른 시작 설치 가이드](../service/quickstart-installation.md)를 참조하세요. 구독이 여러 개 있는 경우 [원하는 구독을 현재 활성 구독으로 설정](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set)할 수 있습니다.
* Windows 10(이 예제의 지침은 일반적으로 macOS 시스템에 대해 동일함)
* 가급적이면 데이터가 있는 미국 동부 지역에 위치한 Linux(Ubuntu)용 DSVM(데이터 과학 가상 머신). [이러한 지침](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)에 따라 Ubuntu DSVM을 프로비전할 수 있습니다. [이 빠른 시작](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)을 참조할 수도 있습니다. 적어도 8개의 코어와 32GB의 메모리가 있는 가상 컴퓨터를 사용하는 것이 좋습니다. 

[지침](../service/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present)에 따라 AML Workbench에 대한 VM에 암호 없이 sudoer 액세스할 수 있도록 설정합니다.  [AML Workbench에서 VM을 만들어 사용할 때 SSH 키 기반 인증을 사용](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets)하도록 선택할 수 있습니다. 이 예제에서는 암호를 사용하여 VM에 액세스합니다.  이후 단계를 위해 DSVM 정보와 함께 다음 표를 저장합니다.

 필드 이름| 값 |  
 |------------|------|
DSVM IP 주소 | xxx|
 사용자 이름  | xxx|
 암호   | xxx|


 [Docker 엔진](https://docs.docker.com/engine/)이 설치된 VM을 사용하도록 선택할 수 있습니다.

* 가급적이면 데이터가 있는 미국 동부 지역에 위치한 Hortonworks Data Platform 버전 3.6 및 Spark 버전 2.1.x가 있는 HDInsight Spark Cluster. HDInsight 클러스터를 만드는 방법에 대한 세부 정보는 [Azure HDInsight에서 Apache Spark 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)를 방문하세요. 16 코어 및 112GB의 메모리가 있는 각각의 작업자에 3작업자 클러스터를 사용하는 것이 좋습니다. 또는 헤드 노드에 대해 VM 유형 `D12 V2`를 선택하고 작업자 노드에 대해 `D14 V2`를 선택할 수 있습니다. 클러스터 배포에는 약 20분이 소요됩니다. 이 예제를 사용하려면 클러스터 이름, SSH 사용자 이름 및 암호가 필요합니다. 이후 단계를 위해 Azure HDInsight 클러스터 정보와 함께 다음 표를 저장합니다.

 필드 이름| 값 |  
 |------------|------|
 클러스터 이름| xxx|
 사용자 이름  | xxx(기본적으로 sshuser)|
 암호   | xxx|


* Azure Storage 계정. [다음 지침](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)을 따라 만들 수 있습니다. 또한 이 저장소 계정에 이름이 `fullmodel` 및 `onemonthmodel`인 두 개인 Blob 컨테이너를 만듭니다. 저장소 계정은 중간 계산 결과와 기계 학습 모델을 저장하는 데 사용됩니다. 이 예제를 사용하려면 저장소 계정 이름 및 액세스 키가 필요합니다. 이후 단계를 위해 Azure 저장소 계정 정보와 함께 다음 표를 저장합니다.

 필드 이름| 값 |  
 |------------|------|
 Storage 계정 이름| xxx|
 액세스 키  | xxx|


계산 대상은 필수 조건 목록에서 생성된 Ubuntu DSVM 및 Azure HDInsight 클러스터입니다. 계산 대상은 Machine Learning Workbench 컨텍스트의 계산 리소스이며 Workbench가 실행되는 컴퓨터와 다를 수 있습니다.   

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Machine Learning Workbench를 엽니다.
2.  **프로젝트** 페이지에서 **+** 기호를 선택하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 **TB 단위 데이터에 대한 워크로드 예측**을 입력하고 템플릿을 선택합니다.
5.  **만들기**를 선택합니다.

이 [지침](./tutorial-classifying-iris-part-1.md)을 따라 사전 생성된 git 리포지토리로 Workbench 프로젝트를 만들 수 있습니다.  
`git status`를 실행하여 버전 추적을 위한 파일의 상태를 조사합니다.

## <a name="data-description"></a>데이터 설명

이 예제에서 사용된 데이터는 합성된 서버 작업 데이터입니다. 미국 동부 지역에서 공개적으로 액세스할 수 있는 Azure Blob Storage 계정에 호스팅됩니다. 특정 저장소 계정 정보는 [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)의 `dataFile` 필드에서 "wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>" 형식으로 찾을 수 있습니다. Blob 저장소에서 직접 데이터를 사용할 수 있습니다. 여러 사용자가 저장소를 동시에 사용하는 경우 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)를 사용하여 고유한 저장소로 데이터를 다운로드하면 보다 나은 실험 환경을 조성할 수 있습니다. 

총 데이터 크기는 약 1TB입니다. 각 파일은 약 1-3GB이며 헤더가 없는 CSV 파일 형식입니다. 각 데이터 행은 특정 서버에서의 트랜잭션 로드를 나타냅니다. 데이터 스키마의 자세한 정보는 다음과 같습니다.

열 번호 | 필드 이름| type | 설명 |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    세션 시작 시간
2  |`SessionEnd`    | DateTime | 세션 종료 시간
3 |`ConcurrentConnectionCounts` | 정수  | 동시 연결 수
4 | `MbytesTransferred` | Double | 정규화된 데이터 전송(메가바이트)
5 | `ServiceGrade` | 정수  |  세션에 대한 서비스 등급
6 | `HTTP1` | 정수 |  세션에서 HTTP1 또는 HTTP2 사용
7 |`ServerType` | 정수    |서버 유형
8 |`SubService_1_Load` | Double |   하위 서비스 1 부하
9 | `SubService_2_Load` | Double |  하위 서비스 2 부하
10 | `SubService_3_Load` | Double |     하위 서비스 3 부하
11 |`SubService_4_Load` | Double |  하위 서비스 4 부하
12 | `SubService_5_Load`| Double |      하위 서비스 5 부하
13 |`SecureBytes_Load`  | Double | 보안 바이트 부하
14 |`TotalLoad` | Double | 서버의 총 부하
15 |`ClientIP` | 문자열|    클라이언트 IP 주소
16 |`ServerIP` | 문자열|    서버 IP 주소



앞의 표에 예상되는 데이터 형식이 나열되어 있습니다. 누락된 값 및 더티 데이터 문제로 인해 데이터 형식이 실제로 예상대로 보장되지 않습니다. 데이터 처리 시 이를 고려해야 합니다. 


## <a name="scenario-structure"></a>시나리오 구조

이 예제에 있는 파일은 다음과 같이 구성됩니다.

| 파일 이름 | type | 설명 |
|-----------|------|-------------|
| `Code` | 폴더 | 폴더는 제의 모든 코드를 포함합니다. |
| `Config` | 폴더 | 폴더는 구성 파일을 포함합니다. |
| `Image` | 폴더 | 추가 정보 파일에 대한 이미지를 저장하는 데 사용되는 폴더입니다. |
| `Model` | 폴더 | Blob 저장소에서 다운로드한 모델 파일을 저장하는 데 사용되는 폴더입니다. |
| `Code/etl.py` | Python 파일 | 데이터 준비 및 기능 엔지니어링에 사용되는 Python 파일입니다. |
| `Code/train.py` | Python 파일 | 3클래스 다중 분류 모델을 학습하는 데 사용되는 Python 파일입니다.  |
| `Code/webservice.py` | Python 파일 | 운영화에 사용되는 Python 파일입니다.  |
| `Code/scoring_webservice.py` | Python 파일 |  데이터 변환에 사용되고 웹 서비스를 호출하는 Python 파일입니다. |
| `Code/O16Npreprocessing.py` | Python 파일 | scoring_webservice.py에 대한 데이터를 전처리하는 데 사용되는 Python 파일  |
| `Code/util.py` | Python 파일 | 읽기 및 쓰기 Azure Blob에 대한 코드가 포함된 Python 파일입니다.  
| `Config/storageconfig.json` | JSON 파일 | 1개월 데이터의 처리 및 학습을 위한 중간 결과 및 모델을 저장하는 Azure Blob 컨테이너의 구성 파일입니다. |
| `Config/fulldata_storageconfig.json` | Json 파일 | 전체 데이터 집합의 처리 및 학습을 위한 중간 결과 및 모델을 저장하는 Azure Blob 컨테이너의 구성 파일입니다.|
| `Config/webservice.json` | JSON 파일 | scoring_webservice.py용 구성 파일입니다.|
| `Config/conda_dependencies.yml` | YAML 파일 | Conda 종속성 파일입니다. |
| `Config/conda_dependencies_webservice.yml` | YAML 파일 | 웹 서비스용 Conda 종속성 파일입니다.|
| `Config/dsvm_spark_dependencies.yml` | YAML 파일 | Ubuntu DSVM용 Spark 종속성 파일입니다. |
| `Config/hdi_spark_dependencies.yml` | YAML 파일 | HDInsight Spark 클러스터용 Spark 종속성 파일입니다. |
| `README.md` | Markdown 파일 | 추가 정보 markdown 파일입니다. |
| `Code/download_model.py` | Python 파일 | Azure Blob에서 로컬 디스크로 모델 파일을 다운로드하는 데 사용되는 Python 파일입니다. |
| `Docs/DownloadModelsFromBlob.md` | Markdown 파일 | `Code/download_model.py`를 실행하는 방법에 대한 지침을 포함하는 markdown 파일입니다. |



### <a name="data-flow"></a>데이터 흐름

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py)의 코드는 공개적으로 액세스할 수 있는 컨테이너([`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)의 `dataFile` 필드)에서 데이터를 로드합니다. 데이터 준비 및 기능 엔지니어링을 포함하며 중간 계산 결과 및 모델을 고유한 개인 컨테이너에 저장합니다. The code in [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) loads the intermediate compute results from the private container, trains the multi-class classification model, and writes the trained machine learning model to the private container. 

1개월 데이터 집합 실험에 하나의 컨테이너를 사용하고 전체 데이터 집합 실험에 또 하나의 컨테이너를 사용해야 합니다. 데이터와 모델은 Parquet 파일로 저장되기 때문에 각 파일은 실제로 컨테이너의 폴더이며 여러 개의 Blob을 포함합니다. 결과 컨테이너 모양은 다음과 같습니다.

| Blob 접두사 이름 | type | 설명 |
|-----------|------|-------------|
| featureScaleModel | Parquet | 숫자 기능용 표준 조정기 모델입니다. |
| stringIndexModel | Parquet | 숫자가 아닌 기능용 문자열 인덱서 모델입니다.|
| oneHotEncoderModel|Parquet | 범주 기능용 원 핫 인코더 모델입니다. |
| mlModel | Parquet | 학습된 기계 학습 모델입니다. |
| info| Python pickle 파일 | 학습 시작, 학습 종료, 지속 시간, 학습-테스트 분리를 위한 타임스탬프, 인덱싱 및 원 핫 인코딩(one-hot encoding)을 위한 열을 포함하여 변환된 데이터에 대한 정보입니다.

앞의 표에 있는 모든 파일 및 Blob이 운영화에 사용됩니다.


### <a name="model-development"></a>모델 개발

#### <a name="architecture-diagram"></a>아키텍처 다이어그램


다음 다이어그램은 Machine Learning Workbench를 사용하여 모델을 개발하는 종단 간 워크플로를 보여 줍니다. ![아키텍처](media/scenario-big-data/architecture.PNG)

다음 섹션에서는 Machine Learning Workbench의 원격 계산 대상 기능을 사용하여 모델 개발을 보여 줍니다. 먼저 적은 양의 샘플 데이터를 로드하고 빠른 반복을 위해 Ubuntu DSVM에서 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 스크립트를 실행합니다. 반복 작업을 더 빠르게 하기 위해 추가 인수를 전달하여 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py)에서 수행할 작업을 추가로 제한할 수 있습니다. 마지막으로, HDInsight 클러스터를 사용하여 전체 데이터를 학습합니다.     

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 파일은 데이터를 로드 및 준비하고 기능 엔지니어링을 수행합니다. 두 인수를 허용합니다.
* 중간 계산 결과 및 모델을 저장하기 위한 Blob 저장소 컨테이너에 대한 구성 파일입니다.
* 더 빠른 반복에 대한 디버그 구성 인수입니다.

첫 번째 인수인 `configFilename`은 Blob 저장소 정보를 저장하고 데이터를 로드할 위치를 지정하는 로컬 구성 파일입니다. 기본적으로 [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json)이며 1개월 데이터 실행에 사용됩니다. 또한 전체 데이터 집합 실행에 사용해야 하는 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json)이 포함됩니다. 구성의 콘텐츠는 다음과 같습니다. 

| 필드 | type | 설명 |
|-----------|------|-------------|
| storageAccount | 문자열 | Azure Storage 계정 이름 |
| storageContainer | 문자열 | 중간 결과를 저장하는 Azure Storage 계정의 컨테이너 |
| storageKey | 문자열 |Azure Storage 계정 액세스 키 |
| dataFile|문자열 | 데이터 원본 파일  |
| duration| 문자열 | 데이터 원본 파일에서 데이터 기간|

`Config/storageconfig.json` 및 `Config/fulldata_storageconfig.json`을 모두 수정하여 저장소 계정, 저장소 키 및 중간 결과를 저장할 Blob 컨테이너를 구성합니다. 기본적으로 1개월 데이터 실행을 위한 Blob 컨테이너는 `onemonthmodel`이고 전체 데이터 집합 실행을 위한 Blob 컨테이너는 `fullmodel`입니다. 이 두 컨테이너를 저장소 계정에 만들어야 합니다. [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json)의 `dataFile` 필드는 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py)에서 로드되는 데이터를 구성합니다. `duration` 필드는 데이터가 포함하는 범위를 구성합니다. 기간을 ONE_MONTH로 설정하면 로드된 데이터는 2016년 6월 데이터의 7개 파일 중 하나의 .csv 파일이어야 합니다. 기간이 FULL이면 전체 데이터 집합(1TB)이 로드됩니다. 이 두 구성 파일에서 `dataFile`과 `duration`을 변경할 필요가 없습니다.

두 번째 인수는 DEBUG입니다. FILTER_IP로 설정하면 더 빠른 반복이 가능합니다. 이 매개 변수를 사용하면 스크립트를 디버그할 때 유용합니다.

> [!NOTE]
> 다음 모든 명령에서 모든 인수 변수를 실제 값으로 대체합니다.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Ubuntu DSVM의 Docker에서 모델 개발

#####  <a name="1-set-up-the-compute-target"></a>1. 계산 대상 설정

**파일** > **명령 프롬프트 열기**를 선택하여 Machine Learning Workbench에서 명령줄을 시작합니다. 그런 후 다음을 실행합니다. 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

다음 두 파일은 프로젝트의 aml_config 폴더에 만들어집니다.

-  dockerdsvm.compute: 이 파일은 원격 실행 대상에 대한 연결 및 구성 정보를 포함합니다.
-  dockerdsvm.runconfig: 이 파일은 Workbench 응용 프로그램 내에서 사용되는 실행 옵션의 집합입니다.

dockerdsvm.runconfig로 이동하고 다음과 같이 이러한 필드의 구성을 변경합니다.

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

다음을 실행하여 프로젝트 환경을 준비합니다.

```az ml experiment prepare -c dockerdsvm```


`PrepareEnvironment`를 true로 설정하여 Machine Learning Workbench에서 작업을 제출할 때마다 런타임 환경을 만듭니다. `Config/conda_dependencies.yml` 및 `Config/dsvm_spark_dependencies.yml`에는 런타임 환경의 사용자 지정이 포함됩니다. 이 두 YMAL 파일을 편집하여 언제든지 Conda 종속성, Spark 구성 및 Spark 종속성을 수정할 수 있습니다. 이 예제에서는 `azure-storage`와 `azure-ml-api-sdk`를 `Config/conda_dependencies.yml`에 추가 python 패키지로 추가했습니다. 또한 `Config/dsvm_spark_dependencies.yml`에는 `spark.default.parallelism`, `spark.executor.instances` 및 `spark.executor.cores`를 추가했습니다. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. DSVM Docker의 데이터 준비 및 기능 엔지니어링

DSVM Docker에서 `etl.py` 스크립트를 실행합니다. 특정 서버 IP 주소로 로드된 데이터를 필터링하는 디버그 매개 변수를 사용합니다.

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

가로 패널로 이동하고 **실행**을 선택하여 `etl.py`의 실행 기록을 확인합니다. 실행 시간은 약 2분입니다. 새로운 기능을 포함하도록 코드를 변경하려는 경우 FILTER_IP를 두 번째 인수로 사용하면 더 빠른 반복을 제공합니다. 데이터 집합을 탐색하거나 새로운 기능을 만들기 위해 자체 기계 학습 문제를 다룰 때 이 단계를 여러 번 실행해야 할 수도 있습니다. 

로드할 데이터에 대한 제한을 사용자 지정하고 처리할 데이터를 추가로 필터링하여 모델 개발에서 반복 프로세스의 속도를 높일 수 있습니다. 실험 시 주기적으로 코드의 변경 사항을 Git 리포지토리에 저장해야 합니다. `etl.py`에서 개인 컨테이너에 액세스할 수 있도록 다음 코드를 사용했습니다.

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


그런 다음 디버그 매개 변수 FILTER_IP 없이 DSVM Docker에서 스크립트 `etl.py`를 실행합니다.

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

가로 패널로 이동하고 **실행**을 선택하여 `etl.py`의 실행 기록을 확인합니다. 실행 시간은 약 4분입니다. 이 단계의 처리된 결과는 컨테이너에 저장되고 train.py에서의 학습을 위해 로드됩니다. 또한 문자열 인덱서, 인코더 파이프라인 및 표준 조정기는 개인 컨테이너에 저장됩니다. 운영화에 사용됩니다. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. DSVM Docker에서 모델 학습

DSVM Docker에서 `train.py` 스크립트를 실행합니다.

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

이 단계는 `etl.py`의 실행에서 중간 계산 결과를 로드하고 기계 학습 모델을 학습합니다. 이 단계는 약 2분 정도 소요됩니다.

소규모 데이터에 대한 실험을 성공적으로 마치면 전체 데이터 집합에 대해 실험을 계속 실행할 수 있습니다. 동일한 코드를 사용하여 시작한 다음 인수 및 계산 대상을 변경하여 실험할 수 있습니다.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>HDInsight 클러스터의 모델 개발

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. HDInsight 클러스터를 위한 Machine Learning Workbench에서 계산 대상 만들기

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

다음 두 파일은 aml_config 폴더에 만들어집니다.
    
-  myhdi.compute: 이 파일은 원격 실행 대상에 대한 연결 및 구성 정보를 포함합니다.
-  myhdi.runconfig: 이 파일은 Workbench 응용 프로그램 내에서 사용되는 실행 옵션의 집합입니다.


myhdi.runconfig로 이동하고 다음과 같이 이러한 필드의 구성을 변경합니다.

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

다음을 실행하여 프로젝트 환경을 준비합니다.

```az ml experiment prepare -c myhdi```

이 단계는 7분 정도 걸릴 수 있습니다.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. HDInsight 클러스터의 데이터 준비 및 기능 엔지니어링

HDInsight 클러스터에서 fulldata로 `etl.py` 스크립트를 실행합니다.

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

이 작업은 비교적 오랜 시간(약 2시간 정도) 지속되므로 `-a`를 사용하여 출력 스트리밍을 사용하지 않도록 설정할 수 있습니다. 작업이 완료되면 **실행 기록**에서 드라이버 및 컨트롤러 로그를 볼 수 있습니다. 더 큰 클러스터가 있는 경우 언제나 `Config/hdi_spark_dependencies.yml`의 구성을 재구성하여 더 많은 인스턴스 또는 코어를 사용할 수 있습니다. 예를 들어 4작업자 노드 클러스터가 있는 경우 `spark.executor.instances` 값을 5에서 7로 늘릴 수 있습니다. 저장소 계정의 **fullmodel** 컨테이너에서 이 단계의 결과를 볼 수 있습니다. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. HDInsight 클러스터에서 모델 학습

HDInsight 클러스터에서 `train.py` 스크립트를 실행합니다.

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

이 작업은 비교적 오랜 시간(약 30분 정도) 지속되므로 `-a`를 사용하여 출력 스트리밍을 사용하지 않도록 설정할 수 있습니다.

#### <a name="run-history-exploration"></a>실행 기록 탐색

실행 기록은 Machine Learning Workbench에서 실험을 추적할 수 있는 기능입니다. 기본적으로는 실험 기간을 추적합니다. 특정 예제에서 실험 중 `Code/etl.py`에 대한 전체 데이터 집합으로 이동하면 기간이 크게 늘어나는 것을 알 수 있습니다. 추적을 위해 특정 메트릭을 기록할 수도 있습니다. 메트릭 추적을 사용하도록 설정하려면 다음 코드 줄을 Python 파일의 헤드에 추가합니다.
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
다음은 특정 메트릭을 추적하는 예제입니다.

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Workbench의 오른쪽 사이드바에서 **실행**으로 이동하여 각 Python 파일의 실행 기록을 확인합니다. GitHub 리포지토리로 이동할 수도 있습니다. 이름이 "AMLHistory"로 시작하는 새 분기를 만들어 각 실행에서 스크립트의 변경 사항을 추적합니다. 


### <a name="operationalize-the-model"></a>모델 운영

이 섹션에서는 이전 단계에서 웹 서비스로 만든 모델을 운영합니다. 또한 웹 서비스를 사용하여 작업을 예측하는 방법을 배웁니다. 기계 언어 운영화 CLI(Command-Line Interface)를 사용하여 코드와 종속성을 Docker 이미지로 패키징하고 모델을 컨테이너화된 웹 서비스로 게시합니다.

Machine Learning Workbench에서 명령줄 프롬프트를 사용하여 CLI를 실행할 수 있습니다.  [설치 가이드](./deployment-setup-configuration.md#using-the-cli)에 따라 Ubuntu Linux에서 CLI를 실행할 수도 있습니다. 

> [!NOTE]
> 다음 모든 명령에서 모든 인수 변수를 실제 값으로 대체합니다. 이 섹션을 마치려면 약 40분 정도 걸립니다.
> 

운영화에 대한 환경으로 고유한 문자열을 선택합니다. 여기서 "[고유]" 문자열을 사용하여 선택하는 문자열을 나타냅니다.

1. 운영화를 위한 환경을 만들고 리소스 그룹을 작성합니다.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   `az ml env setup` 명령에서 `--cluster`를 사용하여 컨테이너 서비스를 환경으로 사용할 수 있습니다. [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes)에서 기계 학습 모델을 운영할 수 있습니다. 컨테이너화된 응용 프로그램의 배포, 크기 조정 및 관리 자동화에 [Kubernetes](https://kubernetes.io/)를 사용합니다. 이 명령은 실행되는 데 약 20분 정도 걸립니다. 다음을 사용하여 배포가 성공적으로 완료되었는지 확인합니다. 

        az ml env show -g [unique]rg -n [unique]

   다음을 실행하여 배포 환경을 방금 만든 환경으로 설정합니다.

        az ml env set -g [unique]rg -n [unique]

2. 모델 관리 계정을 만들고 사용합니다. 모델 관리 계정을 만들려면 다음을 실행합니다.

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   다음을 실행하여 운영화를 위한 모델 관리를 사용합니다.

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   모델 관리 계정은 모델 및 웹 서비스를 관리하는 데 사용됩니다. Azure Portal에서 새 모델 관리 계정이 만들어진 것을 볼 수 있습니다. 이 모델 관리 계정을 사용하여 생성된 모델, 매니페스트, Docker 이미지 및 서비스를 볼 수 있습니다.

3. 모델을 다운로드하고 등록합니다.

   **fullmodel** 컨테이너에 있는 모델을 로컬 컴퓨터의 코드 디렉터리에 다운로드합니다. 이름이 "vmlSource.parquet"인 parquet 데이터 파일을 다운로드하지 마십시오. 모델 파일이 아닙니다. 이는 중간 계산 결과입니다. Git 리포지토리에 포함된 모델 파일을 다시 사용할 수도 있습니다. 자세한 내용은 [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md)를 참조하세요. 

   CLI의 `Model` 폴더로 이동하여 다음과 같이 모델을 등록합니다.

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   각 명령의 출력은 다음 단계에서 필요한 모델 ID를 제공합니다. 나중에 사용할 수 있도록 텍스트 파일에 저장합니다.

4. 웹 서비스에 대한 매니페스트를 만듭니다.

   매니페스트는 웹 서비스 코드, 모든 기계 학습 모델 및 런타임 환경 종속성을 포함합니다. CLI의 `Code` 폴더로 이동하여 다음 명령을 실행합니다.

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   출력에 다음 단계의 매니페스트 ID가 제공됩니다. 

   `Code` 디렉터리에서 다음을 실행하여 webservice.py를 테스트할 수 있습니다. 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Docker 이미지를 만듭니다. 

        az ml image create -n [unique]image --manifest-id $manifestID

   출력은 다음 단계에 이미지 ID를 제공합니다. 이 docker 이미지는 컨테이너 서비스에 사용됩니다. 

6. 컨테이너 서비스 클러스터에 웹 서비스를 배포합니다.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   출력은 서비스 ID를 제공합니다. 이를 사용하여 인증 키와 서비스 URL을 가져와야 합니다.

7. Python 코드에서 웹 서비스를 호출하여 미니 일괄 처리에서 점수를 매깁니다.

   다음 명령을 사용하여 인증 키를 가져옵니다.

         az ml service keys realtime -i $ServiceID 

   다음 명령을 사용하여 서비스 점수 매기기 URL을 가져옵니다.

        az ml service usage realtime -i $ServiceID

   올바른 서비스 점수 매기기 URL 및 인증 키로 `./Config/webservice.json`에서 콘텐츠를 수정합니다. 원본 파일에서 "Bearer"를 유지하고 "xxx" 부분을 대체합니다. 
   
   프로젝트의 루트 디렉터리로 이동하고 다음을 사용하여 미니 일괄 처리 점수 매기기에 대해 웹 서비스를 테스트합니다.

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. 웹 서비스의 크기를 조정합니다. 

   자세한 내용은 [Azure Container Service 클러스터에서 운영화의 크기를 조정하는 방법](how-to-scale-clusters.md)을 참조하세요.
 

## <a name="next-steps"></a>다음 단계

이 예제는 Machine Learning Workbench를 사용하여 빅 데이터에서 기계 학습 모델을 교육하고 학습된 모델을 운영하는 방법을 중점적으로 설명합니다. 특히, 다른 계산 대상을 구성 및 사용하고, 메트릭 추적의 기록을 실행하고, 다른 실행을 사용하는 방법을 배웠습니다.

코드를 확장하여 교차 유효성 검사 및 하이퍼 매개 변수 튜닝을 탐색할 수 있습니다. 교차 유효성 검사 및 하이퍼 매개 변수 튜닝에 대한 자세한 내용은 [이 GitHub 리소스](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)를 참조하세요.  

시계열 예측에 대한 자세한 내용은 [이 GitHub 리소스](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)를 참조하세요.
