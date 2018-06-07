---
title: Azure Machine Learning Workbench를 사용하여 하이퍼 매개 변수의 분산 튜닝 | Microsoft Docs
description: 이 시나리오에서는 Azure Machine Learning Workbench를 사용하여 하이퍼 매개 변수의 분산 튜닝을 수행하는 방법을 보여줍니다.
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 70baa1160e9f521ac5533eaa15d4a9ab57433a49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench를 사용하여 하이퍼 매개 변수의 분산 튜닝

이 시나리오에서는 Azure Machine Learning Workbench를 사용하여 scikit-learn API를 구현하는 Machine Learning 알고리즘에서 하이퍼 매개 변수의 분산 튜닝을 확장하는 방법을 보여줍니다. 원격 Docker 컨테이너와 Spark 클러스터를 하이퍼 매개 변수를 튜닝하는 실행 백 엔드로 구성하고 사용하는 방법을 보여줍니다.

## <a name="link-of-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리 링크
공용 GitHub 리포지토리에 대한 링크는 다음과 같습니다. 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>사용 사례 개요

많은 Machine Learning 알고리즘에는 하이퍼 매개 변수라는 하나 이상의 노브가 있어야 합니다. 이러한 노브를 사용하면 사용자 지정 메트릭(예: 정확성, AUC, RMSE)에 따라 측정된 이후 데이터에 대한 성능을 최적화하도록 알고리즘을 튜닝할 수 있습니다. 데이터 과학자는 학습 데이터에 대한 모델을 작성할 때 및 이후 테스트 데이터를 표시하기 전에 하이퍼 매개 변수의 값을 제공해야 합니다. 모델이 알 수 없는 알려진 테스트 데이터에 대한 성능을 향상하도록 학습 데이터에 따라 하이퍼 매개 변수의 값을 설정할 수 있으려면 어떻게 할까요? 
    
하이퍼 매개 변수를 튜닝하는 일반적인 기술은 *교차 유효성 검사*와 결합된 *그리드 검색*입니다. 교차 유효성 검사는 학습 집합에서 학습된 모델이 테스트 집합을 예측하는 정도를 평가하는 기술입니다. 먼저 이 기술을 사용하여 데이터 집합을 K단계로 나누고 라운드 로빈 방식으로 K번 알고리즘을 학습합니다. “보류된 단계”를 제외한 모든 단계에서 이를 수행합니다. 보류된 K단계에서 K 모델 메트릭의 평균 값을 계산합니다. *교차 유효성 검사된 성능 예측*이라는 평균 값은 K 모델을 만들 때 사용되는 하이퍼 매개 변수의 값에 따라 달라집니다. 하이퍼 매개 변수를 튜닝하는 경우 후보 하이퍼 매개 변수 값의 공간을 검색하여 교차 유효성 검사 성능 추정을 최적화하는 값을 찾습니다. 그리드 검색은 일반적인 검색 기술입니다. 그리드 검색에서 여러 하이퍼 매개 변수의 후보 값 공간은 개별 하이퍼 매개 변수의 후보 값 집합의 교차곱입니다. 

교차 유효성 검사를 사용하는 그리드 검색은 시간이 오래 걸릴 수 있습니다. 알고리즘에 5개의 후보 값을 가진 5개의 하이퍼 매개 변수가 있으면 K=5단계를 사용합니다. 그 다음, 5<sup>6</sup>=15625개 모델을 학습하여 그리드 검색을 완료합니다. 다행히 교차 유효성 검사를 사용하는 그리드 검색은 병렬 프로시저이며 이러한 모든 모델은 병렬로 학습할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
* Workbench를 설치하고 계정을 만들기 위해 [빠른 시작 설치 및 만들기](../service/quickstart-installation.md)에 따라 설치된 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)의 복사본
* 이 시나리오에서는 Docker 엔진이 로컬로 설치된 Windows 10 또는 MacOS에서 Azure ML Workbench를 실행 중이라고 가정합니다. 
* 원격 Docker 컨테이너를 사용하는 시나리오를 실행하려면 [지침](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm)에 따라 Ubuntu DSVM(데이터 과학 Virtual Machine)을 프로비전합니다. 적어도 8개의 코어와 28GB의 메모리가 있는 가상 머신을 사용하는 것이 좋습니다. 가상 머신의 D4 인스턴스에는 이러한 용량이 포함됩니다. 
* 이 시나리오를 Spark 클러스터에서 실행하려면 이러한 [지침](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)에 따라 Spark HDInsight 클러스터를 프로비전합니다. 헤더 및 작업자 노드 모두에서 다음 구성을 포함한 클러스터를 사용하는 것이 좋습니다.
    - 4개의 작업자 노드
    - 8개의 코어
    - 28Gb의 메모리  
      
  가상 머신의 D4 인스턴스에는 이러한 용량이 포함됩니다. 

     **문제 해결**: Azure 구독에는 사용할 수 있는 코어 수에 할당량이 있을 수 있습니다. Azure Portal에서는 총 코어 수가 할당량을 초과하는 클러스터를 만들 수 없습니다. 할당량을 찾으려면 Azure Portal에서 구독 섹션으로 이동하고 클러스터를 배포하는 데 사용되는 구독을 클릭하고 **사용량+할당량**을 클릭합니다. 일반적으로 할당량은 Azure 지역별로 정의되고 충분한 코어를 사용 가능한 지역에서 Spark 클러스터를 배포하도록 선택할 수 있습니다. 

