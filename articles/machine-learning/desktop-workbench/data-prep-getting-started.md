---
title: Azure Machine Learning을 위한 데이터 준비 시작 | Microsoft Docs
description: AML Workbench의 데이터 준비 섹션에 대한 시작 가이드입니다.
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: b0fbb0af433cfad6693b022d7a00373dc39533aa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="getting-started-with-data-preparation"></a>데이터 준비 시작

데이터 준비 시작 가이드를 시작합니다. 

데이터 준비(Data Preparation)는 데이터를 효율적으로 탐색하고, 이해하고, 관련 문제를 해결하기 위한 도구 집합을 제공합니다. 다양한 형식의 데이터를 사용하고, 이 데이터를 다운스트림에서 사용하기 적합한 정리된 데이터로 변환할 수 있습니다.

데이터 준비는 Azure Machine Learning Workbench 환경의 일부로 설치됩니다.  데이터 준비를 로컬로 사용하거나, 대상 클러스터 및 클라우드에 런타임 또는 실행 환경으로 배포합니다.

디자인 타임 런타임은 확장성을 위해 Python을 사용하며, Pandas와 같은 다양한 Python 라이브러리에 의존합니다. Azure ML Workbench의 다른 구성 요소와 마찬가지로, Python도 자동으로 설치되므로 직접 설치할 필요가 없습니다. 그러나 추가 라이브러리를 설치해야 하는 경우, 이러한 라이브러리를 일반 Python 디렉터리가 아닌 Azure ML Workbench Python 디렉터리에 설치해야 합니다. 패키지를 설치하는 방법에 대한 자세한 내용은 [여기](data-prep-python-extensibility-overview.md)에서 찾을 수 있습니다.

데이터 준비를 사용하려면 프로젝트가 필요합니다. 프로젝트가 만들어지면 데이터를 준비할 수 있습니다. 

