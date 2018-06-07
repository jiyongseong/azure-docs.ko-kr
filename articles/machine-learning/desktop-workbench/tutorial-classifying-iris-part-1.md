---
title: Azure Machine Learning 서비스(미리 보기)에서 아이리스 자습서 분류를 위한 데이터 준비 | Microsoft Docs
description: 이 자습서 전체에서 Azure Machine Learning 서비스(미리 보기)를 사용하는 방법을 보여 줍니다. 1부이며 데이터 준비를 설명합니다.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: 1e8ad7a85739f67bf8130011ba18243ad0b4b9d0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>자습서 1: 아이리스 분류 - 데이터 준비

Azure Machine Learning 서비스(미리 보기)는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션으로, 데이터를 준비하고, 실험을 개발하고, 클라우드 규모에서 모델을 배포할 수 있습니다.

이 자습서는 **3부로 구성된 시리즈 중 제1부**입니다. 이 자습서에서는 Azure Machine Learning 서비스(미리 보기)의 기본 사항을 살펴보고, 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning Workbench에서 프로젝트 만들기
> * 데이터 준비 패키지 만들기
> * 데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

이 자습서에서는 변함 없는 [아이리스 꽃 데이터 집합](https://en.wikipedia.org/wiki/Iris_flower_data_set)을 사용합니다. 

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 자습서를 완료하려면 다음 항목이 필요합니다.
- Azure Machine Learning 실험 계정
- Azure Machine Learning Workbench 설치

이러한 필수 조건이 아직 없으면 [빠른 시작: 설치 및 시작](../service/quickstart-installation.md) 문서의 단계에 따라 계정을 설정하고 Azure Machine Learning Workbench 응용 프로그램을 설치합니다. 

## <a name="create-a-new-project-in-workbench"></a>Workbench에서 새 프로젝트 만들기

[빠른 시작: 설치 및 시작](../service/quickstart-installation.md) 문서의 단계를 수행한 경우에는 프로젝트가 이미 있으므로 다음 섹션으로 건너뛸 수 있습니다.

1. Azure Machine Learning Workbench 앱을 열고 필요한 경우 로그인합니다. 
   
   + Windows에서는 **Machine Learning Workbench** 바탕 화면 바로 가기를 사용하여 시작합니다. 
   + macOS에서는 실행 패드에서 **Azure ML Workbench**를 선택합니다.

1. **프로젝트** 창에서 더하기 기호(+)를 선택하고 **새 프로젝트**를 선택합니다.  

   ![새 작업 영역](./media/tutorial-classifying-iris/new_ws.png)

1. 양식 필드에 정보를 채우고 **만들기** 단추를 선택하여 Workbench에 새 프로젝트를 만듭니다.

   필드|자습서에 제안된 값|설명
   ---|---|---
   프로젝트 이름 | myIris |계정을 식별하는 고유한 이름을 입력합니다. 본인의 이름 또는 실험을 가장 잘 식별하는 부서나 프로젝트 이름을 사용할 수 있습니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. 
   프로젝트 디렉터리 | c:\Temp\ | 프로젝트가 만들어지는 디렉터리를 지정합니다.
   프로젝트 설명 | _비워 둠_ | 프로젝트를 설명하기에 유용한 선택적 필드입니다.
   Visualstudio.com GIT 리포지토리 URL |_비워 둠_ | 선택적 필드입니다. 원본 제어 및 공동 작업을 위해 Visual Studio Team Services에서 Git 리포지토리와 프로젝트를 연결할 수 있습니다. [설정하는 방법 알아기](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo) 
   선택한 작업 영역 | IrisGarden(있는 경우) | Azure Portal에서 실험 계정에 대해 만든 작업 영역을 선택합니다. <br/>빠른 시작을 수행한 경우 IrisGarden이라는 이름의 작업 영역이 있어야 합니다. 그렇지 않으면 실험 계정을 만들 때 생성한 항목을 선택하거나 원하는 다른 항목을 선택합니다.
   프로젝트 템플릿 | 아이리스 분류 | 템플릿에는 제품을 탐색하는 데 사용할 수 있는 스크립트와 데이터가 포함됩니다. 템플릿에는 이 설명서 사이트의 빠른 시작 및 기타 자습서에 필요한 스크립트와 데이터가 포함됩니다. 

   ![새 프로젝트](media/tutorial-classifying-iris/new_project.png)
 
 새 프로젝트가 만들어지고 프로젝트 대시보드에서 해당 프로젝트가 열립니다. 이 시점에서 프로젝트 홈페이지, 데이터 소스, 노트북 및 소스 코드 파일을 탐색할 수 있습니다. 

   ![프로젝트 열기](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>데이터 준비 패키지 만들기

다음으로, Azure Machine Learning Workbench에서 데이터를 탐색하고 준비할 수 있습니다. Workbench에서 수행하는 각 변환은 JSON 형식으로 로컬 데이터 준비 패키지(*.dprep 파일)에 저장됩니다. 이 데이터 준비 패키지는 Workbench의 데이터 준비 작업에 대한 기본 컨테이너입니다.

이 데이터 준비 패키지는 나중의 런타임(예: 로컬 C#/CoreCLR, Scala/Spark 또는 Scala/HDI)에 전달될 수 있습니다. 

1. 폴더 아이콘을 선택하여 [파일] 보기를 연 다음, **iris.csv**를 선택하여 이 파일을 엽니다.

   이 파일에는 5개 열, 50개 행이 있는 테이블이 포함되어 있습니다. 네 개의 열은 숫자 기능 열입니다. 다섯 번째 열은 문자열 대상 열입니다. 열에는 헤더 이름이 없습니다.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 특히 파일 크기가 크면 프로젝트 폴더에 해당 데이터 파일을 포함하지 마십시오. **iris.csv** 데이터 파일이 매우 작기 때문에 이 템플릿에 데모용으로 포함되었습니다. 자세한 내용은 [큰 데이터 파일을 읽고 쓰는 방법](how-to-read-write-files.md)을 참조하세요.

2. **데이터 뷰**에서 더하기 기호(**+**)를 선택하여 새 데이터 원본을 추가합니다. **데이터 원본 추가** 페이지가 열립니다. 

   ![Azure Machine Learning Workbench의 데이터 뷰](media/tutorial-classifying-iris/data_view.png)

3. **텍스트 파일(\*.csv, \*.json, \*.txt., ...)** 을 선택하고 **다음**을 클릭합니다.
   ![Azure Machine Learning Workbench의 데이터 원본](media/tutorial-classifying-iris/data-source.png)

4. **iris.csv** 파일을 찾아 **마침**을 클릭합니다. 이렇게 하면 구분 기호 및 데이터 형식과 같은 매개 변수에 대한 기본값이 사용됩니다.

   >[!IMPORTANT]
   >이 연습에서는 현재 프로젝트 디렉터리에 있는 **iris.csv** 파일을 선택해야 합니다. 그렇지 않으면 이후 단계가 실패할 수 있습니다.
 
   ![아이리스 선택](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. **iris-1.dsource**라는 새 파일이 만들어집니다. 샘플 프로젝트에는 번호가 지정되지 않은 **iris.dsource** 파일이 이미 있으므로 파일 이름이 “-1”로 지정되었습니다.  

   파일이 열리고 데이터가 표시됩니다. 일련의 열 머리글(**Column1**에서 **Column5**까지)이 이 데이터 집합에 자동으로 추가됩니다. 아래로 스크롤하여 데이터 집합의 마지막 행이 비어 있음을 확인합니다. 행은 추가 줄 바꿈이 CSV 파일에 있기 때문에 비어 있습니다.

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/iris_data_view.png)

1. **메트릭** 단추를 선택합니다. 히스토그램이 생성되어 표시됩니다.

   **데이터** 단추를 선택하여 데이터 뷰로 다시 전환할 수 있습니다.
   
   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. 히스토그램을 관찰합니다. 각 열에 대한 통계의 전체 집합이 계산됩니다. 

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/iris_metrics_view.png)

8. **준비** 단추를 선택하여 데이터 준비 패키지를 만들기 시작합니다. **준비** 대화 상자가 열립니다. 

   샘플 프로젝트에는 기본적으로 선택되는**iris.dprep** 데이터 준비 파일이 포함되어 있습니다. 

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/prepare.png)

1. 드롭다운 메뉴에서 **+ 새 데이터 준비 패키지**를 선택하여 새 데이터 준비 패키지를 만듭니다.

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/prepare_new.png)

