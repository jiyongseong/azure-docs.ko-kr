---
title: Azure 스택 개발 키트 (ASDK)를 다시 배포 | Microsoft Docs
description: 이 자습서는 ASDK 다시 설치 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fcf1abfe574dd3067f00df7c5ff2632b9cc2ec4f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-redeploy-the-asdk"></a>자습서:는 ASDK 다시 배포
이 자습서는 Azure 스택 개발 키트 (ASDK) 비-프로덕션 환경에서 다시 배포 하는 방법에 설명 합니다. 업그레이드는 ASDK 지원 되지 않는, 완전히 새 버전으로 이동 하 고 다시 배포 해야 합니다. 또한 언제 든 지 처음부터 다시 시작 하려는 ASDK를 다시 배포할 수 있습니다.

> [!IMPORTANT]
> 새 버전으로는 ASDK 업그레이드 지원 되지 않습니다. ASDK 개발 키트 호스트 컴퓨터에 최신 버전의 Azure 스택 평가할 때마다 다시 배포 해야 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 등록을 제거 
> * ASDK 다시 배포

## <a name="remove-azure-registration"></a>Azure 등록을 제거 
Azure와 함께 이전에 ASDK 설치를 등록 하는 경우 등록 리소스는 ASDK 다시 배포 하기 전에 제거 해야 합니다. ASDK는 ASDK 다시 배포할 때 마켓플레이스 배포를 사용 하도록 설정 하려면 다시 등록 합니다. Azure 구독과 ASDK를 이전에 등록 하지 않은 경우에이 섹션을 건너뛸 수 있습니다.

사용 하 여 등록 리소스를 제거 하려면는 **제거 AzsRegistration** Azure 스택 등록을 취소 하는 cmdlet입니다. 그런 다음 사용 하 여 **제거 AzureRMRsourceGroup** Azure 구독에서 Azure 스택 리소스 그룹을 삭제 하는 cmdlet:

1. 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 관리자 권한으로 PowerShell 콘솔을 엽니다. ASDK에 대 한 개발 키트 호스트 컴퓨터입니다.

2. ASDK 설치의 등록을 취소 하 고 삭제 하려면 다음 PowerShell 명령을 실행 하는 **azurestack** Azure 구독에서 리소스 그룹:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Add-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -CloudAdminCredential $YourCloudAdminCredential `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. 스크립트를 실행 하면 로컬 ASDK 설치와 Azure 구독에 로그인 하 라는 메시지가 표시 됩니다.
4. 스크립트가 완료 되 면 다음 예제와 유사한 메시지가 표시 되어야 합니다.

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Azure 스택 이제 성공적으로 취소 해야 Azure 구독에서 등록 합니다. 또한 Azure를 통해는 ASDK를 등록할 때 만들어진 azurestack 리소스 그룹 에서도 삭제 하도록 합니다.

## <a name="redeploy-the-asdk"></a>ASDK 다시 배포
Azure 스택을 다시 배포 하려면 다시 시작 해야 처음부터 아래에서 설명 합니다. 단계는 ASDK를 설치 하려면 Azure 스택 설치 관리자 (asdk installer.ps1) 스크립트를 사용 여부에 따라 다릅니다.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>설치 관리자 스크립트를 사용 하 여 ASDK 다시 배포
1. ASDK 컴퓨터에서 관리자 권한 PowerShell 콘솔 열고 asdk installer.ps1 스크립트에 이동 된 **AzureStack_Installer** 비시스템 드라이브에 있는 디렉터리입니다. 스크립트를 실행 하 고 클릭 **재부팅**합니다.

   ![Asdk installer.ps1 스크립트 실행](media/asdk-redeploy/1.png)

2. 기본 운영 체제를 선택 (하지 **Azure 스택**)를 클릭 하 고 **다음**합니다.

   ![호스트 운영 체제를 다시 시작](media/asdk-redeploy/2.png)

3. 로컬 관리자로 개발 키트 호스트를 기본 운영 체제로 다시 부팅 한 후에 로그인 합니다. 찾아 삭제는 **C:\CloudBuilder.vhdx** 이전 배포의 일환으로 사용 된 파일이 있습니다. 

4. 처음 수행한 동일한 단계를 반복 [배포는 ASDK](asdk-deploy.md)합니다.

### <a name="redeploy-the-asdk-without-using-the-installer"></a>설치 관리자를 사용 하지 않고는 ASDK를 재배포 합니다.
Asdk installer.ps1 스크립트는 ASDK 설치를 사용 하지 않은 경우에는 ASDK 재배포 하려면 먼저 수동으로 개발 키트 호스트 컴퓨터를 재구성 해야 합니다.

1. 시스템 구성 유틸리티를 실행 하 여 시작 **msconfig.exe** ASDK 컴퓨터에 있습니다. 에 **부팅** 탭 호스트 컴퓨터 운영 체제 (Azure 스택 되지 않음)를 선택 하 고 클릭 **기본값으로 설정**, 클릭 하 고 **확인**합니다. 클릭 **다시 시작** 대화 상자가 나타나면 합니다.

      ![부팅 구성 설정](media/asdk-redeploy/4.png)

2. 개발 키트 호스트 컴퓨터의 로컬 관리자로 개발 키트 호스트를 기본 운영 체제로 다시 부팅 한 후에 로그인 합니다. 찾아 삭제는 **C:\CloudBuilder.vhdx** 이전 배포의 일환으로 사용 된 파일이 있습니다. 

3. 처음 수행한 동일한 단계를 반복 [PowerShell을 사용 하 여 ASDK 배포](asdk-deploy-powershell.md)합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure 등록을 제거 
> * ASDK 다시 배포

Azure 스택 마켓플레이스 항목을 추가 하는 방법에 알아보려면 다음 자습서를 진행 합니다.

> [!div class="nextstepaction"]
> [Azure 스택 마켓플레이스 항목 추가](asdk-marketplace-item.md)




