---
title: Azure 등록 Azure 스택에 대 한 유효성 검사 | Microsoft Docs
description: Azure 스택 준비 검사를 사용 하 여 Azure 등록 유효성을 검사 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-registration"></a>Azure 등록 유효성 검사 
Azure 스택 준비 검사 도구 (AzsReadinessChecker)를 사용 하 여 Azure 구독을 Azure 스택과 함께 사용할 수 있는지 확인 하 합니다. Azure 스택 배포 시작 하기 전에 등록 유효성을 검사 합니다. 준비 상태 검사기의 유효성을 검사 합니다.
- 사용 하면 Azure 구독에 지원 되는 형식입니다. 클라우드 서비스 공급자 (CSP) 나 EA (기업 계약)를 구독 해야 합니다. 
- Azure 구독 등록을 사용 하는 계정은 Azure에 로그인 할 수와 구독 소유자입니다. 

Azure 스택 등록에 대 한 자세한 내용은 참조 [Azure 사용한 Azure 스택 등록](azure-stack-registration.md)합니다. 

## <a name="get-the-readiness-checker-tool"></a>준비 검사 도구 가져오기
Azure 스택 준비 검사기 도구 (AzsReadinessChecker)의 최신 버전에서 사용할 수 있는 다운로드 [PSGallery](https://aka.ms/AzsReadinessChecker)합니다.  

## <a name="prerequisites"></a>필수 조건
다음과 같은 전제 조건을 장소에 있어야 합니다.

**도구가 실행 되는 컴퓨터:**
 - Windows 10 또는 Windows Server 2016 인터넷에 연결 합니다.
 - PowerShell 5.1 이상입니다. 사용 중인 버전을 확인 하려면 다음 PowerShell cmd를 실행 한 다음 검토는 *주요* 버전 및 *부* 버전:  

    >`$PSVersionTable.PSVersion` 
 - 구성 [Azure 스택에 대 한 PowerShell](azure-stack-powershell-install.md)합니다. 
 - 최신 버전의 다운로드 [Microsoft Azure 스택 준비 검사](https://aka.ms/AzsReadinessChecker) 도구입니다.  

**Azure Active Directory 환경:**
 - 사용자 이름 및 Azure 스택에 사용할 Azure 구독에 대 한 소유자가 있는 계정의 암호를 식별 합니다.  
 - 사용 하 여 Azure 구독에 대 한 구독 ID를 식별 합니다. 
 - 사용 하 여 AzureEnvironment 식별: *azure 클라우드*, *AzureGermanCloud*, 또는 *AzureChinaCloud*합니다.

## <a name="validate-azure-registration"></a>Azure 등록 유효성 검사
1. 필수 구성 요소를 충족 하는 컴퓨터에서 관리 하는 PowerShell 프롬프트를 열고 하 고는 AzsReadinessChecker를 설치 하려면 다음 명령을 실행 하십시오.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. PowerShell 프롬프트에서 설정 하려면 다음을 실행 *$registrationCredential* 구독 소유자가 있는 계정으로 합니다.   대체 *subscriptionowner@contoso.onmicrosoft.com* 계정 및 테 넌 트를 사용 합니다. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. PowerShell 프롬프트에서 설정 하려면 다음을 실행 *$subscriptionID* 사용 하 여 Azure 구독으로 합니다. 대체 *xxxxxxxx-xxxx-xxxx-개의 자릿수* 자체는 구독 ID로  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. PowerShell 프롬프트에서 다음 구독 유효성 검사를 시작할 실행 
   - AzureEnvironment로에 대 한 값을 지정 *azure 클라우드*, *AzureGermanCloud*, 또는 *AzureChinaCloud*합니다.  
   - Azure Active Directory 관리자와 Azure Active Directory 테 넌 트 이름을 제공 합니다. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. 이 도구를 실행 한 후의 출력을 검토 합니다. 상태는 로그온과 등록 요구 사항이 모두에 대 한 확인을 확인 합니다. 다음 이미지와 같이 유효성 검사에 성공 표시 됩니다.  
![유효성 검사 실행](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>보고서와 로그 파일
유효성을 검사할 때마다 실행 되며, 결과 기록 **AzsReadinessChecker.log** 및 **AzsReadinessCheckerReport.json**합니다. 이러한 파일의 위치는 PowerShell에서 유효성 검사 결과를 표시합니다. 

이러한 파일의 활용 하거나 Azure 스택 배포 유효성 검사 문제를 조사 하기 전에 유효성 검사 상태를 공유 합니다. 두 파일의 각 후속 유효성 검사 결과 유지합니다. 보고서 id 구성의 사용자 배포 팀의 확인을 제공합니다. 로그 파일 배포 또는 지원 팀 유효성 검사 문제를 조사할 수 있습니다. 

기본적으로 두 파일에 기록 됩니다 *C:\Users\<사용자 이름 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*합니다.  
 - 사용 하 여는 **-OutputPath** ***&lt;경로&gt;*** 다른 보고서 위치를 지정 하려면 실행된 명령줄의 끝에 매개 변수입니다.   
 - 사용 하 여 **-CleanReport** 끝에서 정보를 지우려면 run 명령에 매개 변수 *AzsReadinessCheckerReport.json*합니다.  이전에 대 한 도구를 실행합니다. 자세한 내용은 [Azure 스택 유효성 검사 보고서](azure-stack-validation-report.md)합니다.

## <a name="validation-failures"></a>유효성 검사 실패
유효성 검사에 실패 하는 경우 PowerShell 창에서 오류에 대 한 세부 정보에 표시 합니다. 또한이 도구는 AzsReadinessChecker.log에 정보를 기록합니다.

다음 예에서는 일반적인 유효성 검사에 실패 한 지침을 제공 합니다.

### <a name="user-must-be-an-owner-of-the-subscription"></a>사용자 구독 소유자 여야 합니다.   
![구독 소유자](./media/azure-stack-validate-registration/subscription-owner.png)
**원인** -계정이 Azure 구독 관리자가 아닙니다.   

**해결 방법** -Azure 스택 배포에서 사용에 대 한 요금이 청구 된 Azure 구독 관리자 계정을 사용 합니다.


### <a name="expired-or-temporary-password"></a>만료 된 또는 임시 암호 
![만료 된 암호](./media/azure-stack-validate-registration/expired-password.png)
**원인** -계정 로그온 없습니다 암호가 만료 되었거나은 일시적인 현상입니다.     

**해상도** -PowerShell에서를 실행 및 지시에 따라 암호를 다시 설정 합니다. 
  > `Login-AzureRMAccount` 

에 로그인 또는 https://portal.azure.com 대로 계정과 사용자 암호를 변경 해야 합니다.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>등록에 대 한 Microsoft 계정은 지원 되지 않습니다.  
![지원 되지 않는 계정](./media/azure-stack-validate-registration/unsupported-account.png)
**원인** -Microsoft 계정 (예: Outlook.com 또는 Hotmail.com) 지정 되었습니다.  이러한 계정은 지원 되지 않습니다.

**해결 방법** -계정 및 구독에서 클라우드 서비스 공급자 (CSP) 나 EA (기업 계약)를 사용 합니다. 


### <a name="unknown-user-type"></a>알 수 없는 사용자 유형  
![알 수 없는 사용자](./media/azure-stack-validate-registration/unknown-user.png)
**원인** -계정이 지정 된 Azure Active Directory 환경에 로그온 할 수 없습니다. 이 예제에서는 *AzureChinaCloud* 로 지정 된 *AzureEnvironment*합니다.  

**해결 방법** -계정이 지정 된 Azure 환경에 대 한 유효한 지 확인 합니다. PowerShell에서 특정 환경에 대해 유효한 계정을 확인 하려면 다음을 실행 합니다.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>다음 단계
[Azure id 유효성 검사](azure-stack-validate-identity.md)
[준비 보고서를 보려면](azure-stack-validation-report.md)
[일반 Azure 스택 통합 고려 사항](azure-stack-datacenter-integration.md)

