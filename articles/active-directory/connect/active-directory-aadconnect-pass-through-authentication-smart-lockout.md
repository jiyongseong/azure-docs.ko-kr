---
title: 'Azure AD Connect: 통과 인증 - 스마트 잠금 | Microsoft Docs'
description: 이 문서에서는 Azure AD(Active Directory) 통과 인증이 클라우드에서의 무차별 암호 대입 공격으로부터 온-프레미스 계정을 보호하는 방법에 대해 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 9477d47824213d7ea15bcf6c6b615a220bae2e48
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory 통과 인증: 스마트 잠금

## <a name="overview"></a>개요

Azure Active Directory (Azure AD)는 무차별 암호 대입 공격으로부터 보호하고 실제 사용자가 Office 365 및 SaaS 응용 프로그램에서 잠기지 않게 차단합니다. 이 기능을 *스마트 잠금*이라고 하며 로그인 방법으로 통과 인증을 사용할 때 지원됩니다. 스마트 잠금은 통과 일증을 사용하는 테넌트만이 아니라 기본적으로 모든 테넌트에 대해 사용하도록 설정되어 있습니다. 스마트 잠금을 사용하면 사용자 계속을 지속적으로 보호할 수 있습니다.

스마트 잠금은 실패한 로그인 시도 기록을 보존합니다. 특정 *잠금 임계값*에 도달하면 *잠금 기간*이 시작됩니다. 잠금 기간 동안에는 공격자의 로그인 시도가 모두 거부됩니다. 공격이 지속될 경우, 잠금 기간이 종료된 뒤에 실패한 로그인 시도가 발생하면 잠금 기간이 더 길어집니다.

>[!NOTE]
>기본 잠금 임계값은 10회 실패이며 기본 잠금 기간은 60초입니다.

스마트 잠금은 실제 사용자의 로그인과 공격자의 로그인을 구분하며 대부분의 경우 공격자만 차단합니다. 이 기능은 공격자가 악의적으로 실제 사용자를 잠그지 못하게 차단합니다. 스마트 잠금은 과거의 로그인 행위, 사용자의 장치 및 브라우저, 기타 신호를 사용하여 실제 사용자와 공격자를 구분합니다. 스마트 잠금의 알고리즘은 지속적으로 개선됩니다.

통과 인증은 암호 유효성 검사 요청을 온-프레미스 Active Directory로 전달하므로 공격자가 사용자의 Active Directory 계정을 잠그지 못하게 차단해야 합니다. Active Directory에도 [계정 잠금 임계값](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)과 [다음 시간 후 계정 잠금 수를 원래대로 설정](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)이라는 자체 계정 잠금 정책이 있습니다. Azure AD의 잠금 임계값과 잠금 기간 값을 적절하게 구성하여 공격자가 온-프레미스 Active Directory에 도달하기 전에 클라우드의 공격을 필터링해야 합니다.

>[!NOTE]
>>스마트 잠금 기능은 추가 비용 없이 제공되며, 기본적으로 _모든 고객이 사용_할 수 있습니다. 그러나 Graph API를 사용하여 Azure AD의 잠금 임계값 및 잠금 기간을 수정하기 위해 테넌트가 Azure AD Premium P2에 대해 활성화되어야 합니다. 

사용자의 온-프레미스 Active Directory 계정을 안전하게 보호하기 위해서는 다음을 확인해야 합니다.

   * Azure AD 잠금 임계값이 Active Directory 계정 잠금 임계값_보다 작습니다_. Active Directory의 계정 잠금 임계값을 Azure AD의 잠금 임계값보다 최소 2~3배 이상 크게 설정합니다.
   * Azure AD 잠금 기간(단위: 초)이 Active Directory의 다음 시간(단위: 분) 후 계정 잠금 수를 원래대로 설정 값_보다 길어야_ 합니다.

>[!IMPORTANT]
>현재로서는 사용자의 클라우드 계정이 스마트 잠금 기능에 의해 잠긴 경우 관리자가 잠금을 해제할 수 없습니다. 잠금 기간이 만료될 때까지 기다려야 합니다.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Active Directory 계정 잠금 정책 확인

Active Directory 계정 잠금 정책을 확인하려면 다음 단계를 수행합니다.

