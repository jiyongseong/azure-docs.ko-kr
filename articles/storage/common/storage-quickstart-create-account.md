---
title: Azure 빠른 시작 - 저장소 계정 만들기 | Microsoft Docs
description: Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 새 저장소 계정을 만드는 방법을 간단히 알아봅니다.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 01/19/2018
ms.author: tamram
ms.openlocfilehash: 1dffceb629df5d0197bfc8ac66120df3912d70b1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-storage-account"></a>저장소 계정 만들기

Azure Storage 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위해 클라우드에서 고유한 네임스페이스를 제공합니다. 저장소 계정은 해당 계정에서 만든 모든 BLOB, 파일, 큐, 테이블 및 디스크를 포함합니다. 

Azure Storage를 시작하기 위해 먼저 새 저장소 계정을 만들어야 합니다. [Azure Portal](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)를 사용하여 Azure Storage 계정을 만들 수 있습니다. 이 빠른 시작에서는 각각의 옵션을 사용하여 새 저장소 계정을 만드는 방법을 살펴봅니다. 


## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

# <a name="portaltabportal"></a>[포털](#tab/portal)

없음.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

- Azure Cloud Shell의 Azure Portal에서 CLI 명령 실행 
- CLI를 설치하고 로컬에서 CLI 실행  

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Portal 오른쪽 위에 있는 메뉴에서 **Cloud Shell** 버튼을 클릭합니다.

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

이 단추는 이 빠른 시작의 단계를 실행하는 데 사용할 수 있는 대화형 셸을 시작합니다.

