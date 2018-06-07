---
title: 미디어 처리 크기 조정 개요 | Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용하여 미디어 처리의 크기를 조정하는 방법을 대략적으로 설명합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 894b403b59624b6c42ce947169e9c9ac30ec76b9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="scaling-media-processing-overview"></a>미디어 처리 크기 조정 개요
이 페이지에서는 미디어 처리의 크기를 조정하는 방법과 이유에 대해 간략하게 설명합니다. 

## <a name="overview"></a>개요
Media Services 계정은 미디어 처리 작업을 처리하는 속도를 결정하는 예약 단위 형식과 연결됩니다. **S1**, **S2**, **S3** 예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다. 자세한 내용은 [예약 단위 유형](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)을 참조하세요.

예약 단위 유형을 지정하는 것 외에도 계정에 예약 단위를 프로비전하도록 지정할 수 있습니다. 프로비전되는 예약 단위의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되면 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

## <a name="choosing-between-different-reserved-unit-types"></a>여러 예약 단위 유형 중에서 선택
다음 테이블을 참조하여 여러 인코딩 속도 중에서 선택할 때 적절한 속도를 결정할 수 있습니다. 이 테이블에서는 테스트를 직접 수행할 수 있도록 비디오를 다운로드하는 데 사용할 수 있는 SAS URL와 몇 가지 벤치마킹 사례도 제공합니다.

| 시나리오 | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| 대상 사용 사례 |단일 비트 전송률 인코딩 <br/>SD 이하 해상도의 파일, 시간이 중요하지 않은 인코딩, 저가형 비디오. |단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>SD 및 HD 인코딩에서 모두 일반적으로 사용됨 |단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>Full HD 및 4K 해상도 비디오. 시간이 중요하며 소요 시간이 짧은 인코딩 |
| 벤치마크 |[입력 파일: 길이 5분/640x360p/초당 29.97프레임](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>같은 해상도에서 단일 비트 전송률 MP4 파일로 인코딩할 때 약 11분이 걸립니다. |[입력 파일: 길이 5분/1280x720p/초당 29.97프레임](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>"H264 단일 비트 전송률 720p" 사전 설정을 사용하여 인코딩할 때 약 5분이 걸립니다.<br/><br/>"H264 다중 비트 전송률 720p" 사전 설정을 사용하여 인코딩할 때 약 11분 30초가 걸립니다. |[입력 파일: 길이 5분/1920x1080p/초당 29.97프레임](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>"H264 단일 비트 전송률 1080p" 사전 설정을 사용하여 인코딩할 때 약 2.7분이 걸립니다.<br/><br/>"H264 단일 비트 전송률 1080p" 사전 설정을 사용하여 Encoding할 때 약 5.7분이 걸립니다. |

## <a name="considerations"></a>고려 사항
> [!IMPORTANT]
> 이 섹션에서 설명하는 고려 사항을 검토하세요.  
> 
> 

* 예약된 단위는 Azure Media Indexer를 사용하는 인덱싱 작업을 비롯하여 모든 미디어 처리 병렬화에 대해 작동합니다.  그러나 인코딩과 달리 인덱싱 작업은 예약 단위가 더 빠르게 실행되어도 더 빨리 처리되지 않습니다.
* 공유 풀을 사용 중이어서 예약 단위가 없는 경우에는 인코딩 작업의 성능이 S1 RU와 동일해집니다. 그러나 작업이 대기된 상태로 유지될 수 있는 시간의 상한은 없으며 언제든지 작업은 최대 하나만 실행됩니다.

## <a name="billing"></a>결제

미디어 예약 단위의 실제 사용 시간(분)을 기준으로 요금이 청구됩니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.   

## <a name="quotas-and-limitations"></a>할당량 및 제한 사항
할당량 및 제한 사항과 지원 티켓을 여는 방법에 대한 자세한 내용은 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요.

## <a name="next-step"></a>다음 단계
다음 기술 중 하나를 사용하여 미디어 처리 크기 조정 작업을 수행합니다. 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [포털](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> 최신 버전의 Java SDK를 가져와서 Java를 사용하여 개발을 시작하려면 [Media Services용 Java 클라이언트 SDK 시작](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)을 참조하세요. <br/>
> Media Services용 최신 PHP SDK를 다운로드하려면 [Packagist 리포지토리](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)에서 Microsoft/WindowAzure 패키지 버전 0.5.7을 찾습니다.  

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

