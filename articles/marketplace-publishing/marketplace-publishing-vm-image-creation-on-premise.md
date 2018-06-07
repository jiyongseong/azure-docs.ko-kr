---
title: Azure Marketplace용 온-프레미스 가상 머신 이미지 만들기 | Microsoft Docs
description: 온-프레미스 VM 이미지를 만들어 Azure Marketplace에 배포하여 다른 사용자가 구입할 수 있도록 하는 단계를 이해하고 실행합니다.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: 6b927ce6032092ce258eeebca49da0571439dbfb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Azure Marketplace를 위해 온-프레미스 가상 컴퓨터 이미지 개발
원격 데스크톱 프로토콜을 사용하여 Azure VHD(가상 하드 디스크)를 클라우드에서 직접 개발하는 것이 좋습니다. 그러나 꼭 필요한 경우 VHD를 다운로드하고 온-프레미스 인프라를 사용하여 개발할 수 있습니다.  

온-프레미스 개발을 위해서는 만든 VM의 운영 체제 VHD를 다운로드해야 합니다. 다음 단계는 위의 3.3단계의 일부로 수행됩니다.  

## <a name="download-a-vhd-image"></a>VHD 이미지 다운로드
### <a name="locate-a-blob-url"></a>Blob URL 찾기
VHD를 다운로드하려면 먼저 운영 체제 디스크에 대한 Blob URL을 찾습니다.

새 [Microsoft Azure 포털](https://portal.azure.com)에서 Blob URL을 찾습니다.

1. **찾아보기** > **VM**으로 이동하고 배포된 VM을 선택합니다.
2. **구성** 아래에서 **디스크** 타일을 선택하면 디스크 블레이드가 열립니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. **OS 디스크**를 선택하면 VHD 위치를 포함하여 디스크 속성을 표시하는 다른 블레이드가 열립니다.
4. 이 Blob URL을 복사합니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. 이제 백업 디스크를 삭제하지 않고 배포된 VM을 삭제합니다. 또한 VM을 삭제하는 대신 중지할 수 있습니다. VM 실행 중에는 운영 체제 VHD를 다운로드하지 마세요.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>VHD 다운로드
Blob URL을 알고 있는 경우 [Azure 포털](http://manage.windowsazure.com/) 또는 PowerShell을 사용하여 VHD를 다운로드할 수 있습니다.  

> [!NOTE]
> 이 가이드를 작성한 시점에는 VHD를 다운로드하는 기능이 새 Microsoft Azure 포털에 아직 없습니다.  
> 
> 

**현재 [Azure Portal](http://manage.windowsazure.com/)을 통해 운영 체제 VHD 다운로드**

1. 아직 로그인하지 않은 경우 Azure 포털에 로그인합니다.
2. **저장소** 탭을 클릭합니다.
3. VHD가 저장된 저장소 계정을 선택합니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. 그러면 저장소 계정 속성이 표시됩니다. **컨테이너** 탭을 선택합니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. VHD가 저장되는 컨테이너를 선택합니다. 기본적으로 포털에서 만들 때 VHD는 vhd 컨테이너에 저장됩니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. 저장된 URL과 비교하여 올바른 운영 체제 VHD를 선택합니다.
7. **다운로드**를 클릭합니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>PowerShell을 사용하여 VHD 다운로드
Azure 포털 사용 외에도 [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) cmdlet을 사용하여 운영 체제 VHD를 다운로드할 수 있습니다.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
예: Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> **Save-AzureVhd**에는 다운로드에 사용 가능한 대역폭을 가능한 효과적으로 사용할 수 있도록 병렬 처리를 늘리는 데 사용할 수 있는 **NumberOfThreads** 옵션도 있습니다.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Azure 저장소 계정에 VHD 업로드
온-프레미스에서 VHD를 준비하는 경우 Azure의 저장소 계정에 업로드해야 합니다. 이 단계는 온-프레미스에서 VHD를 만든 후, VM 이미지에 대한 인증을 가져오기 전에 수행됩니다.

### <a name="create-a-storage-account-and-container"></a>저장소 계정 및 컨테이너 만들기
VHD를 미국의 지역에 있는 저장소 계정에 업로드하는 것이 좋습니다. 단일 SKU에 대한 모든 VHD는 단일 저장소 계정 내에서 단일 컨테이너에 배치되어야 합니다.

저장소 계정을 만들려면 [Microsoft Azure 포털](https://portal.azure.com/), PowerShell 또는 Linux 명령줄 도구를 사용할 수 있습니다.  

**Microsoft Azure 포털에서 저장소 계정 만들기**

1. **리소스 만들기**를 클릭합니다.
2. **저장소**를 선택합니다.
3. 저장소 계정 이름을 입력하고 위치를 선택합니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. **만들기**를 클릭합니다.
5. 만든 저장소 계정에 대한 블레이드가 열립니다. 그렇지 않은 경우 **찾아보기** > **저장소 계정**을 선택합니다. Storage 계정 블레이드에서 만든 Storage 계정을 선택합니다.
6. **컨테이너**를 선택합니다.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. 컨테이너 블레이드에서 **추가**를 선택하고 컨테이너 이름 및 컨테이너 권한을 입력합니다. 컨테이너 권한에 대해 **개인** 을 선택합니다.

> [!TIP]
> 게시를 계획 중인 SKU당 하나의 컨테이너를 만드는 것이 좋습니다.
> 
> 

  ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>PowerShell을 사용하여 저장소 계정 만들기
PowerShell을 사용하여 [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) cmdlet를 사용 저장소 계정을 만듭니다.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

그런 다음 [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) cmdlet를 사용하여 해당 저장소 계정 내에 컨테이너를 만들 수 있습니다.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> 이러한 명령은 현재 저장소 계정 컨텍스트가 PowerShell에 이미 설정되어 있다고 가정합니다.   PowerShell 설정에 대한 자세한 내용은 [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md) 을 참조하세요.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Mac 및 Linux용 명령줄 도구를 사용하여 저장소 계정 만들기
> [Linux 명령줄 도구](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 다음과 같이 저장소 계정을 만듭니다.
> 
> 

        azure storage account create mystorageaccount --location "West US"

다음과 같이 컨테이너를 만듭니다.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>VHD 업로드
저장소 계정 및 컨테이너를 만든 후에는 준비된 VHD를 업로드할 수 있습니다. PowerShell, Linux 명령줄 도구 또는 기타 Azure Storage 관리 도구를 사용할 수 있습니다.

### <a name="upload-a-vhd-via-powershell"></a>PowerShell 통해 VHD 업로드
[Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) cmdlet를 사용합니다.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Mac 및 Linux용 명령줄 도구를 사용하여 VHD 업로드
[Linux 명령줄 도구](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)에서 azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>를 사용합니다.

## <a name="see-also"></a>참고 항목
* [Marketplace에 대한 가상 머신 이미지 만들기](marketplace-publishing-vm-image-creation.md)
* [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)