[![포털에서 Cloud Shell 창을 보여 주는 스크린샷](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

Azure CLI를 로컬에서 설치하여 사용할 수도 있습니다. 이 빠른 시작에서는 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

---

## <a name="log-in-to-azure"></a>Azure에 로그인

# <a name="portaltabportal"></a>[포털](#tab/portal)

[Azure 포털](https://portal.azure.com) 에 로그인합니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

`Connect-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzureRmAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

CLI의 로컬 설치에 로그인하려면 로그인 명령을 실행합니다.

```cli
az login
```

---

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

# <a name="portaltabportal"></a>[포털](#tab/portal)

Azure Portal에서 리소스 그룹을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스의 메뉴를 열고 **리소스 그룹**을 선택합니다.
2. **추가** 단추를 클릭하여 새 리소스 그룹을 추가합니다.
3. 새 리소스 그룹의 이름을 입력합니다.
4. 새 리소스 그룹을 만들려는 구독을 선택합니다.
5. 리소스 그룹의 위치를 선택합니다.
6. **만들기** 단추를 클릭합니다.  

![Azure Portal에서 리소스 그룹 만들기를 표시하는 스크린샷](./media/storage-quickstart-create-account/create-resource-group.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 새 리소스 그룹을 만들려면 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용합니다. 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

`-Location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) 명령을 사용하여 해당 구독에 대해 지원되는 지역 목록을 검색할 수 있습니다.

```powershell
Get-AzureRmLocation | select Location 
$location = "westus"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI로 새 리소스 그룹을 만들려면 [az group create](/cli/azure/group#az_group_create) 명령을 사용합니다. 

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus
```

`--location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [az account list-locations](/cli/azure/account#az_account_list) 명령을 사용하여 해당 구독에 대해 지원되는 지역을 검색할 수 있습니다.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

---

## <a name="create-a-general-purpose-storage-account"></a>범용 저장소 계정 만들기

범용 저장소 계정은 모든 Azure Storage 서비스(BLOB, 파일, 큐, 테이블)에 대한 액세스를 제공합니다. 범용 저장소 계정은 표준 또는 프리미엄 계층에서 만들 수 있습니다. 이 문서의 예제에서는 표준 계층(기본값)에서 범용 저장소 계정을 만드는 방법을 보여 줍니다.

Azure Storage는 두 가지 유형의 범용 저장소 계정을 제공합니다.

- 범용 v2 계정 
- 범용 v1 계정 

> [!NOTE]
> 해당 계정에 사용할 수 있는 새로운 기능을 활용하도록 **범용 v2 계정**으로 새 저장소 계정을 만드는 것이 좋습니다.  

저장소 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 옵션](storage-account-options.md)을 참조하세요.

저장소 계정의 이름을 지정할 때는 다음 규칙에 유의하세요.

- Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
- 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 두 개의 저장소 계정이 같은 이름을 사용할 수 없습니다.

# <a name="portaltabportal"></a>[포털](#tab/portal)

Azure Portal에서 범용 v2 저장소 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스의 메뉴를 열고 **모든 서비스**를 선택합니다. 그런 다음 **Storage**로 스크롤하고 **Storage 계정**을 선택합니다. 나타나는 **Storage 계정** 창에서 **추가**를 선택합니다.
2. 저장소 계정의 이름을 입력합니다.
3. **계정 종류** 필드를 **StorageV2(범용 v2)**로 설정합니다.
4. **복제** 필드를 **LRS(로컬 중복 저장소)**로 설정된 채로 둡니다. 또는 **영역 중복 저장소(ZRS 미리 보기)**, **GRS(지역 중복 저장소)** 또는 **RA-GRS(읽기 액세스 지역 중복 저장소)**를 선택할 수 있습니다.
5. **배포 모델**, **성능**, **보안 전송 필요** 등의 필드는 기본값을 그대로 둡니다.
6. 저장소 계정을 만들려는 구독을 선택합니다.
7. **리소스 그룹** 섹션에서 **기존 항목 사용**을 선택한 다음 이전 섹션에서 만든 리소스 그룹을 선택합니다.
8. 새 저장소 계정의 위치를 선택합니다.
9. **만들기** 를 클릭하여 저장소 계정을 만들 수 있습니다.      

![Azure Portal에서 저장소 계정을 표시하는 스크린샷](./media/storage-quickstart-create-account/create-account-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 LRS(로컬 중복 저장소)를 사용하여 범용 v2 저장소 계정을 만들려면 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 명령을 사용합니다. 

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

영역 중복 저장소(ZRS 미리 보기), GRS(지역 중복 저장소) 또는 RA-GRS(읽기 액세스 지역 중복 저장소)를 사용하여 범용 v2 저장소 계정을 만들려면 아래 표에서 **SkuName**매개 변수에 대해 원하는 값을 대체합니다. 

|복제 옵션  |SkuName 매개 변수  |
|---------|---------|
|LRS(로컬 중복 저장소)     |Standard_LRS         |
|ZRS(영역 중복 저장소)     |Standard_ZRS         |
|GRS(지역 중복 저장소)     |Standard_GRS         |
|읽기 액세스 GRS(지역 중복 저장소)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 로컬 중복 저장소를 사용하여 범용 v2 저장소 계정을 만들려면 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용합니다.

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

영역 중복 저장소(ZRS 미리 보기), GRS(지역 중복 저장소) 또는 RA-GRS(읽기 액세스 지역 중복 저장소)를 사용하여 범용 v2 저장소 계정을 만들려면 아래 표에서 **sku**매개 변수에 대해 원하는 값을 대체합니다. 

|복제 옵션  |sku 매개 변수  |
|---------|---------|
|LRS(로컬 중복 저장소)     |Standard_LRS         |
|ZRS(영역 중복 저장소)     |Standard_ZRS         |
|GRS(지역 중복 저장소)     |Standard_GRS         |
|읽기 액세스 GRS(지역 중복 저장소)     |Standard_RAGRS         |

---

> [!NOTE]
> [영역 중복 저장소](https://azure.microsoft.com/blog/announcing-public-preview-of-azure-zone-redundant-storage/preview/)는 현재 미리 보기로 제공되며 다음 위치에서만 사용할 수 있습니다.
>    - 미국 동부 2
>    - 미국 중부
>    - 프랑스 중부(이 지역은 현재 미리 보기로 제공됩니다. 액세스를 요청하려면 [프랑스에서 현재 열려 있는 Azure 가용성 영역을 사용하여 Microsoft Azure 미리 보기](https://azure.microsoft.com/blog/microsoft-azure-preview-with-azure-availability-zones-now-open-in-france)를 참조하세요.)
    
사용 가능한 다양한 유형의 복제에 대한 자세한 내용은 [저장소 복제 옵션](storage-redundancy.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스를 정리하려면 간단히 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 연결된 저장소 계정과 기타 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

# <a name="portaltabportal"></a>[포털](#tab/portal)

Azure Portal을 사용하여 리소스 그룹을 제거하려면

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 열고 **리소스 그룹**을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스 그룹을 찾아 목록 오른쪽에 있는 **자세히** 단추(**...** )를 마우스 오른쪽 단추로 클릭합니다.
3. **리소스 그룹 삭제**를 선택하고 확인합니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

새 저장소 계정을 포함하여 리소스 그룹과 관련 리소스를 제거하려면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용합니다. 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

새 저장소 계정을 포함하여 리소스 그룹과 관련 리소스를 제거하려면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 범용 표준 저장소 계정을 만들었습니다. 저장소 계정에서 Blob을 업로드 및 다운로드하는 방법을 알아보려면 계속해서 Blob 저장소 빠른 시작을 진행합니다.

# <a name="portaltabportal"></a>[포털](#tab/portal)

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 Azure Blob Storage에서 개체 전송](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Blob Storage에서 개체 전송](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Azure Blob Storage에서 개체 전송](../blobs/storage-quickstart-blobs-cli.md)

---
