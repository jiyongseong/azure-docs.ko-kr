---
title: "클라우드 폴더에서 Azure App Service로 콘텐츠 동기화"
description: "클라우드 폴더에서 콘텐츠 동기화를 통해 Azure App Service에 앱을 배포하는 방법에 대해 알아봅니다."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>클라우드 폴더에서 Azure App Service로 콘텐츠 동기화
이 자습서에서는 Dropbox 및 OneDrive와 같은 인기 있는 클라우드 저장소 서비스에서 콘텐츠를 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)와 동기화하는 방법을 보여 줍니다. 

## <a name="overview"></a>콘텐츠 동기화 배포 개요
주문형 콘텐츠 동기화 배포는 App Service와 통합된 [Kudu 배포 엔진](https://github.com/projectkudu/kudu/wiki) 에서 제공됩니다. [Azure Portal](https://portal.azure.com)에서 클라우드 저장소의 폴더를 지정하고 해당 폴더에서 앱 코드 및 콘텐츠 작업을 수행한 다음 단추를 클릭하여 App Service를 동기화할 수 있습니다. 콘텐츠 동기화는 빌드 및 배포에 Kudu 프로세스를 활용합니다. 

## <a name="contentsync"></a>콘텐츠 동기화 배포를 사용하는 방법
[Azure Portal](https://portal.azure.com)에서 콘텐츠 동기화를 사용하려면 다음 단계를 따릅니다.

1. Azure Portal의 앱 페이지에서 **설정** > **배포 원본**을 클릭합니다. **원본 선택**을 클릭한 다음 배포 원본으로 **OneDrive** 또는 **Dropbox**를 선택합니다. 
   
    ![콘텐츠 동기화](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > API의 기본적인 차이 때문에 **비즈니스용 OneDrive**는 지원되지 않습니다. 
   > 
   > 
2. 모든 App Service 콘텐츠가 저장될 OneDrive 또는 Dropbox에 대해 미리 정의된 특정 지정 경로에 액세스하도록 App Service를 설정하여 권한 부여 워크플로를 완료합니다. 권한 부여 후에 App Service 플랫폼은 지정된 콘텐츠 경로에 콘텐츠 폴더를 만드는 옵션 또는 이 지정된 콘텐츠 경로에서 기존 콘텐츠 폴더를 선택하는 옵션을 제공합니다. App Service 동기화에 사용된 클라우드 저장소 계정에 지정된 콘텐츠 경로는 다음과 같습니다.  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. 초기 콘텐츠 동기화 후 Azure Portal에서 필요에 따라 콘텐츠 동기화를 시작할 수 있습니다. 배포 기록은 **배포** 페이지에서 확인할 수 있습니다.
   
    ![배포 기록](./media/app-service-deploy-content-sync/onedrive_sync.png)

Dropbox 배포에 대한 자세한 내용은 [Dropbox에서 배포](https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/)를 참조하세요.

