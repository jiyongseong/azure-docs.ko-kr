---
title: Azure Data Factory 시각적 모니터링 | Microsoft Docs
description: Azure Data Factory를 시각적으로 모니터링하는 방법에 대해 알아봅니다
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: shlo
ms.openlocfilehash: b67c384ffd04176653ad434d39361ee67dc1ffea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="visually-monitor-azure-data-factories"></a>Azure Data Factory를 시각적으로 모니터링하는 방법에 대해 알아봅니다
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다.
이 빠른 시작에서는 코드를 전혀 작성하지 않고 Data Factory v2 파이프라인을 시각적으로 모니터링하는 방법을 알아봅니다.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1의 파이프라인 모니터링 및 관리](v1/data-factory-monitor-manage-app.md)를 참조하세요.

## <a name="monitor-data-factory-v2-pipelines"></a>Data Factory v2 파이프라인 모니터링

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
3. Azure Portal에서 생성된 데이터 팩터리 블레이드로 이동한 다음 ‘모니터링 및 관리’ 타일을 클릭합니다. ADF v2 시각적 모니터링 환경이 시작됩니다.

## <a name="list-view-monitoring"></a>목록 보기 모니터링

간단한 목록 보기 인터페이스로 파이프라인과 활동 실행을 모니터링합니다. 모든 실행이 로컬 브라우저 시간대로 표시됩니다. 시간대를 변경할 수 있으며 모든 날짜 시간 필드가 선택된 시간대로 맞춰집니다.  

#### <a name="monitoring-pipeline-runs"></a>파이프라인 실행 모니터링
Data Factory v2 파이프라인에 대해 각 파이프라인 실행을 보여주는 목록 보기. 포함 열:

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인의 이름입니다. |
| 작업 | 활동 실행을 보는 데 사용할 수 있는 단일 작업. |
| 실행 시작 | 파이프라인 실행 시작 날짜 시간 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| 기간 | 실행 기간 (HH:MM:SS) |
| 트리거 주체 | 수동 트리거, 트리거 예약 |
| 상태 | 실패, 성공, 진행 중 |
| 매개 변수 | 파이프라인 실행 매개 변수 (이름, 값 쌍) |
| 오류 | 파이프라인 실행 오류 (있는 경우) |
| 실행 ID | 파이프라인 실행 ID |

![파이프라인 실행 모니터링](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>모니터링 작업 실행
각 파이프라인 실행에 해당하는 활동 실행을 보여주는 목록 보기. 각 파이프라인 실행에 대한 작업 실행을 보려면 **‘작업’** 열 아래 **‘작업 실행’** 아이콘을 클릭합니다. 포함 열:

| **열 이름** | **설명** |
| --- | --- |
| 작업 이름 | 파이프라인 내 작업의 이름입니다. |
| 작업 유형 | 작업 유형. 즉, Copy, HDInsightSpark, HDInsightHive 등. |
| 실행 시작 | 작업 실행 시작 날짜 시간 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| 기간 | 실행 기간 (HH:MM:SS) |
| 상태 | 실패, 성공, 진행 중 |
| 입력 | 작업 입력에 대해 설명하는 JSON 배열 |
| 출력 | 작업 출력에 대해 설명하는 JSON 배열 |
| 오류 | 작업 실행 오류 (있는 경우) |

![작업 실행 모니터링](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 파이프라인 및 활동 실행 목록을 새로 고치려면 맨 위의 **‘새로 고침’** 아이콘을 클릭해야 합니다. 자동 새로 고침은 현재 지원되지 않습니다.
>

![새로 고침](media/monitor-visually/refresh.png)

## <a name="features"></a>기능

#### <a name="rich-ordering-and-filtering"></a>풍부한 정렬 및 필터링

파이프라인 실행을 실행 시작을 기준으로 내림차순/오름차순으로 정렬하고 파이프라인 실행을 다음 열을 기준으로 필터링합니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인의 이름입니다. 옵션에는 ‘마지막 24시간’, ‘마지막 주’, ‘마지막 30일’에 대한 빠른 필터 또는 사용자 지정 날짜 시간 선택이 포함됩니다. |
| 실행 시작 | 파이프라인 실행 시작 날짜 시간 |
| 실행 상태 | 상태(성공, 실패, 진행 중)를 기준으로 실행 필터링 |

![Filter](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>목록 보기에 열 추가/제거
마우스 오른쪽 버튼으로 목록 보기 헤더를 클릭하고 목록 보기에 표시하려는 열을 선택합니다

![열](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>목록 보기의 열 너비 재정렬
열 헤더에 커서를 올려 놓아 목록 보기에서 열 너비를 늘리거나 줄입니다

#### <a name="select-data-factory"></a>데이터 팩터리 선택
왼쪽 상단에 있는 ‘Data Factory’ 아이콘에 커서를 올려 놓습니다. azure 구독 목록 및 모니터링하려는 Data Factory를 보려면 ‘화살표’ 아이콘을 클릭합니다.

![데이터 팩터리 선택](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>둘러보기
파이프라인 및 작업 실행을 모니터링하는 방법에 대한 단계별 지침을 확인하려면 왼쪽 하단의 ‘정보 아이콘’을 클릭하고 ‘둘러보기’를 클릭합니다.

![둘러보기](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>사용자 의견
다양한 기능 또는 경험 중인 문제에 대한 의견을 보내려면 ‘사용자 의견’ 아이콘을 클릭합니다.

![사용자 의견](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) 문서를 참조하세요.
