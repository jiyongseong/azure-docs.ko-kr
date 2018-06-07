---
title: Azure Portal의 다단계 인증 보고에 대한 참조 | Microsoft Docs
description: Azure Portal의 다단계 인증 보고에 대한 참조 정보
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/16/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e92d1383f3179f03194b460e0467314c137fd9e2
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Azure Portal의 다단계 인증 보고에 대한 참조

[Azure Portal](https://portal.azure.com)에서[Azure AD(Azure Active Directory) 보고](active-directory-reporting-azure-portal.md)를 통해 사용자 환경의 상태를 파악하는 데 필요한 모든 정보를 얻을 수 있습니다.

[로그인 활동 보고서](active-directory-reporting-activity-sign-ins.md)는 MFA(Multi-Factor Authentication) 사용에 대한 정보가 포함된 사용자 로그인 활동 및 관리되는 응용 프로그램에 대한 정보를 제공합니다. 

MFA 데이터는 MFA가 조직에서 어떻게 작동하는지에 대한 정보를 제공합니다. 이를 통해 다음과 같은 질문에 답할 수 있습니다. 

- 로그인에 MFA가 요구되었나요? 

- 사용자가 MFA를 어떻게 완료했나요? 

- 사용자가 MFA를 완료할 수 없었던 이유는 무엇인가요?  

모든 로그인 데이터를 통합하면 조직 내의 MFA 환경을 더 잘 이해할 수 있습니다. 이 데이터를 통해 다음과 같은 질문에 답할 수 있습니다. 

- MFA에 어려움을 겪은 사용자가 얼마나 되나요?  

- MFA 챌린지를 완료할 수 없었던 사용자가 얼마나 되나요? 

- 최종 사용자가 겪은 일반적인 MFA 문제는 무엇인가요? 


이 데이터는 Azure Portal alc [보고서 API](active-directory-reporting-api-getting-started-azure-portal.md)를 통해 사용이 가능합니다. 


## <a name="data-structure"></a>데이터 구조


MFA의 로그인 활동 보고서를 통해 다음 정보에 액세스 할 수 있습니다.

**MFA 필요:** 로그인에 MFA가 필요한지 여부. MFA는 사용자별 MFA, 조건부 액세스 또는 기타 이유로 인해 필요할 수 있습니다. 가능한 값은 `Yes` 또는 `No`입니다.

**MFA 인증 방법:** 사용자가 MFA를 완료하는 데 사용한 인증 방법. 가능한 값은 다음과 같습니다. 

- 문자 메시지 

- 모바일 앱 알림 

- 전화 통화(인증 전화) 

- 모바일 앱 확인 코드 

- 전화 통화(사무실 전화) 

- 전화 통화(대체 인증 전화) 

**MFA 인증 세부 정보:** 전화 번호의 삭제된 버전(예: +X XXXXXXXX64). 

**MFA 결과:** MFA 충족 여부에 대한 추가 정보.

- MFA가 충족되면 MFA가 어떻게 충족되었는지에 대한 자세한 정보가 이 열에 제공합니다. 

- MFA가 거부된 경우 거부 이유가 이 열에 제공됩니다. 가능한 값은 `Satisfied` 또는 `Denied`입니다. 

다음 섹션에는 MFA 결과 필드에 사용할 수 있는 문자열 값이 나열됩니다.

## <a name="status-strings"></a>상태 문자열

이 섹션에는 MFA 결과 상태 문자열의 가능한 값이 나열됩니다.

### <a name="satisfied-status-strings"></a>충족한 상태 문자


- Azure Multi-Factor Authentication

    - 클라우드에서 완료 

    - 테넌트에 구성된 정책으로 인해 만료 

    - 등록 메시지가 표시됨 

    - 토큰 클레임으로 충족 

    - 외부 공급자가 제공한 클레임으로 충족 

    - 강력한 인증으로 충족 

    - 실행된 흐름이 Windows 브로커 로그온 흐름이어서 건너뜀 

    - 앱 암호로 인해 건너뜀 

    - 위치로 인해 건너뜀 

    - 등록된 장치로 인해 건너뜀 
    
    - 기억된 장치로 인해 건너뜀 

    - 성공적으로 완료됨 

- 다단계 인증을 위해 외부 공급자로 리디렉션됨 

 
### <a name="denied-status-strings"></a>거부된 상태 문자

Azure Multi-Factor Authentication 거부됨; 

- 인증 진행 중 

- 중복된 인증 시도 

- 잘못된 코드를 너무 많이 입력함 

- 잘못된 인증 

- 잘못된 모바일 앱 확인 코드 

- 잘못된 구성 

- 전화 통화가 음성 사서함으로 연결됨 

- 전화 번호 형식이 잘못됨 

- 서비스 오류 

- 사용자의 전화에 도달할 수 없음 

- 모바일 앱 알림을 장치에 보낼 수 없음 

- 모바일 앱 알림을 보낼 수 없음 

- 사용자가 인증을 거부했음 

- 사용자가 모바일 앱 알림에 응답하지 않음 

- 사용자에게 등록된 인증 방법이 없음 

- 사용자가 잘못된 코드를 입력했음 

- 사용자가 잘못된 PIN을 입력했음 

- 인증을 성공하지 않은 상태에서 사용자가 전화를 끊음 

- 사용자가 차단됨 

- 사용자가 확인 코드를 입력하지 않음 

- 사용자를 찾을 수 없음 
 
- 확인 코드가 이미 한 번 사용됨 



## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Active Directory 보고](active-directory-reporting-azure-portal.md)를 참조하세요.




























