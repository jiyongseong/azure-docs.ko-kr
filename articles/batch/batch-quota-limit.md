---
title: Azure Batch의 서비스 할당량 및 제한 | Microsoft Docs
description: 기본 Azure Batch 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법에 대해 알아봅니다.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 19cdf81a355bae4d59cb49c1c588b995b040309b
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="batch-service-quotas-and-limits"></a>Batch 서비스 할당량 및 제한

다른 Azure 서비스와 마찬가지로 Batch 서비스와 관련하여 특정 리소스에 대한 제한이 있습니다. 이러한 제한 대부분은 Azure 구독 또는 계정 수준에서 적용되는 기본 할당량입니다. 이 문서는 그러한 기본값을 설명하고 할당량 증가를 요청하는 방법을 설명합니다.

Batch 워크로드를 설계 및 강화할 때 이 할당량에 주의합니다. 예를 들어, 풀이 지정한 계산 노드의 대상 수에 도달하지 않는 경우 배치 계정의 주요 할당량 한도에 도달했을 수 있습니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 Batch 계정 간에 워크로드를 배포할 수 있습니다.

Batch에서 프로덕션 작업을 실행하려고 계획하는 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료 온라인 [고객지원 요청](#increase-a-quota) 을 개설합니다.

> [!NOTE]
> 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.
> 
> 

## <a name="resource-quotas"></a>리소스 할당량
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>사용자 구독 모드에서 코어 할당량

풀 할당 모드가 **사용자 구독**으로 설정된 Batch 계정을 만든 경우 할당량이 다르게 적용됩니다. 이 모드에서 Batch VM 및 기타 리소스는 풀이 만들어질 때 구독에서 직접 만들어집니다. Azure Batch 코어 할당량은 이 모드에서 생성된 계정에는 적용되지 않습니다. 대신, 지역별 계산 코어 및 기타 리소스에 대한 구독의 할당량이 적용됩니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에서 이러한 할당량에 대해 자세히 알아보세요.

## <a name="other-limits"></a>기타 제한
| **리소스** | **최대 제한** |
| --- | --- |
| [동시 작업](batch-parallel-node-tasks.md)  |4 x 노드 코어 수 |
| [응용 프로그램](batch-application-packages.md)  |20 |
| 응용 프로그램당 응용 프로그램 패키지 |40 |
| 각 응용 프로그램 패키지 크기 |약 195GB<sup>1</sup> |
| 최대 시작 태스크 크기 | 32768자<sup>2</sup> |
| 최대 작업 수명 | 7일<sup>3</sup> |
| 노드 간 통신 사용 풀의 계산 노드 | 100 |

<sup>1</sup> 최대 블록 Blob 크기에 대한 Azure Storage 용량 한도<br />
<sup>2</sup> 리소스 파일 및 환경 변수를 포함합니다.<br />
<sup>3</sup> 태스크가 작업에 추가될 때부터 완료될 때까지 태스크의 최대 수명은 7일입니다. 완료된 태스크무한 지속됩니다. 최대 수명 이내에 완료되지 않은 태스크에 대한 데이터에는 액세스할 수 없습니다.


## <a name="view-batch-quotas"></a>Batch 할당량 보기
[Azure 포털][portal]에서 Batch 계정 할당량을 봅니다.

1. 포털에서 **Batch 계정** 을 선택한 다음 관심 있는 Batch 계정을 선택합니다.
2. 배치 계정의 메뉴에서 **할당량**을 선택합니다.
3. Batch 계정에 현재 적용된 할당량을 표시합니다.
   
    ![Batch 계정 할당량][account_quotas]



## <a name="increase-a-quota"></a>할당량 증가
[Azure Portal][portal]을 사용하여 Batch 계정 또는 구독에 대해 할당량 증가를 요청하려면 다음 단계를 수행합니다. 할당량 증가 유형은 Batch 계정의 풀 할당 모드에 따라 다릅니다.

### <a name="increase-a-batch-cores-quota"></a>Batch 코어 할당량 증가 

1. 포털 대시보드에서 **도움말 + 지원** 타일을 선택하거나 포털 오른쪽 위 모서리에 있는 물음표(**?**)를 선택합니다.
2. **새 기본 지원 요청** > **기본**을 클릭합니다.
3. **기본 사항**에서
   
    a. **문제 형식** > **할당량**
   
    나. 사용 중인 구독을 선택합니다.
   
    다. **할당량 유형** > **배치**
   
    d. **지원 계획** > **할당량 지원 - 포함됨**
   
    **다음**을 클릭합니다.
4. **문제**에서
   
    a. [비즈니스 영향][support_sev]에 따라 **심각도**를 선택합니다.
   
    나. **세부 정보**에서 변경하려는 각 할당량과 Batch 계정 이름, 새로운 제한을 지정합니다.
   
    **다음**을 클릭합니다.
5. **연락처 정보**에서
   
    a. **기본 연락 방법**을 선택합니다.
   
    나. 필수 연락처 세부 정보를 확인하고 입력합니다.
   
    **만들기** 를 클릭하여 지원 요청을 제출합니다.

지원 요청을 제출하면 Azure 지원 팀에서 연락을 드릴 것입니다. 참고로 요청 완료까지 업무일 기준 최대 2일이 걸릴 수 있습니다.


## <a name="related-topics"></a>관련된 항목
* [Azure Portal을 사용하여 Azure Batch 계정 만들기](batch-account-create-portal.md)
* [Azure Batch 기능 개요](batch-api-basics.md)
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
