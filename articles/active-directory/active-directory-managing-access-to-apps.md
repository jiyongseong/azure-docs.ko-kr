---
title: Azure AD를 사용하는 앱에 대한 액세스 관리 | Microsoft Docs
description: Azure Active Directory는 조직이 앱을 각 사용자가 액세스할 수 있도록 지정하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: barbkess
ms.openlocfilehash: 41b5eaf0fabcc57349df6694696cd8b8b11b9447
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="managing-access-to-apps"></a>앱에 대한 액세스 관리
앱이 조직의 ID 시스템에 통합된 후에 진행 중인 액세스 관리, 사용 평가 및 보고는 계속 쉽지 않을 것입니다. 대부분의 경우 IT 관리자나 기술 지원팀이 앱에 대한 액세스를 관리하는 데 지속적이고 적극적인 역할을 맡습니다. 경우에 따라 할당은 일반 또는 부서 IT 팀에서 수행됩니다. 종종 할당 의사 결정은 비즈니스 의사 결정자에게 위임되며 IT가 할당을 수행하기 전에 승인을 필요로 합니다.  다른 조직에서는 역할 기반 Access Control(RBAC) 또는 특성 기반 Access Control(ABAC)과 같은 기존의 자동화된 ID 및 액세스 관리 시스템의 통합에 투자합니다. 통합 및 규칙 개발은 모두 전문화되고 비용이 높은 경향이 있습니다. 관리 방법에 대한 모니터링 또는 보고는 별도로 비용이 드는 복잡한 투자입니다.

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory는 어떻게 지원합니까?
 Azure AD는 구성된 응용 프로그램에 대한 광범위한 액세스 관리를 지원하며 이는 조직이 위임을 통해 자동으로 특성 기반 할당(ABAC 또는 RBAC 시나리오)에 걸쳐서 관리자 관리를 포함하는 올바른 액세스 정책을 쉽게 달성할 수 있도록 합니다. Azure AD를 사용하여 단일 응용 프로그램에 여러 관리 모델을 결합하는 복잡한 정책을 쉽게 달성할 수 있으며 동일한 고객층을 가진 응용 프로그램에서 관리 규칙을 다시 사용할 수도 있습니다.

* [신규 또는 기존 응용 프로그램 추가](active-directory-enterprise-apps-manage-sso.md)

 Azure AD의 응용 프로그램 할당은 두 가지 기본 할당 모드에 중점을 둡니다.

* **개별 할당** 디렉터리 전역 관리자 권한을 가진 IT 관리자는 개별 사용자 계정을 선택하고 응용 프로그램에 액세스 권한을 부여할 수 있습니다.
* **그룹 기반 할당(유료 Azure AD만 해당)** 디렉터리 전역 관리자 권한을 가진 IT 관리자는 응용 프로그램에 그룹을 할당할 수 있습니다. 특정 사용자의 액세스는 응용 프로그램에 액세스하려고 할 때 그룹의 멤버인지 여부로 결정됩니다. 즉, 관리자는 "할당된 그룹의 현재 멤버는 응용 프로그램에 액세스한다"는 내용의 할당 규칙을 효율적으로 만들 수 있습니다. 이 할당 옵션을 사용하여 관리자는 [특성 기반 동적 그룹](active-directory-groups-create-azure-portal.md), 외부 시스템 그룹(예: 온-프레미스 Active Directory 또는 Workday), 관리자 관리 또는 셀프 서비스 관리된 그룹을 포함하는 Azure AD 그룹 관리 옵션 중 하나에서 혜택을 줄 수 있습니다. 단일 그룹은 쉽게 여러 앱에 할당할 수 있으며 할당 선호도가 있는 응용 프로그램은 할당 규칙을 공유하여 전반적인 관리 복잡성을 줄일 수 있습니다. 중첩 그룹 구성원은 이번 응용 프로그램에 대한 그룹 기반 할당에서 지원되지 않습니다.

이러한 두 가지 할당 모드 관리자를 사용하면 바람직한 할당 관리 방법을 달성할 수 있습니다.

Azure AD를 사용하여 사용 및 할당 보고를 완전히 통합하여 관리자가 할당 상태, 할당 오류 및 사용을 쉽게 보고하도록 설정합니다.

