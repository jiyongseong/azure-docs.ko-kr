---
title: Azure Portal을 사용하여 MSI에 Azure 리소스 액세스 권한을 할당하는 방법
description: Azure Portal을 사용하여 특정 리소스에 MSI를 할당하고 다른 리소스에 대한 액세스 권한을 할당하기 위한 단계별 지침
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 06f316a7c96ff266e9f4593fa3a9ac871b2979aa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Azure Portal을 사용하여 리소스에 관리 서비스 ID 액세스 권한 할당

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

MSI(관리되는 서비스 ID)를 사용하여 Azure 리소스를 구성한 후에 모든 보안 주체와 마찬가지로 MSI에 다른 리소스 액세스 권한을 제공할 수 있습니다. 이 문서에서는 Azure Portal을 사용하여 Azure 가상 머신 또는 가상 머신 확장 집합의 MSI 액세스 권한을 Azure 저장소 계정에 부여하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC를 사용하여 MSI에 다른 리소스 액세스 권한 할당

[Azure VM](qs-configure-portal-windows-vm.md) 또는 [Azure VMSS](qs-configure-portal-windows-vmss.md)와 같은 Azure 리소스에서 MSI를 사용하도록 설정되면 다음을 수행합니다.

1. MSI를 구성한 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 액세스 제어를 수정하려는 리소스로 이동합니다. 이 예에서는 Azure 가상 머신 및 Azure 가상 머신 확장 집합 액세스 권한을 저장소 계정에 부여하므로 저장소 계정으로 이동합니다.

3. Azure 가상 머신의 경우 리소스의 **액세스 제어(IAM)** 페이지를 선택하고 **+ 추가**를 선택합니다. 그런 다음 **역할**을 지정하고, **Virtual Machine 액세스 권한을 할당**하고, 리소스가 있는 해당 **구독** 및 **리소스 그룹**을 지정합니다. 검색 기준 영역 아래에 리소스가 표시되어야 합니다. 리소스를 선택하고 **저장**을 선택합니다. 

   ![액세스 제어(IAM) 스크린샷](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Azure 가상 머신 확장 집합의 경우 리소스의 **액세스 제어(IAM)** 페이지를 선택하고 **+ 추가**를 선택합니다. 그런 다음, **역할**, **다음에 대한 액세스 할당**을 지정합니다. 검색 조건 영역 아래에서 가상 머신 확장 집합을 검색합니다. 리소스를 선택하고 **저장**을 선택합니다.
   
   ![액세스 제어(IAM) 스크린샷](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. 주 **액세스 제어(IAM)** 페이지로 돌아오면 리소스 MSI에 해당하는 새 항목이 표시됩니다.

    Azure 가상 머신:

   ![액세스 제어(IAM) 스크린샷](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Azure 가상 머신 확장 집합:

    ![액세스 제어(IAM) 스크린샷](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>문제 해결

리소스의 MSI가 사용 가능한 ID 목록에 표시되지 않으면 MSI가 올바르게 사용하도록 설정되었는지 확인하세요. 여기서는 Azure 가상 머신으로 돌아가서 다음을 확인할 수 있습니다.

- **구성** 페이지에서 **MSI가 활성화됨** 값이 **예**로 설정되었는지 확인합니다.
- **확장** 페이지를 살펴보고 MSI 확장이 성공적으로 배포되었는지 확인합니다(**확장** 페이지는 Azure 가상 머신 확장 집합에서 사용할 수 없음).

이 두 항목 중 하나가 올바르지 않으면 리소스에서 MSI를 재배포하거나 배포 오류 관련 문제를 해결해야 할 수 있습니다.

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.
- Azure 가상 머신에서 MSI를 사용하도록 설정하려면 [Azure Portal을 사용하여 Azure VM MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vm.md)을 참조하세요.
- Azure 가상 머신 확장 집합에서 MSI를 사용하도록 설정하려면 [Azure Portal을 사용하여 Azure 가상 머신 확장 집합 MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vmss.md)을 참조하세요.


