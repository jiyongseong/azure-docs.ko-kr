---
title: Azure Media Services 개요 | Microsoft Docs
description: 이 항목에서는 Azure Media Services에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2017
ms.author: juliako;anilmur
ms.openlocfilehash: d79a8937c0190246218153e42ead74ada619b121
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="azure-media-services-overview"></a>Azure Media Services 개요 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [버전 2-GA](media-services-overview.md)
> * [버전 3-미리 보기](../latest/media-services-overview.md)

Microsoft AMS(Azure Media Services)는 개발자가 확장 가능한 미디어 관리 및 배달 응용 프로그램을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. Media Services는 다양한 클라이언트(예: TV, PC 및 모바일 장치)로의 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키지할 수 있는 REST API를 기반으로 합니다.

전체 Media Services를 사용하여 종단 간 워크플로를 작성할 수 있습니다. 또한 워크플로의 일부에 타사 구성 요소를 사용하도록 선택할 수 있습니다. 예를 들어 타사 인코더를 사용하여 인코딩합니다. 그런 다음 Media Services를 사용하여 업로드, 보호, 패키징 및 배달합니다.

콘텐츠를 라이브로 스트리밍하고 주문형 콘텐츠를 배달하도록 선택할 수 있습니다. 이 항목은 관련 항목으로도 연결됩니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
여기서 AMS 학습 경로를 볼 수 있습니다.

* [AMS 라이브 스트리밍 워크플로](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [AMS 주문형 스트리밍 워크플로](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>필수 조건

Azure Media Services 사용을 시작하려면 다음이 있어야 합니다.

* Azure 계정. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com)을 참조하십시오.
* Azure Media Services 계정. 자세한 내용은 [계정 만들기](media-services-portal-create-account.md)를 참조하세요.
* (선택 사항) 개발 환경 설정. 개발 환경에 .NET 또는 REST API를 선택합니다. 자세한 내용은 [환경 설정](media-services-dotnet-how-to-use.md)을 참조하세요.

    [프로그래밍 방식으로 AMS API에 연결](media-services-use-aad-auth-to-access-ams-api.md)하는 방법도 알아봅니다.
* 시작된 상태에 있는 표준 또는 프리미엄 스트리밍 끝점.  자세한 내용은 [스트리밍 끝점 관리](media-services-portal-manage-streaming-endpoints.md)를 참조하세요.

## <a name="sdks-and-tools"></a>SDK 및 도구

Media Services 솔루션을 빌드하려면 다음을 사용할 수 있습니다.

* [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* 사용 가능한 클라이언트 SDK 중 하나:
    * [.NET용 Azure Media Services SDK](https://github.com/Azure/azure-sdk-for-media-services),
    * [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js용 Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Node.js SDK의 Microsoft가 아닌 타사 버전입니다. 커뮤니티에서 유지 관리하고 현재 AMS API를 100% 포함하지 않습니다).
* 기존 도구:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE(Azure Media Services 탐색기)는 Windows용 Winforms/C# 응용 프로그램임)

> [!NOTE]
> 최신 버전의 Java SDK를 가져와서 Java를 사용하여 개발을 시작하려면 [Media Services용 Java 클라이언트 SDK 시작](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)을 참조하세요. <br/>
> Media Services용 최신 PHP SDK를 다운로드하려면 [Packagist 리포지토리](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)에서 Microsoft/WindowAzure 패키지 버전 0.5.7을 찾습니다.  

## <a name="code-samples"></a>코드 샘플

**Azure 코드 샘플** 갤러리: [Azure Media Services 코드 샘플](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)에서 여러 코드 샘플을 찾습니다.

## <a name="concepts"></a>개념

Azure Media Services 개념은 [개념](media-services-concepts.md)을 참조하세요.

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>지원되는 시나리오 및 데이터 센터에서 Media Services의 사용 가용성

자세한 내용은 [AMS 시나리오 및 데이터 센터에서 기능 및 서비스의 사용 가용성](scenarios-and-availability.md)을 참조하세요.

## <a name="service-level-agreement-sla"></a>SLA(서비스 수준 계약)

* Media Services Encoding을 위해 REST API 트랜잭션의 99.9% 가용성을 보장합니다.
* 표준 또는 프리미엄 스트리밍 끝점을 구매하는 경우 스트리밍을 위해 기존 미디어 콘텐츠에 대해 99.9% 가용성을 보장하는 요청을 서비스합니다.
* 라이브 채널을 위해 실행 중인 채널이 최소 99.9%의 시간 동안 외부 연결을 사용할 수 있도록 보장됩니다.
* Content Protection을 위해 최소 99.9%의 시간 동안 주요 요청을 처리할 것을 보장합니다.
* 인덱서를 위해 99.9%의 시간 동안 Encoding 예약 단위로 처리되는 인덱서 작업 요청을 서비스합니다.

자세한 내용은 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](scenarios-and-availability.md#availability) 섹션을 참조하세요.

## <a name="support"></a>지원

[Azure 지원](https://azure.microsoft.com/support/options/) 에서는 Media Services를 포함하여 Azure에 대한 지원 옵션을 제공합니다.

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