화면 왼쪽에 있는 데이터 아이콘 ![데이터 원본 아이콘](media/data-prep-getting-started/data-source-icon.png)을 선택하여 프로젝트의 데이터 섹션으로 이동합니다.  "+"를 다시 클릭하여 **데이터 원본을 추가**합니다. 데이터 원본 마법사가 시작되며, 마법사가 완료된 후에는 프로젝트에 **데이터 원본**(.dsource) 파일이 추가됩니다. 기본적으로 데이터의 보기는 그리드 형태입니다. 그리드 위에서 메트릭 보기를 선택할 수도 있습니다. 메트릭 보기에는 요약 통계가 표시됩니다.  요약 통계를 검토한 후 화면 위쪽의 **준비**를 클릭합니다. [데이터 원본 마법사에 대한 자세한 내용](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>데이터 준비의 구성 요소 ##
### <a name="the-package"></a>패키지 ###

패키지는 작업에 대한 기본 컨테이너입니다. 패키지는 디스크에 저장되고 디스크에서 로드하는 아티팩트입니다. 클라이언트 내에서 작업하는 동안 패키지는 백그라운드에서 계속 자동 저장됩니다. 

패키지의 출력/결과는 Python에서, 또는 Jupyter Notebook을 통해 탐색할 수 있습니다.

패키지는 로컬 Python, Spark(Docker에 포함) 및 HDInsight를 비롯한 여러 런타임 간에 실행될 수 있습니다.

패키지에는 데이터에 적용되는 단계 및 변환에 해당하는 하나 이상의 데이터 흐름이 포함되어 있습니다.

패키지는 다른 패키지를 데이터 원본으로 사용할 수 있습니다(참조 데이터 흐름이라고 함).

### <a name="the-dataflow"></a>데이터 흐름 ###
데이터 흐름에는 하나의 원본과, 일련의 단계 및 선택적 대상을 통해 정렬되는 선택적 변환이 있습니다. 단계를 클릭하면 선택한 단계를 포함한 이전의 모든 원본 및 변환이 다시 실행됩니다.  해당 단계를 통해 변환된 데이터는 그리드에 표시됩니다. Step List(단계 목록)를 통해 데이터 흐름 내에서 단계를 추가, 이동 및 삭제할 수 있습니다.

클라이언트의 오른쪽에 있는 단계 목록을 열고 닫아 더 많은 화면 공간을 확보할 수 있습니다.

UI 내에 여러 데이터 흐름이 한꺼번에 포함될 수 있으며 각 데이터 흐름은 UI에 탭으로 표시됩니다.

### <a name="the-source"></a>원본
원본은 데이터가 원래 있던 위치이며 데이터의 원래 형식입니다. 데이터 준비 패키지는 항상 다른 데이터 흐름(데이터 원본)에서 데이터를 소싱합니다. 이러한 참조 데이터 흐름에 정보가 포함되어 있습니다. 원본을 구성할 수 있는 사용자 환경은 각 원본마다 다릅니다. 원본은 데이터의 "사각형"/표 형식 보기를 생성합니다. 원본 데이터가 원래 "왼쪽 정렬" 상태이면 구조는 “사각형”으로 정규화됩니다. [부록 2는 현재 지원되는 원본 목록을 제공합니다](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>변환 ###
변환은 지정된 형식의 데이터를 사용하고, 데이터에 대해 작업을 수행(예: 데이터 형식 변경)한 다음, 새로운 형식의 데이터를 생성합니다. 각 변환에는 자체 UI와 동작이 있습니다. 데이터 흐름의 단계를 통해 여러 변환을 연결하는 것이 데이터 준비 기능의 핵심입니다. [부록 3은 현재 지원되는 변환 목록을 제공합니다](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>검사기 ###

검사기는 데이터를 시각화한 것으로, 데이터의 이해를 향상시키는 데 사용할 수 있습니다.  데이터 및 데이터 품질 문제를 이해하면 수행할 작업(변환)을 결정하는 데 도움이 됩니다. 일부 검사기는 변환을 생성하는 작업을 지원합니다. 예를 들어, Value Count(값 개수) 검사기에서 값을 선택한 다음, 필터를 적용하여 해당 값을 포함하거나 제외할 수 있습니다. 또한 검사기는 변환을 위한 컨텍스트도 제공할 수 있습니다. 예를 들어 하나 이상의 열을 선택하면 적용 가능한 변환이 변경됩니다.

어느 시점에든 하나의 열에 여러 검사기가 있을 수 있습니다(예: Column Statistics(열 통계) 및 Histogram(히스토그램)). 여러 열에 한 검사기의 여러 인스턴스가 있을 수도 있습니다. 예를 들어, 모든 숫자 열에 히스토그램이 동시에 있을 수 있습니다.

검사기는 화면 맨 아래의 Profiling Well(프로파일링 웰)에 표시됩니다.  메인 콘텐츠 영역 내에서 검사기를 최대화하여 더 크게 볼 수 있습니다. 데이터 그리드를 기본 검사기로 생각하면 됩니다. 모든 검사기는 메인 콘텐츠 영역으로 확장할 수 있습니다. 메인 콘텐츠 영역 내의 검사기는 프로파일링 웰로 최소화됩니다. 검사기 내에서 연필 아이콘을 클릭하면 검사기를 사용자 지정할 수 있습니다. 끌어서 놓기를 사용하여 웰 내에서 검사기를 재정렬할 수 있습니다.

일부 검사기는 "Halo" 모드를 지원합니다. 이 모드는 마지막 변환을 적용하기 전의 값 또는 상태를 표시합니다. 이전 값은 회색으로 표시되고 포그라운드에 현재 값이 표시되어 변환의 영향을 볼 수 있습니다. [부록 4는 현재 지원되는 검사기 목록을 제공합니다](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>대상
 대상은 데이터 흐름에서 데이터를 준비한 후에 쓰거나 내보내는 대상 위치입니다. 한 특정 데이터 흐름에는 여러 대상이 있을 수 있습니다. 대상을 구성할 수 있는 사용자 환경은 각 대상마다 다릅니다. 대상은 "사각형"/표 형식의 데이터를 사용하고 지정된 형식으로 특정 위치에 씁니다. [부록 5는 현재 지원되는 대상 목록을 제공합니다](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>데이터 준비 사용 ###
Data Preparation은 데이터 준비를 위한 기본 5단계 방법/접근법을 따릅니다.

#### <a name="step-1-ingestion"></a>1단계: 수집 ####
프로젝트 보기 내에서 **데이터 원본 추가** 옵션을 사용하여 데이터 준비를 위한 데이터를 가져옵니다.  초기 데이터 수집은 모두 데이터 원본 마법사를 통해 처리됩니다.

#### <a name="step-2-understandprofile-the-data"></a>2단계: 데이터 이해/프로파일링 ####

먼저, 각 열 맨 위에 있는 Data Quality Bar(데이터 품질 막대)를 살펴봅니다. 녹색은 값이 있는 행을 나타냅니다. 회색은 누락된 값, null 등이 있는 행을 나타냅니다. 빨간색은 오류 값을 나타냅니다. 각 막대 위로 마우스를 가져가면 세 개의 각 버킷에 포함된 정확한 행 수를 보여 주는 도구 설명이 표시됩니다. 데이터 품질 막대는 로그 척도를 사용하므로 항상 실제 수치를 확인하여 누락된 데이터 양을 대략적으로 이해하도록 합니다.

![열](media/data-prep-getting-started/columns.png)

다음으로, 데이터 특성을 좀 더 잘 이해하기 위해 다른 검사기와 그리드를 함께 사용하도록 합니다.  추가 분석을 위해서는 데이터 준비에 대한 가설을 작성해 봅니다. 대부분 검사기는 한 개의 열 또는 적은 개수의 열에 대해 작동합니다.  

데이터 이해를 위해 여러 열에서 여러 개의 검사기가 필요할 수 있습니다. 다양한 검사기는 프로파일링 웰에서 스크롤하여 확인할 수 있습니다. 웰 내에서 검사기를 목록의 맨 위로 이동하여 즉시 볼 수 있는 영역에서 검사기를 볼 수도 있습니다.

![검사기](media/data-prep-getting-started/inspectors.PNG)

연속 및 범주별 변수/열에 대해 여러 다양한 검사기가 제공됩니다. 검사기 메뉴에서, 존재하는 변수/열 유형에 따라 옵션이 활성화 또는 비활성화됩니다

많은 열이 있는 대규모 데이터 집합으로 작업하는 경우 실용적인 권장 방법은 하위 집합을 사용하는 것입니다. 이 방법은 적은 수의 열(예: 5-10개)에 중점을 두어 준비한 다음, 나머지 열을 작업하는 것입니다. 표 검사기는 열의 수직 분할을 지원하므로 300개 이상의 열이 있는 경우 "페이지" 간 이동하여 확인해야 합니다.
 

#### <a name="step-3-transform-the-data"></a>3단계: 데이터 변환 ####
변환은 데이터를 변경하고 현재 작업 가설을 지원할 수 있게 데이터를 실행하도록 합니다. 변환은 오른쪽의 Step List(단계 목록)에 Steps(단계)로 표시됩니다. Step List(단계 목록)의 임의 지점을 클릭하면 단계 목록을 따라 "시간 이동"할 수 있습니다.

지정된 Step(단계) 왼쪽에 있는 녹색 아이콘은 '단계가 실행되었고 데이터에 변환의 실행이 반영되었음'을 나타냅니다. Step(단계) 왼쪽에 있는 세로 막대는 검사기에 있는 데이터의 현재 상태를 나타냅니다.

![단계](media/data-prep-getting-started/steps.PNG)

데이터를 자주 조금씩 변경해봐서 가설이 변함에 따라 각 변경 내용의 유효성을 검사해봅니다(4단계).

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>4단계: 변환의 영향을 확인합니다. 
가설이 올바른지 확인합니다. 올바른 경우 다음 가설을 만들고 새 가설에 대해 2-3단계를 반복합니다. 가설이 잘못된 경우 마지막 변환을 실행 취소하고 새 가설을 만든 후 2-3단계를 반복합니다.

변환의 영향이 올바른지 확인하는 기본적인 방법은 검사기를 사용하는 것입니다. 기존 검사기를 사용합니다. Halo 효과가 사용 설정된 검사기를 사용하거나 여러 검사기를 실행하여 특정 시점의 데이터를 확인합니다.

![halo 검사기](media/data-prep-getting-started/halo1.PNG) ![halo 검사기](media/data-prep-getting-started/halo2.PNG)

변환을 실행 취소하려면 UI 오른쪽의 Steps List(단계 목록)로 이동합니다. (단계 목록 패널을 다시 표시해야 할 수도 있습니다. 다시 열려면 왼쪽을 가리키는 이중 펼침 단추를 클릭합니다.) 패널에서 실행 취소하려는 실행된 변환을 선택합니다. UI 블록 오른쪽에 있는 드롭다운을 선택합니다. **편집**을 선택하여 변경하거나 **삭제**를 선택하여 단계 목록 및 데이터 흐름에서 변환을 제거합니다.

#### <a name="step-5-output"></a>5단계: 출력 
데이터 준비가 완료되면 데이터 흐름을 출력에 쓸 수 있습니다. 데이터 흐름은 여러 출력으로 쓸 수 있습니다. Transforms(변환) 메뉴에서 어떤 출력으로 데이터 집합을 쓸 것인지 선택할 수 있습니다. 또한 출력의 대상을 선택할 수도 있습니다. 

## <a name="list-of-appendices"></a>부록 목록 
[부록 2 - 지원되는 데이터 원본](data-prep-appendix2-supported-data-sources.md)  
[부록 3 - 지원되는 변환](data-prep-appendix3-supported-transforms.md)  
[부록 4 - 지원되는 검사기](data-prep-appendix4-supported-inspectors.md)  
[부록 5 - 지원되는 대상](data-prep-appendix5-supported-destinations.md)  
[부록 6 - Python 필터 식 샘플](data-prep-appendix6-sample-filter-expressions-python.md)  
[부록 7 - Python 변환 데이터 흐름 식 샘플](data-prep-appendix7-sample-transform-data-flow-python.md)  
[부록 8 - Python 데이터 원본 샘플](data-prep-appendix8-sample-source-connections-python.md)  
[부록 9 - Python 대상 연결 샘플](data-prep-appendix9-sample-destination-connections-python.md)  
[부록 10 - Python 열 변환 샘플](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>참고 항목

[고급 데이터 준비 자습서](tutorial-bikeshare-dataprep.md)