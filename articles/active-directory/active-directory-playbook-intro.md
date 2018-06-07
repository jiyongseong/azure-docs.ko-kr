---
title: "Azure Active Directory PoC 플레이 북 소개 | Microsoft Docs"
description: "ID 및 액세스 관리 시나리오를 탐색하고 신속하게 구현"
services: active-directory
keywords: "Azure Active Directory, 플레이 북, 개념 증명, PoC"
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Azure Active Directory 개념 증명 플레이 북 구성: 소개

이 문서에서는 PoC(개념 증명)에서 다른 Azure 기능을 탐색하기 위한 지침을 제공합니다. 이 문서의 예상 사용자는 ID 설계자, IT 전문가 및 시스템 통합 업체입니다.

## <a name="how-to-use-this-playbook"></a>이 플레이 북을 사용하는 방법

1. [테마](active-directory-playbook-ingredients.md#theme) 섹션을 사용하고 필요에 따라 관심 영역을 선택합니다.  
2. 비즈니스 목표에 맞는 시나리오를 선택하여 PoC 범위를 지정합니다. 짧을수록 좋습니다. 구현하는 데 필요한 복잡성을 최소화하면서 이해 관계자에게 값을 전달할 수 있도록 가능한 한 짧고 간단하게 유지하는 것이 좋습니다.  
3. [구현](active-directory-playbook-implementation.md) 섹션을 사용하여 이러한 시나리오와 각 시나리오가 사용자 환경에 어떤 의미를 갖는지 이해하세요. 각 시나리오에서 설정 방법([구성 요소](active-directory-playbook-building-blocks.md)) 및 시나리오 탐색 방법이 설명됩니다. 
4. 각 구성 요소는 완료하는 데 드는 대략적인 시간 뿐만 아니라 필수 전제 조건도 설명합니다. 이를 통해 계획하는 데 도움을 얻을 수 있습니다. 
5. 위의 1-3에 따라 실행할 [환경](active-directory-playbook-ingredients.md#environment)을 정의합니다. 사용자가 환경에 대해 좋은 평가를 내릴 수 있도록 적절한 프로덕션 환경을 제공하기 위해 노력하고 있습니다. 
6. 상충하는 요구 사항이 있을 경우 이 유용한 상쇄 매트릭스를 사용하세요. 
   * 테마 중심의 값 표시  
   * 원활하게 시나리오를 준비, 설정 및 실행할 수 있는 효율성 
   * 대상 시나리오를 실행하는 데 소요되는 최소 시간 
   * 제약 조건에도 불구하고 최대한 프로덕션에 가깝게 구현 

>[!NOTE]
> 이 문서 전체에서는 편의를 위한 예제로 몇 가지 타사 응용 프로그램 및 제품이 제시되어 있습니다. Azure AD의 [응용 프로그램 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에서는 사용자의 요구 및 환경에 따라 사용할 수 있는 수천 개의 응용 프로그램을 지원합니다. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]