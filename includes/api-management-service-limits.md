---
title: 포함 파일
description: 포함 파일
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
| 리소스 | 제한 |
| --- | --- |
| 배율 단위 | 지역당 10개<sup>1</sup> |
| 캐시 | 단위당 5GB<sup>1</sup> |
| HTTP 기관당 동시 백 엔드 연결<sup>2</sup> | 단위당 2048<sup>3</sup> |
| 캐시된 최대 응답 크기 | 10MB |
| 최대 정책 문서 크기 | 256KB |
| 최대 사용자 지정 게이트웨이 도메인 | 서비스 인스턴스당 20<sup>4</sup> |


<sup>1</sup>각 가격 책정 계층에 따라 API 관리 제한이 서로 다릅니다. 가격 책정 계층 및 해당 크기 조정 제한을 확인하려면 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.
<sup>2</sup> 백 엔드에서 명시적으로 닫히지 않는 한 연결은 풀링되고 다시 사용됩니다.
<sup>3</sup> Basic, Standard 및 Premium 계층 단위당입니다. 개발자 계층은 1024로 제한됩니다.
<sup>4</sup> 프리미엄 계층에서만 사용할 수 있습니다.


