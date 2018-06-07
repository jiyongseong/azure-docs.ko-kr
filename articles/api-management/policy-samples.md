---
title: Azure API Management 정책 샘플 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 3f1d9a96888732e6221722102a687e09f37333c0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="api-management-policy-samples"></a>API Management 정책 샘플

[정책](api-management-howto-policies.md)은 게시자가 구성을 통해 API 동작을 변경할 수 있도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 다음 표는 샘플에 대한 링크를 포함하고 각 샘플에 대해 간략하게 설명합니다.

|||
|---|---|
|**인바운드 정책**||
|[백 엔드 API가 적절한 URL을 생성하도록 전달된 헤더 추가](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |인바운드 요청에 전달된 헤더를 추가하여 백 엔드 API가 적절한 URL을 생성하도록 하는 방법을 보여 줍니다.|
|[상관 관계 ID를 포함하는 헤더 추가](./policies/add-correlation-id.md?toc=api-management/toc.json) |상관 관계 ID를 포함하는 헤더를 인바운드 요청에 추가하는 방법을 보여 줍니다.|
|[백 엔드 서비스에 기능 추가 및 응답 캐시](./policies/cache-response.md?toc=api-management/toc.json) |백 엔드 서비스에 기능을 추가하는 방법을 보여 줍니다. 예를 들어 일기 예보 API에서 위도 및 경도 대신 장소 이름을 허용합니다.|
|[JWT 클레임을 기반으로 액세스 권한 부여](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |JWT 클레임 기반의 API에서 특정 HTTP 메서드에 대한 액세스 권한을 부여하는 방법을 보여 줍니다.|
|[Google OAuth 토큰을 사용하여 액세스 권한 부여](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |OAuth 토큰 공급자로 Google을 사용하여 끝점에 대한 액세스 권한을 부여하는 방법을 보여 줍니다.|
|[공유 액세스 서명 생성 및 Azure 저장소에 요청 전달](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |식을 사용하여 [공유 액세스 서명](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)을 생성하고 rewrite-uri 정책을 사용하여 Azure 저장소로 요청을 전달하는 방법을 보여 줍니다. |
|[AAD에서 OAuth2 액세스 토큰 가져오기 및 백 엔드로 전달](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |게이트웨이와 백 엔드 간의 권한 부여에 OAuth2를 사용하는 예를 제공합니다. AAD에서 액세스 토큰을 가져오고 백 엔드로 전달하는 방법을 보여 줍니다.|
|[요청 보내기 정책을 사용하여 SAP 게이트웨이에서 X-CSRF 토큰 가져오기](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |대부분의 API에서 사용되는 X-CSRF 패턴을 구현하는 방법을 보여 줍니다. 이 예제는 SAP 게이트웨이에만 해당됩니다. |
|[본문 크기에 따라 요청 라우팅](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |본문 크기에 따라 요청을 라우팅하는 방법을 보여 줍니다.|
|[백 엔드 서비스에 요청 컨텍스트 정보 보내기](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |로깅 및 처리를 위해 백 엔드 서비스에 일부 컨텍스트 정보를 전송하는 방법을 보여 줍니다.|
|[응답 캐시 기간 설정](./policies/set-cache-duration.md?toc=api-management/toc.json) |백 엔드에서 전송한 Cache-Control 헤더의 maxAge 값을 사용하여 응답 캐시 기간을 설정하는 방법을 보여 줍니다.|
|**아웃바운드 정책**||
|[응답 콘텐츠 필터링](./policies/filter-response-content.md?toc=api-management/toc.json) | 요청과 연결된 제품에 따라 응답 페이로드에서 데이터 요소를 필터링하는 방법을 보여 줍니다.|
|**오류 발생 시 정책**||
|[Stackify에 오류 기록](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |오류 로깅 정책을 추가하여 로깅을 위해 Stackify에 오류를 보내는 방법을 보여 줍니다.|
