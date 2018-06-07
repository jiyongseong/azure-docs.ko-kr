---
title: Azure Active Directory에서 관리자 역할 할당 | Microsoft Docs
description: 관리자 역할은 사용자를 추가하고, 관리자 역할을 할당하며, 사용자 암호를 다시 설정하고, 사용자 라이선스 또는 도메인을 관리하는 데 사용할 수 있습니다. 관리자 역할이 할당된 사용자는 조직에서 구독한 모든 클라우드 서비스에서 동일한 권한을 갖습니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 6e663fe275fc195cb611e1032adc147bf4e99b1d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932152"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 할당

Azure AD(Azure Active Directory)를 사용하여 다른 기능을 담당하도록 별도의 관리자를 지정할 수 있습니다. 관리자는 역할에 따라 Azure Portal의 다양한 기능에 액세스할 수 있게 되며 사용자 만들기나 편집, 다른 사람에게 관리 역할 할당, 사용자 암호 다시 설정, 사용자 라이선스 관리, 도메인 관리 등을 수행할 수 있습니다. 관리자 역할이 할당된 사용자는 Office 365 포털, Azure Portal 또는 Windows PowerShell용 Azure AD 모듈 중 어느 것을 사용하여 역할을 할당했든지 간에 조직이 가입한 모든 클라우드 서비스에서 동일한 권한을 갖습니다.

## <a name="details-about-the-global-administrator-role"></a>전역 관리자 역할에 대한 세부 정보
전역 관리자는 모든 관리 기능에 액세스할 수 있습니다. 기본적으로 Azure 구독에 등록하는 사람에게는 디렉터리에 대한 전역 관리자 역할이 할당됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다.

## <a name="assign-or-remove-administrator-roles"></a>관리자 역할 할당 또는 제거
Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법에 대한 내용은 [Azure Active Directory에서 관리자 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md)을 참조하세요.

## <a name="available-roles"></a>사용 가능한 역할
다음과 같은 관리자 역할을 사용할 수 있습니다.

* **대금 청구 관리자**: 구입하고, 구독을 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링합니다.

* **호환성 관리자**: 이 역할의 사용자는 Office 365 보안 및 규정 준수 센터 및 Exchange 관리 센터 내 관리 권한이 있습니다. 자세한 내용은 “[Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)”를 참조하세요.

* **조건부 액세스 관리자**: 이 역할의 사용자는 Azure Active Directory 조건부 액세스 설정을 관리할 수 있습니다.
  > [!NOTE]
  > Azure에서 Exchange ActiveSync 조건부 액세스 정책을 배포하려면 사용자도 전역 관리자여야 합니다.
  
* **Dynamics 365 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft CRM Online 내에서 전역 사용 권한을 가지며 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **장치 관리자**: 이 역할의 사용자는 Azure Active Directory에 연결된 모든 Windows 10 장치의 로컬 컴퓨터 관리자가 됩니다. Azure Active Directory의 장치 개체를 관리하는 기능이 없습니다.

* **디렉터리 읽기 권한자**: [동의 프레임워크](active-directory-integrating-applications.md)를 지원하지 않는 응용 프로그램에 할당될 레거시 역할입니다. 모든 사용자에게 할당되면 안 됩니다.

* **디렉터리 동기화 계정**: 사용하지 마십시오. 이 역할은 Azure AD Connect 서비스에 자동으로 할당되고 다른 사용에 적합하거나 지원되지 않습니다.

* **디렉터리 작성자**: [동의 프레임워크](active-directory-integrating-applications.md)를 지원하지 않는 응용 프로그램에 할당될 레거시 역할입니다. 모든 사용자에게 할당되면 안 됩니다.

