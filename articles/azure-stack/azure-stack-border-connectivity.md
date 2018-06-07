---
title: "Azure 스택 통합 시스템에 대 한 연결 네트워크 통합 고려 사항 테두리 | Microsoft Docs"
description: "다중 노드 Azure 스택와 테두리 네트워크 연결을 데이터 센터에 대 한 계획을 수행할 수 있는 작업에 대해 알아봅니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 93dd609df90adac2c84ba8c62cf0d18f55a317bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="border-connectivity"></a>테두리 연결 
네트워크 통합 계획은 성공적으로 통합 하는 Azure 스택 시스템 배포, 작업 및 관리에는 중요 한 필수 구성 요소입니다. 경계 게이트웨이 프로토콜 (BGP)와 동적 라우팅을 사용 여부를 선택 하 여 테두리 연결 계획을 시작 합니다. 이 위해서는 (공용 또는 개인)는 16 비트 BGP 익명 시스템 번호를 할당 하거나 고정 라우팅을 사용 하 여, 여기서 정적 기본 경로에 지정 된 테두리 장치.

> [!IMPORTANT]
> Tor () 스위치의 맨 위에 필요한 지점 간 Ip와 업링크 계층 3 (30 / 네트워크) 실제 인터페이스에 구성 합니다. 계층 2 업링크 Azure 스택 작업 지원 TOR 스위치를 사용 하는 지원 되지 않습니다. 

## <a name="bgp-routing"></a>BGP 라우팅
BGP와 같은 동적 라우팅 프로토콜을 사용 하 여 시스템은 항상 네트워크 변경 내용을 파악 하 고 관리를 용이 하 게 보장 합니다. 

다음 다이어그램에 나와 있는 것 처럼 개인 IP 광고 TOR 스위치에 공간이 접두사 목록을 사용 하 여 제한 합니다. 접두사 목록 경로 맵 TOR와 테두리 사이의 연결에 적용 하 고 개인 IP 서브넷을 거부 합니다.

소프트웨어 부하 분산 장치 SLB () 내에서 실행 되는 Azure 스택 솔루션 피어 TOR 장치에 VIP 주소를 보급할 동적으로 수 있도록 합니다.

오류에서 복구 즉시 하 고 투명 하 게 사용자 트래픽, VPC 또는 MLAG TOR 장치 사이 구성 된 다중 섀시 링크를 사용할 수 있도록 하기 위해 호스트와 HSRP 또는 VRRP 제공 하는 집계 IP 네트워크에 대 한 중복성을 네트워크입니다.

![BGP 라우팅](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>정적 라우팅
고정 경로 사용 하 여 테두리 및 TOR 장치에 더 고정된 구성을 추가 합니다. 모든 변경 하기 전에 철저 한 분석을 해야합니다. 구성 오류로 인해 발생 하는 문제 롤백 변경에 따라 더 많은 시간이 걸릴 수 있습니다. 권장 되는 라우팅 방법,이 하 이지만 지원 됩니다.

이 메서드를 사용 하 여 네트워킹 환경에 Azure 스택와 통합 하려면 테두리 장치 구성 되어야 합니다 향하는 외부 네트워크 트래픽에 대 한 TOR 장치를 가리키는 고정 경로와 공용 Vip.

모든 트래픽을 테두리 장치에 전송 하는 정적 기본 경로 사용 하는 TOR 장치를 구성 합니다. 이 규칙 트래픽 예외는 개인에 대 한 테두리 연결 TOR에 적용할 액세스 제어 목록을 사용 하 여 차단 됨 공간입니다.

다른 모든 항목이 첫 번째 방법으로 동일 해야 합니다. 동적 라우팅 BGP 여전히 내에서 사용 될 랙 때문에 것은 필수적인 도구는 SLB 및 기타 구성 요소에 대 한 및 사용 하지 않도록 설정 하거나 제거할 수 없습니다.

![정적 라우팅](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>투명 프록시
데이터 센터 모든 트래픽이 프록시를 사용 하는 경우 구성 해야는 *투명 프록시* 랙에서 네트워크의 영역 간에 트래픽을 분리 하는 정책에 따라 처리 하는 모든 트래픽을 처리 합니다.

> [!IMPORTANT]
> Azure 스택 솔루션 일반 웹 프록시를 지원 하지 않습니다.  

투명 프록시 (라고도 가로채기, 인라인 또는 강제 프록시) 특수 한 클라이언트 구성 없이 네트워크 계층에서 일반적인 통신을 차단 합니다. 클라이언트 해야 하지 프록시의 존재를 인식 합니다.

![투명 프록시](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>다음 단계
[DNS 통합](azure-stack-integrate-dns.md)