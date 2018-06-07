---
title: Azure Portal을 통해 스트리밍 끝점 크기 조정 | Microsoft 문서
description: 이 자습서에서는 Azure 포털을 사용하여 스트리밍 끝점의 크기를 조정하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: f2a14f2622d78a4222a8518172eb1ce8ed9e6637
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Azure 포털을 통해 스트리밍 끝점 크기 조정
## <a name="overview"></a>개요

> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

**프리미엄** 스트리밍 끝점은 고급 워크로드에 적합하며, 확장성 있는 전용 대역폭 용량을 제공합니다. **프리미엄** 스트리밍 끝점이 있는 고객은 기본적으로 하나의 SU(스트리밍 단위)를 가져옵니다. SU를 추가하여 스트리밍 끝점의 크기를 조정할 수 있습니다. 각 SU는 응용 프로그램에 추가 대역폭 수용작업량을 제공합니다. 스트리밍 끝점 유형 및 CDN 구성에 대한 자세한 내용은 [스트리밍 끝점 개요](media-services-streaming-endpoints-overview.md) 항목을 참조하세요.
 
이 항목에서는 스트리밍 끝점의 크기를 조정하는 방법을 보여 줍니다.

가격 정보에 대한 자세한 내용은 [Media Services 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.

## <a name="scale-streaming-endpoints"></a>스트리밍 끝점 크기 조정

스트리밍 단위 수를 변경하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **스트리밍 끝점**을 선택합니다.
3. 크기를 조정할 스트리밍 끝점을 클릭합니다. 

    > [!NOTE] 
    > **프리미엄** 스트리밍 끝점의 크기만 조정할 수 있습니다.

4. 슬라이더를 이동하여 스트리밍 단위 수를 지정합니다.

    ![스트리밍 끝점](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

