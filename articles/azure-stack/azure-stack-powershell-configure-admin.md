---
title: Azure 스택 PowerShell 환경을 구성 | Microsoft Docs
description: Azure 스택 PowerShell 환경을 구성 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 86608ef8b3623682bd10498605f8b7b62c377ff1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Azure 스택 PowerShell 환경 구성

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

PowerShell을 사용 하 여 제안, 계획, 할당량 및 경고 만들기와 같은 리소스를 관리 하기 위해 Azure 스택의 구성할 수 있습니다. 이 항목에는 연산자 환경을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소에서 실행 하거나는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  
 - 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다.  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>운영자 환경을 구성 하 고 Azure 스택에 로그인

PowerShell과 함께 Azure 스택 운영자 환경을 구성 합니다. 다음 스크립트 중 하나를 실행 AD 또는 AD FS Azure 배포의 유형에 따라: Azure AD tenantName, GraphAudience 끝점 및 ArmEndpoint 값 자체 환경 구성을 대체 합니다.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD) 기반 배포

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) 기반의 배포

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>연결 테스트

이제를 모두 가져온 설정, 보겠습니다 PowerShell을 사용 하 여 Azure 스택 내에서 리소스를 만듭니다. 예를 들어 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 컴퓨터를 추가할 수 있습니다. 다음 명령을 사용 하 여 "MyResourceGroup" 라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>다음 단계
 - [Azure Stack용 템플릿 개발](user/azure-stack-develop-templates.md)
 - [PowerShell을 사용하여 템플릿 배포](user/azure-stack-deploy-template-powershell.md)
