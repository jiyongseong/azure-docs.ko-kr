---
title: 추가 및 제거 VM 이미지를 Azure 스택 | Microsoft Docs
description: 추가 하거나 조직의 사용자 지정 Windows 또는 Linux VM 이미지를 사용 하도록 테 넌 트를 제거 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 309bd35eabe1d5c4af9a35d5f2b879bce225a402
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604372"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>가상 컴퓨터 이미지에서에서 사용할 수 있도록 Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택에서 만들면 가상 컴퓨터 이미지 사용할 수 있는 사용자에 게 됩니다. Azure 리소스 관리자 템플릿을 이러한 이미지를 참조할 수 있습니다 또는 마켓플레이스 항목으로 Azure 마켓플레이스 UI에 추가할 수 있습니다. 유형 중 하나는 이미지 글로벌 Azure Marketplace를 사용 하거나 사용자 지정 이미지를 추가할 수 있습니다. 포털 또는 Windows PowerShell을 사용 하는 VM을 추가할 수 있습니다.

## <a name="add-a-vm-image-through-the-portal"></a>포털을 통해 VM 이미지를 추가 합니다.

> [!NOTE]
> 이 메서드를 별도로 마켓플레이스 항목을 만들 해야 합니다.

이미지를 blob 저장소 URI에서 참조할 수 있어야 합니다. VHD 형식 (하지 VHDX) Windows 또는 Linux 운영 체제 이미지를 준비 하 고 이미지를 Azure 또는 Azure 스택의 저장소 계정에 업로드 합니다. 이미지에 이미 Azure 또는 Azure 스택에서 blob 저장소에 업로드, 1 단계를 건너뛸 수 있습니다.