* 데이터 집합을 저장하는 데 사용되는 Azure Storage 계정을 만듭니다. [지침](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)에 따라 저장소 계정을 만듭니다.

## <a name="data-description"></a>데이터 설명

[TalkingData 데이터 집합](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data)을 사용합니다. 이 데이터 집합에는 휴대폰에 있는 앱의 이벤트가 있습니다. 휴대폰 및 최근에 사용자가 생성한 이벤트의 종류에 따라 휴대폰 사용자의 성별 및 연령 범주를 예측하는 것이 목표입니다.  

## <a name="scenario-structure"></a>시나리오 구조
이 시나리오는 GitHub 리포지토리에 여러 폴더가 있습니다. 코드 및 구성 파일은 **코드** 폴더에 있고 모든 설명서는 **문서** 폴더에 있고 모든 이미지는 **이미지** 폴더에 있습니다. 루트 폴더에는 이 시나리오에 대한 간단한 요약을 포함하는 추가 정보 파일이 있습니다.

### <a name="getting-started"></a>시작
Azure Machine Learning Workbench 아이콘을 클릭하여 Azure Machine Learning Workbench를 실행하고 "하이퍼 매개 변수의 분산 튜닝" 템플릿에서 프로젝트를 만듭니다. [빠른 시작 설치 및 만들기](quickstart-installation.md)에서 새 프로젝트를 만드는 방법에 자세한 지침을 찾을 수 있습니다.   

### <a name="configuration-of-execution-environments"></a>실행 환경 구성
원격 Docker 컨테이너 및 Spark 클러스터에서 코드를 실행하는 방법을 보여줍니다. 환경 모두에 공통적으로 적용되는 설정을 설명하기 시작합니다. 

