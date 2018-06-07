---
title: Windows VM MSI를 사용하여 Azure Resource Manager 액세스
description: Windows VM MSI(관리 서비스 ID)를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 안내하는 자습서입니다.
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
ms.openlocfilehash: 1b23101de9a84da487e73558366cc574a22e849d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Windows VM MSI(관리 서비스 ID)를 사용하여 Resource Manager 액세스

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대해 MSI(관리 서비스 ID)를 사용하도록 설정하는 방법을 보여 줍니다. 그런 다음 해당 ID를 사용하여 Azure Resource Manager API에 액세스할 수 있습니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Windows VM에서 MSI를 사용하도록 설정 
> * VM에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Windows 가상 머신 만들기

이 자습서에서는 새 Windows VM을 만듭니다.  기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1.  Azure Portal의 왼쪽 위에 있는 **리소스 만들기** 단추를 클릭합니다.
2.  **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 
3.  가상 머신 정보를 입력합니다. 여기서 만드는 **사용자 이름** 및 **암호**는 가상 머신에 로그인하는 데 사용하는 자격 증명입니다.
4.  드롭다운에서 가상 머신의 적절한 **구독**을 선택합니다.
5.  가상 컴퓨터를 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6.  VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 설정 페이지에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

    ![대체 이미지 텍스트](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정 

VM MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. VM에서 관리 서비스 ID를 사용하도록 설정하면 해당 관리 ID를 만들기 위해 VM이 Azure Active Directory에 등록되고, VM에서 ID가 구성되는 두 가지 작업이 수행됩니다.

1.  MSI를 사용하도록 설정할 **Virtual Machine**을 선택합니다.  
2.  왼쪽 탐색 모음에서 **구성**을 클릭합니다. 
3.  **관리 서비스 ID**가 표시됩니다. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다. 
4.  **저장**을 클릭하여 구성을 저장합니다.  
    ![대체 이미지 텍스트](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>VM에 Resource Manager의 리소스 그룹 액세스 권한 부여
코드는 MSI를 사용하여 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다.  Azure Resource Manager는 Azure AD 인증을 지원합니다.  먼저 이 VM의 ID에 Azure Resource Manager의 리소스(이 예제에서는 VM이 포함된 리소스 그룹)에 대한 액세스 권한을 부여해야 합니다.  

1.  **리소스 그룹**의 탭으로 이동합니다. 
2.  **Windows VM**용으로 만든 특정 **리소스 그룹**을 선택합니다. 
3.  왼쪽 패널의 **액세스 제어(IAM)** 로 이동합니다. 
4.  그런 다음 **Windows VM**용 새 역할 할당을 **추가**합니다.  **역할**로 **독자**를 선택합니다. 
5.  다음 드롭다운에서 **Virtual Machine** 리소스에 대한 **액세스를 할당**합니다. 
6.  그런 다음 **구독** 드롭다운에 적절한 구독이 나열되어 있는지 확인합니다. 그리고 **리소스 그룹**에서는 **모든 리소스 그룹**을 선택합니다. 
7.  마지막으로 **선택**의 드롭다운에서 Windows VM을 선택하고 **저장**을 클릭합니다.

    ![대체 이미지 텍스트](../media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용 

이 부분에서는 **PowerShell**을 사용해야 합니다.  PowerShell이 설치되어 있지 않으면 [여기](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)서 다운로드하세요. 

1.  Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요**에서 **연결**을 클릭합니다. 
2.  Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다. 
3.  이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다. 
4.  Powershell의 Invoke-WebRequest를 사용하여 로컬 MSI 끝점에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 전체 응답을 추출합니다. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    다음으로는 응답에서 액세스 토큰을 추출합니다.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    마지막으로 액세스 토큰을 사용하여 Azure Resource Manager를 호출합니다. 또한 이 예제에서는 PowerShell의; Invoke-WebRequest를 사용하여 Azure Resource Manager를 호출한 다음 인증 헤더에 액세스 토큰을 포함합니다.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > URL은 대/소문자를 구분하므로 앞서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자를 사용해야 하며, “resourceGroups”와 같이 대문자 “G”를 사용해야 합니다.
        
    다음 명령은 리소스 그룹의 세부 정보를 반환합니다.

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

