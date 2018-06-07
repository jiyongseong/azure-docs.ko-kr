---
title: Linux VM MSI를 사용하여 SAS 자격 증명을 통해 Azure Storage에 액세스
description: Linux VM MSI(관리 서비스 ID)를 사용하여 저장소 계정 액세스 키 대신 SAS 자격 증명으로 Azure Storage에 액세스하는 방법을 보여 주는 자습서입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fc7c5b4ab025666fc7fa1d9073198ec90d8e71c3
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Linux VM 관리 서비스 ID를 사용하여 SAS 자격 증명을 통해 Azure Storage에 액세스

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 Linux Virtual Machine에 대해 MSI(관리 서비스 ID)를 사용하도록 설정한 다음 MSI를 사용하여 SAS(공유 액세스 서명) 자격 증명을 획득하는 방법을 보여 줍니다. 특히 [서비스 SAS 자격 증명](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)에 대한 내용을 다룹니다. 

서비스 SAS는 계정 액세스 키를 노출하지 않고 제한된 시간 동안 특정 서비스(이 경우 Blob 서비스)에 대해 저장소 계정의 개체에 대해 제한된 액세스 권한을 부여하는 기능을 제공합니다. Storage SDK를 사용하는 등의 Storage 작업을 수행할 때 일반적인 방식으로 SAS 자격 증명을 사용할 수 있습니다. 이 자습서에서는 Azure Storage CLI를 사용하여 Blob을 업로드 및 다운로드하는 과정을 보여 줍니다. 다음 방법을 알게 됩니다.


> [!div class="checklist"]
> * Linux Virtual Machine에서 MSI를 사용하도록 설정 
> * Resource Manager의 저장소 계정 SAS에 대한 VM 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져오고 리소스 관리자에서 SAS를 검색하는 데 사용 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Linux 가상 머신 만들기

