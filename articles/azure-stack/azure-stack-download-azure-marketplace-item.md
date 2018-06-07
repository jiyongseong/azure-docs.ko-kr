---
title: Azure에서 마켓플레이스 항목을 다운로드 | Microsoft Docs
description: 클라우드 운영자 내 Azure 스택 배포 환경에 Azure에서 마켓플레이스 항목을 다운로드할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5d403f7c1d0fff466f6c0fb9942ec777ab820eab
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604535"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure에서 Azure 스택에 마켓플레이스 항목을 다운로드

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

클라우드 연산자로 Azure 마켓플레이스에서 항목을 다운로드 하 고 Azure 스택에서 사용할 수 있도록 합니다. 사전 테스트 및 Azure 스택을 사용 하 여 지원 되는 Azure 마켓플레이스 항목의 큐 레이트 목록에서 항목이 선택할 수 있습니다. 하므로 계속 새 내용에 대 한 다시 확인, 추가 항목은 자주이 목록에 추가 됩니다. 

Azure 마켓플레이스에 연결 하기 위한 두 가지 시나리오가 있습니다. 

- **연결 된 시나리오** -인터넷에 연결 하도록 Azure 스택 환경을 필요로 하 합니다. Azure 스택 포털을 사용 하 여를 찾아서 항목을 다운로드 합니다. 
- **연결 되어 있지 않거나 부분적으로 연결 된 시나리오** -필요한 마켓플레이스 배포 도구를 사용 하 여 마켓플레이스 항목을 다운로드 하는 인터넷에 액세스할 수 있습니다. 그런 다음 연결이 끊긴된 Azure 스택 설치에 다운로드를 전송합니다. 이 시나리오에서는 PowerShell을 사용 합니다.

참조 [Azure 스택에 대 한 Azure 마켓플레이스 항목](azure-stack-marketplace-azure-items.md) 목록은 마켓플레이스 항목을 다운로드할 수 있습니다.


## <a name="connected-scenario"></a>연결 된 시나리오
Azure 스택 인터넷에 연결 된 경우에 마켓플레이스 항목을 다운로드 하는 관리 포털을 사용할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
Azure 스택 배포 해야 인터넷에 연결 하 고 해야 [Azure에 등록](azure-stack-register.md)합니다.

### <a name="use-the-portal-to-download-marketplace-items"></a>마켓플레이스 항목을 다운로드 하 여 포털을 사용 하 여  
1. Azure 스택 관리자 포털에 로그인 합니다.

