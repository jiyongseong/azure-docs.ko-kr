---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d8091fdade9cd417af58755d8245c2fb091b86b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
다음 표에서는 PolicyBased VPN 게이트웨이와 RouteBased VPN 게이트웨이에 대한 요구 사항을 나열합니다. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델 모두에 적용됩니다. 클래식 모델의 경우 PolicyBased VPN 게이트웨이는 정적 게이트웨이와 동일하고, Route-based 게이트웨이는 동적 게이트웨이와 동일합니다.

|  | **PolicyBased 기본 VPN Gateway** | **RouteBased 기본 VPN Gateway** | **RouteBased 표준 VPN Gateway** | **RouteBased 고성능 VPN Gateway** |
| --- | --- | --- | --- | --- |
| **사이트 간 연결(S2S)** |PolicyBased VPN 구성 |RouteBased VPN 구성 |RouteBased VPN 구성 |RouteBased VPN 구성 |
| **지점 및 사이트 간 연결(P2S)** |지원되지 않음 |지원됨(S2S와 함께 사용할 수 있음) |지원됨(S2S와 함께 사용할 수 있음) |지원됨(S2S와 함께 사용할 수 있음) |
| **인증 방법** |미리 공유한 키 |-S2S 연결에 대해 미리 공유한 키 -P2S 연결용 인증서 |-S2S 연결에 대해 미리 공유한 키 -P2S 연결용 인증서 |-S2S 연결에 대해 미리 공유한 키 -P2S 연결용 인증서 |
| **S2S 연결의 최대 수** |1 |10 |10 |30 |
| **P2S 연결의 최대 수** |지원되지 않음 |128 |128 |128 |
| **활성 라우팅 지원(BGP)** |지원되지 않음 |지원되지 않음 |지원됨 |지원됨 |
