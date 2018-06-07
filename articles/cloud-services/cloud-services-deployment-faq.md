---
title: Microsoft Azure Cloud Services FAQ에 대한 배포 문제 | Microsoft Docs
description: 이 문서는 Microsoft Azure Cloud Services의 배포에 대한 질문과 대답을 나열합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 05217129d4993514acaf8c717847040584984cb3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services의 배포 문제: FAQ(질문과 대답)

이 문서는 [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)의 배포 문제에 대한 질문과 대답을 포함합니다. 크기 정보는 [Cloud Services VM 크기 페이지](cloud-services-sizes-specs.md) 를 참조할 수도 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>프로덕션 슬롯에 기존 배포가 있는 경우 스테이징 슬롯에 대한 클라우드 서비스 배포가 리소스 할당 오류로 인해 실패하는 이유는 무엇인가요?
클라우드 서비스가 두 슬롯 중 하나에 배포된 경우 전체 클라우드 서비스가 특정 클러스터에 고정됩니다. 이는 프로덕션 슬롯에 이미 배포가 있을 경우 프로덕션 슬롯과 동일한 클러스터에만 새 스테이징 배포를 할당할 수 있다는 의미입니다.

클라우드 서비스가 있는 클러스터가 배포 요청을 충족하기에 충분한 물리적 계산 리소스 없는 경우 할당 오류가 발생합니다.

이러한 할당 오류를 완화하는 도움말은 [Cloud Service 할당 실패: 솔루션](cloud-services-allocation-failures.md#solutions)을 참조하세요.

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>클라우드 서비스 배포를 스케일 업 또는 스케일 아웃하는 경우 할당 오류가 발생하는 이유는 무엇인가요?
클라우드 서비스를 배포할 때 일반적으로 특정 클러스터에 고정됩니다. 즉, 기존 클라우드 서비스를 스케일 업/아웃하면 동일한 클러스터에 새 인스턴스를 할당해야 합니다. 클러스터가 수용작업량에 가까워지거나 원하는 VM 크기/형식을 사용할 수 없는 경우 요청이 실패할 수 있습니다.

이러한 할당 오류를 완화하는 도움말은 [Cloud Service 할당 실패: 솔루션](cloud-services-allocation-failures.md#solutions)을 참조하세요.

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>선호도 그룹에 클라우드 서비스를 배포하는 경우 할당 오류가 발생하는 이유는 무엇인가요?
클라우드 서비스가 선호도 그룹에 고정되어 있지 않는 한, 빈 클라우드 서비스에 새로 배포할 경우 해당 영역에 있는 클러스터의 패브릭에 의해서만 할당할 수 있습니다. 동일한 선호도 그룹에 대한 배포는 동일한 클러스터에서 시도됩니다. 클러스터의 용량이 거의 찼을 경우 요청이 실패할 수 있습니다.

이러한 할당 오류를 완화하는 도움말은 [Cloud Service 할당 실패: 솔루션](cloud-services-allocation-failures.md#solutions)을 참조하세요.

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>VM 크기를 변경하거나 기존 클라우드 서비스에 새 VM을 추가하면 할당 오류가 발생하는 이유는 무엇인가요?
데이터 센터의 클러스터에는 다양한 컴퓨터 종류의 구성이 있습니다(예: A 시리즈, Av2 시리즈, D 시리즈, Dv2 시리즈, G 시리즈, H 시리즈 등). 하지만 클러스터에는 일부 종류의 VM만이 있습니다. 예를 들어 A 시리즈 전용 클러스터에 이미 배포된 클라우드 서비스에 D 시리즈 VM을 추가하려는 경우 할당 오류가 발생합니다. 또한 VM SKU의 크기를 변경하려는 경우에 발생합니다(예: A 시리즈에서 D 시리즈로 전환).

이러한 할당 오류를 완화하는 도움말은 [Cloud Service 할당 실패: 솔루션](cloud-services-allocation-failures.md#solutions)을 참조하세요.

사용자 지역에서 사용할 수 있는 크기를 확인하려면 [Microsoft Azure: 지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services)을 참조하세요.

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>구독 또는 서비스에서 제한/할당량/제한 조건으로 인해 클라우드 서비스를 배포하는 데 실패하는 이유는 무엇인가요?
할당하는 데 필요한 리소스가 지역/데이터 센터 수준에서 서비스에 허용되는 기본 또는 최대 할당량을 초과하는 경우 클라우드 서비스의 배포에 실패할 수 있습니다. 자세한 내용은 [Cloud Services 제한](../azure-subscription-service-limits.md#cloud-services-limits)을 참조하세요.

포털에서 구독에 대한 현재 사용량/할당량도 추적할 수 있습니다. Azure Portal => 구독=> \<적절한 구독=> “사용량 + 할당량”

Azure 청구 API를 통해 리소스 사용/사용 관련 정보를 검색할 수도 있습니다. [Azure 리소스 사용량 API(미리 보기)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview)을 참조하세요.

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>배포된 클라우드 서비스 VM을 다시 배포하지 않고 크기를 변경하려면 어떻게 할까요?
배포된 클라우드 서비스 VM을 다시 배포하지 않고 크기를 변경할 수 없습니다. VM 크기는 CSDEF에 포함되며 재배포로 업데이트할 수 있습니다.

자세한 내용은 [클라우드 서비스를 업데이트하는 방법](cloud-services-update-azure-service.md)을 참조하세요.

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Azure Resource Manager 저장소 계정을 사용하는 경우 Service Management API 또는 PowerShell을 통해 Cloud Services를 배포할 수 없는 이유는 무엇인가요? 

Cloud Service는 Azure Resource Manager 모델과 직접 호환되지 않는 클래식 리소스이므로 Azure Resource Manager 저장소 계정과 함께 연결할 수 없습니다. 다음은 몇 가지 옵션입니다. 
 
- REST API를 통해 배포.

    Service Management REST API를 통해 배포하면 클래식 및 Azure Resource Manager 저장소 계정 모두를 통해 작업할 수 있는 blob 저장소에 대한 SAS URL을 지정하여 이러한 제한의 문제를 해결할 수 있습니다. [여기](https://msdn.microsoft.com/library/azure/ee460813.aspx)에서 ‘PackageUrl’ 속성에 대해 자세히 읽어보세요.
  
- [Azure Portal](https://portal.azure.com)을 통해 배포.

    호출이 Azure Resource Manager와 클래식 리소스 간의 통신을 허용하는 프록시/shim을 통해 전송되므로 이 작업은 [Azure Portal](https://portal.azure.com)에서 작동합니다. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Azure Portal에서 배포할 저장소 계정을 제공하도록 하는 이유는 무엇인가요? 

클래식 포털에서는 패키지가 관리 API 계층에 직접 업로드되면 API 계층에서 패키지를 내부 저장소 계정에 임시로 저장했습니다.  API 계층은 파일 업로드 서비스로 설계되지 않았기 때문에 이 프로세스로 인해 성능 및 확장성 문제가 발생합니다.  Azure Portal(Resource Manager 배포 모델)에서는 API 계층에 먼저 업로드하는 중간 단계를 무시하여 보다 빠르고 안정적인 배포가 가능해졌습니다. 

비용 측면에서는 매우 작지만 모든 배포에서 동일한 저장소 계정을 재사용할 수 있습니다. [요금 계산기](https://azure.microsoft.com/pricing/calculator/#storage1)를 사용하여 서비스 패키지(CSPKG)를 업로드하고 CSPKG를 다운로드한 다음 CSPKG를 삭제하는 비용을 확인할 수 있습니다. 
