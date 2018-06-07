---
title: Azure의 하이브리드 ID 디자인 - 다단계 인증 요구 사항 | Microsoft Docs
description: 조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다.
documentationcenter: ''
services: active-directory
author: femila
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 998aebfc38c4a0971a5071faebdeae4dbca86690
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>하이브리드 ID 솔루션에 대한 다단계 인증 요구 사항 확인
모바일 시대에서는 사용자가 모든 장치에서 클라우드에 있는 데이터 및 응용 프로그램에 액세스하므로 정보 보호가 가장 중요한 일이 되었습니다.  보안 위반에 관한 뉴스 헤드라인이 매일 바뀌고 있습니다.  보안 위반을 완벽하게 방지하는 대책은 없지만, 다단계 인증을 통해 보안 위반을 방지하는 보안을 강화할 수 있습니다.
다단계 인증에 대한 조직 요구 사항을 평가하고 시작하세요. 즉 조직에서 보호하려고 하는 대상이 무엇인지 확인하세요.  이 평가는 조직의 사용자가 다단계 인증을 설정하고 사용할 수 있도록 하는 기술 요구 사항을 정의하기 위해 중요합니다.

> [!NOTE]
> MFA 및 그 기능에 대해 잘 모르는 경우 이 섹션을 계속하기 전에 [Azure Multi-Factor Authentication이란?](authentication/multi-factor-authentication.md) 문서를 읽어보는 것이 좋습니다.
> 
> 

다음 질문에 답변하세요.

* 회사가 Microsoft 앱을 보호하려고 하나요? 
* 이러한 앱은 어떻게 게시되나요?
* 회사에서 직원들이 온-프레미스 앱에 액세스할 수 있도록 원격 액세스를 제공하나요?

그렇다면 어떤 유형의 원격 액세스를 제공하나요? 또한 이러한 응용 프로그램에 액세스하는 사용자의 위치를 평가해야 합니다. 이 평가는 적절한 다단계 인증 전략을 정의하기 위해 또 다른 중요한 단계입니다. 다음 질문에 답변하세요.

* 사용자가 배치될 위치는 어디인가요?
* 다른 곳으로 배치할 수 있나요?
* 회사에서 사용자의 위치에 따른 제한을 설정하려 하나요?

이러한 요구 사항을 이해했다면 다단계 인증에 대한 사용자의 요구 사항을 평가하는 것도 중요합니다. 이 평가는 다단계 인증을 롤아웃하기 위한 요구 사항을 정의하므로 중요합니다. 다음 질문에 답변하세요.

* 사용자가 다단계 인증에 대해 잘 알고 있나요?
* 일부 사용자는 추가 인증을 제공해야 하나요?  
  * 그렇다면 외부 네트워크에서 액세스하거나 특정 응용 프로그램에서 액세스하거나 기타 조건에서 액세스하는 경우는 언제인가요?
* 사용자가 다단계 인증 설정 및 구현 방법에 대한 교육을 받아야 하나요?
* 회사에서 사용자가 다단계 인증을 사용할 수 있도록 하려는 주요 시나리오는 무엇인가요?

이전 질문에 답변한 후 온-프레미스에 다단계 인증이 이미 구현되어 있는지 파악할 수 있습니다. 이 평가는 조직의 사용자가 다단계 인증을 설정하고 사용할 수 있도록 하는 기술 요구 사항을 정의하기 위해 중요합니다. 다음 질문에 답변하세요.

* 회사에서 MFA로 권한 있는 계정을 보호해야 하나요?
* 회사가 규정 준수 상의 이유로 특정 응용 프로그램에 대해 MFA를 사용하도록 설정해야 하나요?
* 회사가 이러한 응용 프로그램에 대해 자격이 있는 모든 사용자에 대해 MFA를 사용하도록 설정해야 하나요, 아니면 관리자에 대해서만 MFA를 사용하도록 설정해야 하나요?
* MFA를 항상 사용하도록 설정해야 하나요, 아니면 사용자가 회사 네트워크 외부에서 로그인하는 경우에만 사용하도록 설정해야 하나요?

## <a name="next-steps"></a>다음 단계
[하이브리드 ID 채택 전략 정의](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>참고 항목
[디자인 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