2.  마켓플레이스 항목을 다운로드 하기 전에 사용 가능한 저장 공간을 검토 합니다. 이상에서는 다운로드에 대 한 항목을 선택 하면 사용 가능한 저장소 용량을 다운로드 크기를 비교할 수 있습니다. 용량 제한 되는 경우에 대 한 옵션을 고려 [사용 가능한 공간을 관리](azure-stack-manage-storage-shares.md#manage-available-space)합니다. 

    사용 가능한 공간을 검토 하려면 **지역 관리** 탐색 하 고 다음으로 이동 하려는 지역을 선택 **리소스 공급자** > **저장소**합니다.

    ![저장소 공간을 검토](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Azure 스택 마켓플레이스를 열고 Azure에 연결 합니다. 이 위해 선택 **마켓플레이스 관리**를 선택한 후 **Azure에서 추가**합니다.

    ![Azure에서 추가](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    포털에는 Azure Marketplace에서 다운로드할 수 있는 항목의 목록을 표시합니다. 해당 설명 및 해당 다운로드 크기를 포함 한,에 대 한 추가 정보를 볼 각 항목을 클릭할 수 있습니다. 

    ![마켓플레이스 목록](media/azure-stack-download-azure-marketplace-item/image03.png)

4. 한 다음 선택 항목을 선택 **다운로드**합니다. 다운로드 시간 달라 집니다.

    ![메시지를 다운로드 합니다.](media/azure-stack-download-azure-marketplace-item/image04.png)

    다운로드가 완료 되는 Azure 스택 연산자 또는 사용자로 새 마켓플레이스 항목을 배포할 수 있습니다.

5. 다운로드 한 항목을 배포 하려면 선택 **+ 새로 만들기**, 한 다음 새 마켓플레이스 항목에 대 한 범주 중을 검색 합니다. 다음 배포 프로세스를 시작 하려면 항목을 선택 합니다. 다른 마켓플레이스 항목에 대 한 프로세스는 다릅니다. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>연결이 끊어진 또는 부분적으로 연결 된 시나리오

PowerShell을 사용 하 여 Azure 스택 연결이 끊어진된 모드에서 한 인터넷 연결 없이 이면 및 *마켓플레이스 배포 도구* 마켓플레이스 항목을 인터넷에 연결 된 컴퓨터에 다운로드 하 합니다. 그런 다음 Azure 스택 환경에 있는 항목을 전송합니다. 연결이 끊어진된 환경에서 Azure 스택 포털을 사용 하 여 마켓플레이스 항목을 다운로드할 수 없습니다. 

연결 된 시나리오에서 마켓플레이스 배포 도구를 사용할 수도 있습니다. 

이 시나리오는 두 부분으로 분류됩니다.
- **1 부:** Azure Marketplace에서 다운로드 합니다. PowerShell을 구성 인터넷에 연결 된 컴퓨터에서 배포 도구를 다운로드 한 다음 항목 형식을 Azure 마켓플레이스를 다운로드 합니다.  
- **2 부:** 업로드 하 고 Azure 스택 마켓플레이스에 게시 합니다. Azure 스택 가져와야 한 후 Azure 스택 마켓플레이스에 게시할 Azure 스택 환경에 다운로드 한 파일을 이동 합니다.  


### <a name="prerequisites"></a>필수 조건
- Azure 스택 배포 해야 [Azure에 등록](azure-stack-register.md)합니다.  

- 인터넷에 연결 된 컴퓨터에 있어야 **Azure 스택 PowerShell 모듈 버전 1.2.11** 이상. 아직 없는 경우, [Azure 스택 특정 PowerShell 모듈 설치](azure-stack-powershell-install.md)합니다.  

- 다운로드 한 마켓플레이스 항목의 가져오기를 설정 하려면는 [Azure 스택 연산자에 대 한 PowerShell 환경](azure-stack-powershell-configure-admin.md) 구성 해야 합니다.  

- 있어야는 [저장소 계정](azure-stack-manage-storage-accounts.md) (즉, 저장소 blob) 공개적으로 액세스할 수 있는 컨테이너에 있는 Azure 스택의 합니다. 마켓플레이스 항목 갤러리 파일에 대 한 임시 저장소로 컨테이너를 사용 합니다. 저장소 계정 및 컨테이너에 익숙하지 않은 경우 참조 [blob-Azure 포털을](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Azure 설명서에서.

- 마켓플레이스 배포 도구는 첫 번째 절차 중에 다운로드 됩니다. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>마켓플레이스 배포 도구를 사용 하 여 마켓플레이스 항목을 다운로드 하려면

1. 인터넷에 연결 된 컴퓨터에서 관리자 권한으로 PowerShell 콘솔을 엽니다.

2. Azure 스택 등록를 사용 하 여 Azure 계정을 추가 합니다. 실행 하는 PowerShell에서 해당 계정을 추가 하려면 `Add-AzureRmAccount` 매개 변수 없이 합니다. Azure 계정 자격 증명을 입력 하는 메시지가 및 사용자 계정 구성에 따라 2 단계 인증을 사용 해야 할 수 있습니다.

3. 여러 구독이 있는 경우 등록에 사용 되는 항목을 선택 하려면 다음 명령을 실행 합니다.  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 다음 스크립트를 사용 하 여 마켓플레이스 배포 도구의 최신 버전을 다운로드 합니다.  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. 배포 모듈을 가져올 하 고 다음 스크립트를 실행 하 여 도구를 시작 합니다. 대체는 *대상 폴더 경로* Azure 마켓플레이스에서 다운로드 파일을 저장할 위치를 사용 합니다.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 이 도구를 실행 하는 경우 Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다. Azure 스택 등록을 사용 하는 Azure 계정에 로그인 합니다. 로그인에 성공 하면 마켓플레이스를 사용할 수 있는 항목의 목록으로 다음 이미지와 같은 화면이 나타납니다.  

   ![Azure 마켓플레이스 항목 팝업](media/azure-stack-download-azure-marketplace-item/image05.png)

7. 선택 항목을 다운로드 하 고 기록 된 *버전*합니다. (누르고 있으면 여러 이미지를 선택할 수 있습니다는 *Ctrl* 키입니다.) 참조 하 게 됩니다는 *버전* 다음 절차에서 항목을 가져올 때. 
   
   사용 하 여 이미지 목록을 필터링 할 수도 있습니다는 **조건을 추가** 옵션입니다.

8. 선택 **확인**, 한 다음 확인 하 고 약관에 동의 합니다. 

9. 다운로드가 시간 항목의 크기에 따라 달라 집니다. 다운로드가 완료 되는 항목의 스크립트에 지정 된 폴더에 다운로드 됩니다. (가상 컴퓨터)에 대 한 VHD 파일을 포함 하는 다운로드 또는 합니다. (가상 컴퓨터 확장)에 대 한 ZIP 파일입니다. 갤러리 패키지에 포함 된 *.azpkg* 형식입니다. (A *.azpkg* 패키지는 한 *.zip* 파일입니다.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>다운로드를 가져오고 Azure 스택 마켓플레이스에 게시
1. 가상 컴퓨터 이미지 또는 솔루션 템플릿을 적용 해야 하는 것에 대 한 파일 [이전에 다운로드 한](#use-the-marketplace-syndication-tool-to-download-marketplace-items) Azure 스택 환경에 로컬로 사용할 수 있어야 합니다.  

2. 가져옵니다. Azure 스택으로 VHD 파일입니다. 가상 컴퓨터 (VM) 이미지를 성공적으로 가져온 VM에 대 한 다음 정보가 있어야 합니다.
   - *버전*, 이전 절차의 7 단계에서 설명 합니다.
   - Vm에 대 한 값 *게시자*, *제공*, 및 *sku*합니다. 이러한 값을 가져오려면의 복사본 이름 바꾸기는 **.azpkg** 해당 파일 확장명을 변경 하기 위해 파일 **.zip**합니다. 텍스트 편집기를 열려면 사용할 수 있습니다 **DeploymentTemplates\CreateUiDefinition.json**합니다. .Json 파일에서 찾습니다는 *imageReference* 마켓플레이스 항목에 대 한 이러한 값을 포함 하는 섹션입니다. 다음 예제에서는이 정보 표시 되는 방법을 보여 줍니다.

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Azure 스택을 사용 하 여 이미지를 가져옵니다는 **추가 AzsPlatformimage** cmdlet. 이 cmdlet을 사용할 때는 확인 바꿔야는 *게시자*, *제공*, 및 다른 매개 변수 값을 가져올 이미지의 값입니다. 가져올 수는 *게시자*, *제공*, 및 *sku* AZPKG 파일과 함께 다운로드 되 고 대상 위치에 저장 된 텍스트 파일에서 이미지의 값 . 

   다음 예제에서는 스크립트에서 Windows Server 2016 Datacenter-서버 코어 가상 컴퓨터에 대 한 값이 사용 됩니다. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **솔루션 템플릿 정보:** 일부 템플릿에서 작은 3MB를 포함할 수 있습니다. VHD 파일 이름의 **fixed3.vhd**합니다. Azure 스택 해당 파일을 가져올 필요가 없습니다. Fixed3.vhd 합니다.  이 파일은 Azure Marketplace에 대 한 게시 요구 사항을 충족 하기 위해 일부 솔루션 서식 파일에 포함 되어 있습니다.

   템플릿 설명을 검토 하 고 다운로드 다음 솔루션 템플릿을 사용 하는 데 필요한 Vhd와 같은 추가 요구를 가져옵니다.

3. 관리자 포털을 사용 하 여 Azure 스택 Blob 저장소는 마켓플레이스 항목 패키지 (.azpkg 파일) 업로드 합니다. 패키지의 업로드 사용할 수 있도록 Azure 스택 항목을 게시할 수 있도록 Azure 스택 마켓플레이스 합니다.

   업로드 공개적으로 액세스할 수 있는 컨테이너와 저장소 계정이 해야 (이 시나리오에 대 한 필수 조건 참조)   
   1. Azure 스택 관리자 포털에서로 이동 **더 많은 서비스** > **저장소 계정은**합니다.  
   
   2. 선택한 저장소 계정, 구독에서 다음에서 **BLOB 서비스**선택, **컨테이너**합니다.  
      ![Blob 서비스](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. 사용 하 고 다음 선택 컨테이너 선택 **업로드** 열려는 **업로드 blob** 창.  
      ![컨테이너](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. 업로드 blob 창에서 파일 저장소에 로드 되 고 다음을 선택 하려면을 찾아 **업로드**합니다.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. 업로드 한 파일 컨테이너 창에 나타납니다. 파일을 선택한 다음에서 URL을 복사는 **속성 Blob** 창. Azure 스택 마켓플레이스 항목을 가져올 때 다음 단계에서이 URL을 사용 합니다.  다음 이미지는 컨테이너는 *blob 테스트 저장소* 파일이 *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*합니다.  URL이 파일 *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*합니다.  
      ![Blob 속성](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  PowerShell을 사용 하 여 사용 하 여 Azure 스택에 마켓플레이스 항목을 게시 하는 **추가 AzsGalleryItem** cmdlet. 예:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. 볼 수 있는 갤러리 항목이 게시 한 후 **더 많은 서비스** > **마켓플레이스**합니다.  솔루션 템플릿을 다운로드가 사용 하는 경우 해당 솔루션 템플릿에 대 한 모든 종속 VHD 이미지를 추가 해야 합니다.  
  ![보기 마켓플레이스](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Azure 스택 PowerShell 1.3.0 릴리스와 함께 이제 가상 컴퓨터 확장을 추가할 수 있습니다.

예: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>다음 단계
[만들기 및 마켓플레이스 항목을 게시](azure-stack-create-and-publish-marketplace-item.md)