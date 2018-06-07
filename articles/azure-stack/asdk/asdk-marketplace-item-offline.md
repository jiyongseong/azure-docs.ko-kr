---
title: 로컬 원본의 Azure 스택 마켓플레이스 항목을 추가 합니다. | Microsoft Docs
description: Azure 스택 마켓플레이스에 로컬 운영 체제 이미지를 추가 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 325ef42d72970f4e0962a9b1a81b78bbd39585d4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>로컬 원본의 Azure 스택 마켓플레이스 항목을 추가 하는 자습서:

Azure 스택 연산자로 가장 먼저 사용자가 가상 컴퓨터 (VM)을 배포할 수 있도록 하기 위해 수행 해야 Azure 스택 마켓플레이스에 VM 이미지를 추가 하는 것입니다. 기본적으로 아무 것도 Azure 스택 마켓플레이스에 게시 하지만 사용자에 게 사용할 수 있도록 하는 VM ISO 이미지를 업로드할 수 있습니다. Azure 스택 연결이 끊긴 시나리오에서 또는 시나리오에서 함께 배포한 연결을 제한 하는 경우이 옵션을 사용 합니다.

이 자습서에서는 Windows Server 2016 VM 이미지를 다운로드 하는 Windows Server 평가 페이지 및 Azure 스택 마켓플레이스로 업로드 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Windows Server 2016 VM 이미지를 추가 합니다.
> * VM 이미지를 사용할 수 확인 
> * VM 이미지를 테스트 합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 설치는 [Azure 스택 호환 Azure PowerShell 모듈](asdk-post-deploy.md#install-azure-stack-powershell)
- 다운로드는 [Azure 스택 도구](asdk-post-deploy.md#download-the-azure-stack-tools)
- 다운로드는 [Windows 서버 2106 가상 컴퓨터 ISO 이미지](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) Windows Server 평가 페이지에서

## <a name="add-a-windows-server-vm-image"></a>Windows Server VM 이미지를 추가 합니다.
PowerShell을 사용 하 여 이전에 다운로드 한 ISO 이미지를 추가 하 여 Windows Server 2016 이미지 Azure 스택 마켓플레이스에 게시할 수 있습니다. 

Azure 스택 연결이 끊긴 시나리오에서 또는 시나리오에서 함께 배포한 연결을 제한 하는 경우이 옵션을 사용 합니다.

1. Azure 스택 가져오기 `Connect` 및 `ComputeAdmin` 다음 명령을 사용 하 여 Azure 스택 도구 디렉터리에 포함 된 PowerShell 모듈:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)을 사용 하 여 Azure 스택 환경 배포 여부에 따라 ASDK 호스트 컴퓨터에 로그온 하려면 다음 스크립트 중 하나를 실행 합니다.

  - 에 대 한 명령 **Azure AD 배포**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - 에 대 한 명령 **AD FS 배포**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Azure 스택 마켓플레이스로 Windows Server 2016 이미지를 추가 합니다. (대체 *fully_qualified_path_to_ISO* 다운로드 한 Windows Server 2016 ISO에 경로 사용 합니다.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>마켓플레이스 항목을 사용할 수 확인
Azure 스택 marketplace에서 새 VM 이미지를 사용할 수 있는지 확인 하려면 다음이 단계를 사용 합니다.

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리**합니다. 

3. Windows Server 2016 데이터 센터 VM 이미지에 성공적으로 추가 되었는지 확인 합니다.

   ![Azure에서 다운로드 한 이미지](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>VM 이미지를 테스트 합니다.
Azure 스택 연산자로 사용할 수 있습니다는 [관리자 포털](https://adminportal.local.azurestack.external) 테스트를 만들려면 이미지 유효성을 검사 하는 VM 수 있게 된 했습니다. 

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 클릭 **새** > **계산** > **Windows Server 2016 데이터 센터** > **만들**합니다.  
 ![마켓플레이스 이미지에서 VM 만들기](media/asdk-marketplace-item/new-compute.png)

3. 에 **기본 사항** 블레이드에서 다음 정보를 입력 하 고 클릭 **확인**:
  - **이름**: 테스트-v m-1
  - **사용자 이름**: AdminTestUser
  - **암호**: AzS TestVM01
  - **구독**: 기본 공급자 구독 허용
  - **리소스 그룹**: rg-vm-테스트
  - **위치**: 로컬

4. 에 **크기를 선택** 블레이드에서 클릭 **표준 A1**, 클릭 하 고 **선택**합니다.  

5. 에 **설정** 블레이드에서 기본값을 적용 하 고 클릭 **확인**

6. **요약** 블레이드에서 클릭 **확인** 가상 컴퓨터를 만듭니다.  
> [!NOTE]
> 가상 컴퓨터 배포를 완료 하려면 몇 분이 걸립니다.

7. 새 VM을 보려면 클릭 **가상 컴퓨터**, 다음 검색 **테스트-v m-1** 해당 이름을 클릭 합니다.
    ![표시 되는 첫 번째 테스트 VM 이미지](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>리소스 정리
성공적으로, VM에 로그인 하 고 테스트 이미지가 제대로 작동 하는지 확인 한, 후 테스트 리소스 그룹을 삭제 해야 합니다. 이렇게 하면 단일 노드 ASDK 설치에 사용할 수 있는 제한 된 리소스 확보 됩니다.

필요 없는 경우 다음이 단계를 수행 하 여 리소스 그룹, VM 및 모든 관련된 리소스를 삭제 합니다.

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.
2. 클릭 **리소스 그룹** > **테스트-vm-rg** > **리소스 그룹 삭제**합니다.
3. 형식 **테스트-vm-rg** 리소스 그룹 이름 및 클릭으로 **삭제**합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Windows Server 2016 VM 이미지를 추가 합니다.
> * VM 이미지를 사용할 수 확인 
> * VM 이미지를 테스트 합니다.

Azure 스택 제공 및 계획을 만드는 방법에 알아보려면 다음 자습서를 진행 합니다.

> [!div class="nextstepaction"]
> [Azure 스택 IaaS 서비스 제공](asdk-offer-services.md)
