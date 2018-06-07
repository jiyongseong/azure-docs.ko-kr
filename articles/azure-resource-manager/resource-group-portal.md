---
title: Azure 포털을 사용하여 Azure 리소스 관리 | Microsoft Docs
description: Azure 포털 및 Azure 리소스 관리자를 사용하여 리소스를 관리합니다. 대시보드를 사용하여 리소스를 모니터링하는 방법을 보여줍니다.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: fda195c03c589361b8a798166b0ae4d93611bcd0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="manage-azure-resources-through-portal"></a>포털을 통해 Azure 리소스 관리

이 문서에서는 [Azure Resource Manager](resource-group-overview.md)를 포함한 [Azure Portal](https://portal.azure.com)을 사용하여 Azure 리소스를 관리하는 방법을 보여 줍니다. 포털을 통해 리소스를 배포하는 방법을 알아보려면 [Resource Manager 템플릿 및 Azure 포털을 사용하여 리소스 배포](resource-group-template-deploy-portal.md)를 참조하세요.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>리소스 그룹 관리

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다. 

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

1. 구독에서 모든 리소스 그룹을 보려면 **리소스 그룹**을 참조하세요.
   
    ![리소스 그룹 찾아보기](./media/resource-group-portal/browse-groups.png)
2. 빈 리소스 그룹을 만들려면 **추가**를 선택합니다.
   
    ![리소스 그룹 추가](./media/resource-group-portal/add-resource-group.png)
3. 새 리소스 그룹에 대한 이름 및 위치를 제공합니다. **만들기**를 선택합니다.
   
    ![리소스 그룹 만들기](./media/resource-group-portal/create-empty-group.png)
4. 최근에 만든 리소스 그룹을 보려면 **새로 고침** 을 선택해야 할 수 있습니다.
   
    ![리소스 그룹 새로 고침](./media/resource-group-portal/refresh-resource-groups.png)
5. 리소스 그룹에 대해 표시된 정보를 사용자 지정하려면 **열**을 선택합니다.
   
    ![열 사용자 지정](./media/resource-group-portal/select-columns.png)
6. 추가할 열을 선택한 후 **업데이트**를 선택합니다.
   
    ![열 추가](./media/resource-group-portal/add-columns.png)
7. 리소스를 새 리소스 그룹에 배포하는 방법을 알아보려면 [Resource Manager 템플릿 및 Azure 포털을 사용하여 리소스 배포](resource-group-template-deploy-portal.md)를 참조하세요.
8. 리소스 그룹에 대한 빠른 액세스의 경우 대시보드에 리소스 그룹을 고정할 수 있습니다.
   
    ![리소스 그룹 고정](./media/resource-group-portal/pin-group.png)
9. 대시보드에 리소스 그룹과 해당 리소스가 표시됩니다. 리소스 그룹 또는 해당 리소스를 선택하여 항목을 탐색할 수 있습니다.
   
    ![리소스 그룹 고정](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>리소스 태그 지정
리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 태그 사용에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>리소스 모니터링
리소스를 선택하면 리소스 종류를 모니터링하기 위한 기본 그래프 및 표가 포털에 표시됩니다.

1. 리소스를 선택하고 **모니터링** 섹션을 살펴봅니다. 이 섹션에는 리소스 유형과 관련된 그래프가 포함되어 있습니다. 다음은 저장소 계정의 기본 모니터링 데이터를 보여주는 이미지입니다.
   
    ![모니터링 표시](./media/resource-group-portal/show-monitoring.png)
2. 섹션 위에 줄임표(...)를 선택하여 대시보드에 섹션을 고정할 수 있습니다. 섹션 크기를 사용자 지정하거나 완전히 제거할 수도 있습니다. 다음 이미지는 CPU 및 메모리 섹션을 고정, 사용자 지정 또는 제거하는 방법을 보여 줍니다.
   
    ![선택 고정](./media/resource-group-portal/pin-cpu-section.png)
3. 대시보드에 섹션을 고정하면 대시보드에 요약이 표시됩니다. 그리고 즉시 선택하면 데이터에 대한 세부 정보로 이동합니다.
   
    ![대시보드 보기](./media/resource-group-portal/view-startboard.png)
4. 포털을 통해 모니터링하는 데이터를 완전히 사용자 지정하려면 기본 대시보드로 이동한 후 **새 대시보드**를 선택합니다.
   
    ![dashboard](./media/resource-group-portal/dashboard.png)
5. 새 대시보드의 이름을 지정하고 타일을 대시보드로 끌어 놓습니다. 타일은 다양한 옵션을 통해 필터링됩니다.
   
    ![dashboard](./media/resource-group-portal/create-dashboard.png)
   
     대시보드를 사용하는 방법을 알아보려면 [Azure 포털에서 대시보드 만들기 및 공유](../azure-portal/azure-portal-dashboards.md)를 참조하세요.

## <a name="manage-resources"></a>리소스 관리
포털에서 리소스를 볼 때 특정 리소스 관리 옵션이 표시됩니다.

![리소스 관리](./media/resource-group-portal/manage-resources.png)

이러한 옵션에서 가상 머신을 시작 및 중지하거나 가상 머신의 속성을 다시 구성하는 등의 작업을 수행할 수 있습니다.

## <a name="move-resources"></a>리소스 이동
다른 리소스 그룹 또는 다른 구독으로 리소스를 이동해야 하는 경우에는 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금
구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 할 수 있게 합니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>구독 및 비용 보기
모든 리소스에 대한 롤업 비용 및 구독에 대한 정보를 볼 수 있습니다. **구독** 을 선택한 다음 보고 싶은 구독을 선택합니다. 선택할 구독이 하나만 있을 수 있습니다.

![subscription](./media/resource-group-portal/select-subscription.png)

진행 속도가 표시됩니다.

![진행 속도](./media/resource-group-portal/burn-rate.png)

그리고 리소스 유형별 비용 분석이 표시됩니다.

![리소스 비용](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>템플릿 내보내기
리소스 그룹을 설정한 후에는 리소스 그룹에 대한 Resource Manager 템플릿을 살펴볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

1. 템플릿에 모든 인프라가 포함되어 있기 때문에 향후 솔루션 배포를 간단하게 자동화할 수 있습니다.
2. 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해질 수 있습니다.

단계별 지침은 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.

## <a name="delete-resource-group-or-resources"></a>리소스 그룹 또는 리소스 삭제
리소스 그룹을 삭제하면 그 안에 포함된 모든 리소스가 삭제됩니다. 리소스 그룹 내부의 개별 리소스를 삭제할 수도 있습니다. 리소스 그룹을 삭제할 때에 주의합니다. 다른 리소스 그룹의 리소스가 종속된 리소스가 해당 리소스 그룹에 있을 수 있습니다.

![그룹 삭제](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>다음 단계
* 활동 로그를 보려면 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
* 배포에 대한 세부 정보를 보려면 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.
* 포털을 통해 리소스를 배포하려면 [Resource Manager 템플릿 및 Azure 포털을 사용하여 리소스 배포](resource-group-template-deploy-portal.md)를 참조하세요.
* 리소스에 대한 액세스를 관리하려면 [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.

