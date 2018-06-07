---
title: Linux VM MSI를 사용하여 Azure Key Vault 액세스
description: Linux VM MSI(관리 서비스 ID)를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 안내하는 자습서입니다.
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
ms.openlocfilehash: 74bc28619996b7cd52d2280d4d8b6288aa7c904f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304310"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>자습서: Linux VM MSI(관리 서비스 ID)를 사용하여 Azure Key Vault에 액세스 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux Virtual Machine에 대해 MSI(관리 서비스 ID)를 사용하도록 설정한 다음 해당 ID를 사용하여 Azure Key Vault에 액세스하는 방법을 보여 줍니다. 부트스트랩 역할을 하는 Key Vault를 사용하면 클라이언트 응용 프로그램에서 비밀을 사용하여 Azure AD(Active Directory)로 보호되지 않는 리소스에 액세스할 수 있습니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Linux Virtual Machine에서 MSI를 사용하도록 설정 
> * Key Vault에 저장된 비밀 액세스 권한을 VM에 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음 Key Vault에서 비밀을 검색하는 데 사용 
 
## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다. 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Linux Virtual Machine 만들기

이 자습서에서는 새 Linux VM을 만듭니다. 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 **리소스 만들기** 단추를 클릭합니다.
2. **Compute**를 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. 가상 머신 정보를 입력합니다. **인증 유형**으로 **SSH 공용 키** 또는 **암호**를 선택합니다. 생성된 자격 증명을 사용하면 VM에 로그인할 수 있습니다.

    ![대체 이미지 텍스트](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 드롭다운에서 가상 머신의 **구독**을 선택합니다.
5. 가상 머신을 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 지원되는 디스크 형식 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정

Virtual Machine MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. VM에서 관리 서비스 ID를 사용하도록 설정하면 해당 관리 ID를 만들기 위해 VM이 Azure Active Directory에 등록되고, VM에서 ID가 구성되는 두 가지 작업이 수행됩니다.

1. MSI를 사용하도록 설정할 **Virtual Machine**을 선택합니다.
2. 왼쪽 탐색 모음에서 **구성**을 클릭합니다.
3. **관리 서비스 ID**가 표시됩니다. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다.
4. **저장**을 클릭하여 구성을 저장합니다.

    ![대체 이미지 텍스트](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Key Vault에 저장된 비밀 액세스 권한을 VM에 부여  

코드는 MSI를 사용하여 Azure Active Directory 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다. 그러나 모든 Azure 서비스가 Azure AD 인증을 지원하지는 않습니다. 이러한 서비스와 함께 MSI를 사용하려면 Azure Key Vault에 서비스 자격 증명을 저장하고 MSI를 사용하여 Key Vault에 액세스하여 자격 증명을 검색합니다. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>VM ID를 사용하여 액세스 토큰을 가져온 다음 Key Vault에서 비밀을 검색하는 데 사용  

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다.  Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 개인 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.
 
1. Portal에서 Linux VM으로 이동한 다음 **개요**에서 **연결**을 클릭합니다. 
2. 선택한 SSH 클라이언트를 사용하여 VM에 **연결**합니다. 
3. 터미널 창에서 CURL을 사용하여 로컬 MSI 끝점에 대한 요청을 수행해 Azure Key Vault용 액세스 토큰을 가져옵니다.  
 
    액세스 토큰에 대한 CURL 요청은 다음과 같습니다.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    응답에는 Resource Manager에 액세스하는 데 필요한 액세스 토큰이 포함되어 있습니다. 
    
    응답:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    이 액세스 토큰을 사용하여 Azure Key Vault에 인증할 수 있습니다.  다음 CURL 요청은 CURL 및 Key Vault REST API를 사용하여 Key Vault에서 비밀을 읽는 방법을 보여 줍니다.  Key Vault **개요** 페이지의 **기본 정보** 섹션에 있는 Key Vault의 URL이 필요합니다.  또한 이전 호출에서 확인한 액세스 토큰도 필요합니다. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    응답은 다음과 같습니다. 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault에서 비밀을 검색한 후에는 이름과 암호가 필요한 서비스에 인증하는 데 비밀을 사용할 수 있습니다.


## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.




