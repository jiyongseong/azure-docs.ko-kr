---
title: 로그인에 Azure VM 관리 서비스 ID를 사용하는 방법
description: 스크립트 클라이언트 로그인 및 리소스 액세스에 Azure VM MSI 서비스 주체를 사용하는 단계별 지침 및 예제입니다.
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
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: c48e4bdf9a8c4b8515028fe45cdf724f5ff9f666
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>로그인에 Azure VM MSI(관리 서비스 ID)를 사용하는 방법 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
이 문서에서는 MSI 서비스 주체를 사용한 로그인에 대한 PowerShell 및 CLI 스크립트 예제 및 오류 처리와 같은 중요한 항목에 대한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

이 문서에서 Azure PowerShell 및 Azure CLI 예제를 사용하려는 경우 최신 버전의 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) 또는 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)을 설치해야 합니다. 

> [!IMPORTANT]
> - 이 문서의 모든 샘플 스크립트에서는 MSI 사용 Virtual Machine에서 명령줄 클라이언트를 실행한다고 가정합니다. Azure Portal에서 VM "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 MSI 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 
> - 리소스 액세스 중에 오류를 방지하려면 적절한 범위(VM 이상)에서 "읽기 권한자" 액세스 이상의 VM MSI를 제공하여 VM에 Azure Resource Manager 작업을 허용해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 MSI(관리 서비스 ID)에 리소스 액세스 권한 할당](howto-assign-access-portal.md)을 참조하세요.

## <a name="overview"></a>개요

MSI는 VM에서 [MSI를 활성화하여 만든](overview.md#how-does-it-work) [서비스 주체 개체](../develop/active-directory-dev-glossary.md#service-principal-object)를 제공합니다. 서비스 주체는 Azure 리소스에 액세스 권한을 부여하고 로그인 및 리소스 액세스를 위한 스크립트/명령줄에 의한 ID로 사용될 수 있습니다. 일반적으로 고유한 ID 하에서 보호된 리소스에 액세스하기 위해 스크립트 클라이언트는 다음을 수행해야 합니다.  

   - Azure AD에서 기밀/웹 클라이언트 응용 프로그램으로 등록하고 동의합니다.
   - 앱의 자격 증명(스크립트에 포함됨)을 사용하여 해당 서비스 주체 하에 로그인합니다.

MSI를 사용하는 경우 MSI 서비스 주체 하에서 로그인할 수 있으므로 스크립트 클라이언트는 더 이상 수행하지 않아도 됩니다. 

## <a name="azure-cli"></a>Azure CLI

다음 스크립트에서는 다음과 같은 방법을 설명합니다.

1. VM의 MSI 서비스 주체 하의 Azure AD에 로그인합니다.  
2. Azure Resource Manager를 호출하고 VM의 서비스 주체 ID를 가져옵니다. CLI에서는 자동으로 토큰 획득/사용을 관리합니다. `<VM-NAME>`의 가상 머신 이름을 대체해야 합니다.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

다음 스크립트에서는 다음과 같은 방법을 설명합니다.

1. VM의 MSI 서비스 주체 하의 Azure AD에 로그인합니다.  
2. Azure Resource Manager cmdlet을 호출하여 VM에 대한 정보를 가져옵니다. PowerShell에서는 자동으로 토큰 획득/사용을 관리합니다.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure 서비스의 리소스 ID

Azure AD를 지원하고 MSI 및 해당하는 리소스 ID를 사용하여 테스트된 리소스의 목록은 [Azure AD 인증을 지원하는 Azure 서비스](services-support-msi.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

## <a name="error-handling-guidance"></a>오류 처리 지침 

다음과 같은 응답은 VM MSI가 올바르게 구성되지 않았음을 나타낼 수 있습니다.

- PowerShell: *Invoke-WebRequest: 원격 서버에 연결할 수 없습니다.*
- CLI: *MSI: 'HTTPConnectionPool(host='localhost', port=50342) 오류를 표시하여 'http://localhost:50342/oauth2/token'에서 토큰을 검색하지 못했습니다.* 

이러한 오류 중 하나를 수신하면 [Azure Portal](https://portal.azure.com)에서 Azure VM에 반환됩니다.

- **구성** 페이지로 이동하여 "관리 서비스 ID"를 "예"로 설정했는지 확인합니다.
- **확장** 페이지로 이동하여 MSI 확장이 올바르게 배포되었는지 확인합니다.

이 두 항목 중 하나가 올바르지 않으면 리소스에서 MSI를 재배포하거나 배포 오류 관련 문제를 해결해야 할 수 있습니다. VM을 구성하는 데 도움이 필요한 경우 [Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vm.md)을 참조하세요.

## <a name="related-content"></a>관련 콘텐츠

- Azure VM에서 MSI를 사용하도록 설정하려면 [PowerShell을 사용하여 VM MSI(관리 서비스 ID) 구성](qs-configure-powershell-windows-vm.md) 또는 [Azure CLI를 사용하여 VM MSI(관리 서비스 ID) 구성](qs-configure-cli-windows-vm.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.