이 자습서에서는 새 Linux VM을 만듭니다. 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **Compute**를 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. 가상 머신 정보를 입력합니다. **인증 유형**으로 **SSH 공용 키** 또는 **암호**를 선택합니다. 생성된 자격 증명을 사용하면 VM에 로그인할 수 있습니다.

    ![대체 이미지 텍스트](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 드롭다운에서 가상 머신의 **구독**을 선택합니다.
5. 가상 머신을 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 지원되는 디스크 형식 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정

Virtual Machine MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. MSI를 사용하도록 설정하는 경우 내부적으로는 두 가지 작업이 수행됩니다. 즉, VM에 MSI VM 확장이 설치되고 VM에 대해 관리 서비스 ID가 사용하도록 설정됩니다.  

1. 새 가상 머신의 리소스 그룹을 찾고 이전 단계에서 만든 가상 머신을 선택합니다.
2. 왼쪽에 있는 VM “설정”에서 **구성**을 클릭합니다.
3. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다.
4. **저장**을 클릭하여 구성을 저장합니다.

    ![대체 이미지 텍스트](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. VM에 있는 확장을 확인하려면 **확장**을 클릭합니다. MSI가 사용하도록 설정된 경우 목록에 **ManagedIdentityExtensionforLinux**가 표시됩니다.

    ![대체 이미지 텍스트](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>저장소 계정 만들기 

저장소 계정이 아직 없는 경우 이제 하나 만들게 됩니다.  또한 이 단계를 건너뛰고 기존 저장소 계정의 키에 대한 VM MSI 액세스 권한을 부여할 수 있습니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **저장소**를 클릭하면 **저장소 계정**, 새 “저장소 계정 만들기” 패널이 표시됩니다.
3. 나중에 사용할 저장소 계정에 대한 **이름**을 입력합니다.  
4. **배포 모델** 및 **계정 종류**는 각각 “리소스 관리자” 및 “범용”으로 설정해야 합니다. 
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 저장소 계정 만들기](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>저장소 계정에 Blob 컨테이너 만들기

나중에 새 저장소 계정에 파일을 업로드 및 다운로드합니다. 파일은 Blob Storage가 필요하기 때문에 파일을 저장할 Blob 컨테이너를 만들어야 합니다.

1. 새로 만든 저장소 계정으로 다시 이동합니다.
2. 왼쪽 패널에서 “Blob service” 아래에 있는 **컨테이너** 링크를 클릭합니다.
3. 페이지 맨 위에 있는 **+ 컨테이너**를 클릭하면 “새 컨테이너” 패널이 나타납니다.
4. 컨테이너에 이름을 지정하고 액세스 수준을 선택한 다음 **확인**을 클릭합니다. 지정한 이름은 이 자습서의 뒷부분에 사용됩니다. 

    ![저장소 컨테이너 만들기](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>저장소 SAS를 사용하도록 VM의 MSI에 액세스 권한 부여 

Azure Storage는 Azure AD 인증을 기본적으로 지원하지 않습니다.  그러나 MSI를 사용하여 Resource Manager에서 저장소 SAS를 검색하고, 해당 SAS를 사용하여 저장소에 액세스할 수 있습니다.  이 단계에서는 VM MSI에 저장소 계정 SAS에 대한 액세스 권한을 부여합니다.   

1. 새로 만든 저장소 계정으로 다시 이동합니다.   
2. 왼쪽 패널의 **액세스 제어(IAM)** 링크를 클릭합니다.  
3. 페이지 위쪽에 있는 **+ 추가**를 클릭하여 VM에 대한 새 역할 할당을 추가합니다.
4. 페이지 오른쪽에서 **역할**을 "저장소 계정 참가자"로 설정합니다. 
5. 다음 드롭다운에서 **다음에 대한 액세스 할당**을 “Virtual Machine” 리소스로 설정합니다.  
6. 다음으로 적절한 구독이 **구독** 드롭다운에 나열되는지 확인하고 **리소스 그룹**을 "모든 리소스 그룹"으로 설정합니다.  
7. 마지막으로 **선택** 드롭다운에서 Linux Virtual Machine을 선택하고 **저장**을 클릭합니다.  

    ![대체 이미지 텍스트](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM의 ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/install_guide)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](~/articles/virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 개인 키 쌍을 만들고 사용하는 방법](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다. 
2. SSH 클라이언트를 사용하여 VM에 연결합니다.  
3. 그리고 나면 **Linux VM**을 만들 때 추가했던 **암호**를 입력하라는 메시지가 표시됩니다. 이제 정상적으로 로그인되어야 합니다.  
4. CURL을 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져옵니다.  

    액세스 토큰에 대한 CURL 요청 및 응답은 다음과 같습니다.
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > 이전 요청에서 "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    > 다음 응답에서는 간단히 나타내기 위해 access_token 요소를 축약했습니다.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>저장소 호출을 위해 Azure Resource Manager에서 SAS 자격 증명 가져오기

이제 이전 섹션에서 검색했던 토큰 액세스를 사용하여 Resource Manager를 호출하여 저장소 SAS 자격 증명을 만드는 데 CURL을 사용하게 됩니다. SAS 자격 증명이 있으면 저장소 업로드/다운로드 작업을 호출할 수 있습니다.

이 요청을 위해 다음 HTTP 요청 매개 변수를 사용하여 SAS 자격 증명을 만듭니다.

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

이러한 매개 변수는 SAS 자격 증명에 대한 요청 POST 본문에 포함됩니다. SAS 자격 증명을 만들기 위한 매개 변수에 대한 자세한 내용은 [서비스 SAS REST 목록 참조](/rest/api/storagerp/storageaccounts/listservicesas)를 참조하세요.

다음 CURL 요청을 사용하여 SAS 자격 증명을 가져옵니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` 및 `<EXPIRATION TIME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<ACCESS TOKEN>` 값을 앞서 검색한 액세스 토큰으로 바꿉니다.

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> 이전 URL의 텍스트는 대/소문자를 구분하므로 리소스 그룹을 적절하게 반영하는 대/소문자를 사용하는지 확인하세요. 또한 이것이 GET 요청이 아니라 POST 요청임을 알아야 합니다.

CURL 응답은 SAS 자격 증명을 반환합니다.  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Blob Storage 컨테이너에 업로드할 샘플 Blob 파일을 만듭니다. Linux VM에서는 다음 명령을 사용하여 이 작업을 수행할 수 있습니다. 

```bash
echo "This is a test file." > test.txt
```

다음으로, SAS 자격 증명을 사용하여 CLI `az storage` 명령을 통해 인증을 받고 Blob 컨테이너에 파일을 업로드합니다. 이 단계에서는 VM에 [최신 Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)해야 합니다(아직 없는 경우).

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

응답: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

또한 Azure CLI를 사용하여 파일을 다운로드하고 SAS 자격 증명을 사용하여 인증을 할 수 있습니다. 

요청: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

응답: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.
- 저장소 계정 키를 사용하여 동일한 자습서를 수행하는 방법을 알아보려면 [Linux VM 관리 서비스 ID를 사용하여 Azure Storage에 액세스](msi-tutorial-linux-vm-access-storage.md)를 참조하세요.
- Azure Storage 계정 SAS 기능에 대한 자세한 내용은 다음을 참조하세요.
  - [SAS(공유 액세스 서명) 사용](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [서비스 SAS 생성(영문)](/rest/api/storageservices/Constructing-a-Service-SAS.md)

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
