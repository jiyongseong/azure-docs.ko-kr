---
title: Azure Machine Learning 서비스(미리 보기)용 모델 자습서 작성 | Microsoft Docs
description: 이 자습서 전체에서 Azure Machine Learning 서비스(미리 보기)를 사용하는 방법을 보여 줍니다. 2부이며 실험을 설명합니다.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 24ac4d699a511cc99936cb2cd4b245de01984163
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>자습서 2: 아이리스 분류: 모델 빌드
Azure Machine Learning 서비스(미리 보기)는 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서는 **3부로 구성된 시리즈 중 제2부**입니다. 이 자습서의 부분에서는 Azure Machine Learning 서비스를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 스크립트 열기 및 코드 검토
> * 로컬 환경에서 스크립트 실행
> * 실행 기록 검토
> * 로컬 Azure CLI 창에서 스크립트 실행
> * 로컬 Docker 환경에서 스크립트 실행
> * 원격 Docker 환경에서 스크립트 실행
> * 클라우드 Azure HDInsight 환경에서 스크립트 실행

이 자습서에서는 변함 없는 [아이리스 꽃 데이터 집합](https://en.wikipedia.org/wiki/Iris_flower_data_set)을 사용합니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.
- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
- [빠른 시작](../service/quickstart-installation.md)에 설명된 대로 설치된 실험 계정 및 Azure Machine Learning Workbench
- [자습서 1부](tutorial-classifying-iris-part-1.md)에서 준비된 아이리스 데이터 및 프로젝트
- 로컬로 설치되고 실행되는 Docker 엔진입니다. Docker의 Community Edition은 충분합니다. Docker 설치 방법에 대해 여기서 https://docs.docker.com/engine/installation/ 알아봅니다.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>iris_sklearn.py 및 구성 파일 검토

1. Machine Learning Workbench 응용 프로그램을 시작합니다.

1. [자습서 시리즈의 1부](tutorial-classifying-iris-part-1.md)에서 만든 **myIris** 프로젝트를 엽니다.

2. 열린 프로젝트에서 가장 왼쪽 창에 있는 **파일** 단추(폴더 아이콘)를 선택하여 프로젝트 폴더의 파일 목록을 엽니다.

   ![Azure Machine Learning Workbench에서 프로젝트 열기](media/tutorial-classifying-iris/2-project-open.png)

3. **iris_sklearn.py** Python 스크립트 파일을 선택합니다. 

   ![스크립트 선택](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   코드가 Workbench의 새 텍스트 편집기 탭에서 열립니다. 자습서의 이 파트 전체에서 사용하는 스크립트입니다. 

   >[!NOTE]
   >이 샘플 프로젝트는 자주 업데이트되므로 사용자가 보는 코드가 이전 코드와 완전히 같지 않을 수도 있습니다.
   
   ![파일 열기](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. 코딩 스타일에 익숙해지기 위해 Python 스크립트 코드를 검사합니다. 

   **iris_sklearn.py** 스크립트에서 수행하는 작업은 다음과 같습니다.

   * **iris.dprep**이라는 기본 데이터 준비 패키지를 로드하여 [pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)을 만듭니다. 

   * 임의의 기능을 추가하여 문제를 해결하기 어렵게 만듭니다. 아이리스는 거의 100% 정확도로 쉽게 분류할 수 있는 작은 데이터 집합이므로 임의성이 필요합니다.

   * [scikit-learn](http://scikit-learn.org/stable/index.html) 기계 학습 라이브러리를 사용하여 로지스틱 회귀 모델을 작성합니다.  이 라이브러리는 기본적으로 Azure Machine Learning Workbench와 함께 제공됩니다.

   * [pickle](https://docs.python.org/3/library/pickle.html) 라이브러리를 사용하여 모델을 `outputs` 폴더의 파일에 직렬화합니다. 
   
   * Serialize된 모델을 로드하고 메모리에 다시 deserialize 합니다.

   * 역직렬화된 모델을 사용하여 새 레코드에 대해 예측을 수행합니다. 

   * [matplotlib](https://matplotlib.org/) 라이브러리를 사용하여 두 개의 그래프, 혼동 행렬 및 다중 클래스 ROC(수신기 조작 특성) 곡선을 그린 다음, `outputs` 폴더에 저장합니다. 아직 존재하지 않는 경우 사용자 환경에서 이 라이브러리를 설치할 수 있습니다.

   * 실행 기록에 정규화 속도 및 모델 정확도를 자동으로 표시합니다. `run_logger` 개체는 정규화 속도 및 모델 정확도를 처음부터 끝까지 로그에 기록하는 데 사용됩니다. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>로컬 환경에서 Iris_sklearn.py를 실행

1. Azure Machine Learning CLI(명령줄 인터페이스)를 시작합니다.
   1. Azure Machine Learning Workbench를 시작합니다.

   1. Workbench 메뉴에서 **파일** > **명령 프롬프트 열기**를 선택합니다. 
   
   Azure Machine Learning CLI(명령줄 인터페이스) 창이 Windows의 프로젝트 폴더 `C:\Temp\myIris\>`에서 시작합니다. 이 프로젝트는 자습서의 1부에서 만든 것과 같습니다.

   >[!IMPORTANT]
   >다음 단계를 완수하려면 이 CLI 창을 사용해야 합니다.

1. CLI 창에서 라이브러리가 아직 있지 않은 경우 Python 플롯팅 라이브러리 **matplotlib**를 설치합니다.

   **iris_sklearn.py** 스크립트는 두 개의 Python 패키지 **scikit-learn** 및 **matplotlib**에 의존합니다.  **scikit-learn** 패키지는 사용자의 편의를 위해 Azure Machine Learning Workbench에 의해 설치되었습니다. 그러나 아직 설치되는 않은 경우 **matplotlib**를 설치해야 합니다.

   **matplotlib**를 설치하지 않고 이동하는 경우에도 이 자습서의 코드는 여전히 성공적으로 실행할 수 있습니다. 그러나 코드는 기록 시각화에 표시된 대로 혼동 행렬 출력 및 다중 클래스 ROC 곡선 그래프를 생성할 수 없습니다.

   ```azurecli
   pip install matplotlib
   ```

   이 설치는 약 1분이 걸립니다.

1. Workbench 응용 프로그램으로 돌아갑니다. 

1. **iris_sklearn.py**라는 탭을 찾습니다. 

   ![스크립트를 사용하여 탭 찾기](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. 해당 탭의 도구 모듬에서 **로컬**을 실행 환경으로 선택하고 `iris_sklearn.py`을 실행할 스크립트로 선택합니다. 이들은 이미 선택됐습니다.

   ![로컬 및 스크립트 선택](media/tutorial-classifying-iris/2-local-script.png)

1. 도구 모음의 오른쪽으로 이동하고 **인수** 필드에 `0.01`을 입력합니다. 

   이 값은 로지스틱 회귀 모델의 정규화 속도에 해당합니다.

   ![로컬 및 스크립트 선택](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. **실행** 단추를 선택합니다. 작업이 바로 예약됩니다. 이 작업은 Workbench 창의 오른쪽에 있는 **작업** 창에 나열됩니다. 

   ![로컬 및 스크립트 선택](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   몇 분 후에 작업 상태가 **제출 중**에서 **실행 중**으로, 마지막으로 **완료됨**으로 전환됩니다.

1. **작업** 창의 작업 상태 텍스트에서 **완료됨**을 선택합니다. 

   ![sklearn 실행](media/tutorial-classifying-iris/2-completed.png)

   팝업 창이 열리고 실행에 대한 표준 출력(stdout) 텍스트가 표시됩니다. 표준 출력 텍스트를 닫으려면 팝업 의 오른쪽 위에 있는 **닫기**(**x**) 단추를 선택합니다.

   ![표준 출력](media/tutorial-classifying-iris/2-standard-output.png)

9. **작업** 창의 동일한 작업 상태에서 **완료됨** 상태 및 시작 시간 바로 위의 파란색 **iris_sklearn.py [n]**(_n_은 실행 번호) 텍스트를 선택합니다. **실행 속성** 창이 열리고 해당 특정 실행에 대한 다음 정보를 표시합니다.
   - **실행 속성** 정보
   - **Outputs**
   - **metrics**
   - 있는 경우 **시각화**
   - **로그** 

   실행이 완료되면 팝업 창에서 다음과 같은 결과를 표시합니다.

   >[!NOTE]
   >자습서가 이전에 학습 집합에 불규칙화를 도입했으므로 정확한 결과는 여기에 표시된 결과와 다를 수 있습니다.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. **실행 속성** 탭을 닫은 다음 **iris_sklearn.py** 탭으로 돌아갑니다. 

11. 추가 실행에 대해 반복합니다. 

    **인수** 필드에서 `0.001`에서 `10`까지 일련의 값을 입력합니다. **실행**을 선택하여 코드를 몇 번 더 실행합니다. 인수 값이 변경될 때마다 코드의 로지스틱 회귀 모델에 제공되므로 매번 다른 결과를 얻게 됩니다.

## <a name="review-the-run-history-in-detail"></a>자세한 실행 기록 검토
Azure Machine Learning Workbench에서는 모든 스크립트 실행이 실행 기록 레코드로 캡처됩니다. **실행** 보기를 여는 경우 특정 스크립트의 실행 기록을 볼 수 있습니다.

1. **실행** 목록을 열려면 왼쪽 도구 모음에서 **실행** 단추(시계 아이콘)를 선택합니다. 그런 다음 **iris_sklearn.py**를 선택하여 `iris_sklearn.py`의 **실행 대시보드**를 표시합니다.

   ![실행 보기](media/tutorial-classifying-iris/run_view.png)

1. **실행 대시보드** 탭이 열립니다. 

   여러 실행에서 캡처된 통계를 검토합니다. 그래프는 탭의 위쪽에서 렌더링합니다. 각 실행에는 연속 번호가 있으며 실행 세부 정보는 화면 맨 아래의 테이블에 나열됩니다.

   ![실행 대시보드](media/tutorial-classifying-iris/run_dashboard.png)

1. 테이블을 필터링한 다음 그래프를 선택하여 각 실행의 상태, 기간, 정확도 및 정규화 속도를 봅니다. 

1. **실행** 테이블에서 두 개 이상의 실행 옆에 있는 확인 상자를 선택합니다. **비교** 단추를 선택해 자세한 비교 창을 엽니다. 병렬 비교를 검토합니다. 

1. **실행 대시보드**로 돌아가려면 **비교** 창의 왼쪽 위에서 **실행 목록** 뒤로 단추를 선택합니다.

   ![실행 목록에 반환](media/tutorial-classifying-iris/2-compare-back.png)

1. 개별 실행을 선택하여 실행 세부 정보 보기를 표시합니다. 선택한 실행에 대한 통계는 **실행 속성** 섹션에서 나열됩니다. 출력 폴더에 기록된 파일은 **출력** 섹션에서 나열되고 거기에서 파일을 다운로드할 수 있습니다.

   ![실행 세부 정보](media/tutorial-classifying-iris/run_details.png)

   두 개의 도면, 즉 혼돈 행렬 및 다중 클래스 ROC 곡선은 **시각화** 섹션에서 렌더링됩니다. 또한 모든 로그 파일은 **로그** 섹션에서 찾을 수 있습니다.


## <a name="run-scripts-in-local-docker-environments"></a>로컬 Docker 환경에서 스크립트 실행

필요에 따라 로컬 Docker 컨테이너에 대해 스크립트를 실행하여 실험할 수 있습니다. Docker와 같은 추가 실행 환경을 구성하고 이러한 환경에서 스크립트를 실행할 수 있습니다. 

>[!NOTE]
>원격 Azure VM 또는 Azure HDInsight Spark 클러스터의 Docker 컨테이너에서 실행할 스크립트를 디스패치하여 실험하기 위해 [Ubuntu 기반 Azure 데이터 과학 Virtual Machine 또는 HDInsight 클러스터를 만드는 지침](how-to-create-dsvm-hdi.md)을 수행할 수 있습니다.

1. 아직 이렇게 하지 않은 경우 Windows 또는 MacOS 컴퓨터에 로컬로 Docker를 설치하고 시작합니다. 자세한 내용은 https://docs.docker.com/install/에서 Docker 설치 지침을 참조하세요. Community Edition은 충분합니다.

1. 왼쪽 창에서 **폴더** 아이콘을 선택하여 프로젝트에 대한 **파일** 목록을 엽니다. `aml_config` 폴더를 펼칩니다. 

2. **docker-python**, **docker-spark** 및 **local**과 같이 미리 구성된 여러 환경이 있습니다. 

   각 환경에는 `docker.compute`(**docker-python** 및 **docker-spark** 모두에 사용) 및 `docker-python.runconfig`와 같은 두 개의 파일이 있습니다. 텍스트 편집기에서 특정 옵션을 구성할 수 있는지 확인하려면 각 파일을 엽니다.  

   정리하려면 열려 있는 텍스트 편집기에 대한 탭에 있는 **닫기**(**x**)를 선택합니다.

3. **docker-python** 환경을 사용하여 **iris_sklearn.py** 스크립트를 실행합니다. 

   - 왼쪽 도구 모음에서 **시계** 아이콘을 선택하여 **실행** 창을 엽니다. **모든 실행**을 선택합니다. 

   - **모든 실행**  탭의 위쪽에서 대상이 되는 환경으로 기본 **로컬** 대신 **docker-python**을 선택합니다. 

   - 그런 다음 오른쪽으로 이동하고 실행할 스크립트로 **iris_sklearn.py**를 선택합니다. 

   - 스크립트에서 기본값을 지정하므로 **인수** 필드를 비워둡니다. 

   - **실행** 단추를 선택합니다.

4. 새 작업이 시작되는지 확인합니다. Workbench 창의 오른쪽에 있는 **작업** 창에 나타납니다.

   Docker를 처음 사용하는 경우 이 작업을 완료하는 데 몇 분 정도의 시간이 더 걸립니다. 

   내부적으로 Azure Machine Learning Workbench에서 새 Docker 파일을 빌드합니다. 
   새 파일은 `docker.compute` 파일에 지정된 기본 Docker 이미지 및 `conda_dependencies.yml` 파일에 지정된 종속성 Python 패키지를 참조합니다. 
   
   Docker 엔진에서 수행하는 작업은 다음과 같습니다.

    - Azure에서 기본 이미지를 다운로드합니다.
    - `conda_dependencies.yml` 파일에 지정된 Python 패키지를 설치합니다.
    - Docker 컨테이너를 시작합니다.
    - 실행 구성에 따라 프로젝트 폴더의 로컬 복사본을 복사 또는 참조합니다.      
    - `iris_sklearn.py` 스크립트를 실행합니다.

   마지막으로, **local**을 대상으로 지정할 때와 똑같은 결과가 나타납니다.

5. 이제 Spark를 사용해 보겠습니다. Docker 기본 이미지에는 미리 설치되고 구성된 Spark 인스턴스가 포함되어 있으며, 이 인스턴스는 PySpark 스크립트를 실행하는 데 사용할 수 있습니다. 이 기본 이미지를 사용하는 것은 Spark를 직접 설치하고 구성하는 데 시간을 소모하지 않고도 Spark 프로그램을 개발하고 테스트할 수 있는 쉬운 방법입니다. 

   `iris_spark.py` 파일을 엽니다. 이 스크립트에서는 `iris.csv` 데이터 파일을 로드하고 Spark Machine Learning 라이브러리의 로지스틱 회귀 알고리즘을 사용하여 아이리스 데이터 집합을 분류합니다. 이제 실행 환경을 **docker-spark**로 변경하고 스크립트를 **iris_spark.py**로 변경한 다음 다시 실행합니다. 이 프로세스는 Docker 컨테이너에서 Spark 세션을 만들고 시작해야 하기 때문에 시간이 약간 더 오래 걸립니다. 표준 출력(stdout)이 `iris_spark.py`의 표준 출력과 다름을 알 수도 있습니다.

6. 몇 가지 실행을 추가로 시작하고 서로 다른 인수를 사용하여 재생합니다. 

7. `iris_spark.py` 파일을 열어 Spark Machine Learning 라이브러리를 사용하여 작성된 로지스틱 회귀 모델을 확인합니다. 

8. **작업** 패널, 실행 기록 목록 보기 및 다른 실행 환경의 실행 세부 정보 보기와 상호 작용합니다.

## <a name="run-scripts-in-the-cli-window"></a>CLI 창에서 스크립트 실행

1. Azure Machine Learning CLI(명령줄 인터페이스)를 시작합니다.
   1. Azure Machine Learning Workbench를 시작합니다.

   1. Workbench 메뉴에서 **파일** > **명령 프롬프트 열기**를 선택합니다. 
   
   Windows의 프로젝트 폴더 `C:\Temp\myIris\>`에서 CLI 프롬프트가 시작합니다. 자습서 1부에서 만든 프로젝트입니다.

   >[!IMPORTANT]
   >다음 단계를 완수하려면 이 CLI 창을 사용해야 합니다.

1. CLI 창에서 Azure에 로그인합니다. [Azure에 대해 자세히 알아봅니다](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   이미 로그인됐을 수 있습니다. 이 경우이 단계를 건너뛸 수 있습니다.

   1. 명령 프롬프트에 다음을 입력합니다.
      ```azurecli
      az login
      ```

      이 명령은 https://aka.ms/devicelogin의 브라우저에서 사용할 코드를 반환합니다.

   1. 브라우저에서 https://aka.ms/devicelogin로 이동합니다.

   1. 메시지가 표시되면 CLI에서 수신된 코드를 브라우저에 입력합니다.

   Workbench 응용 프로그램과 CLI는 Azure 리소스에 대해 인증할 때 독립적인 자격 증명 캐시를 사용합니다. 로그인한 후 캐시된 토큰이 만료될 때까지 다시 인증하지 않아도 됩니다. 

1. 조직에 여러 Azure 구독(엔터프라이즈 환경)이 있는 경우 사용할 구독을 반드시 설정해야 합니다. 구독을 찾아 구독 ID를 사용하여 설정한 다음, 테스트합니다.

   1. 이 명령을 사용하여 액세스할 수 있는 모든 Azure 구독을 나열합니다.
   
      ```azurecli
      az account list -o table
      ```

      **az account list** 명령은 로그인에 사용 가능한 구독 목록을 반환합니다. 
      1 초과의 경우 사용하려는 구독에 대한 구독 ID 값을 식별합니다.

   1. 기본 계정으로 사용하려는 Azure 구독을 설정합니다.
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      \<your-subscription-id\>가 사용하려는 구독에 대한 ID 값인 경우. 대괄호를 포함하지 마십시오.

   1. 현재 구독에 대한 세부 정보를 요청하여 새 구독 설정을 확인합니다. 

      ```azurecli
      az account show
      ```    

1. CLI 창에서 라이브러리가 아직 있지 않은 경우 Python 플롯팅 라이브러리 **matplotlib**를 설치합니다.

   ```azurecli
   pip install matplotlib
   ```

1. CLI 창에서 **iris_sklearn.py** 스크립트를 시험 삼아 제출합니다.

   Iris_sklearn.py 실행은 로컬 계산 컨텍스트에 대해 실행됩니다.

   + Windows에서:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + MacOS에서:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   다음과 같은 출력이 표시되어야 합니다.
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. 출력을 검토합니다. Workbench를 사용하여 스크립트를 실행한 경우와 동일한 출력 및 결과를 갖습니다. 

1. CLI 창에서 Docker 실행 환경(Docker가 컴퓨터에 설치 된 경우)을 사용하여 Python 스크립트 **iris_sklearn.py**를 다시 실행합니다.

   + 컨테이터가 Windows에 있는 경우. 
     |실행<br/>환경|Windows 명령|
     |---------------------|------------------|
     |파이썬|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + 컨테이터가 MacOS에 있는 경우. 
     |실행<br/>환경|Windows 명령|
     |---------------------|------------------|
     |파이썬|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Workbench로 돌아갑니다.
   1. 프로젝트 파일을 나열하려면 왼쪽 창에서 폴더 아이콘을 선택합니다.
   
   1. **run.py**이라는 Python 스크립트를 엽니다. 이 스크립트는 다양한 정규화 속도에서 반복하는 데 유용합니다. 

   ![실행 목록에 반환](media/tutorial-classifying-iris/2-runpy.png)

1. 이러한 속도로 실험을 여러 번 실행합니다. 

   이 스크립트는 `10.0`(터무니 없이 큰 숫자)의 정규화 속도로 `iris_sklearn.py` 작업을 시작합니다. 그런 다음 스크립트는 다음 실행에서 속도를 반으로 줄이고 속도가 `0.005`보다 작지 않을 때까지 계속합니다. 

   스크립트에는 다음 코드가 포함되어 있습니다.

   ![실행 목록에 반환](media/tutorial-classifying-iris/2-runpy-code.png)

1. 다음과 같은 명령줄에서 **run.py** 스크립트를 실행합니다.

   ```cmd
   python run.py
   ```

   이 명령은 다른 정규화 속도로 여러 번 iris_sklearn.py를 제출

   `run.py`가 완료되면 Workbench의 실행 기록 목록 보기에서 서로 다른 메트릭의 그래프를 볼 수 있습니다.

## <a name="run-scripts-in-a-remote-docker-container"></a>원격 Docker 컨테이너에서 스크립트 실행
원격 Linux 컴퓨터의 Docker 컨테이너에서 스크립트를 실행하려면 해당 원격 컴퓨터에 대한 SSH 액세스(사용자 이름 및 암호) 권한이 있어야 합니다. 또한 컴퓨터에는 Docker 엔진이 설치되어 실행 중이어야 합니다. 이러한 Linux 컴퓨터를 얻는 가장 쉬운 방법은 Azure에서 Ubuntu 기반 DSVM(데이터 과학 Virtual Machine)을 만드는 것입니다. [Azure ML Workbench에서 사용하는 Ubuntu DSVM를 만드는 방법](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal)을 알아봅니다.

>[!NOTE] 
>CentOS 기반 DSVM은 지원되지 *않습니다*.

1. VM이 만들어지면 `.runconfig` 및 `.compute` 파일 쌍을 생성하여 VM을 실행 환경으로 연결할 수 있습니다. 다음 명령을 사용하여 파일을 생성합니다. 

 새 계산 대상 `myvm`의 이름을 지정합니다.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >IP 주소는 `vm-name.southcentralus.cloudapp.azure.com`과 같이 공개적으로 주소 지정이 가능한 FQDN(정규화된 도메인 이름)일 수도 있습니다. DSVM에 FQDN을 추가하여 IP 주소 대신 사용하는 것이 좋습니다. 비용을 절약하기 위해 특정 시점에 VM을 끌 수 있으므로 이 방법은 좋습니다. 또한 다음에 VM을 시작할 때 IP 주소가 변경되었을 수도도 있습니다.

   >[!NOTE]
   >사용자 이름 및 암호 인증 외에도, `--private-key-file` 및 `--private-key-passphrase`(선택 사항) 옵션을 사용하여 개인 키와 해당 전달 구(있는 경우)를 지정할 수 있습니다.

   다음으로, 이 명령을 실행하여 **myvm** 계산 대상을 준비합니다.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   이전 명령은 VM에 Docker 이미지를 생성하여 스크립트 실행을 준비하게 합니다.
   
   >[!NOTE]
   >`myvm.runconfig`의 `PrepareEnvironment` 값을 `false`(기본값)에서 `true`로 변경할 수도 있습니다. 이렇게 변경하면 첫 번째 실행의 일부로 Docker 컨테이너를 자동으로 준비합니다.

2. `aml_config` 아래에 생성된 `myvm.runconfig` 파일을 편집하고 프레임워크를 `PySpark`(기본값)에서 `Python`으로 변경합니다.

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >PySpark도 작동해야 하지만 실제로 Python 스크립트를 실행하는 데 Spark 세션이 필요하지 않으면, Python을 사용하는 것이 더 효율적입니다.

3. 원격 Docker 컨테이터에서 iris_sklearn.py를 실행하려면 이번에는 대상 _myvm_을 사용하여 CLI 창에서 이전에 수행한 것과 동일한 명령을 실행합니다.
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   이 명령은 원격 Linux VM에서 실행된다는 것을 제외하고는 `docker-python` 환경에 있는 것처럼 실행됩니다. CLI 창에서 동일한 출력 정보가 표시됩니다.

4. 컨테이너에서 Spark를 사용해 보겠습니다. 파일 탐색기를 엽니다. `myvm.runconfig` 파일의 복사본을 만들고 이름을 `myvm-spark.runconfig`로 지정합니다. 새 파일을 편집하여 `Framework` 설정을 `Python`에서 `PySpark`로 변경합니다.
   ```yaml
   Framework: PySpark
   ```
   `myvm.compute` 파일은 변경하지 마세요. 동일한 VM의 동일한 Docker 이미지가 Spark 실행에 사용됩니다. 새 `myvm-spark.runconfig`에서 `Target` 필드는 `myvm`이라는 이름을 통해 동일한 `myvm.compute` 파일을 가리킵니다.

5. 다음 명령을 입력하여 원격 Docker 컨테이너 내부에서 실행되는 Spark 인스턴스에서 **iris_spark.py** 스크립트를 실행합니다.
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>HDInsight 클러스터에서 스크립트 실행
이 스크립트는 HDInsight Spark 클러스터에서도 실행할 수 있습니다. [Azure ML Workbench에서 사용할 HDInsight Spark 클러스터를 만드는 방법](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)을 참조하세요.

>[!NOTE] 
>HDInsight 클러스터는 Azure Blob을 주 저장소로 사용해야 합니다. 아직 Azure Data Lake Storage를 사용하도록 지원되지 않습니다.

1. Azure HDInsight용 Spark 클러스터에 액세스할 수 있으면 다음과 같이 HDInsight 실행 구성 명령을 생성합니다. HDInsight 클러스터 이름, HDInsight 사용자 이름 및 암호를 매개 변수로 제공합니다. 

   다음 명령을 사용하여 HDInsight 클러스터를 가리키는 계산 대상을 만듭니다.

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   HDInsight 클러스터를 준비하려면 이 명령을 실행합니다.

   ```
   az ml experiment prepare -c myhdi
   ```

   클러스터 헤드 노드 FQDN은 일반적으로 `<your_cluster_name>-ssh.azurehdinsight.net`입니다.

   >[!NOTE]
   >`username`은 HDInsight 설정 시 클러스터 SSH 사용자 이름입니다. 기본적으로 값은 `sshuser`입니다. 값은 설치 중에 만들어져 클러스터의 관리 웹 사이트에 액세스할 수 있도록 설정되는 다른 사용자인 `admin`이 아닙니다. 

2. 이 명령을 사용하여 HDInsight 클러스터에서 **iris_spark.py** 스크립트를 실행합니다.

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >원격 HDInsight 클러스터에 대해 실행하는 경우 `admin` 사용자 계정을 사용하여 `https://<your_cluster_name>.azurehdinsight.net/yarnui`에서 YARN(Yet Another Resource Negotiator) 작업 실행 세부 정보를 볼 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계
3부분으로 이루어진 자습서 시리즈의 2번째 부분에서는 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * Workbench에서 스크립트 열고 코드 검토
> * 로컬 환경에서 스크립트 실행
> * 실행 기록 검토
> * 로컬 Docker 환경에서 스크립트 실행

이제, 실시간 웹 서비스로서 만든 로지스틱 회귀 모델을 배포할 수 있는 이 자습서 시리즈의 3번째 부분을 시도해볼 수 있습니다.

> [!div class="nextstepaction"]
> [자습서 3 - 모델 배포](tutorial-classifying-iris-part-3.md)
