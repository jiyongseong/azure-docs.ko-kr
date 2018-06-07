---
title: "마켓플레이스 도구 키트를 사용 하 여 만들고 마켓플레이스 항목을 게시 | Microsoft Docs"
description: "게시 도구 키트에 마켓플레이스 항목을 만들 신속 하 게 하는 방법을 알아봅니다"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>게시 도구를 사용 하 여 마켓플레이스 항목을 추가 합니다.
에 콘텐츠를 추가 [Azure 스택 마켓플레이스](azure-stack-marketplace.md) 및 배포에 대 한 테 넌 트를 솔루션을 사용할 수 있게 합니다.  마켓플레이스 Toolkit IaaS Azure 리소스 관리자 템플릿 또는 VM 확장에 따라 Azure 마켓플레이스 패키지 (.azpkg) 파일을 만듭니다.  사용 하거나 도구를 사용 하 여 만든.azpkg 파일을 게시할 마켓플레이스 도구 키트를 사용할 수도 있습니다 [수동](azure-stack-create-and-publish-marketplace-item.md) 단계입니다.  이 항목에서는 도구를 다운로드 하 고 VM 템플릿을 기반으로 마켓플레이스 항목을 만드는 다음 Azure 스택 Marketplace에 해당 항목을 게시 안내 합니다.     


## <a name="prerequisites"></a>필수 조건
 - Azure 스택 호스트에서이 도구 키트를 실행 하거나 한 해야 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 도구를 실행 하면 컴퓨터에서 연결 합니다.

 - 다운로드는 [Azure 스택 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) 고 압축을 풉니다.

 - 다운로드는 [Azure 갤러리 패키징 도구](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - 마켓플레이스에 게시 아이콘 및 미리 보기 파일에 필요합니다.  직접 사용 하거나 저장할 수는 [샘플](azure-stack-marketplace-publisher.md#support-files) 이 예제에 대 한 로컬 파일입니다.

## <a name="download-the-tool"></a>도구 다운로드
마켓플레이스 Toolkit 수 [Azure 스택 도구 리포지토리에서 다운로드](azure-stack-powershell-download.md)합니다.


##  <a name="create-marketplace-items"></a>마켓플레이스 항목 만들기
이 섹션에서는.azpkg 형식에서 마켓플레이스 항목 패키지를 만들려면 마켓플레이스 도구 키트를 사용 합니다.  

### <a name="provide-marketplace-information-with-wizard"></a>마법사를 사용 하 여 마켓플레이스 정보를 제공 합니다.
1. PowerShell 세션에서 마켓플레이스 도구 키트를 실행 합니다.
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. 클릭는 **솔루션** 탭 합니다.  이 화면 마켓플레이스 항목에 대 한 정보를 허용합니다. 시장에서 원하는 만큼 항목에 대 한 정보를 입력 합니다.  지정할 수 있습니다는 [매개 변수 파일](azure-stack-marketplace-publisher.md#use-a-parameters-file) 폼을 미리 채웁니다.  
    
    ![마켓플레이스 Toolkit 첫 번째 화면의 스크린 샷](./media/azure-stack-marketplace-publisher/image7.png)
3. 클릭 **찾아보기** 각 아이콘과 스크린 샷 필드에 대 한 이미지 파일을 선택 하 고 있습니다.  사용자 지정 아이콘 또는의 샘플 아이콘을 사용할 수는 [지원 파일](azure-stack-marketplace-publisher.md#support-files) 섹션.
4. 모든 필드 채워진 후 시장 내에서 솔루션의 미리 보기에 대 한 "솔루션 미리 보기"를 선택 합니다.  수정 하 고 클릭 하기 전에 텍스트, 이미지 및 스크린 샷 편집 수 **다음**합니다.  

### <a name="import-template-and-create-package"></a>서식 파일을 가져오고 패키지 만들기
이 섹션에서는 템플릿을 가져오고 솔루션에 대 한 입력으로 작동 합니다.

1.  클릭 **찾아보기** 선택 하 고는 *azuredeploy.json* 다운로드 된 서식 파일에서 단순 Windows VM 101 폴더에서.

    ![두 번째 화면 마켓플레이스 도구 키트의 스크린샷](./media/azure-stack-marketplace-publisher/image8.png)
2.  배포 마법사는 채워집니다는 *기본* 템플릿에 지정 된 각 매개 변수에 대해 단계 및 입력 항목입니다.  단계를 더 추가할 한 단계 사이의 입력을 이동할 수 있습니다.  예를 들어 서 솔루션에 대 한 "프런트 엔드 구성" 및 "백 엔드 구성" 단계를 만들 수 있습니다.
3.  AzureGalleryPackager.exe에 대 한 경로 지정 합니다.  
4.  클릭 **만들기** 마켓플레이스 Toolkit.azpkg 파일로 솔루션을 패키지 하 고 있습니다.  완료 되 면 마법사는 솔루션 파일의 경로를 표시 하 고 계속 Azure 스택에 패키지를 게시 하는 옵션을 제공 합니다.


## <a name="publish-marketplace-items"></a>마켓플레이스 항목을 게시
이 섹션에서는 Azure 스택 마켓플레이스에 마켓플레이스 항목을 게시합니다.

![마켓플레이스 Toolkit 첫 번째 화면의 스크린 샷](./media/azure-stack-marketplace-publisher/image9.png)

1.  마법사에는 솔루션을 게시 하는 정보가 필요 합니다.
    
    |필드|설명|
    |-----|-----|
    | 서비스 관리자 이름 | 서비스 관리자 계정입니다.  예제: ServiceAdmin@mydomain.onmicrosoft.com |
    | 암호 | 서비스 관리자 계정에 대 한 암호입니다. |
    | API 끝점 | Azure 스택 Azure 리소스 관리자 끝점입니다.  예: management.local.azurestack.external |
2.  클릭 **게시** 게시 로그 표시 됩니다.
3.  이제 Azure 스택 포털을 통해 게시 된 항목을 배포할 수 있습니다.


## <a name="use-a-parameters-file"></a>매개 변수 파일을 사용 하 여
또한 마켓플레이스 항목 정보를 완료 하는 매개 변수 파일을 사용할 수 있습니다.  

마켓플레이스 도구 키트는 *solution.parameters.ps1* 고유한 매개 변수 파일을 만드는 데 사용할 수 있습니다.


## <a name="support-files"></a>지원 파일
| 설명 | 샘플 |
| ----- | ----- |
| 40x40 .png icon | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90x90 .png icon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115x115 .png icon | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255x115 .png icon | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324.png 미리 보기 | ![](./media/azure-stack-marketplace-publisher/image5.png) |