1.  그룹 정책 관리 도구를 엽니다.
2.  모든 사용자에게 적용된 그룹 정책(예: **기본 도메인 정책**)을 편집합니다.
3.  **컴퓨터 구성** > **정책** > **Windows 설정** > **보안 설정** > **계정 정책** > **계정 잠금 정책**으로 이동합니다.
4.  **계정 잠금 임계값**과 **다음 시간 후 계정 잠금 수를 원래대로 설정** 값을 확인합니다.

![Active Directory 계정 잠금 정책](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Graph API를 사용하여 테넌트의 스마트 잠금 값 관리(Premium 라이선스 필요)

>[!IMPORTANT]
>Graph API를 이용하여 Azure AD 잠금 임계값과 잠금 기간 값을 수정하려면 Azure AD Premium P2 라이선스가 필요합니다. 테넌트의 전역 관리자 계정도 필요합니다.

[Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 Azure AD 스마트 잠금 값을 읽고, 설정하고, 업데이트할 수 있습니다. 해당 작업은 프로그래밍 방식으로 수행할 수도 있습니다.

### <a name="view-smart-lockout-values"></a>스마트 잠금 값 보기

테넌트의 스마트 잠금 값을 보려면 다음 단계를 수행합니다.

1. Graph Explorer에 테넌트의 전역 관리자로 로그인합니다. 메시지가 표시되면 요청된 권한에 대해 액세스를 부여합니다.
2. **권한 수정**을 선택하고 **Directory.ReadWrite.All** 권한을 선택합니다.
3. Graph API 요청을 다음과 같이 구성합니다. 버전을 **BETA**로, 요청 유형을 **GET**으로, URL을 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`로 설정합니다.
4. **쿼리 실행**을 선택하여 테넌트의 스마트 잠금 값을 봅니다. 앞서 테넌트의 값을 설정하지 않은 경우 빈 집합이 표시됩니다.

### <a name="set-smart-lockout-values"></a>스마트 잠금 값 설정

테넌트의 스마트 잠금 값을 설정하려면 다음 단계를 수행합니다(처음 한 번만 수행하면 됩니다).

1. Graph Explorer에 테넌트의 전역 관리자로 로그인합니다. 메시지가 표시되면 요청된 권한에 대해 액세스를 부여합니다.
2. **권한 수정**을 선택하고 **Directory.ReadWrite.All** 권한을 선택합니다.
3. Graph API 요청을 다음과 같이 구성합니다. 버전을 **BETA**로, 요청 유형을 **POST**로, URL을 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`로 설정합니다.
4. 아래의 JSON 요청을 복사하여 **요청 본문** 필드에 붙여넣습니다.
5. **쿼리 실행**을 선택하여 테넌트의 스마트 잠금 값을 설정합니다.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>사용하지 않는 경우 **BannedPasswordList** 값은 비워 두고(**""**), **EnableBannedPasswordCheck** 값은 **false**로 설정합니다.

[스마트 잠금 값 보기](#view-smart-lockout-values)의 단계를 사용하여 테넌트의 스마트 잠금 값을 올바르게 설정했는지 확인합니다.

### <a name="update-smart-lockout-values"></a>스마트 잠금 값 업데이트

테넌트의 스마트 잠금 값을 (기존에 설정한 적이 있는 경우) 업데이트하려면 다음 단계를 수행합니다.

1. Graph Explorer에 테넌트의 전역 관리자로 로그인합니다. 메시지가 표시되면 요청된 권한에 대해 액세스를 부여합니다.
2. **권한 수정**을 선택하고 **Directory.ReadWrite.All** 권한을 선택합니다.
3. [테넌트의 스마트 잠금 값을 보려면 이 단계를 수행합니다](#view-smart-lockout-values). **displayName**이 **PasswordRuleSettings**인 항목의 `id` 값(GUID)을 복사합니다.
4. Graph API 요청을 다음과 같이 구성합니다. 버전을 **BETA**로, 요청 유형을 **PATCH**로, URL을 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`로 설정합니다. `<id>`에는 3단계에서 복사한 GUID를 입력합니다.
5. 아래의 JSON 요청을 복사하여 **요청 본문** 필드에 붙여넣습니다. 스마트 잠금 값을 적절하게 변경합니다.
6. **쿼리 실행**을 선택하여 테넌트의 스마트 잠금 값을 업데이트합니다.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

[스마트 잠금 값 보기](#view-smart-lockout-values)의 단계를 사용하여 테넌트의 스마트 잠금 값을 올바르게 업데이트했는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.
