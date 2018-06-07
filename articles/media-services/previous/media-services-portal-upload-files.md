---
title: Azure Portal에서 Media Services 계정에 파일 업로드 | Microsoft Docs
description: 이 자습서에서는 Azure Portal에서 Media Services 계정에 파일을 업로드하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 36e1f797263e367a73fde140d979243f96e83948
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Azure Portal에서 Media Services 계정에 파일 업로드
> [!div class="op_single_selector"]
> * [포털](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST (영문)](media-services-rest-upload-files.md)
> 
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 

Azure Media Services에서 자산에 디지털 파일을 업로드합니다. 자산에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

> [!NOTE]
> Media Services는 파일 처리를 위해 최대 파일 크기를 포함합니다. 파일 크기 제한에 대한 자세한 내용은 [Media Services 할당량 및 제한](media-services-quotas-and-limitations.md)을 참조하세요.
>

## <a name="upload-files"></a>파일 업로드
1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. 계정 배포 진행 상태를 보려면 **설정** > **자산**을 참조하세요. 그런 다음 **업로드** 단추를 선택합니다.
   
    ![파일 업로드](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    **비디오 자산 업로드** 창이 나타납니다.
   
   > [!NOTE]
   > Media Services는 비디오를 업로드하기 위한 파일 크기를 제한하지 않습니다.
 
3. 컴퓨터에서 업로드하려는 비디오로 이동합니다. 비디오를 선택한 다음 **확인**을 선택합니다.  
   
    업로드가 시작됩니다. 파일 이름 아래에 진행 상태가 표시됩니다.  

업로드가 완료되면 새 자산이 **자산** 창에 나열됩니다. 

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
* [업로드된 자산을 인코딩](media-services-portal-encode.md)하는 방법에 대해 알아보세요.

* 또한 Azure Functions를 사용하여 파일이 구성된 컨테이너에 도착할 때 인코딩 작업을 트리거할 수도 있습니다. 자세한 내용은 [Media Services: Azure Media Services를 Azure Functions 및 Logic Apps와 통합](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/)에서 샘플을 참조하세요.


