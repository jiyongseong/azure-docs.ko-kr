---
title: Azure 스택 저장소에 대 한 도구 | Microsoft Docs
description: Azure 스택 저장소 데이터에 대 한 자세한 내용은 전송 도구
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 3d9bd187a70e8b8292e9c47497c2c6b13764045d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604729"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Azure 스택 저장소에 대 한 데이터 전송 도구를 사용 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Microsoft Azure 스택 디스크, blob, 테이블, 큐 및 계정 관리 기능에 대 한 저장소 서비스를 제공합니다. Azure 저장소 도구 집합을 관리 하거나 Azure 스택 저장소에서 데이터를 이동 하려는 경우 사용할 수 있습니다. 이 문서에서는 사용 가능한 도구에 대 한 개요를 제공 합니다.

요구 사항에는 다음 도구 중 적합 한 경우를 결정 합니다.

* [AZCopy](#azcopy)

    저장소별, 명령줄 유틸리티에서에서 데이터를 복사 한 개체가 다른 개체에 저장소 계정 내에서 또는 저장소 계정 간에 다운로드할 수 있습니다.

* [Azure PowerShell](#azure-powershell)

    , 작업 기반 명령줄 셸이자 스크립팅 언어 시스템 관리를 위해 특별히 설계 되었습니다.

* [Azure CLI](#azure-cli)

    Azure 및 Azure 스택 플랫폼을 사용 하기 위한 명령 집합을 제공 하는 오픈 소스, 플랫폼 간 도구입니다.

* [Microsoft 저장소 탐색기](#microsoft-azure-storage-explorer)

    사용자 인터페이스를 사용 하기 쉬운 독립 실행형 앱입니다.

Azure 및 Azure 스택 간에 저장소 서비스 차이 때문에 다음 섹션에서 설명 하는 각 도구에 대 한 몇 가지 특정 요구 사항이 있을 수 있습니다. Azure 스택 저장소와 Azure 저장소 간의 비교를 참조 하십시오. [Azure 스택 저장소: 차이점과 고려 사항을](azure-stack-acs-differences.md)합니다.

## <a name="azcopy"></a>AzCopy

AzCopy는 간단한 명령을 사용 하 여 최적의 성능으로 Microsoft Azure blob 및 테이블 저장소에서 데이터를 복사 하도록 명령줄 유틸리티입니다. 저장소 계정 내에서나 저장소 계정 사이에서 개체 간에 데이터 복사할 수 있습니다.

### <a name="download-and-install-azcopy"></a>AzCopy 다운로드 및 설치

AzCopy 유틸리티의 두 버전: Windows 및 Linux에서 AzCopy에 AzCopy 합니다.

 - **Windows에서 AzCopy**
    - Azure 스택에 대 한 AzCopy의 지원 되는 버전을 다운로드 합니다. 설치 하 고 Azure와 같은 방식으로 Azure 스택에 AzCopy를 사용할 수 있습니다. 자세한 내용은 참고 [Windows에서 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)합니다.
        - 1802 업데이트 또는 새 버전에 대 한 [AzCopy 7.1.0 다운로드](https://aka.ms/azcopyforazurestack20170417)합니다.
        - 이전 버전에 대 한 [AzCopy 5.0.0 다운로드](https://aka.ms/azcopyforazurestack20170417)합니다.

 - **Linux에서 AzCopy**

    - Linux에서 AzCopy는 Azure 스택 1802 업데이트 또는 최신 버전을 지원합니다. 설치 하 고 Azure와 같은 방식으로 Azure 스택에 AzCopy를 사용할 수 있습니다. 자세한 내용은 참고 [Linux에서 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)합니다.

### <a name="azcopy-command-examples-for-data-transfer"></a>데이터 전송에 대 한 AzCopy 명령 예제

다음 예제에서는 Azure 스택 blob에서 데이터를 복사 하기 위한 일반적인 시나리오를 따릅니다. 자세한 내용은 참고 [Windows에서 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) 및 [Linux에서 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)합니다.

### <a name="download-all-blobs-to-a-local-disk"></a>로컬 디스크에 모든 blob를 다운로드 합니다.

**Windows**

````AzCopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>가상 디렉터리에 단일 파일 업로드

**Windows**

```AzCopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Azure 및 Azure 스택 저장소 간에 데이터를 이동

Azure 저장소 및 Azure 스택 사이의 비동기 데이터 전송을 지원 되지 않습니다. 사용 하 여 전송을 지정 하는 **/SyncCopy** 또는 **-동기화 복사** 옵션입니다.

**Windows**

````AzCopy
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Azcopy 알려진 문제

 - 파일 저장소에 대해 AzCopy 연산을 사용할 수 없는 경우 파일 저장소가 아직 사용할 수 없기 때문에 Azure 스택
 - Azure 저장소 및 Azure 스택 사이의 비동기 데이터 전송을 지원 되지 않습니다. 사용 하 여 전송을 지정할 수는 **/SyncCopy** 옵션 데이터를 복사 합니다.
 - Linux 버전의 Azcopy 1802 업데이트 또는 이후 버전에만 지원합니다. 및 테이블 서비스 지원 하지 않습니다.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 모두 Azure 및 Azure 스택 서비스를 관리 하기 위한 cmdlet을 제공 하는 모듈입니다. 이기는, 작업 기반 명령줄 셸이자 스크립팅 언어 시스템 관리를 위해 특별히 설계 되었습니다.

### <a name="install-and-configure-powershell-for-azure-stack"></a>PowerShell 설치 및 구성에 대 한 Azure 스택

Azure 스택 호환 Azure PowerShell 모듈은 Azure 스택이 작동 해야 합니다. 자세한 내용은 참조 [Azure 스택 위한 PowerShell 설치](azure-stack-powershell-install.md) 및 [Azure 스택 사용자의 PowerShell 환경을 구성](azure-stack-powershell-configure-user.md) 자세한 합니다.

### <a name="powershell-sample-script-for-azure-stack"></a>Azure 스택에 대 한 PowerShell 예제 스크립트 

이 샘플 성공적으로 사용 한다고 가정 [Azure 스택에 대 한 설치 된 PowerShell](azure-stack-powershell-install.md)합니다. 이 스크립트는 구성을 완료 하 고 Azure 스택 테 넌 트에 게 로컬 PowerShell 환경에 사용자 계정을 추가 하려면 자격 증명을 요청 하는 데 도움이 됩니다. 그런 다음 스크립트는 기본 Azure 구독을 설정, Azure에서 새 저장소 계정을 만드는,이 새 저장소 계정에서 새 컨테이너 만들기 및 해당 컨테이너에 기존 이미지 파일 (blob)을 업로드 합니다. 해당 컨테이너의 모든 blob를 나열 하는 스크립트, 후 로컬 컴퓨터에 새 대상 디렉터리를 만들 되며 이미지 파일을 다운로드 합니다.

1. 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.
2. 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다.
3. 열기 **Windows PowerShell ISE** 및 **관리자 권한으로 실행**, 클릭 **파일** > **새로** 새 스크립트 파일을 만듭니다.
4. 아래의 스크립트를 복사한 새 스크립트 파일에 붙여 넣습니다.
5. 구성 설정에 따라 스크립트 변수를 업데이트 합니다.
   > [!NOTE]
   > 이 스크립트에 대 한 루트 디렉터리에서 실행할 **AzureStack_Tools**합니다.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
````

### <a name="powershell-known-issues"></a>PowerShell의 알려진 문제

Azure 스택에 대 한 호환 되는 Azure PowerShell 모듈은 현재 버전 1.3.0입니다. 최신 버전의 Azure PowerShell에서 차이가 있습니다. 이러한 차이 저장소 서비스 작업에 영향을 줍니다.

* 반환 값 형식을 `Get-AzureRmStorageAccountKey` 버전 1.3.0에 두 개의 속성이: `Key1` 및 `Key2`, 현재 Azure 버전은 모든 account 키를 포함 하는 배열을 반환 하는 반면, 합니다.

   ```
   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```

   자세한 내용은 참조 [Get AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0)합니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI에는 Azure의 Azure 리소스를 관리 하기 위한 명령줄 환경을 합니다. MacOS, Linux 및 Windows에 설치 하 고 명령줄에서 실행할 수 있습니다.

관리 하 고 명령줄에서 Azure 리소스를 관리 하 고 Azure 리소스 관리자에 대해 작동 하는 자동화 스크립트를 작성 하기 위한 azure CLI 최적화 됩니다. 여러 가지 다양 한 데이터 액세스를 포함 하 여 Azure 스택 포털의 동일한 기능을 제공 합니다.

Azure 스택 Azure CLI 버전 2.0 필요합니다. 설치 하 고 Azure 스택에 Azure CLI를 구성 하는 방법에 대 한 자세한 내용은 참조 [설치 하 고 Azure 스택 CLI 구성](azure-stack-version-profiles-azurecli2.md)합니다. Azure 스택 저장소 계정의 리소스를 사용 하는 여러 작업을 수행할 Azure CLI 2.0을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure CLI2.0를 사용 하 여 Azure 저장소와](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure 스택에 대 한 azure CLI 샘플 스크립트

CLI 설치 및 구성을 완료 하면 Azure 스택 저장소 리소스와 상호 작용 하는 작은 셸 샘플 스크립트를 작성 하려면 다음 단계를 시도할 수 있습니다. 스크립트는 다음 작업을 완료합니다.

* 저장소 계정에 새 컨테이너를 만듭니다.
* 컨테이너에 (blob)으로 기존 파일을 업로드합니다.
* 컨테이너의 모든 blob을 나열합니다.
* 대상 지정 하는 로컬 컴퓨터에 파일을 다운로드 합니다.

이 스크립트를 실행 하기 전에 성공적으로 연결 하 고 수 있는 Azure 스택 대상에 로그인 되어 있는지 확인 합니다.

1. 원하는 텍스트 편집기를 연 다음 앞서의 스크립트를 복사하여 편집기에 붙여넣습니다.
2. 스크립트의 변수 구성 설정을 반영 하도록 업데이트 합니다.
3. 필요한 변수를 업데이트 한 후 스크립트를 저장 하 고 편집기를 종료 합니다. 다음 단계에서는 스크립트 이름으로 **my_storage_sample.sh**를 지정했다고 가정합니다.
4. 필요한 경우 다음과 같이 스크립트를 실행 가능으로 표시합니다. `chmod +x my_storage_sample.sh`
5. 스크립트를 실행합니다. 예를 들어 Bash에서는 `./my_storage_sample.sh`입니다.

```bash
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
````

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 저장소 탐색기

Microsoft Azure 저장소 탐색기는 Microsoft에서 독립 실행형 앱입니다. 쉽게 사용할 Azure 저장소와 Azure 스택 저장소가 모두 데이터 창, macOS 및 Linux 컴퓨터에 있습니다. 쉽게 Azure 스택 저장소 데이터를 관리 하는 방법을 원하는 다음 Microsoft Azure 저장소 탐색기를 사용 하십시오.

* Azure 스택 사용 하려면 Azure 저장소 탐색기 구성에 대 한 자세한 참조 [스택 Azure 구독에 저장소 탐색기 연결](azure-stack-storage-connect-se.md)합니다.
* Microsoft Azure 저장소 탐색기에 대 한 자세한 참조 [저장소 탐색기를 시작 하려면](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>다음 단계

* [저장소 탐색기는 스택 Azure 구독에 연결](azure-stack-storage-connect-se.md)
* [저장소 탐색기를 시작](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [일관 된 azure 저장소: 차이점과 고려 사항](azure-stack-acs-differences.md)
* [Microsoft Azure 저장소 소개](../../storage/common/storage-introduction.md)
