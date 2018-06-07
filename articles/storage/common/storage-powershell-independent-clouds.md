---
title: Azure PowerShell을 사용하여 Azure 독립 클라우드에서 Storage 관리 | Microsoft Docs
description: Azure PowerShell을 사용한 중국 클라우드, 정부 클라우드, 독일 클라우드의 Storage 관리
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: 3eecf8918e6628071e44cc588b1826df68a00f10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>PowerShell을 사용하여 Azure 독립 클라우드에서 Storage 관리

대부분의 사람들은 글로벌 Azure 배포에 Azure Public Cloud를 사용합니다. 또한 통치권 등의 사유로 인한 몇 가지 Microsoft Azure 독립 배포도 존재합니다. 이러한 독립 배포를 "환경"이라고 칭합니다. 다음 목록에서는 현재 사용할 수 있는 독립 클라우드에 대해 자세히 설명합니다.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [중국 21Vianet이 운영하는 Azure China Cloud](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>독립 클라우드 사용 

독립 클라우드 중 한 곳에서 Azure Storage를 사용하려면 Azure Public이 아닌 해당 클라우드에 연결합니다. Azure Public이 아닌 독립 클라우드 중 하나를 사용하려면

* 연결할 *환경*을 지정합니다.
* 사용 가능한 지역을 결정하여 사용합니다.
* Azure Public과 달리 정확한 끝점 접미사를 사용합니다.

이 예제에는 Azure PowerShell 모듈 버전 4.4.0 이상이 필요합니다. PowerShell 창에서 `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 확인합니다. 나타나는 항목이 없거나 업그레이드가 필요한 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인

[Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) cmdlet을 실행하여 사용 가능한 Azure 환경을 확인합니다.
   
```powershell
Get-AzureRmEnvironment
```

연결하려는 클라우드에 대한 액세스 권한이 있는 계정에 로그인하고 환경을 설정합니다. 이 예제에서는 Azure Government Cloud를 사용하는 계정에 로그인하는 방법을 보여 줍니다.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

중국 클라우드에 액세스하려면 **AzureChinaCloud** 환경을 사용합니다. 독일 클라우드에 액세스하려면 **AzureGermanCloud**를 사용합니다.

이 시점에서 저장소 계정을 만들 위치나 다른 리소스 목록이 필요한 경우 [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation)을 사용하여 선택한 클라우드에 사용 가능한 위치를 쿼리할 수 있습니다.

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

다음 표에서는 독일 클라우드에 대해 반환되는 위치를 보여 줍니다.

|위치 | DisplayName |
|----|----|
| germanycentral | 독일 중부|
| germanynortheast | 독일 북동부 | 


## <a name="endpoint-suffix"></a>끝점 접미사

이러한 각각의 환경에 대한 끝점 접미사는 Azure Public 끝점과 다릅니다. 예를 들어 Azure Public의 Blob 끝점 접미사는 **blob.core.windows.net**입니다. 독일 클라우드의 Blob 끝점 접미사는 **blob.core.usgovcloudapi.net**합니다. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Get-AzureRMEnvironment를 사용하여 끝점 가져오기 

[Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment)를 사용하여 끝점 접미사를 검색합니다. 끝점은 환경의 *StorageEndpointSuffix* 속성입니다. 다음 코드 조각에서는 이 방법을 보여 줍니다. 이 명령은 모두 "core.cloudapp.net" 또는 "core.cloudapi.de" 등과 유사한 항목을 반환합니다. 해당 서비스에 액세스하는 저장소 서비스에 이 항목을 추가합니다. 예를 들어 "queue.core.cloudapi.de"는 독일 클라우드의 큐 서비스에 액세스하게 됩니다.

이 코드 조각은 각각에 대한 모든 환경과 끝점 접미사를 검색합니다.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

이 명령은 다음 결과를 반환합니다.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

지정된 환경에 대해 모든 속성을 검색하려면 **Get-AzureRmEnvironment**를 호출하고 클라우드 이름을 지정합니다. 이 코드 조각은 속성 목록을 반환합니다. 목록에서 **StorageEndpointSuffix**를 찾습니다. 다음은 독일 클라우드에 대한 예제입니다.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

결과는 다음과 유사합니다.

|속성 이름|값|
|----|----|
| Name | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

저장소 끝점 접미사 속성만 검색하려면 특정 클라우드를 검색하고 해당 속성 하나만 요청합니다.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

그러면 다음 정보가 반환됩니다.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>저장소 계정에서 끝점을 가져오기

끝점을 가져오기 위해 저장소 계정의 속성을 조사할 수도 있습니다. 이것은 PowerShell 스크립트에서 이미 저장소 계정을 사용하고 있을 때 유용하며 필요한 끝점만 검색할 수 있습니다. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

정부 클라우드의 저장소 계정의 경우 다음을 반환합니다. 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>환경 설정 후

이제 [Azure Storage에서 Azure PowerShell 사용](storage-powershell-guide-full.md)에서 설명된 것처럼 저장소 계정 관리 및 데이터 평면 액세스에 사용하는 것과 같은 PowerShell을 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 연습에 대해 새 리소스 그룹과 저장소 계정을 만든 후에는 리소스 그룹을 제거하여 모든 자산을 제거할 수 있습니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 사례에서는 만든 저장소 계정 및 리소스 그룹 자체가 제거됩니다.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

* [PowerShell 세션 간 사용자 로그인 유지](/powershell/azure/context-persistence)
* [Azure Government 저장소](../../azure-government/documentation-government-services-storage.md)
* [Microsoft Azure Government 개발자 가이드](../../azure-government/documentation-government-developer-guide.md)
* [Azure China Applications 개발자 노트](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Germany 설명서](../../germany/germany-welcome.md)
