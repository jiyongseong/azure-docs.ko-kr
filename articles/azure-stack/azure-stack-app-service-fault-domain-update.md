---
title: 'Azure 스택 앱 서비스: 도메인 업데이트 오류 | Microsoft Docs'
description: 오류 도메인 간에 Azure 스택에 Azure 앱 서비스를 재배포 하는 방법
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>오류 도메인 간에 Azure 스택에 Azure 앱 서비스를 재배포 하는 방법

*적용 대상: Azure 스택 시스템 통합*

1802 업데이트와 함께 Azure 스택 이제 지원 배포 작업의 오류 도메인 고가용성을 위해 중요 한 기능을 합니다.

> [!IMPORTANT]
> Azure 스택 통합 시스템 오류 도메인 지원 기능을 사용할 수 있게 되기를 1802를 업데이트 해야 합니다.  이 문서는 1802 업데이트 전에 완료 된 앱 서비스 리소스 공급자 배포에만 적용 됩니다.  Azure 스택에 1802 업데이트가 적용 된 후 Azure 스택 앱 서비스를 배포한, 하는 경우 리소스 공급자가 이미 오류 도메인 분산.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>앱 서비스 리소스 공급자 오류 도메인 간에 균형을 다시 조정

크기 집합 앱 서비스 리소스 공급자에 대 한 배포를 재배포 하기 위해 각 크기 집합에 대해 다음 단계를 수행 해야 합니다.  기본적으로 scaleset 이름은 다음과 같습니다.

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> 작업자 계층 크기 집합의 일부 배포 인스턴스가 있는 경우 이러한 크기 집합 균형을 다시 조정할 필요가 없습니다.  나중에 확장할 크기 집합이 올바르게 균형 됩니다.
>
>

1. Azure 스택 관리자 포털에서 가상 컴퓨터 크기 집합을 찾습니다.  앱 서비스 배포의 일부로 배포 된 기존 크기 집합 인스턴스 개수 정보와 함께 나열 됩니다.

    ![가상 컴퓨터 크기 조정 설정 UX에 나열 된 azure 앱 서비스 크기 집합][1]

2. 그런 다음 각 집합을 확장 합니다.  예를 들어 세 개의 기존 인스턴스 크기 집합에 있는 경우 수평 확장 해야 합니다 6으로 오류 도메인 간에 세 개의 새 인스턴스를 프로 비전 할 수 있도록 합니다.
    a. [PowerShell에서 Azure 스택 관리자 환경을 설정](azure-stack-powershell-configure-admin.md) b 합니다. 이 예제를 사용 하 여 크기 집합을 확장 하려면:
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> 이 단계는 다양 한 유형의 역할 및 인스턴스 수에 따라 완료 하는 데 시간이 걸릴 수 있습니다.
>
>

3. 앱 서비스 관리 역할 블레이드의 새 역할 인스턴스 상태를 모니터링 합니다.  목록에서 역할 유형을 클릭 하 여 개별 역할 인스턴스의 상태를 확인 합니다.

    ![Azure 스택 역할에서 azure 앱 서비스][2]

4. 새 인스턴스를 하나는 **준비** 상태, 가상 컴퓨터 크기 집합 블레이드로 다시 이동 하 고 **삭제** 이전 인스턴스.

5. 에 대 한 이러한 단계를 반복 **각** 가상 컴퓨터 크기 집합입니다.

## <a name="next-steps"></a>다음 단계

또한을 수행 하려면 다른 [플랫폼 서비스 (PaaS) 서비스로](azure-stack-tools-paas-services.md)합니다.

* [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
