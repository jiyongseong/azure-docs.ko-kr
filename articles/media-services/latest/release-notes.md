---
title: Azure Media Services v3 릴리스 정보 | Microsoft 문서
description: 최신 개발 정보를 확인할 수 있도록 이 문서에서는 Azure Media Services v3의 최신 업데이트에 대한 정보를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3(미리 보기) 릴리스 정보 

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능
* 변경 계획

## <a name="may-07-2018"></a>2018년 5월 7일

### <a name="net-sdk"></a>.Net SDK

.Net SDK에는 다음과 같은 기능이 있습니다.

1. 미디어 콘텐츠를 인코딩하거나 분석할 수 있는 **Transform** 및 **Job**. 예를 들어 [스트림 파일](stream-files-tutorial-with-api.md) 및 [분석](analyze-videos-tutorial-with-api.md)을 참조하세요.
2. 최종 사용자 장치에 콘텐츠를 게시하고 스트리밍하는 **StreamingLocator**.
3. 콘텐츠 전송 시 키 배달 및 콘텐츠 보호(DRM)를 구성하는 **StreamingPolicy** 및 **ContentKeyPolicy**.
4. 라이브 스트리밍 콘텐츠의 수집 및 보관을 구성하는 **LiveEvent** 및**LiveOutput**.
5. Azure Storage에 미디어 콘텐츠를 저장하고 게시하는 **Asset**. 
6. 실시간 및 주문형 미디어 콘텐츠에 대한 동적 패키징, 암호화 및 스트리밍을 구성 및 확장하는 **StreamingEndpoint**.

### <a name="known-issues"></a>알려진 문제

알려진 문제:

원본 콘텐트를 가리키는 HTTPS URL(JobInputHttp)이 포함된 작업을 제출하는 경우에는 HTTP 서버가 'HEAD' 요청을 지원하는지 확인해야 합니다. 그렇지 않으면 작업이 거부됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개요](media-services-overview.md)
