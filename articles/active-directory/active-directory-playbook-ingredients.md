---
title: "Azure Active Directory PoC 플레이 북 구성 | Microsoft Docs"
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
ms.openlocfilehash: ff4a8601b619837d835ec25c26b1f7e69b46ae85
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Azure Active Directory 개념 증명 플레이 북 구성 

## <a name="theme"></a>테마
Azure AD는 기업의 여러 영역에서 ID 및 액세스 솔루션을 제공합니다. 해당 시나리오를 다음 영역으로 구분할 수 있습니다. 

* [다수의 앱, 하나의 ID](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [보안 향상](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [셀프 서비스에 맞게 크기 조정](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

PoC를 규정하는 테마를 정의하면 비즈니스 목표에 맞게 노력을 집중하도록 하는 데 도움이 되며, 처음부터 개념 증명에 관심이 생길 수 있습니다. 

## <a name="environment"></a>Environment

PoC를 전달하는 환경의 세부 정보를 확인하는 것이 중요합니다. 이상적으로는 PoC가 완료된 후에 환경을 구축할 수 있습니다. 목표 환경은 중요하며, 가능한 한 실제와 같이 유지하면서도 제약이나 추가 고려 사항에 따른 오버헤드를 고려해야 합니다. PoC에 대한 일반적인 환경은 다음과 같습니다.
* **프로덕션:** 시나리오가 라이브 환경에서 구현되며, 이미 Microsoft Cloud 서비스(프로덕션 AD, Office 365, Azure AD 테넌트/SSO 솔루션)가 배포되어 있습니다. 
* **UAT(사용자 수용 테스트)/개발 환경:** 프로덕션과 유사한 테스트 데이터를 포함하는 테스트 인프라(병렬 AD 및 잠재적으로 Azure AD 테넌트/SSO 솔루션)가 있습니다. 일반적으로 테스트 환경은 엔터프라이즈의 여러 프로젝트 간에 공유됩니다.

이 가이드의 시나리오 대부분은 기본적으로 추가가 가능합니다. 따라서 PoC 외부 사용자에게 영향을 주지 않으면서 프로덕션 테넌트에 배포될 수 있습니다. 이 문서 전체에서는 테넌트 차원에서 영향을 미치는 시나리오를 살펴볼 것입니다. 이러한 경우 프로덕션이 아닌 환경을 고려하려고 할 수 있습니다. 


## <a name="target-users"></a>대상 사용자

시나리오를 연습할 대상 사용자 집합을 결정하는 것이 중요합니다. 이 작업은 환경이 프로덕션 또는 테스트 환경일 때 특히 중요합니다. PoC에 대한 대상 사용자 범주는 다음과 같습니다.
* **파일럿 사용자:** 당일 작업을 수행하기 위해 해당 하루 동안 사용하는 계정으로 솔루션을 사용하게 되는 환경의 실제 사용자
* **테스트 사용자:** 환경에서 생성된 테스트 계정 

이 가이드의 시나리오 대부분은 파일럿 사용자가 실행할 수 있습니다. 이 문서 전체에서는 필요할 때 대상 사용자 고려 사항을 언급합니다.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]