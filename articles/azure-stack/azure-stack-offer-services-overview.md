---
title: "Azure 스택에서 서비스를 제공 합니다. | Microsoft Docs"
description: "클라우드 연산자로 사용자에 게 서비스를 제공할 수 있습니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 5b117a9b174f5d2419ff596cc90436e4b9d21645
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Azure 스택에서 서비스 제공의 개요

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

[Microsoft Azure 스택](azure-stack-poc.md) 는 데이터 센터에서 서비스를 제공할 수 있도록 하는 하이브리드 클라우드 플랫폼입니다. 서비스 공급자로 테 넌 트에 서비스를 제공할 수 있습니다. 기업 또는 정부 기관 내에서 직원에 게 온-프레미스 서비스를 제공할 수 있습니다. 배달할 수 있는 서비스를 포함 하지만로 제한 되지 않습니다.

- 플랫폼 서비스 (PaaS) 서비스로 응용 프로그램 서비스, API 앱, API 함수, SQL, MySQL 등입니다.

통합 한 여러 사용자에 대해 복잡 한 솔루션을 작성 하는 서비스를 결합할 수도 있습니다.

이러한 서비스를 사용자에 게 제공 하려면 만들어야 [계획과 제안, 할당량](azure-stack-plan-offer-quota-overview.md)합니다. 사용자가 다음 서비스를 사용 하 여 서비스를 구독할 수 있습니다.

## <a name="plan-your-service-offers"></a>계획 서비스 제공

해당 제공을 계획할 때는 다음 사항을 염두에서에 둬야 합니다.

**평가판 제안**: 그런 다음 추가 서비스를 업그레이드할 수 있는 새 사용자가 갖게 하기 위한 평가판을 사용할 수 있습니다. 평가판 제품을 만들려면 작은 만들 [기본 계획](azure-stack-plan-offer-quota-overview.md#base-plan) 선택적 더 큰 추가 기능 플랜과 합니다.

**용량 계획**: 잡고 대용량 리소스 및 모든 사용자에 대 한 시스템을 방해 하는 사용자에 대해 염려할 수도 있습니다. 성능을 위해 수 [계획 할당량 구성](azure-stack-plan-offer-quota-overview.md#plans) 캡을 사용 하도록 합니다.

**공급자에 위임**: 다른 사용자를 사용자 환경에서 서비스를 만들 수를 부여할 수 있습니다. 예를 들어 서비스 공급자 인 경우 할 수 있습니다 [위임](azure-stack-delegated-provider.md) 프로그램 대리점에는이 기능입니다. 또는 사용 하는 조직의 다른 사업부/자회사에 위임할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택에는 제안을 만듭니다.](azure-stack-create-offer.md)

