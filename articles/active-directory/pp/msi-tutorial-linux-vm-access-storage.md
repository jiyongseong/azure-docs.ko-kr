---
title: Linux VM에서 사용자 할당 MSI를 사용하여 Azure Storage에 액세스
description: Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Storage에 액세스하는 프로세스를 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Storage에 액세스

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 Linux 가상 머신에서 사용자 할당 MSI(관리 서비스 ID)를 만들고 사용하여 Azure Storage에 액세스하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 할당 MSI(관리 서비스 ID) 만들기
> * Linux 가상 머신에 사용자 할당 MSI 할당
> * Azure Storage 인스턴스에 대한 MSI 액세스 권한 부여
> * 사용자 할당 MSI ID를 사용하여 액세스 토큰을 가져오고, 해당 토큰을 사용하여 Azure Storage에 액세스

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 "사용해 보세요" 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Linux 가상 머신 만들기

먼저 새 Linux VM을 만듭니다. 원한다면 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **Compute**를 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. 가상 머신 정보를 입력합니다. **인증 유형**으로 **SSH 공용 키** 또는 **암호**를 선택합니다. 생성된 자격 증명을 사용하면 VM에 로그인할 수 있습니다.

    ![대체 이미지 텍스트](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 드롭다운에서 가상 머신의 **구독**을 선택합니다.
5. 가상 머신을 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 지원되는 디스크 형식 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

## <a name="create-a-user-assigned-msi"></a>사용자 할당 MSI 만들기

1. Azure Cloud Shell 세션 대신 CLI 콘솔을 사용하는 경우 먼저 Azure에 로그인합니다. 새 MSI를 만들려는 Azure 구독과 연결된 계정을 사용하세요.

    ```azurecli
    az login
    ```

2. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 MSI를 만듭니다. `-g` 매개 변수는 MSI가 만들어지는 리소스 그룹을 지정하고, `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<MSI NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    응답에는 다음 예제와 같이 생성된 사용자 할당 MSI에 대한 세부 정보가 포함됩니다. 다음 단계에서 사용되므로 MSI의 `id` 값을 적어 둡니다.

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Linux VM에 사용자 할당 MSI 할당

시스템 할당 MSI와는 달리, 사용자 할당 MSI는 클라이언트가 여러 Azure 리소스에 사용할 수 있습니다. 이 자습서에서는 단일 VM에 할당하겠습니다. 여러 VM에 할당해도 됩니다.

[az vm assign-identity](/cli/azure/vm#az-vm-identity-assign)를 사용하여 Linux VM에 사용자 할당 MSI를 할당합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. 이전 단계에서 반환된 `id` 속성을 `--identities` 매개 변수 값에 사용합니다.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기 

저장소 계정이 아직 없는 경우 지금 만듭니다. 원하는 경우 이 단계를 건너뛰고 기존 저장소 계정을 사용해도 됩니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **저장소**, **저장소 계정**을 차례로 클릭하면 새 “저장소 계정 만들기” 패널이 표시됩니다.
3. 나중에 사용할 저장소 계정의 **이름**을 입력합니다.  
4. **배포 모델** 및 **계정 종류**는 각각 “리소스 관리자” 및 “범용”으로 설정해야 합니다. 
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 저장소 계정 만들기](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>저장소 계정에 Blob 컨테이너 만들기

파일에 Blob Storage가 필요하기 때문에 파일을 저장할 Blob 컨테이너를 만들어야 합니다. 그런 다음 새 저장소 계정에서 blob 컨테이너에 파일을 업로드 및 다운로드합니다.

1. 새로 만든 저장소 계정으로 다시 이동합니다.
2. 왼쪽에서 “Blob service” 아래에 있는 **컨테이너** 링크를 클릭합니다.
3. 페이지 맨 위에 있는 **+ 컨테이너**를 클릭하면 “새 컨테이너” 패널이 나타납니다.
4. 컨테이너에 이름을 지정하고 액세스 수준을 선택한 다음 **확인**을 클릭합니다. 지정한 이름은 이 자습서의 뒷부분에도 사용됩니다. 

    ![저장소 컨테이너 만들기](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 컨테이너 이름을 클릭하고, **업로드**를 클릭하고, 파일을 선택하고, **업로드**를 클릭하여 새로 만든 컨테이너에 파일을 추가합니다.

    ![텍스트 파일 업로드](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Azure Storage 컨테이너에 대한 사용자 할당 MSI 액세스 권한 부여

코드는 MSI를 사용하여 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다. 이 자습서에서는 Azure Storage를 사용합니다.

먼저 Azure Storage 컨테이너에 대한 MSI ID 액세스 권한을 부여합니다. 이 예에서는 앞에서 만든 컨테이너를 사용합니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>` 및 `<CONTAINER NAME>`의 값을 환경에 적합하게 업데이트합니다. 또한 `<MSI PRINCIPALID>`를 [사용자 할당 MSI 만들기](#create-a-user-assigned-msi)의 `az identity create` 명령에서 반환한 `principalId` 속성으로 바꿉니다.

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

응답에는 생성된 역할 할당에 대한 세부 정보가 포함됩니다.

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>사용자 할당 MSI ID를 사용하여 액세스 토큰을 가져오고, 해당 토큰을 사용하여 Azure Storage 호출

자습서의 나머지 부분은 이전에 만든 VM에서 수행해야 합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](~/articles/virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 개인 키 쌍을 만들고 사용하는 방법](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다.
2. 선택한 SSH 클라이언트를 사용하여 VM에 **연결**합니다. 
3. 터미널 창에서 CURL을 사용하여 로컬 MSI 엔드포인트에 대한 요청을 만들어서 Azure Storage에 대한 액세스 토큰을 가져옵니다.

   다음 예제에는 액세스 토큰을 획득하는 CURL 요청이 나와 있습니다. `<CLIENT ID>`를 [사용자 할당 MSI 만들기](#create-a-user-assigned-msi)의 `az identity create` 명령에서 반환한 `clientId` 속성으로 바꿉니다.
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > 이전 요청에서 `resource` 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Storage 리소스 ID를 사용할 때에는 URI에 후행 슬래시를 포함해야 합니다.
   > 다음 응답에서는 간단히 나타내기 위해 access_token 요소를 축약했습니다.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. 이제 액세스 토큰을 사용하여 Azure Storage에 액세스합니다. 예를 들어 앞에서 컨테이너에 업로드한 샘플 파일의 내용을 읽습니다. `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` 및 `<FILE NAME>` 값을 앞에서 지정한 값으로 바꾸고, `<ACCESS TOKEN>`을 이전 단계에서 반환된 토큰으로 바꿉니다.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   응답에는 파일의 내용이 포함됩니다.

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.
- 저장소 SAS 자격 증명을 사용하여 동일한 자습서를 수행하는 방법을 알아보려면 [Linux VM 관리 서비스 ID를 사용하여 SAS 자격 증명을 통해 Azure Storage에 액세스](msi-tutorial-linux-vm-access-storage-sas.md)를 참조하세요.
- Azure Storage 계정 SAS 기능에 대한 자세한 내용은 다음을 참조하세요.
  - [SAS(공유 액세스 서명) 사용](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [서비스 SAS 생성(영문)](/rest/api/storageservices/Constructing-a-Service-SAS.md)

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.





