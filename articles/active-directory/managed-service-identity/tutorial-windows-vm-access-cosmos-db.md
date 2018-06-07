---
title: Windows VM MSI를 사용하여 Azure Cosmos DB에 액세스
description: Windows VM에서 시스템 할당 MSI(관리 서비스 ID)를 사용하여 Azure Cosmos DB에 액세스하는 프로세스를 안내하는 자습서입니다.
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
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: d04bfe601760dcbf832aeec0cc66d8e1eda51178
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304344"
---
# <a name="tutorial-use-a-windows-vm-msi-to-access-azure-cosmos-db"></a>자습서: Windows VM MSI를 사용하여 Azure Cosmos DB에 액세스

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM MSI를 만들고 사용하여 Cosmos DB에 액세스하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * MSI 지원 Windows VM 만들기 
> * Cosmos DB 계정 만들기
> * Cosmos DB 계정 액세스 키에 대한 Windows VM MSI 액세스 부여
> * Windows VM의 MSI를 통해 액세스 토큰을 가져온 후 Azure Resource Manager 호출
> * Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키 가져오기

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Windows 가상 머신 만들기

이 자습서에서는 새 Windows VM을 만듭니다.  기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 **리소스 만들기** 단추를 클릭합니다.
2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 
3. 가상 머신 정보를 입력합니다. 여기서 만드는 **사용자 이름** 및 **암호**는 가상 머신에 로그인하는 데 사용하는 자격 증명입니다.
4. 드롭다운에서 가상 머신의 적절한 **구독**을 선택합니다.
5. 가상 컴퓨터를 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 설정 페이지에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

   ![대체 이미지 텍스트](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>VM에서 MSI를 사용하도록 설정 

Virtual Machine MSI를 사용하면 코드에 자격 증명을 포함하지 않고도 Azure AD에서 액세스 토큰을 가져올 수 있습니다. 내부적으로 Azure Portal을 통해 가상 머신에서 MSI를 사용하도록 설정하면 두 가지 작업이 수행됩니다. 즉, VM이 Azure AD에 등록되어 관리 ID를 생성하고, 해당 VM에서 ID가 구성됩니다.

1. MSI를 사용하도록 설정할 **Virtual Machine**을 선택합니다.  
2. 왼쪽 탐색 모음에서 **구성**을 클릭합니다. 
3. **관리 서비스 ID**가 표시됩니다. MSI를 등록하고 사용하도록 설정하려면 **예**를 선택하고, 사용하지 않도록 설정하려면 아니요를 선택합니다. 
4. **저장**을 클릭하여 구성을 저장합니다.  
   ![대체 이미지 텍스트](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기 

Cosmos DB 계정이 아직 없는 경우 지금 만듭니다. 이 단계를 건너뛰고 기존 Cosmos DB 계정을 사용해도 됩니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **데이터베이스**를 클릭한 다음, **Azure Cosmos DB**를 클릭하면 새로운 "새 계정" 패널이 표시됩니다.
3. 나중에 사용하는 Cosmos DB 계정에 대한 **ID**를 입력합니다.  
4. **API**는 "SQL"로 설정되어야 합니다. 이 자습서에서 설명하는 방식은 사용 가능한 다른 API 형식으로 사용될 수 있지만 이 자습서의 단계는 SQL API에 대한 것입니다.
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.  Cosmos DB를 사용할 수 있는 **위치**를 선택합니다.
6. **만들기**를 클릭합니다.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Cosmos DB 계정에서 컬렉션을 만듭니다.

다음으로, 이후 단계에서 쿼리할 수 있는 Cosmos DB 계정에서 데이터 컬렉션을 추가합니다.

1. 새로 만든 Cosmos DB 계정으로 이동합니다.
2. **개요** 탭에서 **+/컬렉션 추가** 단추를 클릭하면 "컬렉션 추가" 패널이 슬라이드됩니다.
3. 컬렉션에 데이터베이스 ID, 컬렉션 ID를 제공하고, 저장소 용량을 선택하고, 파티션 키를 입력하고, 처리량 값을 입력한 다음, **확인**을 클릭합니다.  이 자습서의 경우 데이터베이스 ID 및 컬렉션 ID로 "Test"를 사용하고, 고정된 저장소 용량 및 가장 낮은 처리량(400RU/s)을 선택하는 데 충분합니다.  

## <a name="grant-windows-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Cosmos DB 계정 액세스 키에 대한 Windows VM MSI 액세스 부여

Cosmos DB는 Azure AD 인증을 기본적으로 지원하지 않습니다. 그러나 MSI를 사용하여 Resource Manager에서 Cosmos DB 액세스 키를 검색한 다음, 해당 키를 사용하여 Cosmos DB에 액세스할 수 있습니다. 이 단계에서 Cosmos DB 계정에 키에 대한 MSI 액세스를 부여합니다.

PowerShell을 사용하여 Azure Resource Manager의 Cosmos DB 계정에 대해 MSI ID 액세스를 부여하려면 작업 환경의 `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<COSMOS DB ACCOUNT NAME>` 값을 업데이트합니다. `<MSI PRINCIPALID>`를 [Linux VM MSI의 principalID 검색](#retrieve-the-principalID-of-the-linux-VM's-MSI)의 `az resource show` 명령에서 반환된 `principalId` 속성으로 바꿉니다.  Cosmos DB는 액세스 키를 사용할 때 두 가지 수준의 세분성: 계정에 대한 읽기/쓰기 액세스 및 계정에 대한 읽기 전용 액세스를 지원합니다.  계정에 대한 읽기/쓰기 키를 가져오려는 경우 `DocumentDB Account Contributor` 역할을 할당하거나 계정에 대한 읽기 전용 키를 가져오려는 경우 `Cosmos DB Account Reader Role` 역할을 할당합니다.

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-msi-to-call-azure-resource-manager"></a>Windows VM의 MSI를 통해 액세스 토큰을 가져온 후 Azure Resource Manager 호출

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다. 

이 부분에서는 Azure Resource Manager PowerShell cmdlet을 사용해야 합니다.  설치하지 않은 경우 계속하기 전에 [최신 버전을 다운로드](https://docs.microsoft.com/powershell/azure/overview)합니다.

또한 Windows VM에 최신 버전의 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)을 설치해야 합니다.

1. Azure Portal에서 **Virtual Machines**, Windows 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. 
2. Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다. 
3. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 PowerShell을 엽니다.
4. Powershell의 Invoke-WebRequest를 사용하여 로컬 MSI 끝점에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 “Content” 요소를 추출합니다. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    다음으로는 응답에서 액세스 토큰을 추출합니다.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키 가져오기

이제 PowerShell을 사용하여 이전 섹션에서 검색한 액세스 토큰을 사용하여 Resource Manager를 호출하여 Cosmos DB 계정 액세스 키를 검색합니다. 액세스 키가 있으면 Cosmos DB를 쿼리할 수 있습니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<COSMOS DB ACCOUNT NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<ACCESS TOKEN>` 값을 앞서 검색한 액세스 토큰으로 바꿉니다.  읽기/쓰기 키를 검색하려는 경우 키 작업 유형 `listKeys`를 사용합니다.  읽기 전용 키를 검색하려는 경우 키 작업 유형 `readonlykeys`를 사용합니다.

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
이 응답에서는 키 목록을 제공합니다.  예를 들어, 읽기 전용 키를 가져오는 경우

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
이제 Cosmos DB 계정에 대한 액세스 키가 있으므로 Cosmos DB SDK에 전달하고 계정에 액세스하는 호출을 만들 수 있습니다.  빠른 예제의 경우 Azure CLI에 액세스 키를 전달할 수 있습니다.  Azure Portal에서 Cosmos DB 계정 블레이드의 **개요** 탭에서 <COSMOS DB CONNECTION URL>을 가져올 수 있습니다.  <ACCESS KEY>를 위에서 얻은 값으로 바꿉니다.

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

이 CLI 명령은 컬렉션에 대한 세부 정보를 반환합니다.

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.