1. [리소스 관리자 배포에 대 한 Windows VM 이미지를 Azure에 업로드](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) 또는 Linux 이미지에 대 한에 설명 된 지침에 따라 [배포 Linux 가상 컴퓨터 Azure 스택에](azure-stack-linux.md)합니다. 이미지를 업로드 하기 전에 다음 사항을 고려해 야 할 주요은:

   - Azure 스택 VHD 형식의 고정된 디스크를 지원합니다. 고정된 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장 되므로 파일 내에 선형적으로 논리적 디스크를 구조 합니다. Blob 끝에는 작은 바닥글에서는 VHD의 속성을 설명합니다. 디스크의 해결 여부를 확인 하려면 사용 하 여는 [GET-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 명령입니다.  

    > [!IMPORTANT]
    >  Azure 스택 동적 디스크 Vhd를 지원 하지 않습니다. 동적 디스크는 VM에 연결 하는 크기 조정 실패 상태에 VM이 종료 됩니다. 이 문제를 해결 하려면 VM의 디스크를 저장소 계정에 VHD blob을 삭제 하지 않고 VM을 삭제 합니다. 동적 디스크에서 VHD 고정된 디스크와 가상 컴퓨터를 다시 만들를 변환 합니다.

   * Azure 스택 blob 저장소 보다 더 적은 시간이 이미지 스택 Azure 이미지 리포지토리에 푸시를 사용 하기 때문에 blob 저장소를 Azure에 이미지를 업로드 하 더 효율적입니다.

   * 업로드 하는 경우는 [Windows VM 이미지](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), 대체 해야는 **Azure에 로그인** 단계와 함께 [Azure 스택 운영자의 PowerShell 환경을 구성](azure-stack-powershell-configure-admin.md) 단계입니다.  

   * Blob 저장소 URI 이미지를 업로드 하기를 기록해 둡니다. Blob 저장소 URI 형식은 다음과 같습니다: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd 합니다.

   * Blob를 익명으로 액세스할 수 있도록로 저장소 계정 blob 컨테이너는 VM 이미지의 VHD 업로드 되었습니다. 선택 **Blob**를 선택한 후 **액세스 정책**합니다. 필요에 따라 대신는 컨테이너에 대 한 공유 액세스 서명 생성 및 blob URI의 일부분으로 포함 수 있습니다.

   ![저장소 계정 blob로 이동](./media/azure-stack-add-vm-image/image1.png)

   ![Public에 대 한 집합 blob 액세스](./media/azure-stack-add-vm-image/image2.png)

2. 연산자와 Azure 스택에 로그인 합니다. 메뉴에서 선택 **더 많은 서비스** > **리소스 공급자**합니다. 그런 다음 선택 **계산** > **VM 이미지** > **추가**합니다.

3. 아래 **VM 이미지를 추가할**, 게시자, 제품, SKU, 가상 컴퓨터 이미지의 버전을 입력 합니다. 이러한 이름 세그먼트는 리소스 관리자 템플릿에서 VM 이미지를 참조 하십시오. 선택 하는 **osType** 올바르게 값입니다. 에 대 한 **OS 디스크 Blob URI**를 이미지 업로드 된 Blob URI를 입력 합니다. 그런 다음 선택 **만들기** VM 이미지 만들기를 시작할 수 있습니다.

   ![이미지를 만들기 시작 합니다.](./media/azure-stack-add-vm-image/image4.png)

   이미지는 성공적으로 만들어지면로 VM 이미지 상태 변경 **Succeeded**합니다.

4. 가상 컴퓨터 이미지 보다 쉽게를 사용 하려면 사용자가 사용할 수 있도록 UI에는 것이 좋습니다를 [마켓플레이스 항목을 만듭니다.](azure-stack-create-and-publish-marketplace-item.md)합니다.

## <a name="remove-a-vm-image-through-the-portal"></a>포털을 통해 VM 이미지를 제거 합니다.

1. 관리 포털을 열고 [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)합니다.

2. 선택 **마켓플레이스 관리**, 한 다음 삭제 하려면 VM을 선택 합니다.

3. **삭제**를 클릭합니다.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>PowerShell을 사용 하 여 시장 VM 이미지 추가

1. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.  

2. 운영자 Azure 스택에 로그인 합니다. 자세한 내용은 [연산자로 스택 Azure에 로그인](azure-stack-powershell-configure-admin.md)합니다.

3. 프롬프트를 관리자 권한으로 PowerShell을 열고 실행 합니다.

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  **추가 AzsPlatformimage** cmdlet이 VM 이미지를 참조 하 여 Azure 리소스 관리자 템플릿을 사용 하는 값을 지정 합니다. 값은 다음과 같습니다.
  - **publisher**  
    예: `Canonical`  
    사용자가 이미지를 배포할 때 사용 되는 VM 이미지의 게시자 이름 세그먼트입니다. 예로 **Microsoft**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **offer**  
    예: `UbuntuServer`  
    사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 제안 이름 세그먼트입니다. 예로 **windows Server**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **sku**  
    예: `14.04.3-LTS`  
    사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 SKU 이름 세그먼트입니다. 예로 **Datacenter2016**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **version**  
    예: `1.0.0`  
    사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 버전입니다. 이 버전은 형식에서  *\#.\#합니다. \#*. 예로 **1.0.0**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **osType**  
    예: `Linux`  
    이미지의 osType 납입이 되어야 **Windows** 또는 **Linux**합니다.  
  - **OSUri**  
    예: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Blob 저장소 URI를 지정할 수 있습니다에 대 한 프로그램 `osDisk`합니다.  

    자세한 내용은 참조에 대 한 PowerShell 참조의 [추가 AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet 및 [새로 DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>PowerShell을 사용 하 여 사용자 지정 VM 이미지를 시장에 추가

1. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. 사용 하는 경우 **Active Directory Federation Services**를 다음 cmdlet을 사용 합니다.

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. 운영자 Azure 스택에 로그인 합니다. 자세한 내용은 [연산자로 스택 Azure에 로그인](azure-stack-powershell-configure-admin.md)합니다.

4. 전역 Azure 또는 Azure 스택을 사용자 지정 VM 이미지를 저장할 저장소 계정을 만듭니다. 에 대 한 참조 [퀵 스타트: 업로드, 다운로드 및 Azure 포털을 사용 하 여 blob 나열](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)합니다.

5. VHD 형식 (VHDX) Windows 또는 Linux 운영 체제 이미지를 준비 하 고 저장소 계정에 이미지를 업로드 합니다. PowerShell에서 VM 이미지를 검색할 수 있는 URI를 가져옵니다.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (선택 사항) VM 이미지의 일부로 데이터 디스크의 배열을 업로드할 수 있습니다. 새로 만들기-DataDiskObject cmdlet을 사용 하 여 데이터 디스크를 만듭니다. PowerShell에서 관리자 권한 프롬프트를 열고 실행 합니다.

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. 프롬프트를 관리자 권한으로 PowerShell을 열고 실행 합니다.

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    추가 AzsPlatformimage cmdlet 및 새로운 DataDiskObject cmdlet에 대 한 자세한 내용은 참조는 Microsoft PowerShell [Azure 스택 연산자 모듈 문서](https://docs.microsoft.com/powershell/module/)합니다.

## <a name="remove-a-vm-image-by-using-powershell"></a>PowerShell을 사용 하 여 VM 이미지를 제거 합니다.

가상 컴퓨터 이미지 업로드 필요 없는 경우 다음 cmdlet을 사용 하 여 시장에서 삭제할 수 없습니다.

1. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.

2. 운영자 Azure 스택에 로그인 합니다.

3. 프롬프트를 관리자 권한으로 PowerShell을 열고 실행 합니다.

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  **제거 AzsPlatformImage** cmdlet이 VM 이미지를 참조 하 여 Azure 리소스 관리자 템플릿을 사용 하는 값을 지정 합니다. 값은 다음과 같습니다.
  - **publisher**  
    예: `Canonical`  
    사용자가 이미지를 배포할 때 사용 되는 VM 이미지의 게시자 이름 세그먼트입니다. 예로 **Microsoft**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **offer**  
    예: `UbuntuServer`  
    사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 제안 이름 세그먼트입니다. 예로 **windows Server**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **sku**  
    예: `14.04.3-LTS`  
    사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 SKU 이름 세그먼트입니다. 예로 **Datacenter2016**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
  - **version**  
    예: `1.0.0`  
    사용자가 VM 이미지를 배포할 때 사용 되는 VM 이미지의 버전입니다. 이 버전은 형식에서  *\#.\#합니다. \#*. 예로 **1.0.0**합니다. 이 필드에는 공백이 나 다른 특수 문자를 포함 하지 마십시오.  
    
    TheRemove AzsPlatformImage cmdlet에 대 한 자세한 내용은 참조는 Microsoft PowerShell [Azure 스택 연산자 모듈 문서](https://docs.microsoft.com/powershell/module/)합니다.

## <a name="next-steps"></a>다음 단계

[가상 머신 프로비전](azure-stack-provision-vm.md)