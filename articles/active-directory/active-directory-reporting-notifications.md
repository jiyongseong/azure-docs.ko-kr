---
title: Azure Active Directory Reporting 알림
description: 의심스러운 로그인에 Azure Active Directory Reporting 알림을 사용하는 방법입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: c279b923ed4e63d810861c579dd54b3a2b7f415e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-reporting-notifications"></a>Azure Active Directory Reporting 알림
## <a name="what-reports-generate-email-notifications"></a>어떤 보고서가 전자 메일 알림을 생성하나요?
지금은 비정상적인 로그인 활동 보고서만 메일 알림을 트리거합니다.

## <a name="what-is-an-irregular-sign-in"></a>"비정상적인 로그인"이란 무엇일까요?
비정상적인 로그인은 비정상적인 로그인 위치 및 장치와 결합된 "불가능한 이동" 조건을 기준으로 기계 학습 알고리즘에서 식별된 로그인입니다. 이는 해커가 이 계정을 사용하여 로그인하려고 하고 있음을 나타낼 수 있습니다.

## <a name="who-receives-the-email-notifications"></a>누가 전자 메일 알림을 받나요?
Active Directory Premium 라이선스가 할당된 모든 전역 관리자에게 전자 메일이 전송됩니다. 성공적인 배달을 위해 관리자 대체 전자 메일 주소로도 보냅니다. 관리자는 전자 메일이 누락되지 않도록 aad-alerts-noreply@mail.windowsazure.com 을 수신 허용 - 보낸 사람 목록에 포함해야 합니다.

## <a name="how-often-are-these-emails-sent"></a>이러한 전자 메일은 얼마나 자주 전송되나요?
지난 30일 또는 마지막 메일이 전송된 이후 중 더 짧은 시간 동안 비정상적인 로그인 활동 10개가 새로 발생한 경우 메일이 전송됩니다.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>전자 메일에 언급된 보고서에 액세스하려면 어떻게 해야 하나요?
링크를 클릭하면 Azure Portal 내의 보고서 페이지로 리디렉션됩니다. 보고서에 액세스하려면 다음 조건을 둘 다 충족해야 합니다.

* Azure 구독의 관리자 또는 공동 관리자
* 디렉터리의 전역 관리자 및 Active Directory Premium 라이선스가 할당됨. 자세한 내용은 [Azure Active Directory 버전](active-directory-whatis.md)을 참조하세요.

## <a name="can-i-turn-off-these-emails"></a>이러한 전자 메일을 끌 수 있나요?
예, Azure Portal 내에서 비정상적인 로그인과 관련된 알림을 끄려면 **구성**을 클릭한 다음 **알림** 아래에서 **사용 안 함**을 선택합니다.

## <a name="whats-next"></a>다음 단계
* 사용 가능한 보안, 감사 및 작업 보고서는 [Azure AD 보안, 감사 및 작업 보고서](active-directory-view-access-usage-reports.md) 확인
* [Azure Active Directory Premium 시작](active-directory-get-started-premium.md)
* [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](customize-branding.md)

