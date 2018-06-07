---
title: Azure는 ASDK 등록 | Microsoft Docs
description: Azure 마켓플레이스 배포 및 사용 보고를 사용 하도록 설정 하려면 Azure 스택을 등록 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bc17045106b2656cdeb5f51037b3138aeac9ee0f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757470"
---
# <a name="azure-stack-registration"></a>Azure 스택 등록
Azure에서 Azure 마켓플레이스 항목을 다운로드 하 고 상용 데이터를 Microsoft에 다시 보고를 설정 하 여 Azure 스택 개발 키트 (ASDK) 설치를 등록할 수 있습니다. 등록은 마켓플레이스 배포를 포함 하 여 전체 Azure 스택 기능을 지 원하는 데 필요 합니다. 마켓플레이스 배포 및 사용 보고와 같은 중요 한 Azure 스택 기능을 테스트 하면 있기 때문에 등록을 사용 하는 것이 좋습니다. Azure 스택 등록 한 후 사용 Azure 상거래에 보고 됩니다. 등록에 사용 되는 구독에서 볼 수 있습니다. 그러나 ASDK 사용자가 보고 사용에 대 한 요금이 청구 되지 않습니다.

표시 될 수 있습니다 프로그램 ASDK 등록 하지 않은 경우는 **활성화 필요** Azure 스택 개발 키트를 등록 해야 한다는 경고 성 알림입니다. 이 동작은 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
Azure는 ASDK를 등록 하려면 이러한 지침을 사용 하기 전에 Azure 스택 PowerShell을 설치 하 고에 설명 된 대로 Azure 스택 도구를 다운로드 했는지 확인는 [배포 후 구성](asdk-post-deploy.md) 문서.

PowerShell 언어 모드 설정 해야 또한 **FullLanguageMode** 는 ASDK Azure에 등록 하는 데 사용 하는 컴퓨터에 있습니다. 전체 관리자 PowerShell 창을 열고 다음 PowerShell 명령을 실행 하는 현재 언어 모드가 설정 되어 있는지 확인 합니다.

```powershell
$ExecutionContext.SessionState.LanguageMode
```

출력은 반환 확인 **FullLanguageMode**합니다. 다른 언어 모드가 반환, 다른 컴퓨터에서 실행 되도록 등록 해야 합니다 되거나 언어 모드로 설정 해야 합니다 **FullLanguageMode** 계속 하기 전에.

## <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록
Azure는 ASDK를 등록 하려면 다음이 단계를 수행 합니다.

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 실행 되어야 합니다. ASDK에 대 한 개발 키트 호스트 컴퓨터입니다.

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.  

2. Azure와 ASDK 설치를 등록 하려면 다음 PowerShell 명령을 실행 합니다. Azure 구독 및 로컬 ASDK 설치 모두에 로그인 해야 합니다. 아직 Azure 구독이 없는 할 수 있습니다 [여기 무료 Azure 계정을 만들](https://azure.microsoft.com/free/?b=17.06)합니다. Azure 스택 등록 무료로 Azure 구독에서 발생 합니다.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. 스크립트가 완료 되는 경우이 메시지가 표시 됩니다: **해당 환경에서 이제 등록 되 고 제공된 된 매개 변수를 사용 하 여 활성화 합니다.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>등록에 성공 했는지 확인
Azure와 ASDK 등록에 성공 했음을 확인 하려면 다음이 단계를 수행 합니다.

1. 에 로그인 하 고 [스택 Azure 관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 클릭 **마켓플레이스 관리** > **Azure에서 추가**합니다.

    ![](media/asdk-register/2.PNG)

3. Azure에서 사용할 수 있는 항목 목록이 표시 되 면 인증 과정에 성공 했습니다.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>다음 단계
[Azure 스택 마켓플레이스 항목 추가](asdk-marketplace-item.md)
