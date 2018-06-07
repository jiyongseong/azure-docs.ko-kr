---
title: Azure AD 사용자 계정 만들기
description: 이 문서에서는 Azure Automation에서 Runbook에 대해 Azure AD 사용자 계정 자격 증명을 만들어서 Azure에서 인증하는 방법을 설명합니다.
keywords: azure active directory 사용자, azure 서비스 관리, azure ad 사용자 계정
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 105dcf5564ec5c1d0b3528e9b5667d89d98c6cab
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Azure 클래식 배포 및 Resource Manager를 사용하여 Runbook 인증
이 문서에서는 Azure 클래식 배포 모델 또는 Azure Resource Manager 리소스에 대해 실행되는 Azure Automation Runbook에 대한 Azure AD 사용자 계정을 구성하기 위해 수행해야 하는 단계를 설명합니다. Azure Resource Manager 기반 Runbook에 대해 이 인증 ID가 계속 지원되겠지만 Azure 실행 계정을 사용할 것을 권장합니다.       

## <a name="create-a-new-azure-active-directory-user"></a>새 Azure Active Directory 사용자 만들기
1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure Portal에 로그인합니다.
2. **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자** > **새 사용자**를 선택합니다.
3. **이름** 및 **사용자 이름**과 같은 사용자에 대한 세부 정보를 입력합니다.  
4. 사용자의 전체 이름 및 임시 암호를 기록해 둡니다.
5. **디렉터리 역할**을 선택합니다.
6. 전역 또는 제한된 관리자 역할을 할당합니다.
7. Azure에서 로그 아웃한 다음, 방금 만든 계정으로 다시 로그인합니다. 사용자의 암호를 변경하라는 메시지가 표시됩니다.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Azure Portal에서 Automation 계정 만들기
이 섹션에서는 다음 단계를 수행하여 Azure Portal에서 Azure Resource Manager 모드에서 리소스를 관리하는 데 Runbook을 사용하기 위한 Azure Automation 계정을 만듭니다.  

1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure Portal에 로그인합니다.
2. **Automation 계정**을 선택합니다.
3. **추가**를 선택합니다.<br><br>![Automation 계정 추가](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. **Automation 계정 추가** 블레이드의 **이름** 상자에 새 Automation 계정에 대한 이름을 입력합니다.
5. 구독이 둘 이상인 경우 새 계정의 구독을 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.
6. **Azure 실행 계정 만들기** 옵션에 **예** 값을 선택하고 **만들기** 단추를 클릭합니다.  
   
    > [!NOTE]
    > **아니요** 옵션을 선택하여 실행 계정을 만들지 않도록 선택하면 **Automation 계정 추가** 블레이드에 경고 메시지가 나타납니다. 계정이 생성되고 구독의 **참가자** 역할에 할당되는 동안은 구독 디렉터리 서비스 내에 해당 인증 ID가 없으므로 구독에 액세스하는 리소스도 없습니다. 이렇게 하면 이 계정을 참조하는 Runbook이 Azure Resource Manager 리소스에 대한 작업을 인증하고 수행할 수 없게 됩니다.
    > 
    >

    <br>![Automation 계정 경고 추가](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Azure에서 Automation 계정을 만드는 동안 메뉴의 **알림**에서 진행률을 추적할 수 있습니다.

자격 증명이 생성되면 자격 증명 자산을 만들어서 Automation 계정을 앞에서 만든 AD 사용자 계정과 연결해야 합니다. Automation 계정을 만들기만 했을 뿐, 아직 인증 ID와 연결하지는 않았다는 점을 잊지 마세요. [Azure Automation의 자격 증명 자산](automation-credentials.md#creating-a-new-credential-asset) 문서에 설명된 단계를 수행하고 **사용자 이름** 값을 **도메인\사용자** 형식으로 입력합니다.

## <a name="use-the-credential-in-a-runbook"></a>Runbook에서 자격 증명 사용
[Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) 작업을 사용하여 Runbook에서 자격 증명을 검색한 후 [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx)와 함께 사용하여 Azure 구독에 연결할 수 있습니다. 자격 증명이 여러 Azure 구독의 관리자인 경우 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx)을 사용하여 올바른 관리자를 지정해야 합니다. 이것은 일반적으로 대부분의 Azure Automation Runbook 맨 위에 나타나는 다음 PowerShell 샘플에 표시됩니다.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Runbook의 모든 [검사점](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) 뒤에 이 줄을 반복합니다. Runbook이 일시 중단된 후 다른 작업자에서 다시 시작되는 경우에는 인증을 다시 수행해야 합니다.

## <a name="next-steps"></a>다음 단계
* 다음 [Azure Automation Runbook 형식](automation-runbook-types.md) 문서에서 다양한 Runbook 유형 및 고유한 Runbook을 만드는 단계를 검토합니다.

