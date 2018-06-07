---
title: Azure 스택 정책 모듈을 사용 하 여 | Microsoft Docs
description: Azure 스택 구독 처럼 동작 하는 Azure 구독을 제한 하는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Azure 스택 정책 모듈을 사용 하 여 Azure 정책 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 정책 모듈을 사용 하면 동일한 버전 관리 및 Azure 스택과 서비스 가용성을 사용 하 여 Azure 구독을 구성할 수 있습니다.  이 모듈에서는 사용는 **새로 AzureRMPolicyAssignment** cmdlet을 사용 리소스 종류 및 구독에서 사용할 수 있는 서비스를 제한 하는 Azure 정책을 만듭니다.  정책의 구성한 후 Azure 스택에 대 한 대상으로 하는 앱을 개발 하려면 Azure 구독을 사용할 수 있습니다.

## <a name="install-the-module"></a>모듈 설치

1. 1 단계에 설명 된 대로 필요한 AzureRM PowerShell 모듈 버전을 설치 [Azure 스택 위한 PowerShell 설치](azure-stack-powershell-install.md)합니다.
2. [GitHub에서 Azure 스택 도구를 다운로드 합니다.](azure-stack-powershell-download.md)
3. [Azure Stack과 함께 사용하도록 PowerShell 구성](azure-stack-powershell-configure-user.md)

4. AzureStack.Policy.psm1 모듈을 가져옵니다.

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Azure 구독에 정책을 적용합니다

Azure 구독에 대해 기본 Azure 스택 정책을 적용 하려면 다음 명령을 사용할 수 있습니다. 이 명령을 실행 하기 전에 바꿉니다 *Azure 구독 이름을* Azure 구독.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>리소스 그룹에 정책을 적용합니다

보다 세부적인 정책을 적용할 수 있습니다. 예를 들어, 동일한 구독에서 실행 중인 다른 리소스가 있을 수 있습니다. Azure 리소스를 사용 하 여 Azure 스택 앱을 테스트할 수 있는 특정 리소스 그룹에 정책 적용을 범위를 지정할 수 있습니다. 다음 명령을 실행 하기 전에 바꿉니다 *Azure 구독 이름을* 을 Azure 구독 이름입니다.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>실행 정책

금지 하는 리소스를 배포 하려고 할 때 오류가 발생 하는 Azure 정책을 배포 하 고 나면 정책에 의해 합니다.

![정책 제약 조건으로 인해 리소스 배포 실패의 결과](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)
* [Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)
* [Visual Studio 사용 하 여 템플릿을 배포 합니다.](azure-stack-deploy-template-visual-studio.md)
