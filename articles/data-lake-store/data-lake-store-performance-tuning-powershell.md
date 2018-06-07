---
title: Data Lake Store에서 PowerShell을 사용하기 위한 성능 조정 지침 | Microsoft Docs
description: Data Lake Store에서 Azure PowerShell을 사용할 때 성능을 향상하는 방법
services: data-lake-store
documentationcenter: ''
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 7b19972ed4a75ac899a4b78b28ab36ba305a5a64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Azure Data Lake Store에서 PowerShell을 사용하기 위한 성능 조정 지침

이 문서에서는 Data Lake Store와 함께 PowerShell을 사용할 때 보다 나은 성능을 얻을 수 있도록 조정 가능한 속성에 대해 설명합니다.

## <a name="performance-related-properties"></a>성능 관련 속성

| 자산            | 기본값 | 설명 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | 이 매개 변수로 각 파일의 업로드 또는 다운로드를 위한 병렬 스레드 수를 선택할 수 있습니다. 이 숫자는 파일당 할당할 수 있는 최대 스레드 수를 나타내지만 시나리오에 따라 스레드 수가 줄어들 수 있습니다 .예를 들어 1KB 파일을 업로드하는 경우 20개의 스레드를 요청해도 하나의 스레드만 발생합니다.  |
| ConcurrentFileCount | 10      | 이 매개 변수는 특히 폴더 업로드 또는 다운로드를 위한 것입니다. 이 매개 변수는 업로드 또는 다운로드할 수 있는 동시 파일 수를 결정합니다. 이 숫자는 한 번에 업로드 또는 다운로드할 수 있는 최대 동시 파일 수를 나타내지만 시나리오에 따라 동시 수가 줄어들 수 있습니다. 예를 들어 두 개 파일을 업로드하는 경우 15개를 요청해도 두 개의 동시 파일 업로드가 발생합니다. |

**예제**

이 명령은 파일당 20개의 스레드와 100개의 동시 파일을 사용하여 Azure Data Lake Store에서 사용자의 로컬 드라이브로 파일을 다운로드합니다.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>이러한 속성의 값은 어떻게 결정할까요?

그 다음으로 나올 수 있는 질문은 성능 관련 속성에 입력해야 하는 값을 결정하는 방법입니다. 다음은 사용할 수 있는 몇 가지 지침입니다.

* **1단계: 총 스레드 수 확인** - 사용할 총 스레드 수를 계산하는 것으로 시작합니다. 일반적으로 물리적 코어 하나당 6개의 스레드를 사용해야 합니다.

        Total thread count = total physical cores * 6

    **예제**

    16개의 코어가 있는 D14 VM에서 PowerShell 명령을 실행 중이라고 가정합니다.

        Total thread count = 16 cores * 6 = 96 threads


* **2단계: PerFileThreadCount 계산**  - 파일 크기를 기반으로 PerFileThreadCount를 계산합니다. 파일 크기가 2.5GB 미만인 경우 기본값인 10이면 충분하므로 이 매개 변수를 변경하지 않아도 됩니다. 파일 크기가 2.5GB를 초과하는 경우 처음 2.5GB를 기준으로 10개 스레드를 사용하고 파일 크기가 256MB 증가할 때마다 1개 스레드를 추가해야 합니다. 더 큰 크기의 파일 범위로 폴더를 복사하는 경우 보다 작은 파일 크기로 그룹화하는 것이 좋습니다. 다른 파일 크기를 사용하면 성능이 저하될 수 있습니다. 유사한 파일 크기를 그룹화할 수 없는 경우 가장 큰 파일 크기를 기준으로 PerFileThreadCount를 설정해야 합니다.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **예제**

    1GB ~ 10GB 범위의 파일 100개가 있다고 가정하면 수식에서 가장 큰 파일 크기로 10GB를 사용하며 다음과 같습니다.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **3단계: ConcurrentFilecount 계산** - 총 스레드 수와 PerFileThreadCount를 사용하여 다음 수식에 따라 ConcurrentFileCount를 계산합니다.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **예제**

    사용 중인 예제 값 기준

        96 = 40 * ConcurrentFileCount

    따라서 **ConcurrentFileCount**는 **2.4**이고 **2**로 반올림할 수 있습니다.

## <a name="further-tuning"></a>추가 조정

작동하는 파일 크기 범위가 다양하므로 추가 조정이 필요할 수 있습니다. 위의 계산은 모든 또는 대부분의 파일이 10GB 범위보다 크거나 비슷한 경우 잘 작동합니다. 그렇지 않고 작은 파일이 많고 파일 크기가 매우 다양한 경우 PerFileThreadCount를 줄이면 됩니다. PerFileThreadCount를 줄여서 ConcurrentFileCount를 늘릴 수 있습니다. 따라서 대부분의 파일이 5GB 범위보다 작다면 계산을 다시 실행할 수 있습니다.

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

즉, **ConcurrentFileCount**는 96/20(4.8)이 되고 **4**로 반올림됩니다.

파일 크기 분포에 따라 **PerFileThreadCount**를 늘리거나 줄여 이러한 설정을 계속해서 조정할 수 있습니다.

### <a name="limitation"></a>제한 사항

* **파일 수가 ConcurrentFileCount보다 작음**: 업로드 중인 파일 수가 계산한 **ConcurrentFileCount**보다 작은 경우 **ConcurrentFileCount**가 파일 수와 같도록 줄여야 합니다. 나머지 스레드를 사용하여 **PerFileThreadCount**를 늘릴 수 있습니다.

* **스레드 수 너무 많음**: 클러스터 크기를 늘리지 않고 스레드 수를 너무 늘리면 성능 저하 위험이 있습니다. CPU에서 컨텍스트 전환 시 경합 문제가 발생할 수 있습니다.

* **동시성 부족**: 동시성이 부족한 경우 클러스터가 너무 작을 수 있습니다. 클러스터에서 노드 수를 늘려 동시성을 높일 수 있습니다

* **제한 오류**: 동시성이 너무 높으면 제한 오류가 표시될 수 있습니다. 제한 오류가 표시되면 동시성을 줄이거나 문의해 주세요.

## <a name="next-steps"></a>다음 단계
* [빅 데이터 요구 사항에 Azure Data Lake Store 사용](data-lake-store-data-scenarios.md) 
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 Data Lake Store 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