* **Exchange 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft Exchange Online 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **전역 관리자 / 회사 관리자**: 이 역할의 사용자는 Azure Active Directory의 모든 관리 기능 및 Exchange Online, SharePoint Online 및 비즈니스용 Skype Online과 같은 Azure Active Directory에 페더레이션하는 서비스에 대한 액세스를 가집니다. Azure Active Directory 테넌트에 등록하는 사람이 전역 관리자가 됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다. 회사에 여러 전역 관리자가 있을 수 있습니다. 전역 관리자는 모든 사용자 및 모든 다른 관리자의 암호를 다시 설정할 수 있습니다.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "회사 관리자"로 식별됩니다. [Azure portal](https://portal.azure.com)에서 "전역 관리자"입니다.
  >
  >

* **게스트 초대자**: 이 역할의 사용자는 "구성원이 초대할 수 있음" 사용자 설정이 '아니요'로 설정된 경우 Azure Active Directory B2B 게스트 사용자 초대를 관리할 수 있습니다. B2B 공동 작업에 대한 자세한 내용은 [Azure AD B2B 공동 작업 정보](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 참조하세요. 다른 권한은 포함되지 않습니다.

* **Information Protection 관리자**: 이 역할의 사용자는 Azure Information Protection 서비스에 대한 사용자 권한만 가집니다. ID 보호 센터, Privileged Identity Management, Office 365 Service Health 또는 Office 365 Security & Compliance에 대한 사용자 권한은 부여받지 않습니다. Azure Information Protection 정책에 대한 레이블을 구성하고, 보호 템플릿을 관리하고, 보호를 활성화할 수 있습니다.

* **Intune 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft Intune Online 내에서 전역 사용 권한을 가집니다. 또한 이 역할은 정책을 연결하고 그룹을 만들고 관리하기 위해 사용자와 장치를 관리하는 기능을 포함합니다.

* **사서함 관리자**: 이 역할은 RIM Blackberry 장치에 대한 Exchange Online 전자 메일 지원의 일부로만 사용됩니다. 조직이 RIM Blackberry 장치에서 Exchange Online 전자 메일을 사용하지 않는 경우 이 역할을 사용하지 마십시오.

* **파트너 계층 1 지원**: 사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

* **파트너 계층 2 지원**: 사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

* **암호 관리자/기술 지원팀 관리자**: 이 역할의 사용자는 암호를 변경하고, 서비스 요청을 관리하고, 서비스 상태를 모니터링할 수 있습니다. 기술 지원팀 관리자는 사용자 및 다른 기술 지원팀 관리자에 대해서만 암호를 다시 설정할 수 있습니다. 

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "기술 지원팀 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서 "암호 관리자"입니다.
  >
  >
  
* **Power BI 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft Power BI 내에서 전역 사용 권한을 가지며 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US)를 참조하세요.

* **권한 있는 역할 관리자**: 이 역할의 사용자는 Azure Active Directory 및 Azure AD Privileged Identity Management 내에서 역할 할당을 관리할 수 있습니다. 또한 이 역할을 통해 Privileged Identity Management의 모든 측면을 관리할 수 있습니다.

* **보고서 구독자**: 이 역할의 사용자는 Office 365 관리 센터의 사용량 보고 데이터 및 보고서 대시보드와 PowerBI의 채택 컨텍스트 팩을 볼 수 있습니다. 또한 역할은 Azure AD의 로그온 보고서 및 활동과 함께 Microsoft Graph Reporting API에서 반환되는 데이터에 대한 액세스를 제공합니다. 보고서 구독자 역할에 할당된 사용자는 관련된 사용량 및 채택 메트릭에만 액세스할 수 있습니다. 설정을 구성하거나 Exchange와 같은 제품 특정 관리 센터에 액세스할 수 있는 관리자 권한은 없습니다. 

* **보안 관리자**: 이 역할의 사용자는 보안 읽기 역할의 모든 읽기 전용 권한 및 보안 관련 서비스(Azure Active Directory ID 보호, Azure Information Protection, Privileged Identity Management 및 Office 365 보안 및 규정 준수 센터)에 대한 구성을 관리하는 기능을 가집니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.

* **보안 판독기**: 이 역할의 사용자는 Azure Active Directory, ID 보호, Privileged Identity Management의 모든 정보를 포함한 전역 읽기 전용 액세스와 Azure Active Directory 로그인 보고서 및 감사 로그를 읽는 기능을 가집니다. 또한 역할은 Office 365 보안 및 규정 준수 센터의 읽기 전용 권한을 부여합니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.

* **서비스 지원 관리자**: 이 역할의 사용자는 Azure 및 Office 365 서비스에 대한 Microsoft로의 지원 요청 및 Azure Portal 및 Office 365 관리자 포털에서 서비스 대시보드 및 메시지 센터에 대한 뷰를 열 수 있습니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **SharePoint 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft SharePoint Online 내에서 전역 사용 권한을 가지며 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **비즈니스용 Skype / Lync Service 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft 비즈니스용 Skype 내에서 전역 사용 권한을 가지며 Azure Active Directory에서 Skype 관련 사용자 특성을 관리합니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능을 부여합니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "Lync Service 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서 "비즈니스용 Skype 서비스 관리자"입니다.
  >
  >

* **사용자 계정 관리자**: 이 역할의 사용자는 사용자 및 그룹의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능을 포함합니다. 몇 가지 제한 사항이 적용됩니다. 예를 들어 이 역할은 전역 관리자를 삭제할 수 없습니다. 사용자 계정 관리자는 사용자, 기술 지원팀 관리자 및 다른 사용자 계정 관리자에 대한 암호만 변경할 수 있습니다.

## <a name="administrator-permissions"></a>관리자 권한

### <a name="billing-administrator"></a>대금 청구 관리자

| 가능한 작업 | 불가능한 작업 |
| --- | --- |
|<p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>Office 제품의 대금 청구 및 구매 작업 수행</p> |<p>사용자 암호 다시 설정</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>감사 로그 보기</p>|

### <a name="conditional-access-administrator"></a>조건부 액세스 관리자

| 가능한 작업 | 불가능한 작업 |
| --- | --- |
|<p>회사 및 사용자 정보 보기</p><p>조건부 액세스 설정 관리</p> |<p>사용자 암호 다시 설정</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>감사 로그 보기</p>|

### <a name="global-administrator"></a>전역 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
|<p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>사용자 암호 다시 설정</p><p>다른 관리자의 암호를 다시 설정</p> <p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>다단계 인증 사용/사용 안 함</p><p>감사 로그 보기</p> |해당 없음 |

### <a name="password-administrator--helpdesk-administrator"></a>암호 관리자/기술 지원팀 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>사용자 및 다른 기술 지원팀 관리자에 대한 암호만 변경</p>|<p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>보고서 보기</p>|

### <a name="information-protection-administrator"></a>Information Protection 관리자
그런 다음 | 가능한 작업
-------- | ---------
Azure Information Protection | <li>전역 및 범위 지정된 정책에서 레이블 및 설정 구성<li>보호 템플릿 구성 및 관리<li>보호 활성화 또는 비활성화--
 
### <a name="reports-reader"></a>보고서 구독자 
가능한 작업 | 불가능한 작업
------ | ----------
Azure AD 로그인 보고서 및 감사 로그 보기<br>회사 및 사용자 정보 보기<br>Office 365 사용량 대시보드 액세스 | 사용자 보기 만들기 및 관리<br>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리<br>다른 사용자에게 관리 역할 위임<br>회사 정보 관리

### <a name="security-reader"></a>보안 판독기
| 그런 다음 | 가능한 작업 |
| --- | --- |
| ID 보호 센터 |모든 보안 보고서 및 보안 기능에 대한 설정 정보를 읽습니다.<ul><li>스팸 방지<li>암호화<li>데이터 손실 방지<li>맬웨어 방지<li>Advanced Threat Protection<li>피싱 방지<li>메일 흐름 규칙 |
| Privileged Identity Management |<p>Azure AD PIM에 표시되는 다음과 같은 모든 정보에 대한 읽기 전용 액세스 권한을 갖습니다. Azure AD 역할 할당에 대한 정책 및 보고서, 보안 검토, Azure AD 역할 할당 외의 시나리오에 대한 정책 데이터 및 보고서에 대한 향후 읽기 액세스 권한.<p>Azure AD PIM에 로그인을 하거나 어떠한 변경도 할 수 **없습니다**. PIM 포털이나 PowerShell을 통해, 이 역할을 가진 담당자는 사용자가 후보자일 경우 추가 역할(예: 전역 관리자 또는 권한 있는 역할 관리자)을 활성화할 수 있습니다. |
| <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터</p> |<ul><li>경고 읽기 및 관리<li>보안 정책 읽기<li>위협 인텔리전스, 클라우드 앱 검색 및 검색 및 조사의 격리 읽기<li>모든 보고서 읽기 |

### <a name="security-administrator"></a>보안 관리자
| 그런 다음 | 가능한 작업 |
| --- | --- |
| ID 보호 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>또한 암호 재설정을 제외한 모든 IPC 작업을 수행할 능력. |
| Privileged Identity Management |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>**없습니다** . |
| <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>고급 위협 방지 기능(맬웨어 및 바이러스 보호, 악성 URL 구성, URL 추적 등)에서 모든 설정을 구성할 수 있습니다. |

### <a name="service-administrator"></a>서비스 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p> |<p>사용자 암호 다시 설정</p><p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>감사 로그 보기</p> |

### <a name="user-account-administrator"></a>사용자 계정 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>사용자, 기술 지원팀 관리자 및 다른 사용자 계정 관리자에 대한 암호만 변경</p><p>사용자 보기 만들기 및 관리</p><p>제한 사항과 함께 사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리. 전역 관리자를 삭제하거나 다른 관리자를 만들 수 없습니다.</p> |<p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>다단계 인증 사용/사용 안 함</p><p>감사 로그 보기</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>동료를 전역 관리자로 추가하려면

1. 테넌트 디렉터리에 대한 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

   ![Azure AD 관리 센터 열기](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. **사용자 및 그룹 &gt; 모든 사용자**를 차례로 선택합니다.

3. 전역 관리자로 지정하려는 사용자를 찾아 해당 사용자에 대한 블레이드를 엽니다.

4. 사용자 블레이드에서 **디렉터리 역할**을 선택합니다.
 
5. 디렉터리 역할 블레이드에서 **전역 관리자** 역할을 선택하고 저장합니다.

## <a name="deprecated-roles"></a>사용되지 않는 역할

다음 역할은 사용할 수 없습니다. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다.

* 임시 라이선스 관리자
* 전자 메일 확인 사용자 생성자
* 장치 연결
* 장치 관리
* 장치 사용자
* 작업 공간 장치 연결

## <a name="next-steps"></a>다음 단계

* Azure 구독에 대한 관리자를 변경하는 방법에 대해 자세히 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](active-directory-how-subscriptions-associated-directory.md)
* [사용자 관리](active-directory-create-users.md)
* [암호 관리](active-directory-manage-passwords.md)
* [그룹 관리](active-directory-manage-groups.md)
