---
title: "Azure AD 갤러리 응용 프로그램에 대한 사용자 프로비저닝이 오래 걸림 | Microsoft Docs"
description: "응용 프로그램에 대한 프로비저닝이 예상보다 오래 걸리는 이유를 확인하는 방법"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9ed12569c0adc5ed8625a8d9fc81c9bee874cd4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Azure AD 갤러리 응용 프로그램에 대한 사용자 프로비저닝이 오래 걸림

응용 프로그램에 대한 자동 프로비저닝을 처음 사용하도록 설정할 경우 초기 동기화는 Azure AD 디렉터리의 크기와 프로비전 범위에 있는 사용자 수에 따라 20분에서 수 시간이 걸릴 수 있습니다. 

프로비전 서비스는 초기 동기화 후에 두 시스템의 상태를 나타내는 워터마크를 저장하므로, 초기 동기화 이후의 후속 동기화가 빨라져 후속 동기화의 성능이 향상됩니다.

## <a name="how-to-improve-provisioning-performance"></a>프로비저닝 성능을 향상시키는 방법

초기 동기화가 오래 걸리는 경우 성능 향상을 위해 다음 작업을 수행할 수 있습니다.

-   **사용자 범위 지정 필터** 범위 지정 필터를 사용하면 특정 특성 값에 따라 사용자를 필터링하여 프로비저닝 서비스가 Azure AD에서 추출하는 데이터를 미세 조정할 수 있습니다. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)

