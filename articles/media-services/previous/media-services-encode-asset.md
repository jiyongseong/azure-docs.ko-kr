---
title: Azure 주문형 미디어 인코더 개요 및 비교 | Microsoft 문서
description: 이 항목에서는 Azure 주문형 미디어 인코더를 대략적으로 설명하고 비교한 데이터를 제공합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 6e44cff930f943f5cf30fb5335ad6a7fd1d08ab5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Azure 주문형 미디어 인코더 개요 및 비교
## <a name="encoding-overview"></a>Encoding 개요
Azure Media Services는 클라우드에서 미디어의 인코딩에 대한 여러 옵션을 제공합니다.

Media Services로 시작하는 경우 코덱과 파일 형식 간의 차이점을 이해하는 것은 중요합니다.
코덱은 압축/압축 해제 알고리즘을 구현하는 소프트웨어이고 파일 형식은 압축된 비디오를 보관하는 컨테이너입니다.

Media Services는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 Media Services에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 끝점은 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 끝점은 **실행** 상태에 있어야 합니다. [동적 패키징](media-services-dynamic-packaging-overview.md)을 이용하려면 다음을 수행해야 합니다.
>
>또한 원본 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).

Media Services는 이 문서에서 설명하는 다음 주문형 인코더를 지원합니다.

* [미디어 인코더 표준](media-services-encode-asset.md#media-encoder-standard)
* [미디어 인코더 Premium 워크플로](media-services-encode-asset.md#media-encoder-premium-workflow)

이 문서에서는 주문형 미디어 인코더에 대한 간략한 개요와 보다 자세한 정보를 제공하는 문서에 대한 링크를 제공합니다. 또한 항목에서는 인코더에 대한 비교를 제공합니다.

>[!NOTE]
>기본적으로 각 Media Services 계정은 한번에 하나의 인코딩 작업을 활성화할 수 있습니다. 구입한 각 인코딩 예약 단위에 대해 하나씩, 여러 인코딩 작업을 동시에 실행할 수 있는 인코딩 단위를 예약할 수 있습니다. 자세한 내용은 [인코딩 단위 크기 조정](media-services-scale-media-processing-overview.md)을 참조하세요.

## <a name="media-encoder-standard"></a>미디어 인코더 표준
### <a name="how-to-use"></a>사용 방법
[미디어 인코더 표준으로 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>형식
[형식 및 코덱](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>기본 설정
미디어 인코더 표준은 [여기](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다.

### <a name="input-and-output-metadata"></a>입력 및 출력 메타데이터
인코더 입력 메타데이터는 [여기](media-services-input-metadata-schema.md)에서 설명합니다.

인코더 출력 메타데이터는 [여기](media-services-output-metadata-schema.md)에서 설명합니다.

### <a name="generate-thumbnails"></a>미리 보기 생성
자세한 내용은 [미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법](media-services-advanced-encoding-with-mes.md#thumbnails)을 참조하세요.

### <a name="trim-videos-clipping"></a>비디오 자르기(클리핑)
자세한 내용은 [미디어 인코더 표준을 사용하여 비디오를 자르는 방법](media-services-advanced-encoding-with-mes.md#trim_video)을 참조하세요.

### <a name="create-overlays"></a>오버레이 만들기
자세한 내용은 [미디어 인코더 표준을 사용하여 오버레이를 만드는 방법](media-services-advanced-encoding-with-mes.md#overlay)을 참조하세요.

### <a name="see-also"></a>참고 항목
[Media Services 블로그](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>미디어 인코더 Premium 워크플로
### <a name="overview"></a>개요
[Azure Media Services의 프리미엄 Encoding 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>사용 방법
미디어 인코더 Premium 워크플로는 복잡한 워크플로를 사용하여 구성됩니다. 워크플로 파일은 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 만들고 업데이트할 수 있습니다.

[Azure Media Services의 프리미엄 Encoding 사용 방법](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>알려진 문제
입력된 비디오에 자막이 없는 경우, 출력 자산은 빈 TTML 파일을 포함합니다.


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>관련 문서
* [미디어 인코더 표준 사전 설정을 사용자 지정하여 고급 인코딩 작업 수행](media-services-custom-mes-presets-with-dotnet.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
