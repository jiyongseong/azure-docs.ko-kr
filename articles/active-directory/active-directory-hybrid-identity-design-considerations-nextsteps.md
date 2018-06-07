---
title: "Azure Active Directory 하이브리드 ID 디자인 고려 사항 - 다음 단계 | Microsoft Docs"
description: "하이브리드 ID 디자인 고려 사항 가이드를 읽은 후 개요 및 다음 단계"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 02d48768-ea9e-4bfe-ae54-b54c4bd0a789
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 29c045af81134847321d9ef69943f0154e9ee5a0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations--next-steps"></a>Azure Active Directory 하이브리드 ID 디자인 고려 사항 - 다음 단계
요구 사항을 정의하고 모바일 장치 관리 솔루션에 대한 모든 옵션을 검토했으므로 사용자와 조직에 적합한 지원 인프라 배포에 대한 다음 단계를 수행할 준비가 되었습니다.

## <a name="hybrid-identity-solutions"></a>하이브리드 ID 솔루션
-요구 사항에 맞는 특정 솔루션 시나리오를 활용하는 것은 모바일 장치 관리 인프라 배포에 대한 세부 사항을 검토하고 계획하는 좋은 방법입니다. 다음 솔루션은 가장 일반적인 몇몇 모바일 장치 관리 시나리오를 설명합니다.

* [엔터프라이즈 환경 솔루션에서 모바일 장치 및 PC 관리](https://technet.microsoft.com/library/dn582037.aspx) 는 Microsoft Intune을 사용하여 온-프레미스 System Center 2012 Configuration Manager 인프라를 클라우드로 확장하여 모바일 장치를 관리하는 데 도움이 됩니다. 이 하이브리드 인프라를 사용하면 중간 규모 및 대규모 환경에서 IT 전문가가 BYOD 및 원격 액세스를 사용하면서 관리의 복잡성을 줄일 수 있습니다.
* [Configuration Manager 2007 솔루션에 대한 모바일 장치 관리](https://technet.microsoft.com/library/dn508400.aspx) 는 인프라가 System Center Configuration Manager 2007에 있을 때 모바일 장치를 관리하는 데 도움이 됩니다. 이 솔루션에서는 System Center 2012 Configuration Manager를 실행하는 단일 서버를 설정하는 방법을 보여 주므로 Microsoft Intune을 실행하고 MDM 기능을 활용할 수 있습니다.
* [소규모 환경 솔루션에서 모바일 장치 관리](https://technet.microsoft.com/library/dn715906.aspx) 는 MDM을 지원해야 하는 소기업을 위한 시나리오입니다. Microsoft Intune을 사용하여 모바일 장치 관리 및 BYOD를 지원하도록 현재 인프라를 확장하는 방법을 설명합니다. 이 솔루션은 독립 실행형의, 로컬 서버가 없는 클라우드 전용 구성에서 Microsoft Intune 사용을 지원하는 가장 간단한 시나리오를 설명합니다.

## <a name="hybrid-identity-documentation"></a>하이브리드 ID 설명서
개념 및 절차 계획, 배포 및 관리 콘텐츠는 모바일 장치 관리 솔루션을 구현할 때 유용합니다.

* [Microsoft System Center](https://technet.microsoft.com/library/cc507089.aspx) 솔루션은 IT 직원이 관리하기 쉬운 시스템을 구축하고 작업을 자동화할 수 있도록 인프라, 정책, 프로세스 및 모범 사례에 대한 지식을 파악하고 집계하는 데 도움이 될 수 있습니다.
* [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) 은 컴퓨터 및 모바일 장치를 관리하고 회사 정보를 보호하는 데 도움이 되는 클라우드 기반 장치 관리 서비스입니다.
* [MDM for Office 365](https://technet.microsoft.com/library/ms.o365.cc.devicepolicy.aspx) 를 사용하면 Office 365 조직에 연결되어 있을 때 모바일 장치를 관리 및 보호할 수 있습니다. MDM for Office 365를 사용하여 장치 보안 정책 및 액세스 규칙을 설정하고 분실하거나 도난당한 경우 모바일 장치를 초기화할 수 있습니다.

## <a name="hybrid-identity-resources"></a>하이브리드 ID 리소스
다음 리소스를 모니터링하면 모바일 장치 관리 솔루션에 대한 최신 뉴스 및 업데이트를 확인할 수 있습니다.

* [Microsoft Enterprise Mobility 블로그](http://blogs.technet.com/b/enterprisemobility/)
* [Microsoft In The Cloud 블로그](http://blogs.technet.com/b/in_the_cloud/)
* [Microsoft Intune 블로그](http://blogs.technet.com/b/microsoftintune/)
* [Microsoft System Center Configuration Manager 블로그](http://blogs.technet.com/b/configurationmgr/)
* [Microsoft System Center Configuration Manager 팀 블로그](http://blogs.technet.com/b/configmgrteam/)

## <a name="see-also"></a>참고 항목
[디자인 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

