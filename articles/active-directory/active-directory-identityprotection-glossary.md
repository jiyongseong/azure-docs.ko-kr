---
title: "Azure Active Directory ID 보호 용어집 | Microsoft Docs"
description: "Azure Active Directory ID 보호 용어집"
services: active-directory
keywords: "Azure Active Directory ID 보호, Cloud App Discovery, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약성, 보안 정책, 용어집"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 30cf3911d0f22e2d9351fc606cd6697ef437e452
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory ID 보호 용어집
### <a name="at-risk-user"></a>위험(사용자)
하나 이상의 활성 위험 이벤트가 있는 사용자입니다. 

### <a name="atypical-sign-in-location"></a>비정상적인 로그인 위치
특정 사용자, 비슷한 사용자 또는 테넌트에 일반적이지 않은 지리적 위치에서 시도하는 로그인입니다.

### <a name="azure-ad-identity-protection"></a>Azure AD ID 보호
조직의 ID에 영향을 주는 위험 이벤트와 잠재적 취약성에 대한 통합된 뷰를 제공하는 Azure Active Directory의 보안 모듈입니다.

### <a name="conditional-access"></a>조건부 액세스
리소스에 대한 액세스를 보호하는 정책입니다. 조건부 액세스 규칙은 Azure Active Directory에 저장되고 리소스에 대한 액세스 권한을 부여하기 전에 Azure AD에서 평가됩니다.  예제 규칙은 사용자 위치, 장치 상태 또는 사용자 인증 방법에 따라 액세스를 제한하게 됩니다.

### <a name="credentials"></a>자격 증명
로컬 및 네트워크 리소스에 대한 액세스 권한을 얻는 데 사용되는 ID 및 ID의 증명을 포함하는 정보입니다. 자격 증명의 예는 사용자 이름 및 암호, 스마트 카드 및 인증서가 있습니다.

### <a name="event"></a>행사
Azure Active Directory에서 작업의 기록입니다.

### <a name="false-positive-risk-event"></a>가양성(위험 이벤트)
위험 이벤트 상태는 ID 보호 사용자가 수동으로 설정하며 위험 이벤트를 조사하여 위험 이벤트로 플래그가 잘못 지정되었다는 것을 나타냅니다.

### <a name="identity"></a>ID
암호 또는 인증서와 같은 조건에 따라 인증이라는 방법으로 확인되어야 하는 개인 및 엔터티입니다.

### <a name="identity-risk-event"></a>ID 위험 이벤트
ID 보호에서 비정상으로 플래그가 지정되고 ID가 손상되었음을 나타낼 수 있는 AAD 이벤트입니다.

### <a name="ignored-risk-event"></a>무시된(위험 이벤트)
위험 이벤트가 수정 작업을 수행하지 않고 닫혀 있음을 나타내는 ID 보호 사용자가 수동으로 설정한 위험 이벤트 상태입니다.

### <a name="impossible-travel-from-atypical-locations"></a>비정상적 위치에서 불가능한 이동
동일한 사용자에 대한 두 개의 로그인이 감지되는 경우, 하나 이상이 비정상적인 로그인 위치에서 비롯되고 두 로그인 간의 시간이 이러한 위치 간의 물리적인 이동에 걸리는 시간보다 짧으면 트리거되는 위험 이벤트입니다.  

### <a name="investigation"></a>조사
수정 또는 완화 단계가 필요한지를 결정하고 ID가 어떻게 손상되었고 손상된 ID가 어떻게 사용되었는지를 이해하기 위해 활동, 로그 및 위험 이벤트와 관련된 위험 기타 관련 정보를 검토하는 과정입니다.

### <a name="leaked-credentials"></a>유출된 자격 증명
연구원이 Dark 웹에 공개적으로 게시하여 현재 사용자 자격 증명(사용자 이름 및 암호)가 발견된 경우 트리거되는 위험 이벤트입니다.

### <a name="mitigation"></a>해결 방법
ID 또는 장치를 안전한 상태로 복원하지 않고 공격자의 능력을 제한하거나 제거하여 공격에 노출된 ID 또는 장치를 악용하는 작업입니다. 완화 조치는 ID 또는 장치와 연결된 이전 위험 이벤트를 확인하지 않습니다.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
인증서 등 사용자가 가진 정보를 포함하는 두 개 이상의 인증 방법이 필요한 인증 방법입니다. 사용자 이름, 암호 또는 암호 문구 등 사용자가 아는 정보 혹은 지문 등 물리적 특성 및 개인 서명 등 개인 특성일 수 있습니다.

### <a name="offline-detection"></a>오프라인 검색
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 사후 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="policy-condition"></a>정책 조건
엔터티(그룹, 사용자, 앱, 장치 플랫폼, 장치 상태, IP 범위, 클라이언트 형식)를 정의하는 보안 정책의 일부는 정책에 포함되었거나 제외됩니다.

### <a name="policy-rule"></a>정책 규칙
정책을 트리거하는 상황 및 정책이 트리거될 때 수행된 작업을 설명하는 보안 정책의 일부입니다.

### <a name="prevention"></a>방지
손상이 우려되거나 손상된 ID 또는 장치를 남용하여 조직에 대한 손상을 방지하는 동작입니다. 예방 조치는 장치 또는 ID를 보호하지 않고 이전 위험 이벤트를 해결하지 않습니다.

### <a name="privileged-user"></a>권한 있는(사용자)
위험 이벤트가 발생한 경우 사용자는 전역 관리자, 대금 청구 관리자, 서비스 관리자, 사용자 관리자 및 암호 관리자와 같은 Azure Active Directory에서 하나 이상의 리소스에 대한 영구 또는 임시 관리자 권한을 가집니다. 

