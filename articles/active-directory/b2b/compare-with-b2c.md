---
title: Azure Active Directory에서 B2B 공동 작업과 B2C 비교 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업과 Azure AD B2C 간의 차이는 무엇인가요?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 19e8e9d22938fa8a18299d67aa77824aaae3f6da
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Azure Active Directory에서 B2B 공동 작업과 B2C 비교

Azure Active Directory(Azure AD) B2B 공동 작업 및 Azure AD B2C 모두를 사용하여 Azure AD에서 외부 사용자와 함께 작업할 수 있습니다. 하지만 어떻게 비교할 수 있나요?


B2B 공동 작업 기능 |     Azure AD B2C 독립 실행형 제품
-------- | --------
대상: ID 공급자에 관계없이 파트너 조직의 사용자를 인증할 수 있기를 원하는 조직 | 대상: 개인이든, 기관이든, 조직이든 상관없이 모바일 및 웹앱 고객을 Azure AD에 초대하려는 사용자
지원되는 ID: 회사 또는 학교 계정이 있는 직원, 회사 또는 학교 계정이 있는 파트너 또는 모든 전자 메일 주소입니다. 직접 페더레이션 기능을 곧 지원합니다.  | 지원되는 ID: 로컬 응용 프로그램 계정(모든 전자 메일 주소 또는 사용자 이름)이 있는 소비자 사용자 또는 직접 페더레이션이 포함된 지원되는 소셜 ID입니다.
파트너 사용자가 위치한 디렉터리: 외부 조직의 파트너 사용자는 직원과 같은 디렉터리에서 관리되지만 특별하게 주석 처리됩니다. 이들은 직원과 같은 방식으로 관리하고 동일한 그룹에 추가할 수 있습니다.  | 고객 사용자 엔터티가 위치한 디렉터리: 응용 프로그램 디렉터리에 있습니다. 조직의 직원 및 파트너 디렉터리(있는 경우)에서 별도로 관리됩니다.
모든 Azure AD 연결 앱에 대한 SSO(Single Sign-On)가 지원됩니다. 예를 들어 Office 365 또는 온-프레미스 앱 및 다른 SaaS 앱(예: Salesforce 또는 Workday)에 대한 액세스를 제공할 수 있습니다.  |  Azure AD B2C 테넌트 내의 고객 소유 앱에 대한 SSO가 지원됩니다. Office 365 또는 다른 Microsoft 앱 및 타사 SaaS 앱에 대한 SSO가 지원되지 않습니다.
파트너 수명 주기: 호스트/초대한 조직에 의해 관리됩니다.  | 고객 수명 주기: 셀프 서비스 또는 응용 프로그램에 의해 관리됩니다.
보안 정책 및 규정 준수: 호스트/초대한 조직에 의해 관리됩니다.  | 보안 정책 및 규정 준수: 응용 프로그램에 의해 관리됩니다.
브랜딩: 호스트/초대한 조직의 브랜드가 사용됩니다.  |    브랜딩: 응용 프로그램에 의해 관리됩니다. 일반적으로 조직의 존재가 희미해지고 제품 브랜드가 되는 경향이 있습니다.
추가 정보: [블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [설명서](what-is-b2b.md)  | 추가 정보: [제품 페이지](https://azure.microsoft.com/services/active-directory-b2c/), [설명서](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>다음 단계

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [B2B 공동 작업 사용자 속성](user-properties.md)

