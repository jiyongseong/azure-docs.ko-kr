---
title: Windows VM MSI를 사용하여 Azure Key Vault 액세스
description: Windows VM MSI(관리 서비스 ID)를 사용하여 Azure Key Vault에 액세스하는 프로세스를 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: c4570e43c23ff7a23b5d6a97ebd6da0244ed6fb2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34300729"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>자습서: Windows VM MSI(관리 서비스 ID)를 사용하여 Azure Key Vault 액세스 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows Virtual Machine에 대해 MSI(관리 서비스 ID)를 사용하도록 설정한 다음 해당 ID를 사용하여 Azure Key Vault에 액세스하는 방법을 보여 줍니다. 부트스트랩 역할을 하는 Key Vault를 사용하면 클라이언트 응용 프로그램에서 비밀을 사용하여 Azure AD(Active Directory)로 보호되지 않는 리소스에 액세스할 수 있습니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 

다음 방법에 대해 알아봅니다.


> [!div class="checklist"]
> * Windows Virtual Machine에서 관리 서비스 ID를 사용하도록 설정 
> * Key Vault에 저장된 비밀 액세스 권한을 VM에 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음 Key Vault에서 비밀을 검색하는 데 사용 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Windows 가상 머신 만들기

이 자습서에서는 새 Windows VM을 만듭니다. 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1.  Azure Portal의 왼쪽 위에 있는 **리소스 만들기** 단추를 클릭합니다.
2.  **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 
3.  가상 머신 정보를 입력합니다. 여기서 만드는 **사용자 이름** 및 **암호**는 가상 머신에 로그인하는 데 사용하는 자격 증명입니다.
4.  드롭다운에서 가상 머신의 적절한 **구독**을 선택합니다.
5.  가상 머신을 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6.  VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

    ![대체 이미지 텍스트](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정 

Virtual Machine MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. MSI를 사용하도록 설정하면 Virtual Machine용으로 관리 ID를 만들도록 Azure에 지시하게 됩니다. MSI를 사용하도록 설정하면 그 배경에서는 두 작업이 수행됩니다. 즉 해당 관리 ID를 만들기 위해 VM이 Azure Active Directory에 등록되고, VM에서 ID가 구성됩니다.

1.  MSI를 사용하도록 설정할 **Virtual Machine**을 선택합니다.  
2.  왼쪽 탐색 모음에서 **구성**을 클릭합니다. 
3.  **관리 서비스 ID**가 표시됩니다. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다. 
4.  **저장**을 클릭하여 구성을 저장합니다.  

    ![대체 이미지 텍스트](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Key Vault에 저장된 비밀 액세스 권한을 VM에 부여 
 
코드는 MSI를 사용하여 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다.  그러나 모든 Azure 서비스가 Azure AD 인증을 지원하지는 않습니다. 이러한 서비스와 함께 MSI를 사용하려면 Azure Key Vault에 서비스 자격 증명을 저장하고 MSI를 사용하여 Key Vault에 액세스하여 자격 증명을 검색합니다. 

먼저 Key Vault를 만들고 VM ID에 Key Vault 액세스 권한을 부여해야 합니다.   

1. 왼쪽 탐색 모음 맨 위에서 **리소스 만들기** > **보안 + ID** > **Key Vault**를 차례로 선택합니다.  
2. 새 Key Vault의 **이름**을 입력합니다. 
3. 앞에서 만든 VM과 같은 구독 및 리소스 그룹에 Key Vault를 배치합니다. 
4. **액세스 정책**을 선택하고 **새로 추가**를 클릭합니다. 
5. 템플릿에서 구성에서 **비밀 관리**를 선택합니다. 
6. **주체 선택**을 선택하고 검색 필드에 앞에서 만든 VM의 이름을 입력합니다.  결과 목록에서 VM을 선택하고 **선택**을 클릭합니다. 
7. **확인**을 클릭하여 새 액세스 정책 추가를 완료하고, **확인**을 클릭하여 액세스 정책 선택을 완료합니다. 
8. **만들기**를 클릭하여 Key Vault 만들기를 완료합니다. 

    ![대체 이미지 텍스트](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


다음으로는 VM에서 실행되는 코드를 사용하여 나중에 비밀을 검색할 수 있도록 Key Vault에 암호를 추가합니다. 

1. **모든 리소스**를 선택하고 만든 Key Vault를 찾아서 선택합니다. 
2. **비밀**을 선택하고 **추가**를 클릭합니다. 
3. **업로드 옵션**에서 **수동**을 선택합니다. 
4. 비밀의 이름과 값을 입력합니다.  원하는 어떤 값이나 입력할 수 있습니다. 
5. 활성화 날짜와 만료 날짜는 비워 두고 **사용 가능**은 **예**로 유지합니다. 
6. **만들기**를 클릭하여 비밀을 만듭니다. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>VM ID를 사용하여 액세스 토큰을 가져온 다음 Key Vault에서 비밀을 검색하는 데 사용  

PowerShell 4.3.1 이상이 설치되어 있지 않으면 [최신 버전을 다운로드하고 설치](https://docs.microsoft.com/powershell/azure/overview)해야 합니다.

먼저 VM의 MSI를 사용하여 Key Vault에 인증하기 위한 액세스 토큰을 가져옵니다.
 
1. Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요**에서 **연결**을 클릭합니다.
2. **Windows VM**을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다.  
3. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 PowerShell을 엽니다.  
4. PowerShell에서 테넌트에 대해 웹 요청을 호출하여 VM의 특정 포트에서 로컬 호스트용 토큰을 가져옵니다.  

    PowerShell 요청은 다음과 같습니다.
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 전체 응답을 추출합니다.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    다음으로는 응답에서 액세스 토큰을 추출합니다.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    마지막으로 PowerShell의 Invoke-WebRequest 명령을 사용하여 앞에서 Key Vault에 만든 비밀을 검색하고 인증 헤더에서 액세스 토큰을 전달합니다.  Key Vault **개요** 페이지의 **기본 정보** 섹션에 있는 Key Vault의 URL이 필요합니다.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    응답은 다음과 같습니다. 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault에서 비밀을 검색한 후에는 이름과 암호가 필요한 서비스에 인증하는 데 비밀을 사용할 수 있습니다. 

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