1. 패키지 이름에 대한 새 값(**iris-1** 사용)을 입력한 다음, **확인**을 선택합니다.

   **iris-1.dprep**라는 새 데이터 준비 패키지가 만들어지고 데이터 준비 편집기에서 열립니다.

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/prepare_iris-1.png)

   이제 몇 가지 기본적인 데이터 준비 작업을 수행해 보겠습니다. 

1. 각 열 머리글을 선택하여 헤더 텍스트를 편집할 수 있도록 합니다. 그런 다음, 각 열의 이름을 다음과 같이 바꿉니다. 

   순서대로, 5개 열 각각에 대해 **꽃받침 길이**, **꽃받침 너비**, **꽃잎 길이**, **꽃잎 너비** 및 **종류**를 순서대로 입력합니다.

   ![열 이름 바꾸기](media/tutorial-classifying-iris/rename_column.png)

1. 고유 값을 계산합니다.
   1. **종류** 열을 선택합니다.
   1. 마우스 오른쪽 단추를 클릭하여 선택합니다. 
   1. 드롭다운 메뉴에서 **값 계산**을 선택합니다. 

   데이터 아래에 **검사기** 창이 열립니다. 네 개의 막대가 있는 히스토그램이 나타납니다. 대상 열에는 4개의 고유 값, 즉 **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** 및 **(null)** 값이 있습니다.

   ![값 개수 선택](media/tutorial-classifying-iris/value_count.png)

   ![값 개수 히스토그램](media/tutorial-classifying-iris/filter_out.png)

