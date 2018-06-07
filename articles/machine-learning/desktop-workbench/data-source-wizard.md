---
title: Azure Machine Learning에 대한 Azure 데이터 원본 마법사 | Microsoft Docs
description: AML 워크벤치의 데이터 원본 마법사를 설명합니다
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4f9a2249142220f3c57cb2e479a175d4227749fd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="data-source-wizard"></a>데이터 원본 마법사 #

데이터 원본 마법사는 코드 없이 데이터 집합을 Azure ML Workbench로 가져오는 쉽고 빠른 방법입니다. 또한 여기서 데이터 집합에 대한 샘플 전략 및 각 열에 대한 데이터 형식을 선택할 수 있습니다. 

## <a name="step-1-trigger-the-data-source-wizard"></a>1 단계: 데이터 원본 마법사를 트리거합니다. ## 

데이터 원본 마법사를 사용하여 데이터를 프로젝트로 가져오려면. 데이터 뷰에서 검색 상자 옆의 **+** 단추를 선택하고 데이터 원본 추가를 선택합니다. 

![데이터 원본 추가](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>2 단계: 데이터가 저장된 곳을 선택합니다 ##
먼저, 데이터가 현재 어떻게 들어 있는지 지정합니다. 플랫 파일 또는 디렉터리에서 parquet 파일, Excel 파일 또는 데이터베이스로 저장할 수 있습니다. 자세한 내용은 [지원되는 데이터 원본](data-prep-appendix2-supported-data-sources.md)을 참조하세요.

![1단계](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>3 단계: 데이터 파일을 선택합니다 ##
파일/디렉터리에 대한 파일 경로를 지정합니다. 드롭다운에서 데이터의 위치를 선택합니다 - 그 위치는 로컬 파일 경로나 Azure Blob Storage가 될 수 있습니다. 

입력하여 경로를 지정하거나 **찾아보기...** 단추를 클릭하여 검색합니다. 디렉터리 또는 하나 이상의 파일을 찾아볼 수 있습니다.

나머지 단계에서 기본값을 받아들이려면 **마침**을 클릭하고 다음 단계로 진행하려면 Next를 클릭합니다.


![4단계](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>4 단계: 파일 매개 변수를 선택합니다. ##

데이터 원본 마법사는 파일 형식, 구분 기호 및 인코딩을 자동으로 감지할 수 있습니다. 또한 데이터가 어떤 모양인지 예를 들어 보여줄 것입니다. 또한 이러한 매개 변수를 수동으로 변경할 수 있습니다. 

![5단계](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>5 단계: 열의 데이터 형식을 설정합니다 ##

데이터 원본 마법사는 데이터 집합 열의 데이터 형식을 자동으로 감지합니다. 형식이 누락되었거나 데이터 형식을 강제하려면 데이터 형식을 수동으로 변경할 수 있습니다. **샘플 출력 데이터** 열은 데이터가 어떤 모양인지 예를 들어 보여줄 것입니다.

데이터 준비에서 날짜를 포함하도록 유추하는 열의 경우 날짜 형식으로 월 및 일의 순서를 선택하라는 메시지가 표시될 수 있습니다. 예를 들어 1/2/2013은 1월 2일(이 경우 *일-월*) 또는 2월 1일(*월-일* 선택)을 나타낼 수 있습니다.

![6단계](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>6 단계: 데이터에 대한 샘플링 전략을 선택합니다 ##

데이터 집합에 대해 하나 이상의 샘플링 전략을 지정하고, 하나를 활성 전략으로 선택할 수 있습니다. 기본값은 맨 위값 10000 행을 로드하는 것입니다. 도구 모음에서 **편집** 단추를 클릭하여 이 샘플을 편집하거나 +새로 만들기를 클릭하여 새로운 전략을 추가할 수 있습니다. 현재 지원되는 전략은

-     행 수(맨 위)
-     행 수(난수)
-     행 수(임의 백분율)
-     전체 파일

원하는 수만큼 많은 샘플링 전략을 지정할 수 있지만, 데이터를 준비할 때 오직 한 전략 만을 활성으로 설정할 수 있습니다. 전략을 선택하고 도구 모음에서 활성으로 설정을 클릭하여 어떤 전략이라도 활성이 되도록 설정할 수 있습니다.

데이터 원본 위치에 따라 몇 가지 샘플 전략은 지원되는 않을 수도 있습니다. 샘플링에 관한 더 자세한 내용은 [이 문서](data-prep-user-guide.md)의 샘플링 섹션을 참조하세요. 

![6단계](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>7 단계: 경로 열 처리 ##

파일 경로에 중요한 데이터가 포함되는 경우 그것을 데이터 집합에 첫 번째 열로 포함되도록 선택할 수 있습니다. 여러 파일을 가져올 경우 이 옵션이 유용할 수 있습니다. 원하지 않을 경우 포함하지 않도록 선택할 수 있습니다.

![7단계](media/data-source-wizard/step6.png)

완료를 클릭하면 새 데이터 원본이 프로젝트에 추가됩니다. 데이터 뷰의 Data Sources 그룹 아래에서 찾거나 **파일 보기**에서 dsource 파일로 찾을 수 있습니다.
