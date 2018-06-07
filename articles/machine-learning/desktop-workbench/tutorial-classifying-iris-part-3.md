---
title: Azure Machine Learning 서비스용 모델 자습서 배포
description: 이 자습서 전체에서 Azure Machine Learning 서비스를 사용하는 방법을 보여줍니다. 3부이며 배포 모델을 설명합니다.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ms.openlocfilehash: 8eb6470afb44ba1b41e0077a890a36601db5387e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>자습서 3: 아이리스 분류: 배포 모델
Azure Machine Learning(미리 보기)는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다. 데이터 과학자는 클라우드 규모로 데이터를 준비하고, 실험을 개발하며, 모델을 배포하는 데 사용할 수 있습니다.

이 자습서는 **3부로 구성된 시리즈 중 제3부**입니다. 자습서의 이 부분에서는 Machine Learning(미리 보기)을 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 모델 파일 찾기
> * 점수 매기기 스크립트 및 스키마 파일 생성
> * 환경 준비
> * 실시간 웹 서비스 만들기
> * 실시간 웹 서비스 실행
> * 출력 Blob 데이터 검사 

이 자습서에서는 변함 없는 [아이리스 꽃 데이터 집합](https://en.wikipedia.org/wiki/Iris_flower_data_set)을 사용합니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.
- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
- [빠른 시작](../service/quickstart-installation.md)에 설명된 대로 설치된 실험 계정 및 Azure Machine Learning Workbench
- [자습서 2부](tutorial-classifying-iris-part-2.md)에서 모델 분류
- 로컬로 설치되고 실행되는 Docker 엔진

## <a name="download-the-model-pickle-file"></a>모델 pickle 파일 다운로드
이 자습서의 이전 부분에서는 **iris_sklearn.py** 스크립트가 Machine Learning Workbench에서 로컬로 실행되었습니다. 이 작업은 인기 있는 [pickle](https://docs.python.org/3/library/pickle.html) Python 개체 직렬화 패키지를 사용하여 로지스틱 회귀 모델을 직렬화했습니다. 

1. Machine Learning Workbench 응용 프로그램을 엽니다. 그런 다음, 이 자습서 시리즈의 이전 부분에서 만든 **myIris** 프로젝트를 엽니다.

2. 프로젝트가 열리면 왼쪽 창에 있는 **파일** 단추(폴더 아이콘)를 선택하여 프로젝트 폴더의 파일 목록을 엽니다.

3. **iris_sklearn.py** 파일을 선택합니다. Python 코드가 Workbench의 새 텍스트 편집기 탭에서 열립니다.

4. **iris_sklearn.py** 파일을 검토하여 pickle 파일이 생성된 위치를 확인합니다. Ctrl+F를 선택하여 **찾기** 대화 상자를 연 다음 Python 코드에서 **pickle**이라는 단어를 찾습니다.

   이 코드 조각에서는 pickle 처리된 출력 파일이 생성된 방법을 보여 줍니다. 디스크에서 출력 pickle 파일의 이름은 **model.pkl**입니다. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 이전 실행의 출력 파일에서 모델 pickle 파일을 찾습니다.
   
   **iris_sklearn.py** 스크립트를 실행한 경우 모델 파일은 **outputs** 폴더에 **model.pkl** 이름으로 기록되어 있습니다. 이 폴더는 로컬 프로젝트 폴더가 아니라 스크립트를 실행하도록 선택한 실행 환경에 있습니다. 
   
   a. 파일을 찾으려면 왼쪽 창의 **실행** 단추(시계 아이콘)를 선택하여 **모든 실행**의 목록을 엽니다. 

   나. **모든 실행** 탭이 열립니다. 실행 테이블에서 대상이 **로컬**이고 스크립트 이름이 **iris_sklearn.py**인 최근 실행 중 하나를 선택합니다. 

   다. **실행 속성** 창이 열립니다. 창의 오른쪽 위 섹션에서 **출력** 섹션을 확인합니다.

   d. pickle 파일을 다운로드하려면 **model.pkl** 파일 옆에 있는 확인란을 선택한 다음, **다운로드**를 선택합니다. 프로젝트 폴더의 루트에 파일을 저장합니다. 이후 단계에서 파일이 필요합니다.

   ![pickle 파일 다운로드](media/tutorial-classifying-iris/download_model.png)

   [큰 데이터 파일을 읽고 쓰는 방법](how-to-read-write-files.md)에서 `outputs` 폴더에 대해 자세히 알아보세요.

## <a name="get-the-scoring-script-and-schema-files"></a>점수 매기기 스크립트 및 스키마 파일 가져오기
모델 파일과 함께 웹 서비스를 배포하려면 점수 매기기 스크립트도 필요합니다. 선택적으로 웹 서비스 입력 데이터에 대한 스키마가 필요합니다. 점수 매기기 스크립트는 현재 폴더에서 **model.pkl** 파일을 로드하고, 이를 사용하여 새 예측을 생성합니다.

1. Machine Learning Workbench 응용 프로그램을 엽니다. 그런 다음, 자습서 시리즈의 이전 부분에서 만든 **myIris** 프로젝트를 엽니다.

2. 프로젝트가 열리면 왼쪽 창에 있는 **파일** 단추(폴더 아이콘)를 선택하여 프로젝트 폴더의 파일 목록을 엽니다.

3. **score_iris.py** 파일을 선택합니다. Python 스크립트가 열립니다. 이 파일은 점수 매기기 파일로 사용됩니다.

   ![점수 매기기 파일](media/tutorial-classifying-iris/model_data_collection.png)

4. 스키마 파일을 가져오려면 스크립트를 실행합니다. 명령 모음에서 **로컬** 환경과 **score_iris.py** 스크립트를 선택한 다음, **실행**을 선택합니다. 

   이 스크립트는 모델에 필요한 입력 데이터 스키마를 캡처하는 **출력** 섹션에 JSON 파일을 만듭니다.

6. **프로젝트 대시보드** 창의 오른쪽에서 **작업** 창을 확인합니다. 최신 **score_iris.py** 작업에 녹색 **완료됨** 상태가 표시될 때까지 기다립니다. 그런 다음, 최신 작업 실행에 대한 **score_iris.py** 하이퍼링크를 선택하여 실행 세부 정보를 확인합니다. 

7. **실행 속성** 창의 **출력** 섹션에서 새로 만든 **service_schema.json** 파일을 선택합니다. 파일 이름 옆의 확인란을 선택한 다음 **다운로드**를 선택합니다. 파일을 프로젝트 루트 폴더에 저장합니다.

8. **score_iris.py** 스크립트를 연 이전 탭으로 돌아갑니다. 데이터 수집을 사용하여 웹 서비스에서 모델 입력 및 예측을 캡처할 수 있습니다. 다음 단계는 데이터 수집에 특히 중요합니다.

9. 모델 데이터 수집 기능이 포함되어 있으므로 **ModelDataCollector** 클래스를 가져오는 파일의 맨 위에 있는 코드를 검토합니다.

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. **init()** 함수에서 **ModelDataCollector**를 인스턴스화하는 다음 코드 줄을 검토합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

11. **run(input_df)** 함수에서 입력 및 예측 데이터를 수집하는 다음 코드 줄을 검토합니다.

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

이제 모델을 조작할 환경이 준비되었습니다.

## <a name="prepare-to-operationalize-locally-for-development-and-testing-your-service"></a>로컬에서 조작 준비 [서비스 개발 및 테스트용]
_로컬 모드_ 배포를 사용하여 로컬 컴퓨터의 Docker 컨테이너에서 실행합니다.

개발 및 테스트에는 _로컬 모드_를 사용할 수 있습니다. 다음 단계를 수행하여 모델을 조작하려면 Docker 엔진이 로컬로 실행되어야 합니다. 각 명령의 끝에 `-h` 플래그를 사용하여 해당 도움말 메시지를 표시할 수 있습니다.

>[!NOTE]
>Docker 엔진을 로컬에 설치하지 않았다면 Azure에서 배포용 클러스터를 만들어 계속 진행할 수 있습니다. 요금이 지속적으로 발생하지 않도록 자습서 완료 후에 해당 클러스터를 재사용하거나 삭제할 수 있습니다.

>[!NOTE]
>로컬로 배포 된 웹 서비스는 Azure Portal의 서비스 목록에 표시되지 않습니다. 웹 서비스는 로컬 컴퓨터의 Docker에서 실행됩니다.

1. CLI(명령줄 인터페이스)를 엽니다.
   Machine Learning Workbench 응용 프로그램의 **파일** 메뉴에서 **명령 프롬프트 열기**를 선택합니다.

   명령줄 프롬프트가 현재 프로젝트 폴더 위치인 **c:\temp\myIris>** 에서 열립니다.


2. **Microsoft.ContainerRegistry** Azure 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 3단계에서 환경을 만들려면 먼저 이 리소스 공급자를 등록해야 합니다. 다음 명령을 사용하여 이미 등록되어 있는지 확인할 수 있습니다.
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   다음과 유사한 출력이 표시됩니다. 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   **Microsoft.ContainerRegistry**가 등록되어 있지 않으면 다음 명령을 사용하여 등록할 수 있습니다.
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   등록은 몇 분 정도 걸릴 수 있습니다. 이전 **az provider list** 명령 또는 다음 명령을 사용하여 해당 상태를 확인할 수 있습니다.
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   출력의 세 번째 줄에 **“registrationState”: “Registering”**이 표시됩니다. 몇 분 정도 기다렸다가 출력에 **"registrationState": "Registered"** 가 표시될 때까지 **show** 명령을 반복합니다.

   >[!NOTE] 
   ACS 클러스터를 배포하는 경우 정확히 동일한 방법을 사용하여 **Microsoft.ContainerService** 리소스 공급자를 등록해야 합니다.

3. 환경을 만듭니다. 환경당 한 번씩 이 단계를 실행해야 합니다. 예를 들어, 개발 환경에 대해 한 번, 프로덕션에 대해 한 번씩 실행합니다. 첫 번째 환경에는 _로컬 모드_를 사용합니다. 나중에 다음 명령에서 `-c` 또는 `--cluster` 스위치를 사용하여 환경을 _클러스터 모드_로 설정할 수 있습니다.

   다음 설정 명령에서는 구독에 대한 참가자 액세스 권한이 있어야 합니다. 해당 권한이 없는 경우 적어도 배포하려는 리소스 그룹에 대한 참가자 액세스 권한이 필요합니다. 후자의 경우 `-g` 플래그를 사용하여 setup 명령의 일부로 리소스 그룹 이름을 지정해야 합니다. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   화면의 지침에 따라 Docker 이미지 저장을 위한 저장소 계정, Docker 이미지 나열을 위한 Azure 컨테이너 레지스트리, 원격 분석 수집을 위한 Azure Application Insights 계정을 프로비전합니다. `-c` 스위치를 사용하면 이 명령에서 컨테이너 서비스 클러스터를 추가로 만들 수 있습니다.
   
   클러스터 이름은 환경을 식별할 수 있는 방법입니다. 위치는 Azure Portal에서 만든 모델 관리 계정의 위치와 동일해야 합니다.

   환경이 성공적으로 설정되었는지 확인하려면 다음 명령을 사용하여 상태를 확인합니다.

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   5단계에서 환경을 설정하기 전에 그림과 같이 "프로비전 상태" 값이 "성공"인지 확인하세요.

   ![프로비전 상태](media/tutorial-classifying-iris/provisioning_state.png)
 
3. 이 자습서의 이전 부분에서 모델 관리 계정을 만들지 않았다면 지금 해당 계정을 만듭니다. 이 설정은 한 번만 수행하면 됩니다.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. 모델 관리 계정을 설정합니다.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. 환경을 설정합니다.

   설정이 완료되면 다음 명령을 사용하여 환경 조작에 필요한 환경 변수를 설정합니다. 앞서 3단계에서 사용한 이름과 동일한 환경 이름을 사용합니다. 설정 프로세스가 완료될 때 명령 창에 출력된 것과 동일한 리소스 그룹 이름을 사용합니다.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. 로컬 웹 서비스 배포를 위한 조작 가능한 환경을 제대로 구성했는지 확인하려면 다음 명령을 입력합니다.

   ```azurecli
   az ml env show
   ```

이제 실시간 웹 서비스를 만들 준비가 되었습니다.

>[!NOTE]
>후속 웹 서비스 배포를 위해 모델 관리 계정 및 환경을 다시 사용할 수 있습니다. 각 웹 서비스에 대해 만들 필요가 없습니다. 계정 또는 환경은 연결된 여러 웹 서비스를 가질 수 있습니다.

## <a name="create-a-real-time-web-service-in-one-command"></a>한 가지 명령으로 실시간 웹 서비스 만들기
1. 실시간 웹 서비스를 만들려면 다음 명령을 사용합니다.

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   이 명령은 나중에 사용할 수 있는 웹 서비스 ID를 생성합니다.

   다음 스위치가 **az ml service create realtime** 명령에 사용됩니다.

   * `-f`: 점수 매기기 스크립트 파일 이름입니다.

   * `--model-file`: 모델 파일입니다. 이 경우 pickle 처리된 model.pkl 파일입니다.

   * `-s`: 서비스 스키마입니다. 이는 이전 단계에서 **score_iris.py** 스크립트를 로컬로 실행하여 생성되었습니다.

   * `-n`: 모두 소문자여야 하는 앱 이름입니다.

   * `-r`: 모델의 런타임입니다. 이 경우 Python 모델입니다. 유효한 런타임은 `python` 및 `spark-py`입니다.

   * `--collect-model-data true`: 이 스위치는 데이터 컬렉션을 활성화합니다.

   * `-c`: 추가 패키지가 지정된 conda 종속성 파일의 경로입니다.

   >[!IMPORTANT]
   >서비스 이름(새 Docker 이미지 이름이기도 함)은 모두 소문자여야 합니다. 그렇지 않으면 오류가 발생합니다. 

2. 명령을 실행하면 환경 설정의 일부로 만든 저장소 계정에 모델 및 점수 매기기 파일이 업로드됩니다. 배포 프로세스에서는 모델, 스키마, 점수 매기기 파일이 포함된 Docker 이미지를 빌드하고, 이를 **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>** Azure 컨테이너 레지스트리에 푸시합니다. 

   명령은 이미지를 로컬로 컴퓨터에 가져온 다음, 해당 이미지를 기반으로 하여 Docker 컨테이너를 시작합니다. 환경이 클러스터 모드로 구성된 경우 Docker 컨테이너는 Azure Cloud Services Kubernetes 클러스터에 대신 배포됩니다.

   배포의 일부로 로컬 컴퓨터에 웹 서비스에 대한 HTTP REST 끝점이 만들어집니다. 몇 분 후에 명령이 성공 메시지와 함께 완료됩니다. 웹 서비스에서 작업할 준비가 준비되었습니다!

3. 실행 중인 Docker 컨테이너를 보려면 **docker ps** 명령을 사용합니다.

   ```azurecli
   docker ps
   ```

## <a name="optional-alternative-create-a-real-time-web-service-by-using-separate-commands"></a>[선택적 대안] 별도의 명령을 사용하여 실시간 웹 서비스 만들기
이전에 표시된 **az ml service create realtime** 명령에 대한 대안으로 단계를 별도로 수행할 수도 있습니다. 

먼저 모델을 등록합니다. 그런 다음 매니페스트를 생성하고 Docker 이미지를 빌드하며 웹 서비스를 만듭니다. 이 단계별 방법으로 각 단계마다 더 많은 유연성이 제공됩니다. 또한 이전 단계에서 생성한 엔터티를 다시 사용하고 필요한 경우에만 이 엔터티를 다시 작성할 수 있습니다.

1. pickle 파일 이름을 제공하여 모델을 등록합니다.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   이 명령은 모델 ID를 생성합니다.

2. 매니페스트를 만듭니다.

   매니페스트를 만들려면 다음 명령을 사용하고, 이전 단계의 모델 ID 출력을 제공합니다.

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   이 명령은 매니페스트 ID를 생성합니다.

3. Docker 이미지를 만듭니다.

   Docker 이미지를 만들려면 다음 명령을 사용하고 이전 단계에서 출력한 매니페스트 ID 값을 제공합니다. 선택적으로 `-c` 스위치를 사용하여 conda 종속성을 포함시킬 수도 있습니다.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
   ```
   이 명령은 Docker 이미지 ID를 생성합니다.
   
4. 서비스를 만듭니다.

   서비스를 만들려면 다음 명령을 사용하고, 이전 단계의 이미지 ID 출력을 제공합니다.

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   이 명령은 웹 서비스 ID를 생성합니다.

이제 웹 서비스를 실행할 준비가 되었습니다.

## <a name="run-the-real-time-web-service"></a>실시간 웹 서비스 실행

실행 중인 **irisapp** 웹 서비스를 테스트하려면 4개의 난수가 있는 배열을 포함한 JSON 인코딩 레코드를 사용합니다.

1. 웹 서비스는 샘플 데이터를 포함합니다. 로컬 모드에서 실행할 때는 **az ml service usage realtime** 명령을 호출할 수 있습니다. 이 호출은 서비스를 테스트하는 데 사용할 수 있는 샘플 실행 명령을 검색합니다. 호출은 자신의 사용자 지정 앱에 서비스를 통합하는 데 사용할 수 있는 점수 매기기 URL도 검색합니다.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. 서비스를 테스트하려면 반환된 서비스 실행 명령을 실행합니다.
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   출력은 예상되는 클래스인 **"Iris-setosa"** 입니다. (결과가 다를 수도 있습니다.) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Azure Blob 저장소에서 수집된 데이터 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정을 찾습니다. 이렇게 하려면 **모든 서비스**를 선택합니다.

3. 검색 상자에서 **저장소 계정**을 입력한 다음, Enter를 선택합니다.

4. **저장소 계정** 검색 상자에서 환경에 맞는 **저장소 계정** 리소스를 선택합니다. 

   > [!TIP]
   > 사용 중인 저장소 계정을 확인하려면:
   > 1. Machine Learning Workbench를 엽니다.
   > 2. 작업 중인 프로젝트를 선택합니다.
   > 3. **파일** 메뉴에서 명령줄 프롬프트를 엽니다.
   > 4. 명령줄 프롬프트에서 `az ml env show -v`를 입력하고, *storage_account* 값을 확인합니다. 이는 저장소 계정의 이름입니다.

5. **저장소 계정** 창이 열리면 **서비스** 섹션에서 **Blob**을 선택합니다. **modeldata**라는 컨테이너를 찾습니다. 
 
   데이터가 표시되지 않을 경우 첫 번째 웹 서비스 요청 후 최대 10분 동안 기다려야 데이터가 저장 계정으로 전파되는 것을 확인할 수 있습니다.

   데이터는 다음 컨테이너 경로를 사용하여 Blob으로 흐릅니다.

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Azure Blob 저장소에서 이 데이터를 사용할 수 있습니다. 다음과 같은 Microsoft 소프트웨어 및 오픈 소스 도구를 사용하는 다양한 도구가 있습니다.

   * Machine Learning: CSV 파일을 데이터 원본으로 추가하여 CSV 파일을 엽니다.

   * Excel: 일별CSV 파일을 스프레드시트로 엽니다.

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): Blob의 CSV 데이터에서 가져온 데이터로 차트를 만듭니다.

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): Hive 테이블에 CSV 데이터를 로드하고, Blob에 대해 SQL 쿼리를 직접 수행합니다.

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): CSV 데이터의 많은 부분을 사용하여 데이터 프레임을 만듭니다.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계
3부 자습서 시리즈의 제3부에서는 Machine Learning을 사용하여 다음을 수행하는 방법을 알아보았습니다.
> [!div class="checklist"]
> * 모델 파일 찾기
> * 점수 매기기 스크립트 및 스키마 파일 생성
> * 환경 준비
> * 실시간 웹 서비스 만들기
> * 실시간 웹 서비스 실행
> * 출력 Blob 데이터 검사 

다양한 컴퓨팅 환경에서 학습 스크립트를 성공적으로 실행했습니다. 또한 Docker 기반 웹 서비스를 통해 모델을 만들고, 직렬화하고, 조작했습니다. 

이제 고급 데이터 준비를 수행할 준비가 되었습니다.
> [!div class="nextstepaction"]
> [자습서 4 - 고급 데이터 준비](tutorial-bikeshare-dataprep.md)