1. null 값을 필터링하려면 "(null)" 상자를 선택한 다음, 빼기 기호(**-**)를 선택합니다. 

   그러면 (null) 행이 회색으로 바뀌어 필터링되었음을 나타냅니다. 

   ![null 필터링](media/tutorial-classifying-iris/filter_out2.png)

1. 개별 데이터 준비 단계는 **단계** 창에서 자세히 설명합니다. 열 이름을 바꾸고 null 값 행을 필터링할 때 각 작업은 데이터 준비 단계로 기록되었습니다. 개별 단계를 편집하여 해당 설정을 조정하고, 단계의 순서를 바꾸고, 단계를 제거할 수 있습니다.

   ![단계](media/tutorial-classifying-iris/steps.png)

1. 데이터 준비 편집기를 닫습니다. 그래프 아이콘이 있는 **iris-1** 탭의 **x** 아이콘을 선택하여 탭을 닫습니다. 작업은 **데이터 준비** 제목 아래 표시된 **iris-1.dprep** 파일에 자동으로 저장됩니다.

   ![닫습니다](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

 데이터 준비 패키지의 출력은 Python 또는 Jupyter Notebook에서 직접 탐색할 수 있습니다. 패키지는 로컬 Python, Spark(Docker에 포함) 및 HDInsight를 포함한 여러 런타임에서 실행될 수 있습니다. 

1. 데이터 준비 탭에서 **iris-1.dprep** 파일을 찾습니다.

1. **iris-1.dprep** 파일을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **데이터 액세스 코드 파일 생성**을 선택합니다. 

   ![코드 생성](media/tutorial-classifying-iris/generate_code.png)

   다음 코드 줄이 포함된 **iris-1.py**라는 새 파일이 열려 데이터 준비 패키지로 만든 논리를 호출합니다.

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   이 코드가 실행되는 컨텍스트에 따라 `df`는 다른 종류의 데이터 프레임을 나타냅니다.
   + Python 런타임에서 실행되는 경우 [pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)이 사용됩니다.
   + Spark 컨텍스트에서 실행되면 [Spark 데이터 프레임](https://spark.apache.org/docs/latest/sql-programming-guide.html)이 사용됩니다. 
   
   Azure Machine Learning Workbench에서 데이터를 준비하는 방법을 자세히 알아보려면 [데이터 준비 시작](data-prep-getting-started.md) 가이드를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning Workbench를 사용하여 다음을 수행했습니다.
> [!div class="checklist"]
> * 새 프로젝트 만들기
> * 데이터 준비 패키지 만들기
> * 데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

Azure Machine Learning 모델을 빌드하는 방법을 배우는 자습서 시리즈의 다음 부분으로 넘어갈 준비가 되었습니다.
> [!div class="nextstepaction"]
> [자습서 2 - 모델 빌드](tutorial-classifying-iris-part-2.md)
