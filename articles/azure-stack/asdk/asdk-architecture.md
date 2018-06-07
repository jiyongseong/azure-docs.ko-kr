---
title: "Azure 스택 개발 키트 아키텍처 | Microsoft Docs"
description: "Azure 스택 개발 키트 (ASDK) 아키텍처에 설명 합니다."
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
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure 스택 개발 키트 아키텍처
Azure 스택 개발 키트 (ASDK)은 Azure 스택의 단일 노드 배포를 수행 합니다. 모든 구성 요소는 단일 호스트 컴퓨터에서 실행 중인 가상 컴퓨터에 설치 됩니다. 

## <a name="logical-architecture-diagram"></a>논리적 아키텍처 다이어그램
다음 다이어그램은 ASDK 및 해당 구성 요소의 논리적 아키텍처를 보여 줍니다.

![ASDK 아키텍처](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>가상 컴퓨터 역할
ASDK 개발 키트 호스트 컴퓨터에서 호스팅되는 다음 Vm을 사용 하 여 서비스를 제공 합니다.

| 이름 | 설명 |
| ----- | ----- |
| **AzS-ACS01** | Azure 스택 저장소 서비스입니다.|
| **AzS-ADFS01** | Active Directory Federation Services (ad FS)입니다.  |
| **AzS-BGPNAT01** | 에 지 라우터 및 Azure 스택에 대 한 NAT 및 VPN 기능을 제공 합니다. |
| **AzS-CA01** | Azure 스택 역할 서비스에 대 한 인증서 기관 서비스입니다.|
| **AzS-DC01** | Microsoft Azure 스택 active Directory, DNS 및 DHCP 서비스.|
| **AzS-ERCS01** | 응급 복구 콘솔 VM입니다. |
| **AzS-GWY01** | 테 넌 트 네트워크에 대 한 VPN 사이트 간 연결 같은 지 게이트웨이 서비스.|
| **AzS-NC01** | Azure 스택 네트워크 서비스를 관리 하는 네트워크 컨트롤러입니다.  |
| **AzS-SLB01** | 테 넌 트와 Azure 스택 인프라 서비스에 대 한 분산 Azure 스택의 멀티플렉서 서비스를 로드 합니다.  |
| **AzS-SQL01** | Azure 스택 인프라 역할에 대 한 내부 데이터 저장소입니다.  |
| **AzS-WAS01** | Azure 스택 관리 포털 및 Azure Resource Manager 서비스입니다.|
| **AzS-WASP01**| Azure 스택 사용자 (테 넌 트) 포털 및 Azure Resource Manager 서비스입니다.|
| **AzS-XRP01** | Microsoft Azure 스택, 계산, 네트워크 및 저장소 리소스 공급자를 포함 하 여에 대 한 인프라 관리 컨트롤러입니다.|


## <a name="next-steps"></a>다음 단계
[기본 ASDK 관리 작업에 알아보기](asdk-admin-basics.md)
