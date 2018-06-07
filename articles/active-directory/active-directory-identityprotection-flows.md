---
title: Azure AD ID 보호를 사용하는 로그인 환경 | Microsoft Docs
description: ID 보호가 완화되었거나 사용자를 재구성한 경우 또는 정책에서 다단계 인증을 요구하는 경우의 사용자 환경에 대한 개요를 제공합니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 43681ce665bec088472b5c33f64d52d3d9fda5a1
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164206"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Azure AD ID 보호를 사용하는 로그인 환경
Azure Active Directory ID 보호를 사용하여 다음을 수행할 수 있습니다.

* 사용자에게 다단계 인증 등록 요구
* 위험한 로그인 및 손상된 사용자 처리

더 이상 사용자 이름과 암호를 제공하여 직접 로그인할 수 없기 때문에 이러한 문제에 대한 시스템의 응답은 사용자의 로그인 환경에 영향을 줍니다. 사용자를 업무 환경으로 안전하게 복귀시키려면 추가 단계가 필요합니다.

이 항목에서는 발생할 수 있는 모든 경우의 사용자 로그인 환경에 대한 개요를 제공합니다.

**Multi-Factor Authentication**

* Multi-Factor Authentication 등록

**위험한 로그인**

* 위험한 로그인 복구
* 위험한 로그인 차단됨
* 위험한 로그인 시 Multi-Factor Authentication 등록

**위험한 사용자**

* 손상된 계정 복구
* 손상된 계정 차단됨

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 등록
손상된 계정 복구 흐름 및 위험한 로그인 흐름 모두에 대한 최상의 사용자 환경은 사용자가 자체 복구할 수 있는 경우입니다. 사용자가 다단계 인증에 등록된 경우 보안 과제를 전달하는 데 사용될 수 있는 해당 계정으로 연결된 전화 번호가 있습니다. 계정 손상을 복구하는 데 기술 지원팀 또는 관리자의 관여는 필요하지 않습니다. 따라서 사용자를 Multi-Factor Authentication에 등록하는 것이 좋습니다. 

관리자는 사용자가 추가 보안 확인을 위해 계정을 설정해야 하는 정책을 설정할 수 있습니다. 이 정책을 사용하면 최대 14일 동안 다단계 인증 등록을 건너뛸 수 있습니다. 14일 유예 기간은 구성할 수 없습니다.

**다단계 인증 등록에 세 가지 단계가 있습니다.**

1. 첫 번째 단계에서 다단계 인증에 대한 계정을 설정하라는 요구 사항에 대한 알림이 나타납니다. 
   
    ![수정](./media/active-directory-identityprotection-flows/140.png "수정")
2. 다단계 인증을 설정하려면 연결하려는 방법을 시스템에 알려야 합니다.
   
    ![수정](./media/active-directory-identityprotection-flows/141.png "수정")
3. 시스템에서 챌린지를 제출하면 응답해야 합니다.
   
    ![수정](./media/active-directory-identityprotection-flows/142.png "수정")

## <a name="risky-sign-in-recovery"></a>위험한 로그인 복구
관리자가 로그인 위험에 대한 정책을 구성하는 경우 로그인하려고 할 때 영향을 받는 사용자에게 알려지게 됩니다. 

**위험한 로그인 흐름에는 두 단계가 있습니다.** 

1. 사용자는 새 위치, 장치 또는 앱에서 로그인과 같은 해당 로그인에 대해 비정상적인 점이 검색되었음을 통보받습니다. 
   
    ![수정](./media/active-directory-identityprotection-flows/120.png "수정")
2. 사용자가 보안 과제를 해결하여 해당 ID를 증명해야 합니다. 사용자가 Multi-Factor Authentication에 등록된 경우 전화 번호에 대한 보안 코드를 왕복해야 합니다. 손상된 계정이 아닌 위험한 로그인일 뿐이기 때문에 사용자는 이 흐름에서 암호를 변경할 필요가 없습니다. 
   
    ![수정](./media/active-directory-identityprotection-flows/121.png "수정")

## <a name="risky-sign-in-blocked"></a>위험한 로그인 차단됨
관리자는 로그인 위험 정책을 설정하도록 선택하여 위험 수준에 따라 로그인 시 사용자를 차단할 수 있습니다. 차단을 해제하려면 최종 사용자는 관리자나 기술 지원팀에 문의해야 하고 익숙한 위치 또는 장치에서 로그인을 시도할 수 있습니다. 다단계 인증을 해결하는 자체 복구는 이 경우에 불가능합니다.

![수정](./media/active-directory-identityprotection-flows/200.png "수정")

## <a name="compromised-account-recovery"></a>손상된 계정 복구
사용자 위험 보안 정책이 구성된 경우 정책에 지정된 사용자 위험 수준을 만족하는 사용자(따라서 손상되었다고 가정됨)는 로그인할 수 있기 전에 사용자 손상 복구 흐름을 통해 이동해야 합니다. 

**사용자 손상 복구 흐름에는 세 가지 단계가 있습니다.**

1. 사용자는 해당 계정 보안이 의심스러운 작업 또는 유출된 자격 증명으로 인해 위험에 노출되었다는 알림을 받습니다.
   
    ![수정](./media/active-directory-identityprotection-flows/101.png "수정")
2. 사용자가 보안 과제를 해결하여 해당 ID를 증명해야 합니다. Multi-Factor Authentication에 사용자를 등록하는 경우 손상되지 않도록 자체 복구할 수 있습니다. 해당 전화 번호에 보안 코드를 왕복해야 합니다. 
   
   ![수정](./media/active-directory-identityprotection-flows/110.png "수정")
3. 마지막으로, 다른 사람이 해당 계정에 액세스했을 수 있으므로 사용자는 해당 암호를 변경하도록 강제됩니다. 
   이러한 환경의 스크린 샷은 다음과 같습니다.
   
   ![수정](./media/active-directory-identityprotection-flows/111.png "수정")

## <a name="compromised-account-blocked"></a>손상된 계정 차단됨
차단 해제된 사용자 위험 보안 정책에 의해 차단된 사용자를 가져오려면 사용자는 관리자 또는 기술 지원팀에 문의해야 합니다. 다단계 인증을 해결하는 자체 복구는 이 경우에 불가능합니다.

![수정](./media/active-directory-identityprotection-flows/104.png "수정")

## <a name="reset-password"></a>암호 재설정
손상된 사용자가 로그인할 수 없으면 관리자가 해당 사용자에게 임시 암호를 생성할 수 있습니다. 사용자는 다음 로그인 시 자신의 암호를 변경해야 합니다.

![수정](./media/active-directory-identityprotection-flows/160.png "수정")

## <a name="see-also"></a>참고 항목
* [Azure Active Directory ID 보호](active-directory-identityprotection.md) 

