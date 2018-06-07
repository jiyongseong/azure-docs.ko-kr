---
title: "고급 Azure 스택 평가 작업 | Microsoft Docs"
description: "이 문서에서는 고급 Azure 스택 평가 작업을 설명 합니다."
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
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Azure 스택 개발 키트의 고급 평가 작업
기본 Azure 스택 개발 키트 (ASDK) 서비스 기능 및 기능에 익숙한 나면 더 고급 시나리오를 테스트 하 여 Azure 스택 추가 대 한 이해를 향상 시킬 수 있습니다. 이러한 고급 평가 작업은 Azure 스택 연산자 설명서에서 완벽 하 게 설명 되어 있습니다.

> [!NOTE]
> ASDK와 다중 노드 Azure 스택 배포는 프로덕션 환경에 대 한 다양 한 연산자 작업은 지원 ASDK 배포에 대 한 모든 사용 시나리오는 지원 됩니다. 참조 [ASDK 및 다중 노드 Azure 스택 차이점](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) 자세한 정보에 대 한 합니다.

## <a name="delegate-offers-in-azure-stack"></a>Azure Stack에서 제품 위임
Azure 스택 연산자로 종종 다른 사용자를 담당 하 제안 만들기 및 사용자가 등록 하려고 합니다. 예를 들어 서비스 공급자를 사용 하는 경우 고객 등록 및 사용자를 대신 하 여 관리 하는 대리점 경우가 있습니다. 또는 사용 하는 엔터프라이즈에서는 중앙 IT 그룹의 일부 경우에 사용자가 사용자 개입 없이 등록 하는 자회사를 할 수 있습니다.

[Azure 스택에서 offers 위임](.\.\azure-stack-delegated-provider.md) 를 사용 하면 이러한 작업에 연결 하 고 직접는 것 보다 더 많은 사용자가 관리할 수 있도록 하 여 합니다. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Azure 스택 사용자에 게 SQL 데이터베이스를 사용할 수 있게
Azure 스택 연산자로 사용자가 제공을 만들 수 있습니다 (테 넌 트) 클라우드-네이티브 앱, 웹 사이트 및 작업 함께 사용할 수 있는 SQL 데이터베이스를 만듭니다. 이러한 사용자 지정, 주문형, 클라우드 기반 데이터베이스를 사용자에 게 제공 하 여 시간 및 리소스가 저장할 수 있습니다. 

SQL Server 리소스 공급자 어댑터를 사용 하 여 [Azure 스택 사용자에 게 SQL 데이터베이스를 사용할 수 있게](.\.\azure-stack-tutorial-sql-server.md) Azure 스택의 서비스로 합니다. 리소스 공급자를 설치한 후 하나 이상의 SQL Server 인스턴스에 연결 합니다.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>사용자에 게 웹 앱 및 API 앱 사용할 수 있는 Azure 스택
Azure 스택 연산자로 사용자가 제공을 만들 수 있습니다 (테 넌 트) Azure 함수 및 웹 및 API 응용 프로그램을 만듭니다. 사용자에 게 이러한 주문형, 클라우드 기반 앱에 대 한 액세스를 제공 함으로써 시간과 리소스가 저장할 수 있습니다.

앱 서비스 리소스 공급자를 배포 [사용자에 게 웹 앱 및 API 앱 사용할 수 있는 Azure 스택](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>다음 단계
[Azure 스택 통합 시스템을 사용 하 여 서비스를 제공 하는 방법에 대 한 자세한 정보](.\.\azure-stack-offer-services-overview.md)