Azure Machine Learning Workbench의 기본 Docker 컨테이너에서 제공되지 않은 [scikit-learn](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost) 및 [azure-storage](https://pypi.python.org/pypi/azure-storage) 패키지를 사용합니다. azure-storage 패키지는 [cryptography](https://pypi.python.org/pypi/cryptography) 및 [azure](https://pypi.python.org/pypi/azure) 패키지를 설치해야 합니다. 이러한 패키지를 Docker 컨테이너 및 Spark 클러스터의 노드에 설치하려면 conda_dependencies.yml 파일을 수정합니다.

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

수정된 conda\_dependencies.yml 파일은 자습서의 aml_config 디렉터리에 저장됩니다. 

다음 단계에서는 Azure 계정에 실행 환경을 연결합니다. AML Workbench의 왼쪽 위 모서리에서 파일 메뉴를 클릭합니다. 그리고 “명령 프롬프트 열기”를 선택합니다. 그런 다음 CLI에서 실행합니다.

    az login

메시지가 나타납니다.

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

이 웹 페이지로 이동하고, 코드를 입력하고, Azure 계정에 로그인합니다. 이 단계 이후 CLI에서 실행하고

    az account list -o table

AML Workbench 작업 영역 계정이 있는 Azure 구독 ID를 찾습니다. 마지막으로 CLI에서 실행하여

    az account set -s <subscription ID>

Azure 구독에 대한 연결을 완료합니다.

다음 두 섹션에서는 원격 Docker 및 Spark 클러스터의 구성을 완료하는 방법을 보여줍니다.

#### <a name="configuration-of-remote-docker-container"></a>원격 Docker 컨테이너 구성

 원격 Docker 컨테이너를 설정하려면 CLI에서

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

DSVM의 IP 주소, 사용자 이름 및 암호를 사용하여 실행합니다. Azure Portal에 있는 DSVM 페이지의 개요 섹션에서 DSVM의 IP 주소를 찾을 수 있습니다.

![VM IP](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Spark 클러스터 구성

Spark 환경을 설정하려면 CLI에서

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

클러스터의 이름, 클러스터의 SSH 사용자 이름 및 암호를 사용하여 실행합니다. 클러스터를 프로비전하는 동안 변경하지 않으면 SSH 사용자 이름의 기본값은 `sshuser`입니다. 클러스터의 이름은 Azure Portal에 있는 클러스터 페이지의 속성 섹션에서 찾을 수 있습니다.

![클러스터 이름](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

spark-sklearn 패키지를 사용하여 하이퍼 매개 변수의 분산 튜닝을 위한 실행 환경으로 Spark를 사용합니다. Spark 실행 환경을 사용하는 경우 이 패키지를 설치하기 위해 spark_dependencies.yml 파일을 수정했습니다.

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

수정된 spark\_dependencies.yml 파일은 자습서의 aml_config 디렉터리에 저장됩니다. 

### <a name="data-ingestion"></a>데이터 수집
이 시나리오의 코드는 데이터가 Azure Blob Storage에 저장되었다고 가정합니다. 데이터를 Kaggle 사이트에서 컴퓨터에 다운로드하고 Blob Storage에 업로드하는 방법을 보여줍니다. 그런 다음 Blob Storage에서 데이터를 읽는 방법을 보여줍니다. 

Kaggle에서 데이터를 다운로드하려면 [데이터 집합 페이지](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data)로 이동하고 다운로드 단추를 클릭합니다. Kaggle에 로그인하라는 요청을 받습니다. 로그인한 후에 데이터 집합 페이지로 다시 리디렉션됩니다. 처음 7개의 파일을 선택하고 다운로드 단추를 클릭하여 왼쪽 열에서 다운로드합니다. 다운로드한 파일의 전체 크기는 289MB입니다. Blob Storage에 이러한 파일을 업로드하려면 저장소 계정에 blob Storage 컨테이너 '데이터 집합'을 만듭니다. 저장소 계정의 Azure 페이지로 이동하고 Blob을 클릭하고 +컨테이너를 클릭하여 수행할 수 있습니다. '데이터 집합'을 이름으로 입력하고 확인을 클릭합니다. 다음 스크린샷에서는 다음과 같은 단계를 보여줍니다.

![Blob 열기](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![컨테이너 열기](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

그런 후에 목록에서 데이터 집합 컨테이너를 선택하고 업로드 단추를 클릭합니다. Azure Portal에서는 여러 파일을 동시에 업로드할 수 있습니다. "Blob 업로드" 섹션에서 폴더 단추를 클릭하고, 데이터 집합의 모든 파일을 선택하고, 열기를 클릭한 후 업로드를 클릭합니다. 다음 스크린샷은 이러한 단계를 보여 줍니다.

![Blob 업로드](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

인터넷 연결에 따라 파일을 업로드하는 데 몇 분이 걸립니다. 

코드에서 [Azure Storage SDK](https://azure-storage.readthedocs.io/en/latest/)를 사용하여 Blob Storage에서 현재 실행 환경으로 데이터 집합을 다운로드합니다. 다운로드는 load_data.py 파일의 load\_data() 함수에서 수행됩니다. 이 코드를 사용하려면 <ACCOUNT_NAME> 및 <ACCOUNT_KEY>를 데이터 집합을 호스트하는 저장소 계정의 이름 및 기본 키로 바꿔야 합니다. 저장소 계정의 Azure 페이지에서 왼쪽 위 모서리에 계정 이름이 표시됩니다. 계정 키를 가져오려면 저장소 계정의 Azure 페이지에서 선택키를 선택하고(데이터 수집 섹션의 첫 번째 스크린샷 참조) 키 열의 첫 번째 행에서 긴 문자열을 복사합니다.
 
![선택키](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

load_data() 함수의 다음 코드는 단일 파일을 다운로드합니다.

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

load_data.py 파일을 수동으로 실행할 필요가 없습니다. 나중에 다른 파일에서 호출됩니다.

### <a name="feature-engineering"></a>기능 엔지니어링
모든 기능을 계산하는 코드는 feature\_engineering.py 파일에 있습니다. feature_engineering.py 파일을 수동으로 실행할 필요가 없습니다. 나중에 다른 파일에서 호출됩니다.

여러 개의 기능 집합을 만듭니다.
* 휴대폰의 브랜드 및 모델 원 핫 인코딩(one-hot encoding)(one\_hot\_brand_model 함수)
* 매일 사용자에 의해 생성된 이벤트의 비율(weekday\_hour_features 함수)
* 매시간 사용자에 의해 생성된 이벤트의 비율(weekday\_hour_features 함수)
* 날짜 및 시간의 조합별로 사용자에 의해 생성된 이벤트의 비율(weekday\_hour_features 함수)
* 각 앱에서 사용자에 의해 생성된 이벤트의 비율(one\_hot\_app_labels 함수)
* 각 앱 레이블에서 사용자에 의해 생성된 이벤트의 비율(one\_hot\_app_labels 함수)
* 각 앱 범주에서 사용자에 의해 생성된 이벤트의 비율(text\_category_features 함수)
* 생성된 이벤트에 대해 사용된 앱의 범주에 대한 표시기 기능(one\_hot_category 함수)

이러한 기능은 Kaggle 커널 [앱 및 레이블의 선형 모델](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels)에 기반했습니다.

이러한 기능을 계산하는 데 상당한 양의 메모리가 필요합니다. 처음에는 16GB RAM이 있는 로컬 환경에서 기능을 계산하려고 했습니다. 처음 4개의 기능 집합을 계산할 수 있지만 다섯 번째 집합을 계산할 때 '메모리 부족' 오류가 수신되었습니다. 처음 4개 기능 집합의 계산은 singleVMsmall.py 파일에 있고 

     az ml experiment submit -c local .\singleVMsmall.py   

CLI 창에서 실행하여 로컬 환경에서 실행할 수 있습니다.

로컬 환경이 모든 기능 집합을 계산하기에는 너무 작기 때문에 더 큰 메모리가 있는 원격 DSVM로 전환합니다. DSVM 내의 실행은 AML Workbench에서 관리되는 Docker 컨테이너 내에서 수행됩니다. 이 DSVM을 사용하여 모든 기능을 계산하고 모델을 학습하고 하이퍼 매개 변수를 튜닝할 수 있습니다(다음 섹션 참조). singleVM.py 파일에는 전체 기능 계산 및 모델링 코드가 있습니다. 다음 섹션에서는 원격 DSVM에서 singleVM.py를 실행하는 방법을 보여줍니다. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>원격 DSVM을 사용하여 하이퍼 매개 변수 튜닝
그라데이션 트리 승격의 [xgboost](https://anaconda.org/conda-forge/xgboost) 구현 [1]을 사용합니다. 또한 [scikit-learn](http://scikit-learn.org/) 패키지를 사용하여 xgboost의 하이퍼 매개 변수도 튜닝합니다. Xgboost가 scikit-learn 패키지의 일부가 아니지만 scikit-learn API를 구현하고 따라서 scikit-learn의 기능을 튜닝하는 하이퍼 매개 변수와 함께 사용될 수 있습니다. 

Xgboost에는 [여기서](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md) 설명하는 8개의 하이퍼 매개 변수가 있습니다.
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objective  
 
처음에는 원격 DSVM을 사용하고 후보 값의 소규모 그리드에서 하이퍼 매개 변수를 조정합니다.

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

이 그리드에는 하이퍼 매개 변수 값의 4가지 조합이 있습니다. 5중 교차 유효성 검사를 통해 xgboost를 4 x 5 = 20회 실행하게 됩니다. 모델의 성능을 측정하려면 음의 로그 손실 메트릭을 사용합니다. 다음 코드는 교차 유효성 검사된 음의 로그 손실을 최대화하는 그리드에서 하이퍼 매개 변수의 값을 찾습니다. 또한 코드는 다음 값을 사용하여 전체 학습 집합에 대한 최종 모델을 학습합니다.

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

모델을 만든 후에 하이퍼 매개 변수 튜닝의 결과를 저장합니다. AML Workbench의 로깅 API를 사용하여 하이퍼 매개 변수의 가장 적합한 값 및 음의 로그 손실의 해당 교차 유효성 검사된 예측값을 저장합니다.

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

또한 그리드에서 하이퍼 매개 변수 값을 모두 조합한 교차 유효성 검사된 음의 로그 손실을 사용하여 sweeping_results.txt 파일을 만듭니다.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

이 파일은 특별한 ./outputs 디렉터리에 저장됩니다. 나중에 다운로드하는 방법을 보여줍니다.  

 원격 DSVM에서 처음으로 singleVM.py를 실행하기 전에 

    az ml experiment prepare -c dsvm

CLI에서 실행하여 Docker 컨테이너를 만듭니다. Docker 컨테이너를 생성하는 데 몇 분 정도가 걸립니다. 그 후에 DSVM에서 singleVM.py를 실행합니다.

    az ml experiment submit -c dsvm .\singleVM.py

이 명령은 DSVM에 8개의 코어와 28GB의 메모리가 있는 경우 1시간 38분 내에 완료됩니다. AML Workbench의 실행 기록 창에서 기록된 값을 볼 수 있습니다.

![실행 기록](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

기본적으로 실행 기록 창은 첫 번째 1~2개의 기록된 값의 값 및 그래프를 표시합니다. 하이퍼 매개 변수에서 선택한 값의 전체 목록을 보려면 이전 스크린샷에서 빨간색 원으로 표시된 설정 아이콘을 클릭합니다. 그런 다음 테이블에 표시될 하이퍼 매개 변수를 선택합니다. 또한 실행 기록 창의 위쪽에 표시된 그래프를 선택하려면 파란색 원으로 표시된 설정 아이콘을 클릭하고 목록에서 그래프를 선택합니다. 

실행 속성 창에서 선택한 하이퍼 매개 변수 값을 검사할 수 있습니다. 

![실행 속성](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

실행 속성 창의 오른쪽 위 모서리에는 '.\output' 폴더에 생성된 모든 파일의 목록을 포함한 출력 파일 섹션이 있습니다. 여기에서 sweeping\_results.txt를 선택하고 다운로드 단추를 클릭하여 다운로드할 수 있습니다. sweeping_results.txt는 다음과 같이 출력됩니다.

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Spark 클러스터를 사용하여 하이퍼 매개 변수 튜닝
Spark 클러스터를 사용하여 하이퍼 매개 변수의 튜닝을 확장하고 큰 그리드를 사용합니다. 새 그리드는 다음과 같습니다.

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

이 그리드에는 하이퍼 매개 변수 값의 16가지 조합이 있습니다. 5단계 교차 유효성 검사를 사용하므로 16x5=80번 xgboost를 실행합니다.

scikit-learn 패키지에는 Spark 클러스터를 사용하여 하이퍼 매개 변수를 튜닝하는 네이티브 지원이 없습니다. 다행히 Databricks의 [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) 패키지는 이 간격을 채웁니다. 이 패키지에서는 scikit-learn의 GridSearchCV 함수와 거의 동일한 API를 포함한 GridSearchCV 함수를 제공합니다. Spark를 사용하여 spark-sklearn을 사용하고 하이퍼 매개 변수를 튜닝하려면 Spark 컨텍스트를 만들어야 합니다.

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

그런 다음 

    from sklearn.model_selection import GridSearchCV

다음으로 바꿀 수 있습니다. 

    from spark_sklearn import GridSearchCV

또한 scikit-learn에서 spark-sklearn으로 GridSearchCV에 대한 호출을 바꿉니다.

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Spark를 사용하여 하이퍼 매개 변수를 튜닝하는 최종 코드는 distributed\_sweep.py 파일에 있습니다. singleVM.py와 distributed_sweep.py 간의 차이점은 그리드의 정의 및 4개의 추가 코드 줄에 있습니다. AML Workbench 서비스로 인해 실행 환경을 원격 DSVM에서 Spark 클러스터로 변경할 때 로깅 코드는 변경되지 않습니다.

Spark 클러스터에서 처음으로 distributed_sweep.py를 실행하기 전에 Python 패키지를 설치해야 합니다. 이 작업은 

    az ml experiment prepare -c spark

CLI에서 실행하여 수행될 수 있습니다. 이 설치는 몇 분 정도 걸릴 수 있습니다. 그 후에 Spark 클러스터에서 distributed_sweep.py를 실행합니다.

    az ml experiment submit -c spark .\distributed_sweep.py

이 명령은 Spark 클러스터에 28GB의 메모리를 가진 6개의 작업자 노드가 있는 경우 1시간 6분 내에 완료됩니다. 하이퍼 매개 변수 조정의 결과는 원격 DSVM 실행과 동일한 방식으로 Azure Machine Learning Workbench에서 액세스할 수 있습니다. (즉 로그, 하이퍼 매개 변수의 최적 값 및 sweeping_results.txt 파일)

### <a name="architecture-diagram"></a>아키텍처 다이어그램

다음 다이어그램에서는 전체 워크플로를 보여줍니다.![architecture](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>결론 

이 시나리오에서는 Azure Machine Learning Workbench를 사용하여 원격 가상 머신 및 Spark 클러스터에서 하이퍼 매개 변수의 조정을 수행하는 방법을 살펴보았습니다. Azure Machine Learning Workbench에서 제공하는 실행 환경의 구성을 용이하게 하는 도구를 살펴보았습니다. 또한 이들 도구를 사용하면 실행 환경 간에 쉽게 전환할 수 있습니다. 

## <a name="references"></a>참조

[1] T. Chen 및 C. Guestrin [XGBoost: 확장성 있는 트리 부팅 시스템](https://arxiv.org/abs/1603.02754) KDD 2016




