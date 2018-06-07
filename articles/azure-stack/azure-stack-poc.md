---
title: Azure Stack이란? | Microsoft Docs
description: Azure 스택을 사용 하면 데이터 센터에서 Azure 서비스를 실행할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: d54f392eddfcca99e7fe0b037b9efd0a4e90433c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-stack"></a>Azure Stack이란?

Microsoft Azure 스택은 조직의 데이터 센터에서 Azure 서비스를 제공할 수 있는 하이브리드 클라우드 플랫폼입니다.  Azure 스택 가장자리와 연결이 끊어진된 환경 또는 특정 보안 및 규정 준수 요구 사항을 충족와 같은 주요 시나리오에서 최신 응용 프로그램에 새 시나리오를 가능 하도록 설계 되었습니다.  Azure 스택 두 가지 배포 옵션 사용자의 요구를 충족 하기 위해 제공 됩니다.

## <a name="azure-stack-integrated-systems"></a>Azure Stack 통합 시스템
통합된 시스템의 Microsoft 파트너 관계를 통해 제공 되는 azure 스택 및 [하드웨어 파트너](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), 분산 관리에 대 한 간단한 설명이 된 혁신 클라우드 속도 제공 하는 솔루션을 만드는 합니다.  Azure 스택 하드웨어 및 소프트웨어의 통합된 된 시스템을 제공 하는, 때문에 적절 한 양의 유연성 및 제어를 여전히 클라우드에서 혁신을 채택 하는 동안 제공 됩니다.  Azure 스택 통합 시스템 4-12 노드에서 하 고 공동으로 하드웨어 파트너 및 Microsoft에서 지원 됩니다.  프로덕션 작업에 대 한 새로운 시나리오를 사용 하도록 설정 하려면 Azure 스택 통합 시스템을 사용 합니다.    

## <a name="azure-stack-development-kit"></a>Azure Stack 개발 키트
Microsoft [Azure 스택 개발 키트 (ASDK)](.\asdk\asdk-what-is.md) 단일 노드 배포를 평가 하 여 Azure 스택에 대 한 자세한 내용은 사용할 수 있는 Azure 스택입니다.  사용할 수도 있습니다는 ASDK 개발자 환경으로 Azure와 일치 하는 도구 및 Api를 사용 하 여 개발할 수 있습니다. ASDK 프로덕션 환경으로 사용할 수 없습니다.

ASDK에 다음과 같은 제한이 있습니다.
* ASDK 단일 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) id 공급자와 연결 됩니다. 이 디렉터리에 여러 사용자를 만들고 각 사용자에게 구독을 할당할 수 있습니다.
* 단일 컴퓨터에 배포된 모든 구성 요소와 함께, 테넌트 리소스에 사용할 수 있는 제한된 물리적 리소스가 있습니다. 눈금 또는 성능 평가 위해이 구성은 적합 하지 않습니다.
* 네트워킹 시나리오는 단일 호스트/NIC 요구 사항으로 인해 제한됩니다.  

## <a name="next-steps"></a>다음 단계
[주요 기능 및 개념](azure-stack-key-features.md)

[Azure 스택: Azure (pdf)의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)