### <a name="real-time"></a>실시간
실시간 탐지를 참조하세요.

### <a name="real-time-detection"></a>실시간 탐지
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 이벤트를 진행하기 전에 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="remediated-risk-event"></a>수정된(위험 이벤트)
위험 이벤트 상태는 ID 보호에서 자동으로 설정되며 위험 이벤트가 이러한 유형의 위험 이벤트에 대한 표준 수정 작업을 사용하여 수정된다는 점을 나타냅니다. 예를 들어, 사용자 암호가 다시 설정되면 이전 암호가 손상되었음을 나타내는 많은 위험 이벤트가 자동으로 수정됩니다.

### <a name="remediation"></a>재구성
이전에 손상이 우려되거나 손상된 ID 또는 장치를 보호하는 작업입니다. 수정 작업은 ID 또는 장치를 안전한 상태로 복원하고 ID 또는 장치와 연결된 이전 위험 이벤트를 확인합니다.

### <a name="resolved-risk-event"></a>확인된(위험 이벤트)
사용자가 ID 보호 외부에서 적절한 수정 작업을 수행했으며 위험 이벤트가 닫힌 것으로 간주되어야 한다는 점을 나타내며 ID 보호 사용자가 수동으로 설정한 위험 이벤트 상태입니다.

### <a name="risk-event-status"></a>위험 이벤트 상태
이벤트가 활성 상태인지, 닫힌 경우 이유가 무엇인지를 나타내는 위험 이벤트의 속성입니다.

### <a name="risk-event-type"></a>위험 이벤트 유형
위험한 것으로 간주되는 이벤트를 발생시킨 비정상의 유형을 나타내는 위험 이벤트에 대한 범주입니다.

### <a name="risk-level-risk-event"></a>위험 수준(위험 이벤트)
ID 보호 사용자가 조직에 대한 위험을 줄이기 위해 수행하는 동작을 우선하도록 하는 위험 이벤트의 심각도를 표시(높음, 중간 또는 낮음)합니다. 

### <a name="risk-level-sign-in"></a>위험 수준(로그인)
사용자의 ID를 사용하려는 다른 사용자라는 특정 로그인에 대한 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-user-compromise"></a>위험 수준(사용자 손상)
ID가 손상된 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-vulnerability"></a>위험 수준(취약점)
ID 보호 사용자가 조직에 대한 위험을 줄이기 위해 수행하는 동작을 우선하도록 하는 취약점의 심각도를 표시(높음, 중간 또는 낮음)합니다.

### <a name="secure-identity"></a>보안(ID)
잠재적으로 노출된 ID를 비교할 수 없는 상태로 복원하기 위해 이미지로 다시 설치한 암호 변경 또는 컴퓨터와 같은 수정 작업을 수행합니다.

### <a name="security-policy"></a>보안 정책
정책 규칙 및 조건의 컬렉션입니다. 사용자, 그룹, 앱, 장치, 장치 플랫폼, 장치 상태, IP 범위 및 Auth2.0 클라이언트 유형과 같은 엔터티에 정책을 적용할 수 있습니다. 정책을 사용하는 경우 정책에 포함된 엔터티가 리소스에 토큰을 발급할 때마다 평가됩니다.

### <a name="sign-in-v"></a>로그인(v)
Azure Active Directory의 ID를 인증하려면.

### <a name="sign-in-n"></a>로그인(n)
Azure Active Directory에서 ID를 인증하는 프로세스 또는 동작이며 이 작업을 캡처하는 이벤트입니다.

### <a name="sign-in-from-anonymous-ip-address"></a>익명 IP 주소에서 로그인
익명 프록시 IP 주소로 식별된 IP 주소에서 성공적인 로그인 후에 트리거된 위험 이벤트입니다.

### <a name="sign-in-from-infected-device"></a>감염된 장치에서 로그인
하나 이상의 손상된 장치에서 사용된다고 알려진 IP 주소에서 로그인이 시작되는 경우 트리거된 위험 이벤트는 봇 서버와 적극적으로 통신하려고 시도합니다.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>의심스러운 동작으로 IP 주소에서 로그인
짧은 기간 동안 여러 사용자 계정에서 여러 번 실패한 로그인을 시도한 IP 주소의 성공적인 로그인 후에 트리거된 위험 이벤트입니다.

### <a name="sign-in-from-unfamiliar-location"></a>알 수 없는 위치에서 로그인
사용자가 새 위치(IP, 위도/경도 및 ASN)에서 성공적으로 로그인한 경우 트리거되는 위험 이벤트입니다.

### <a name="sign-in-risk"></a>로그인 위험
위험 수준(로그인)을 참조하세요.

### <a name="sign-in-risk-policy"></a>로그인 위험 정책
특정 로그인에 대한 위험을 평가하고 미리 정의된 조건 및 규칙에 따라 완화를 적용하는 조건부 액세스 정책입니다.

### <a name="user-compromise-risk"></a>사용자 손상 위험
위험 수준(사용자 손상)을 참조하세요

### <a name="user-risk"></a>사용자 위험
위험 수준(사용자 손상)을 참조하세요.

### <a name="user-risk-policy"></a>사용자 위험 정책
로그인을 고려하고 미리 정의된 조건 및 규칙에 따라 완화를 적용하는 조건부 액세스 정책입니다.

### <a name="users-flagged-for-risk"></a>위험에 대한 플래그가 지정된 사용자
활성화되거나 수정된 위험 이벤트를 가진 사용자입니다

### <a name="vulnerability"></a>취약점
디렉터리가 악용 또는 위협에 취약하게 만드는 Azure Active Directory의 구성 또는 조건입니다.

## <a name="see-also"></a>참고 항목
* [Azure Active Directory ID 보호](active-directory-identityprotection.md)