## <a name="complex-application-assignment-with-azure-ad"></a>Azure AD를 사용하여 복잡한 응용 프로그램 할당
Salesforce와 같은 응용 프로그램을 고려합니다. 많은 조직에서 Salesforce는 마케팅 및 판매 조직에서 주로 사용됩니다. 종종 영업 팀의 멤버가 Salesforce에 대해 제한된 액세스만 가능한 반면 마케팅 팀의 멤버는 높은 액세스 권한이 있습니다. 대부분의 경우 정보 근로자의 광범위한 집단은 응용 프로그램에 액세스가 제한되었습니다. 이러한 규칙의 예외는 문제를 복잡하게 만듭니다. 이러한 일반 규칙에 관계 없이 사용자에게 액세스 권한을 부여하거나 해당 역할을 변경하는 것은 마케팅 또는 판매 리더십 팀의 특권입니다.

Azure AD를 사용하여 Single Sign-On(SSO) 및 자동화된 프로비전에 Salesforce와 같은 응용 프로그램을 미리 구성할 수 있습니다. 응용 프로그램이 구성되면 관리자는 일회성 작업을 만들어 적절한 그룹을 만들고 할당할 수 있습니다. 이 예제에서 관리자는 다음의 할당을 실행할 수 있습니다.

* [동적 그룹](active-directory-groups-create-azure-portal.md) 은 역할 또는 부서와 같은 특성을 사용하여 자동으로 마케팅 및 판매 팀의 모든 멤버를 나타내도록 정의될 수 있습니다.
  
  * 마케팅 그룹의 모든 멤버는 Salesforce에서 "마케팅" 역할에 할당됩니다.
  * 판매 팀 그룹의 모든 멤버는 Salesforce에서 "판매" 역할에 할당됩니다. 추가로 개선하여 다른 Salesforce 역할에 할당된 지역 판매 팀을 나타내는 여러 그룹을 사용할 수 있습니다.
* 예외 메커니즘을 사용하려면 각 역할에 셀프 서비스 그룹을 만들 수 있습니다. 예를 들어 "Salesforce 마케팅 예외" 그룹을 셀프 서비스 그룹으로 만들 수 있습니다. 그룹을 Salesforce 마케팅 역할에 할당할 수 있고 마케팅 리더십 팀을 소유자가 만들 수 있습니다. 마케팅 리더십 팀의 멤버는 사용자를 추가 또는 제거하거나 가입 정책을 설정, 개별 사용자의 가입 요청을 승인 또는 거부합니다. 소유자 또는 멤버에 대한 특별한 교육을 요구하지 않는 정보 작업자에 적절한 환경을 통해 지원됩니다.

이 경우 해당 역할 할당이 Salesforce에서 업데이트된 다른 그룹에 추가되면서 모든 할당된 사용자는 자동으로 Salesforce에 프로비전됩니다. 사용자는 Microsoft 응용 프로그램 액세스 패널, Office 웹 클라이언트를 통하거나 해당 조직의 Salesforce 로그인 페이지로 이동하여 Salesforce를 검색하고 액세스할 수 있습니다. 관리자는 Azure AD 보고를 사용하여 사용 및 할당 상태를 쉽게 볼 수 있습니다.

관리자는 [Azure AD 조건부 액세스](active-directory-conditional-access-azure-portal.md) 를 채택하여 특정 역할에 대한 액세스 정책을 설정할 수 있습니다. 이러한 정책은 기업 환경 외부 및 Multi-Factor Authentication 또는 장치 요구에 액세스가 허용되는지 여부를 포함하여 다양한 경우에 액세스를 달성할 수 있습니다.

## <a name="how-can-i-get-started"></a>어떻게 시작하나요?
우선 Azure AD를 아직 사용하지 않는 IT 관리자인 경우입니다.

* [사용해 보기](https://azure.microsoft.com/trial/get-started-active-directory/) - 지금 무료 30일 평가판에 등록하면 이 링크를 사용하여 5분 내에 첫 번째 클라우드 솔루션을 배포할 수 있습니다.

계정에 공유를 사용하는 Azure AD 기능은 다음과 같습니다.

* [그룹 할당](active-directory-accessmanagement-self-service-group-management.md)
* 응용 프로그램을 Azure AD에 추가
* 할당 시작
* 응용 프로그램 할당 FAQ
* [앱 사용 대시보드/보고서](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>자세한 내용을 알아보려면 어떤 정보를 참조해야 하나요?
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [조건부 액세스를 사용한 앱 보호](active-directory-conditional-access-azure-portal.md)
* [셀프 서비스 그룹 관리/SSAA](active-directory-accessmanagement-self-service-group-management.md)

