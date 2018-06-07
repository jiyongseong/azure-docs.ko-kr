---
title: "2단계 인증 설정 관리 | Microsoft Docs"
description: "연락처 정보를 변경하거나 장치를 구성하는 등 Azure Multi-Factor Authentication을 사용하는 방법을 관리합니다."
services: multi-factor-authentication
keywords: "다단계 인증 클라이언트, 인증 문제, 상관관계 ID"
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 8d84574283aa0c94ce303b0a7e3bde335c0eb2b8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-your-settings-for-two-step-verification"></a>2단계 인증을 위한 설정 관리
이 문서에서는 2단계 인증 또는 다단계 인증에 대한 설정을 업데이트하는 방법에 대한 질문에 대답합니다. 계정에 로그인하는 데 문제가 있는 경우 문제 해결 도움말을 보려면 [2단계 인증에 문제 발생](multi-factor-authentication-end-user-troubleshoot.md)을 참조하세요.

## <a name="where-to-find-the-settings-page"></a>설정 페이지 위치
회사에서 Azure Multi-Factor Authentication을 설정하는 방법에 따라 전화 번호와 같은 설정을 변경할 수 있는 몇 가지 위치가 있습니다.

회사에서 2단계 인증을 관리하기 위한 특정 URL 또는 단계 보내기를 지원하는 경우 해당 지침을 따릅니다. 그렇지 않으면 다른 모든 사용자는 다음 지침을 따르면 됩니다. 이러한 지침을 따랐지만 동일한 옵션이 표시되지 않는다면 회사 또는 학교에서 고유한 포털을 사용자 지정했을 수 있습니다. 관리자에게 Azure Multi-Factor Authentication 포털에 대한 링크 요청

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.  
2. 오른쪽 맨 위에서 계정 이름을 선택한 후 **프로필**을 선택합니다.  
3. **추가 보안 인증**을 선택합니다.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 추가 보안 인증 페이지에 설정이 로드됩니다.

    ![검사](./media/multi-factor-authentication-end-user-manage/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>전화 번호를 변경하거나 기타 전화 번호를 추가하려는 경우
기타 인증 전화 번호를 추가로 구성하는 것은 중요합니다.  기본 전화 번호로 등록된 기기에 모바일 앱이 설치된 경우가 대부분이므로 이 전화를 분실하거나 도난당했을 경우 기타 전화 번호가 있어야만 계정에 다시 로그인할 수 있습니다.

> [!NOTE]
> 기본 전화 번호에 대한 액세스 권한이 없고 계정에 대한 도움이 필요한 경우 [2단계 인증에 문제 발생](multi-factor-authentication-end-user-troubleshoot.md)에서 도움말 항목을 참조하세요.  

**기본 전화 번호를 변경하려면:**  

1. 추가 보안 인증 페이지에서 현재 전화 번호가 있는 텍스트 상자를 선택하고 새 전화 번호로 편집합니다.  
2. **저장**을 선택합니다.  
3. 기본 설정된 인증 옵션에 사용할 번호인 경우 저장하기 전에 새 번호를 확인해야 합니다.  

**기타 전화 번호를 추가하려면:**  

1. 추가 보안 인증 페이지에서 **대체 인증 전화** 옆의 상자를 선택합니다.  
2. 텍스트 상자에 기타 전화 번호를 입력합니다.  
3. **저장**을 선택하면 변경 내용이 완료됩니다.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>신뢰할 수 있는 것으로 표시된 장치에서 2단계 인증이 다시 필요

조직 설정에 따라 브라우저에서 2단계 인증을 수행할 때 "**X**일 동안 다시 묻지 않음"이라는 확인란이 포함될 수 있습니다. 이 확인란을 선택한 후 장치를 분실하거나 계정이 손상된 것으로 생각되는 경우 2단계 인증을 모든 장치로 복원해야 합니다.

1. 추가 보안 확인 페이지에서 **이전에 신뢰할 수 있는 장치에서 Multi-Factor Authentication 복원**을 선택합니다.
2. 장치에 다음에 로그인하면 2단계 인증을 수행하라는 메시지가 표시됩니다.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>이전 장치에서 Microsoft Authenticator를 제거하고 새 장치로 이동하려는 경우
장치에서 앱을 제거하거나 장치를 재설정하는 경우 백 엔드에서 정품 인증을 제거하지 않습니다. 자세한 내용은 [Microsoft Authenticator](microsoft-authenticator-app-how-to.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [2단계 인증에 문제 발생](multi-factor-authentication-end-user-troubleshoot.md)에 대한 문제 해결 팁 및 도움말 얻기
* 2단계 인증을 지원하지 않는 앱에 대해 [앱 암호](multi-factor-authentication-end-user-app-passwords.md) 설정.
