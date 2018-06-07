---
title: 자전거 공유 자습서 - Azure Machine Learning Workbench에서 고급 데이터 준비
description: 이 자습서에서는 Azure Machine Learning Workbench를 사용하여 종단 간 데이터 준비 작업을 수행합니다.
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 51ed7dbead7f9085de563d2fb20c4e4a08f90bb2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>자습서: 고급 데이터 준비를 위해 Azure Machine Learning Workbench 사용(자전거 공유 데이터)
Azure Machine Learning(미리 보기)은 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서에서는 Machine Learning(미리 보기)을 사용하여 다음을 수행하는 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Machine Learning 데이터 준비 도구를 사용하여 대화형으로 데이터를 준비합니다.
> * 테스트 데이터 집합을 가져오고, 변환하고, 만듭니다.
> * 데이터 준비 패키지를 생성합니다.
> * Python을 사용하여 데이터 준비 패키지를 실행합니다.
> * 추가 입력 파일에 대한 데이터 준비 패키지를 다시 사용하여 학습 데이터 집합을 생성합니다.
> * 로컬 Azure CLI 창에서 스크립트 실행
> * 클라우드 Azure HDInsight 환경에서 스크립트 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning Workbench가 로컬에 설치되어 있습니다. 자세한 내용은 [설치 빠른 시작](../service/quickstart-installation.md)을 참조하세요.
* Azure CLI가 설치되어 있지 않으면 지침에 따라 [최신 Azure CLI 버전](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)을 설치하세요.
* Azure에 [HDInsights Spark 클러스터](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)가 만들어져 있습니다.
* Azure 저장소 계정.
* Workbench에서 새 프로젝트는 만드는 방법에 익숙해야 합니다.
* 필수는 아니지만, [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 설치하면 저장소 계정에서 BLOB을 업로드, 다운로드 및 확인할 수 있으므로 유용합니다.

## <a name="data-acquisition"></a>데이터 취득
이 자습서에서는 [Boston Hubway 데이터 집합](https://s3.amazonaws.com/hubway-data/index.html) 및 [NOAA](http://www.noaa.gov/)의 보스턴 날씨 데이터를 사용합니다.

1. 다음 링크의 데이터 파일을 로컬 개발 환경에 다운로드합니다.

   * [보스턴 날씨 데이터](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Hubway 웹 사이트의 Hubway 여행 데이터:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. 각 .zip 파일을 다운로드한 후에 압축을 풉니다.

## <a name="upload-data-files-to-azure-blob-storage"></a>Azure Blob 저장소에 데이터 파일 업로드
Azure Blob 저장소를 사용하여 데이터 파일을 호스트할 수 있습니다.

1. 사용 중인 HDInsight 클러스터에 사용되는 것과 동일한 저장소 계정을 사용합니다.

    ![HDInsight 클러스터 저장소 계정](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. **자전거 공유** 데이터 파일을 저장할 **data-files**라는 새 컨테이너를 만듭니다.

3. 데이터 파일을 업로드합니다. `BostonWeather.csv`를 `weather` 폴더에 업로드합니다. 여행 데이터 파일을 `tripdata` 폴더에 업로드합니다.

    ![데이터 파일 업로드](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Storage 탐색기를 사용하여 BLOB을 업로드할 수도 있습니다. 자습서에서 생성된 파일의 내용을 보려는 경우에도 이 도구를 사용합니다.

## <a name="learn-about-the-datasets"></a>데이터 집합에 대한 자세한 정보
1. __보스턴 날씨__ 파일에는 시간 단위로 보고된 다음과 같은 날씨 관련 필드가 포함됩니다.

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. __Hubway__ 데이터는 연별 및 월별 파일로 구성됩니다. 예를 들어 `201501-hubway-tripdata.zip` 파일에는 2015년 1월에 대한 데이터를 포함하는 .csv 파일이 포함됩니다. 데이터에는 각 행이 자전거 여행을 나타내는 다음 필드가 포함됩니다.

   * **여행 기간(초 단위)**

   * **시작 시간 및 날짜**

   * **중지 시간 및 날짜**

   * **시작 스테이션 이름 및 ID**

   * **끝 스테이션 이름 및 ID**

   * **자전거 ID**

   * **사용자 유형(일반 = 24시간 또는 72시간 패스 사용자, 멤버 = 월간 또는 연간 멤버)**

   * **우편 번호(사용자가 멤버인 경우)**

   * **성별(멤버가 직접 보고)**

## <a name="create-a-new-project"></a>새 프로젝트 만들기
1. 시작 메뉴 또는 시작 관리자에서 **Machine Learning Workbench**를 시작합니다.

2. 새 Machine Learning 프로젝트를 만듭니다. **프로젝트** 페이지에서 **+** 단추를 선택하거나 **파일** > **새로 만들기**를 선택합니다.

   * **자전거 공유** 템플릿을 사용합니다.

   * 프로젝트 이름을 **자전거 공유**로 지정합니다. 

## <a id="newdatasource"></a>새 데이터 원본 만들기

1. 새 데이터 원본을 만듭니다. 왼쪽 도구 모음에서 **데이터** 단추(실린더 아이콘)를 클릭하여 **데이터** 뷰를 표시합니다.

   ![데이터 뷰 탭](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. 데이터 원본을 추가합니다. **+** 아이콘을 선택하고 나서 **데이터 원본 추가**를 선택합니다.

   ![데이터 원본 추가 옵션](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>날씨 데이터 추가

1. **데이터 저장소**: 데이터를 포함하는 데이터 저장소를 선택합니다. 파일을 사용하고 있으므로 **파일/디렉터리**를 선택합니다. **다음**을 선택하여 계속합니다.

   ![파일/디렉터리 항목](media/tutorial-bikeshare-dataprep/datasources.png)

2. **파일 선택 영역**: 날씨 데이터를 추가합니다. 이전에 Blob Storage에 업로드한 `BostonWeather.csv` 파일을 찾아서 선택합니다. **다음**을 선택합니다.

   ![BostonWeather.csv를 선택하여 파일 선택](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **파일 세부 정보**: 검색된 파일 스키마를 확인합니다. Machine Learning Workbench는 파일의 데이터를 분석하여 사용할 스키마를 유추합니다.

   ![파일 세부 정보 확인](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Workbench가 올바른 스키마를 검색하지 못할 수도 있습니다. 매개 변수가 데이터 집합에 대해 올바른지 항상 확인하십시오. 날씨 데이터의 경우 다음과 같은 값으로 설정되어 있는지 확인합니다.
   >
   > * __파일 형식__: 분리된 파일(csv, tsv, txt 등)
   > * __구분 기호__: 쉽표[,]
   > * __주석 줄 문자__: 값이 설정되지 않습니다.
   > * __건너뛰기 선 모드__: 건너뛰지 않습니다.
   > * __파일 인코딩__: utf-8
   > * __승격 헤더 모드__: 첫 번째 파일에서 헤더를 사용합니다.

   데이터의 미리 보기는 다음과 같은 열에 표시되어야 합니다.

   * **Path**

   * **DATE**

   * **REPORTTYPE**

   * **HOURLYDRYBULBTEMPF**
   
   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

   계속하려면 **다음**을 선택합니다.

4. **데이터 형식**: 자동으로 검색되는 데이터 형식을 검토합니다. Machine Learning Workbench는 파일의 데이터를 분석하고 사용할 데이터 형식을 유추합니다.

   a. 이 데이터의 경우 모든 열의 **데이터 형식**을 **문자열**로 변경합니다.

   > [!NOTE]
   > 문자열은 이 자습서의 뒷부분에 나오는 Workbench의 기능을 강조 표시하는 데 사용됩니다. 

   ![데이터 형식 검토](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   나. 계속하려면 __다음__을 선택합니다. 

5. **샘플링**: 샘플링 구성표를 만들려면 **편집**을 선택합니다. 추가된 새 __상위 10000__ 행을 선택하고 __편집__을 선택합니다. __샘플 전략__을 **전체 파일**로 설정한 후 **적용**을 선택합니다.

   ![새로운 샘플링 전략 추가](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   __전체 파일__ 전략을 사용하려면 __전체 파일__ 항목을 선택하고 __활성 상태로 설정__을 선택합니다. __전체 파일__ 옆에 별이 있으면 활성 전략임을 나타냅니다.

   ![활성 상태로 설정된 전체 파일](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   계속하려면 **다음**을 선택합니다.

6. **경로 열**: 전체 파일 경로를 가져온 데이터의 열로 포함 시키려면 __경로 열__ 섹션을 사용합니다. __경로 열을 포함하지 않습니다.__ 를 선택합니다.

   > [!TIP]
   > 경로를 열로 포함하면 파일 이름이 다른 많은 파일의 폴더를 가져오는 경우에 유용합니다. 나중에 추출하려는 정보가 파일 이름에 포함된 경우에도 유용합니다.

   ![포함하지 않도록 설정된 경로 열](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **마침**: 데이터 원본 만들기를 완료하려면 **마침**을 선택합니다.

    __BostonWeather__라는 새 데이터 원본 탭이 열립니다. 데이터의 샘플은 그리드 뷰에 표시됩니다. 샘플은 이전에 지정한 활성 샘플링 구성표를 기반으로 합니다.

    화면 오른쪽에 있는 **단계** 창에 이 데이터 원본을 만드는 동안 수행되는 개별 작업이 표시됩니다.

   ![데이터 원본, 샘플 및 단계 표시](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>데이터 원본 메트릭 보기

탭의 그리드 뷰 왼쪽 위에서 __메트릭__을 선택합니다. 이 뷰는 샘플링된 데이터의 배포 및 기타 집계 통계를 표시합니다.

![메트릭 표시](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> 통계의 표시 여부를 구성하려면 **메트릭 선택** 드롭다운 목록을 사용합니다. 여기에서 메트릭을 선택하고 선택을 취소하여 그리드 뷰를 변경합니다.

__데이터__ 뷰로 돌아가려면 페이지의 왼쪽 위에서 __데이터__를 선택합니다.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>데이터 준비 패키지에 데이터 원본 추가

1. __준비__를 선택하여 데이터 준비를 시작합니다. 

2. 메시지가 표시되면 데이터 준비 패키지의 이름을 입력합니다(예: **자전거 공유 데이터 준비**). 

3. __확인__ 을 선택하여 계속합니다.

   ![준비 대화 상자](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. **자전거 공유 데이터 준비**라는 새 패키지가 __데이터__ 탭의 __데이터 준비__ 섹션 아래에 표시됩니다. 

   패키지를 표시하려면 이 항목을 선택합니다. 

5. **>>** 단추를 선택하고 __데이터 흐름__을 펼쳐서 패키지에 포함된 데이터 흐름을 표시합니다. 이 예제에서 __BostonWeather__는 유일한 데이터 흐름입니다.

   > [!IMPORTANT]
   > 패키지에는 여러 데이터 흐름이 포함될 수 있습니다.

   ![패키지의 데이터 흐름](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>값으로 데이터 필터링
1. 데이터를 필터링하려면 특정 값이 있는 셀을 마우스 오른쪽 단추로 클릭하고 __필터__를 선택합니다. 다음으로, 필터 유형을 선택합니다.

2. 이 자습서에서는 `FM-15` 값이 포함된 셀을 선택합니다. 그런 다음, 필터를 **같음**으로 설정합니다.  이제 __REPORTTYPE__이 `FM-15`인 행만 반환하도록 데이터가 필터링되었습니다.

   ![필터 대화 상자](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15는 METAR(Meteorological Terminal Aviation Routine) 형식의 일기 예보입니다. FM-15 보고서는 경험적으로 가장 완벽하고 누락된 데이터가 없는 것으로 보입니다.

## <a name="remove-a-column"></a>열 제거

__REPORTTYPE__ 열이 더 이상 필요하지 않습니다. 열 헤더를 마우스 오른쪽 단추로 클릭하고 **열 제거**를 선택합니다.

   ![열 제거 옵션](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>데이터 형식 변경 및 오류 제거
1. 여러 열을 동시에 선택하려면 열 머리글을 선택한 상태에서 Ctrl(Mac의 경우 Command ⌘)을 선택합니다. 이 기법을 사용하여 다음 열 헤더를 선택합니다.

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. 선택한 열 헤더 중 하나를 마우스 오른쪽 단추로 클릭하고 **필드 형식을 숫자로 변환**을 선택합니다. 이 옵션은 열의 데이터 형식을 숫자로 변환합니다.

   ![여러 열을 숫자로 변환](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. 오류 값을 필터링합니다. 일부 열에는 데이터 형식 변환 문제가 발생합니다. 이 문제는 열의 __데이터 품질 목표__에서 빨간색으로 표시됩니다.

   오류가 있는 행을 제거하려면 **HOURLYDRYBULBTEMPF** 열 제목을 마우스 오른쪽 단추로 클릭합니다. **필터 열**을 선택합니다. 기본 **관심 사항**을 **행 유지**로 사용합니다. **is not error**(오류가 아님)가 선택되도록 **조건** 드롭다운 목록을 변경합니다. 필터를 적용하려면 **확인**을 선택합니다.

   ![오류 값 필터링](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. 다른 열에서 나머지 오류 행을 제거하려면 **HOURLYRelativeHumidity** 및 **HOURLYWindSpeed** 열에서 이 필터링 프로세스를 반복합니다.

## <a name="use-by-example-transformations"></a>예제 변환에서 사용

두 시간 블록 동안 예측에서 데이터를 사용하려면 2시간 동안 평균 날씨 조건을 계산해야 합니다. 다음 작업을 사용합니다.

* **DATE** 열을 별도의 **Date** 및 **Time** 열로 분할합니다. 자세한 단계는 다음 섹션을 참조하세요.

* **Time** 열에서 **Hour_Range** 열이 파생됩니다. 자세한 단계는 다음 섹션을 참조하세요.

* **DATE** 및 **Hour_Range** 열에서 **Date\_Hour\_Range** 열이 파생됩니다. 자세한 단계는 다음 섹션을 참조하세요.

### <a name="split-column-by-example"></a>예제별 열 분할

1. **DATE** 열을 별도의 **Date** 및 **Time** 열로 분할합니다. **DATE** 열 헤더를 마우스 오른쪽 단추로 클릭하고 **예제별 열 분할**을 선택합니다.

   ![예제별 열 분할 항목](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Machine Learning Workbench는 자동으로 의미 있는 구분 기호를 식별하고 데이터를 날짜 및 시간 값으로 분할하여 두 개의 열을 만듭니다. 

3. __확인__을 선택하여 분할 작업 결과를 수용합니다.

   ![DATE_1 및 DATE_2 열 분할](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>예제별 열 파생

1. 두 시간 범위를 파생하려면 __DATE\_2__ 열 머리글을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다.

   ![예제별 열 파생 항목](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   새롭게 비어 있는 열은 null 값으로 추가됩니다.

2. 새 열의 첫 번째 빈 셀을 선택합니다. 원하는 시간 범위의 예제를 제공하려면 새 열에 **오전 12시-오전 2시**를 입력한 다음, Enter를 선택합니다.

   ![값이 오전 12시~오전 2시인 새 열](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Machine Learning Workbench는 사용자가 제공한 예제를 기반으로 프로그램을 합성하고 나머지 행에서 동일한 프로그램을 적용합니다. 다른 모든 행은 사용자가 제공한 예제에 따라 자동으로 채워집니다. 또한 Workbench는 데이터를 분석하고 에지 사례를 식별하려고 합니다. 

   > [!IMPORTANT]
   > 현재 버전의 Workbench에서는 Mac에서 에지 사례의 식별이 작동하지 않을 수 있습니다. Mac에서 다음 3단계 및 4단계를 건너뜁니다. 대신 모든 행에 파생값이 채워진 후 __확인__을 선택합니다.
   
3. 그리드 위에 있는 **데이터 분석** 텍스트는 Workbench에서 에지 사례를 검색하고 있음을 나타냅니다. 완료되면 상태가 **다음 제안된 행 검토** 또는 **제안 없음**으로 변경됩니다. 이 예제에서 **다음 제안된 행 검토**가 반환됩니다.

4. 제안된 변경 내용을 검토하려면 **다음 제안된 행 검토**를 선택합니다. (필요한 경우)검토하고 수정해야 하는 셀이 화면에 강조 표시됩니다.

   ![다음 제안된 행 검토](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    __확인__을 선택하여 변환을 적용합니다.
 
5. __BostonWeather__에 대한 데이터의 그리드 뷰로 돌아갑니다. 이제 그리드는 이전에 추가한 세 개의 열을 포함합니다.

   ![행이 추가된 그리드 뷰](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > 모든 변경 내용이 **단계** 창에서 유지됩니다. **단계** 창에서 만든 단계로 이동하고 아래쪽 화살표를 클릭하고 **편집**을 선택합니다. **예제별 열 파생**의 고급 창이 표시됩니다. 모든 예제가 여기에서 유지됩니다. 아래 그리드에서 행을 두 번 클릭하여 예제를 수동으로 추가할 수도 있습니다. **취소**를 선택하여 변경 내용을 적용하지 않고 기본 그리드로 돌아갑니다. **예제별 열 파생** 변환을 수행하는 동안 **고급 모드**를 선택하여 이 뷰에 액세스할 수도 있습니다.

6. 열 이름을 바꾸려면 열 헤더를 두 번 클릭하고 **시간 범위**를 입력합니다. Enter를 선택하여 변경 내용을 저장합니다.

   ![열 이름 바꾸기](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. 날짜 및 시간 범위를 파생하려면 **Date\_1** 및 **시간 범위** 열을 다중 선택하고 마우스 오른쪽 단추로 클릭한 후 **예제별 열 파생**을 선택합니다.

   ![예제별 열 파생](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   **2015년 1월 1일 오전 12시~오전 2시**를 첫 번째 행에 대한 예제로 입력하고 Enter를 선택합니다.

   사용자가 제공한 예제를 기반으로 Workbench에서 변환이 결정됩니다. 이 예제에서 결과적으로 날짜 형식이 변경되고 두 시간 창과 연결됩니다.

   ![예제 2015년 1월 1일 오전 12시~오전 2시](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Mac에서 8단계 대신 다음 단계를 수행합니다.
   >
   > * **2015년 2월 1일 오전 12시~오전 2시**가 포함된 첫 번째 셀로 이동합니다. 15행이 여기에 해당됩니다. 값을 **2015년 1월 2일 오전 12시~오전 2시**로 수정하고 Enter를 선택합니다. 
   

8. 상태가 **데이터 분석**에서 **다음 제안된 행 검토**로 변경되기를 기다립니다. 변경에 몇 초 정도 걸릴 수 있습니다. 상태 링크를 선택하여 제안된 행으로 이동합니다. 

   ![검토할 제안된 행](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   원본 날짜 값이 dd/mm/yyyy 또는 mm/dd/yyyy가 될 수 있으므로 행에 null 값이 있습니다. **2015년 1월 13일 오전 2시~오전 4시**의 올바른 값을 입력하고 Enter를 선택합니다. Workbench는 나머지 행에 대한 파생을 개선하는 데 두 가지 예제를 사용합니다.

   ![올바른 형식의 데이터](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. **확인**을 선택하여 변환을 적용합니다.

   ![완료된 변환 그리드](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > 이 단계에서 **예제별 열 파생**의 **고급 모드**를 사용하려면: **단계** 창에서 아래쪽 화살표를 선택합니다. 데이터 그리드의 **DATE\_1** 및 **시간 범위** 열 옆에 확인란이 있습니다. **시간 범위** 열 옆에 있는 확인란의 선택을 취소하여 출력이 어떻게 변하는지 확인합니다. 입력인 **시간 범위** 열이 없는 경우 **오전 12시~오전 2시**는 상수로 처리되고 파생된 값에 추가됩니다. **취소**를 선택하여 변경 내용을 적용하지 않고 기본 그리드로 돌아갑니다.
   ![고급 모드](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. 열 이름을 바꾸려면 헤더를 두 번 클릭합니다. 이름을 **날짜 시간 범위**로 변경한 다음, Enter를 선택합니다.

11. **DATE**, **DATE\_1**, **DATE\_2** 및 **시간 범위** 열을 다중 선택합니다. 마우스 오른쪽 단추로 클릭한 후 **열 삭제**를 선택합니다.

## <a name="summarize-data-mean"></a>데이터 요약(평균)

다음 단계에서는 시간 범위 별로 그룹화된 값의 평균을 수행하여 날씨 조건을 요약합니다.

1. **날짜 시간 범위** 열을 선택한 다음, **변환** 메뉴에서 **요약**을 선택합니다.

    ![변환 메뉴](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. 데이터를 요약하려면 페이지의 맨 아래에서 맨 위의 왼쪽 및 오른쪽 창으로 그리드의 열을 끌어옵니다. 왼쪽 창에는 **데이터를 그룹화하려면 여기에 열을 끌어오세요.** 라는 텍스트가 포함됩니다. 오른쪽 창에는 **데이터를 요약하려면 여기에 열을 끌어오세요.** 라는 텍스트가 포함됩니다. 

    a. 아래쪽에 있는 그리드에서 왼쪽 창으로 **날짜 시간 범위** 열을 끌어옵니다. 오른쪽 창으로 **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** 및 **HOURLYWindSpeed**를 끌어옵니다. 

    나. 오른쪽 창에서 **평균**을 각 열에 대한 **집계** 측정값으로 선택합니다. **확인**을 선택하여 요약을 마칩니다.

    ![요약된 데이터 화면](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>스크립트를 사용하여 데이터 흐름 변환

숫자 열의 데이터를 0~1 범위로 변경하면 일부 모델을 신속하게 수렴할 수 있습니다. 현재는 이 변환을 일반적으로 수행하도록 기본 제공되는 변환이 없습니다. 이 작업을 수행하려면 Python 스크립트를 사용하세요.

1. **변환** 메뉴에서 **데이터 흐름 변환(스크립트)** 을 선택합니다.

2. 표시되는 텍스트 상자에 다음 코드를 입력합니다. 열 이름을 사용한 경우 수정하지 않고 코드가 작동해야 합니다. Python으로 간단한 최소 최대 정규화 논리를 작성합니다.

    > [!WARNING]
    > 스크립트는 이 자습서에서는 이전에 사용한 열 이름을 사용합니다. 다른 열 이름이 있는 경우 스크립트의 이름을 변경해야 합니다.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Python 스크립트는 끝에 `df`를 반환해야 합니다. 이 값은 그리드를 채우는 데 사용됩니다.
    
   ![데이터 흐름 변환(스크립트) 대화 상자](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. __확인__을 선택하여 스크립트를 사용합니다. 이제 그리드의 숫자 열에는 0~1이라는 범위의 값이 포함됩니다.

    ![0~1 사이의 값이 포함된 그리드](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

날씨 데이터를 준비했습니다. 다음으로 여행 데이터를 준비합니다.

## <a name="load-trip-data"></a>여행 데이터 로드

1. `201701-hubway-tripdata.csv` 파일을 가져오려면 [새 데이터 원본 만들기](#newdatasource) 섹션의 단계를 사용합니다. 가져오기 프로세스 중에 다음 옵션을 사용합니다.

    * __파일 선택__: 파일을 찾아서 선택할 때에는 **Azure Blob**을 선택합니다.

    * __샘플링 구성표__: **전체 파일** 샘플링 구성표를 선택하고 샘플을 활성화합니다.

    * __데이터 형식__: 기본값 적용합니다.

2. 파일을 가져온 다음, __준비__를 선택하여 데이터 준비를 시작합니다. 기존 **BikeShare Data Prep.dprep** 패키지를 선택한 후 __확인__을 선택합니다.

    이 프로세스는 파일을 새로 만드는 대신 **데이터 흐름**을 기존 **데이터 준비** 파일에 추가합니다.

    ![기존 패키지 선택](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. 그리드가 로드되면 __DATAFLOWS__를 펼칩니다. **BostonWeather** 및 **201701-hubway-tripdata**라는 두 개의 데이터 흐름이 있습니다. **201701-hubway-tripdata** 항목을 선택합니다.

    ![201701-hubway-tripdata 항목](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>맵 검사기 사용

데이터 준비를 위해, 검사기라는 유용한 시각화를 문자열, 숫자 및 지리 데이터에 사용할 수 있습니다. 데이터를 더 잘 이해하고 이상값을 식별하는 데 도움이 됩니다. 맵 검사기를 사용하려면 다음 단계를 수행합니다.

1. **시작 스테이션 위도** 및 **시작 스테이션 경도** 열을 다중 선택합니다. 열 중 하나를 마우스 오른쪽 단추로 클릭한 다음, **맵**을 선택합니다.

    > [!TIP]
    > 다중 선택을 사용하려면 Ctrl 키(Mac의 경우 Command ⌘)를 누른 채 각 열의 머리글을 선택합니다.

    ![맵 시각화](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. 맵 시각화를 최대화하려면 **최대화** 아이콘을 선택합니다. 맵을 창에 맞추려면 시각화의 왼쪽 위에 있는 **E** 아이콘을 선택합니다.

    ![최대화된 이미지](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. **최소화** 단추를 선택하여 그리드 뷰로 돌아갑니다.

## <a name="use-the-column-statistics-inspector"></a>열 통계 검사기 사용

열 통계 검사기를 사용하려면 __대여 시간__ 열을 마우스 오른쪽 단추로 클릭하고 __열 통계__를 선택합니다.

![열 통계 항목](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

이 프로세스는 __검사자__ 창에서 __대여 시간 통계__라는 제목의 새 시각화를 추가합니다.

![대여 시간 통계 검사기](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> 대여 시간의 최대 값은 2년 정도인 961,814분입니다. 데이터 집합에 이상값이 있는 것 같습니다.

## <a name="use-the-histogram-inspector"></a>히스토그램 검사기 사용

이상값 식별을 시도하려면 __대여 시간__ 열을 마우스 오른쪽 단추로 클릭하고 __히스토그램__을 선택합니다.

![히스토그램 검사기](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

이상값이 그래프를 왜곡하기 때문에 히스토그램은 도움이 되지 않습니다.

## <a name="add-a-column-by-using-script"></a>스크립트를 사용하여 열 추가

1. **대여 시간**을 마우스 오른쪽 단추로 클릭하고 **열 추가(스크립트)** 를 선택합니다.

    ![열 추가(스크립트) 메뉴](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. __열 추가(스크립트)__ 대화 상자에서 다음 값을 사용합니다.

    * __새 열 이름__: logtripduration

    * __다음 뒤에 이 새 열 삽입__: tripduration

    * __새 열 코드__: `math.log(row.tripduration)`

    * __코드 블록 형식__: 식

   ![열 추가(스크립트) 대화 상자](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. __확인__을 선택하여 **logtripduration** 열을 추가합니다.

4. 열을 마우스 오른쪽 단추로 클릭하고 **히스토그램**을 선택합니다.

    ![logtripduration 열의 히스토그램](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    시각적으로 이 히스토그램은 비정상적인 꼬리가 있는 정규 분포처럼 보입니다.

## <a name="use-an-advanced-filter"></a>고급 필터 사용

데이터에 필터를 사용하여 새 배포에서 검사기를 업데이트합니다. 

1. **logtripduration** 열을 마우스 오른쪽 단추로 클릭하고 **필터 열**을 선택합니다. 

2. __편집__ 대화 상자에서 다음 값을 사용합니다.

    * __이 숫자 열 필터링__: logtripduration

    * __관심 사항__: 행 유지

    * __시기__: 아래 조건 중 하나라도 True(논리적 OR)인 경우

    * __이 열 조건__: 다음보다 적음

    * __값__: 9

    ![필터 옵션](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. 필터를 적용하려면 __확인__을 선택합니다.

    ![필터를 적용한 후에 업데이트된 히스토그램](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Halo 효과

1. **logtripduration** 히스토그램 최대화 회색 히스토그램에 파란색 히스토그램이 겹쳐 있습니다. 이 화면을 **Halo 효과**라고 합니다.

    * 회색 히스토그램은 작업 전 분포를 나타냅니다(이 경우 필터링 작업).

    * 파란색 히스토그램은 작업 후 히스토그램을 나타냅니다. 

   Halo 효과를 통해 데이터에 대한 작업의 효과를 시각화할 수 있습니다.

   ![Halo 효과](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > 파란색 히스토그램이 이전에 비해 짧게 표시됩니다. 이러한 차이는 새 범위에서 데이터를 자동으로 다시 버킷하기 때문입니다.

2. Halo를 제거하려면 __편집__을 선택하고 __Halo 표시__ 선택을 취소합니다.

    ![히스토그램의 옵션](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. **OK**를 선택하여 Halo 효과를 사용하지 않도록 설정합니다. 그런 다음, 히스토그램을 최소화합니다.

### <a name="remove-columns"></a>열 제거

여행 데이터에서 각 행은 자전거 픽업 이벤트를 나타냅니다. 이 자습서에서는 **starttime** 및 **시작 스테이션 ID** 열만 필요합니다. 다른 열을 제거하려면 이 두 개의 열을 다중 선택하고 열 머리글을 마우스 오른쪽 단추로 클릭한 다음, **열 유지**를 선택합니다. 다른 열이 제거됩니다.

![열 유지 옵션](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>데이터 요약(총계)

2시간 동안 자전거 수요를 요약하려면 파생 열을 사용합니다.

1. **starttime** 열을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다.

    ![예제별 열 파생 옵션](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. 예를 들어 첫 번째 행에 **2017년 1월 1일 오전 12시-오전 2시** 값을 입력합니다.

    > [!IMPORTANT]
    > 파생 열의 이전 예제에서는 날짜 및 시간 기간을 포함하는 열을 파생시키는 여러 단계를 사용했습니다. 이 예제에서는 최종 출력의 예제를 제공하여 단일 단계로 이 작업을 수행할 수 있는지 확인합니다.

    > [!NOTE]
    > 행에 대한 예제를 제공할 수 있습니다. 이 예에서는 **2017년 1월 1일 오전 12시-오전 2시** 값이 데이터의 첫 번째 행에 유효합니다.

    ![예제 데이터](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Mac에서 3단계 대신 이 단계를 수행합니다.
   >
   > * **2017년 1월 1일 오전 1시~오전 2시**가 포함된 첫 번째 셀로 이동합니다. 14행이 여기에 해당됩니다. 값을 **2017년 1월 1일 오전 12시~오전 2시**로 수정하고 Enter를 선택합니다. 

3. 응용 프로그램이 모든 행에 대한 값을 계산할 때까지 기다립니다. 프로세스에 몇 초 정도 걸릴 수 있습니다. 분석이 완료되면 __다음으로 제안된 행 검토__ 링크를 사용하여 데이터를 검토합니다.

   ![검토 링크가 있는 완료된 분석](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    계산된 값이 정확한지 확인합니다. 그렇지 않으면 값을 예상 값으로 업데이트하고 Enter를 선택합니다. 그런 다음, 분석을 마칠 때까지 기다립니다. **제안 없음**이 표시되면 **다음 제안된 행 검토** 프로세스가 완료됩니다. **제안 없음**은 응용 프로그램이 에지 사례를 살펴봤고 합성된 프로그램에 만족한다는 것을 의미합니다. 변환을 수락하기 전에 변환된 데이터의 시각적 검사를 수행하는 것이 좋습니다. 

4. **확인**을 선택하여 변환을 적용합니다. 새로 만든 열의 이름을 **날짜 시간 범위**로 바꿉니다.

    ![이름이 바뀐 열](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. **starttime** 열 머리글을 마우스 오른쪽 단추로 클릭하고 **열 제거**를 선택합니다.

6. 데이터를 요약하려면 __변환__ 메뉴에서 __요약__을 선택합니다. 변환을 만들려면 다음 단계를 수행합니다.

    * __날짜 시간 범위__ 및 __시작 스테이션 ID__를 왼쪽의 **그룹화 방법**창으로 끌어옵니다.

    * __시작 스테이션 ID__를 오른쪽의 **데이터 요약** 창으로 끌어옵니다.

   ![요약 옵션](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. **확인**을 선택하여 요약 결과를 적용합니다.

## <a name="join-dataflows"></a>데이터 흐름 조인

여정 데이터와 날씨 데이터를 조인하려면 다음 단계를 사용합니다.

1. __변환__ 메뉴에서 __조인__을 선택합니다.

2. __테이블__: **BostonWeather**를 **왼쪽** 데이터 흐름으로 선택하고 **201701-hubway-tripdata**를 **오른쪽** 데이터 흐름으로 선택합니다. 계속하려면 **다음**을 선택합니다.

    ![테이블 선택](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __키 열__: 두 테이블에서 **날짜 시간 범위** 열을 선택하고 __다음__을 선택합니다.

    ![키 열 선택](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __조인 형식__: __행 일치__를 조인 형식으로 선택하고 __마침__을 선택합니다.

    ![행 조인 형식 일치](media/tutorial-bikeshare-dataprep/joinscreen.png)

    이 프로세스에서는 __조인 결과__라는 새 데이터 흐름을 만듭니다.

## <a name="create-additional-features"></a>추가 기능 만들기

1. 요일을 포함하는 열을 만들려면 **날짜 시간 범위** 열을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다. 일요일에 발생한 날짜에 __일요일__ 값을 사용합니다. 예를 들어 **2017년 1월 1일 오전 12시~오전 2시**입니다. Enter를 선택한 다음, **확인**을 선택합니다. 이 열의 이름을 __평일__로 변경합니다.

    ![요일에 대한 새 열 만들기](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. 행에 시간을 포함하는 열을 만들려면 **날짜 시간 범위** 열을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다. **2017년 1월 1일 오전 12시~오전 2시**를 포함하는 행에 **오전 12시~오전 2시** 값을 사용합니다. Enter를 선택한 다음, **확인**을 선택합니다. 이 열의 이름을 **기간**으로 변경합니다.

    ![기간 열](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. **날짜 시간 범위** 및 **r_Date 시간 범위** 열을 제거하려면 Ctrl(Mac의 경우 Command ⌘)을 누른 다음, 각 열 머리글을 선택합니다. 마우스 오른쪽 단추로 클릭한 후 **열 제거**를 선택합니다.

## <a name="read-data-from-python"></a>Python의 데이터를 읽기

Python 또는 PySpark의 데이터 준비 패키지를 실행하고 결과를 **데이터 프레임**으로 검색할 수 있습니다.

예제 Python 스크립트를 생성하려면 __자전거 공유 데이터 준비__를 마우스 오른쪽 단추로 클릭하고 __데이터 액세스 코드 파일 생성__을 선택합니다. 예제 Python 파일이 **프로젝트 폴더**에 만들어지고 Workbench 내의 탭에도 로드됩니다. 다음 Python 스크립트는 생성되는 코드의 예제입니다.

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

이 자습서의 경우 파일의 이름은 `BikeShare Data Prep.py`입니다. 이 파일은 자습서의 뒷부분에서 사용됩니다.

## <a name="save-test-data-as-a-csv-file"></a>CSV 파일로 테스트 데이터 저장

**조인 결과** 데이터 흐름을 .csv 파일로 저장하려면 `BikeShare Data Prep.py` 스크립트를 변경해야 합니다. 

1. Visual Studio Code에서 편집할 프로젝트를 엽니다.

    ![Visual Studio Code에서 프로젝트 열기](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. 다음 코드를 사용하여 `BikeShare Data Prep.py` 파일의 Python 스크립트를 업데이트합니다.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. `Your Azure Storage blob path`를 만들려는 출력 파일의 경로로 바꿉니다. `blobfolder` 및 `csvfiles` 변수를 바꿉니다.

## <a name="create-an-hdinsight-run-configuration"></a>HDInsight 실행 구성 만들기

1. Machine Learning Workbench에서 명령줄 창을 열고, **파일** 메뉴를 선택한 다음, **명령 프롬프트 열기**를 선택합니다. 명령 프롬프트가 프로젝트 폴더에서 `C:\Projects\BikeShare>` 프롬프트와 함께 시작됩니다.

    ![명령 프롬프트 열기](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >다음 단계를 수행하려면 명령줄 창(Workbench에서 열림)을 사용해야 합니다.

2. 명령 프롬프트를 사용하여 Azure에 로그인합니다. 

   Workbench 앱과 CLI는 Azure 리소스에 대해 인증할 때 독립적인 자격 증명 캐시를 사용합니다. 캐시된 토큰이 만료될 때까지 한 번만 수행하면 됩니다. `az account list` 명령은 로그인에 사용 가능한 구독 목록을 반환합니다. 둘 이상이 있는 경우 원하는 구독에서 ID 값을 사용합니다. 해당 구독을 기본 계정으로 설정하여 `az account set -s` 명령으로 사용한 다음, 구독 ID 값을 제공합니다. 그런 다음, account `show` 명령을 사용하여 설정을 확인합니다.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. HDInsight 실행 구성을 만듭니다. 클러스터 이름과 `sshuser` 암호가 필요합니다.

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> 빈 프로젝트가 생성되면 기본 실행 구성은 **로컬** 및 **docker**입니다. 이 단계에서는 스크립트를 실행할 때 Workbench에서 사용할 수 있는 새로운 실행 구성을 만듭니다. 

## <a name="run-in-an-hdinsight-cluster"></a>HDInsight 클러스터에서 실행

Machine Learning Workbench 응용 프로그램으로 돌아가서 HDInsight 클러스터에서 스크립트를 실행합니다.

1. 왼쪽의 **홈** 아이콘을 선택하여 프로젝트의 홈 화면으로 돌아갑니다.

2. HDInsight 클러스터에서 스크립트를 실행할 수 있도록 드롭다운 목록에서 **hdinsight**를 선택합니다.

3. **실행**을 선택합니다. 스크립트가 작업으로 제출됩니다. 파일이 저장소 컨테이너의 지정된 위치에 기록된 후 작업 상태가 __완료됨__으로 변경됩니다.

    ![HDInsight 실행 스크립트](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>데이터 원본 대체

이전 단계에서는 `201701-hubway-tripdata.csv` 및 `BostonWeather.csv` 데이터 원본을 사용하여 테스트 데이터를 준비했습니다. 다른 여행 데이터 파일을 포함한 패키지를 사용하려면 다음 단계를 사용합니다.

1. 프로세스를 다음과 같이 변경하고 이전에 지정된 단계를 사용하여 새 데이터 원본을 만듭니다.

    * __파일 선택__: 파일을 선택할 때 나머지 6개의 여행 데이터 .csv 파일을 중복 선택합니다.

    ![나머지 6개의 파일 로드](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > __+5__ 항목은 나열된 하나의 항목 이외에도 5개의 추가 파일이 있음을 나타냅니다.

    * __파일 세부 정보__: __승격 헤더 모드__를 **모든 파일에 동일한 헤더 포함**으로 설정합니다. 이 값은 파일이 각각 동일한 헤더를 포함하고 있음을 나타냅니다.

    ![파일 세부 정보 선택 영역](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   이 데이터 원본이 이후 단계에서 사용되므로 이름을 저장합니다.

2. 프로젝트에서 파일을 보려는 폴더 아이콘을 선택합니다. __aml\_config__ 디렉터리를 확장하고 `hdinsight.runconfig` 파일을 선택합니다.

    ![hdinsight.runconfig의 위치](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. **편집** 단추를 선택하여 Visual Studio Code에서 파일을 엽니다.

4. `hdinsight.runconfig` 파일의 끝에 다음 줄을 추가하고 디스크 아이콘을 선택하여 파일을 저장합니다.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    이러한 변경으로 인해 데이터 원본을 6개의 여행 데이터 파일을 포함하는 데이터로 바꿉니다.

## <a name="save-training-data-as-a-csv-file"></a>학습 데이터를 CSV 파일로 저장

1. 이전에 편집한 Python 파일인 `BikeShare Data Prep.py`로 이동합니다. 학습 데이터를 저장할 다른 파일 경로를 제공합니다.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. 학습 데이터 출력에 `traindata` 폴더를 사용합니다.

3. 새 작업을 제출하려면 **실행**을 선택합니다. **hdinsight**를 선택합니다. 새로운 구성으로 작업이 제출됩니다. 이 작업의 출력은 학습 데이터입니다. 이 데이터는 이전에 수행한 것과 동일한 데이터 준비 단계를 사용하여 생성됩니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계
자전거 공유 데이터 준비 자습서를 마쳤습니다. 이 자습서에서는 Machine Learning(미리 보기)을 사용하여 다음을 수행하는 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * Machine Learning 데이터 준비 도구를 사용하여 대화형으로 데이터를 준비합니다.
> * 테스트 데이터 집합을 가져오고, 변환하고, 만듭니다.
> * 데이터 준비 패키지를 생성합니다.
> * Python을 사용하여 데이터 준비 패키지를 실행합니다.
> * 추가 입력 파일에 대한 데이터 준비 패키지를 다시 사용하여 학습 데이터 집합을 생성합니다.

다음으로 데이터 준비에 대해 알아보겠습니다.
> [!div class="nextstepaction"]
> [데이터 준비 사용자 가이드](data-prep-user-guide.md)
