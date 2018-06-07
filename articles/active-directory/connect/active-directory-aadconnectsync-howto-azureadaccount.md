---
title: "Azure AD Connect 동기화: Azure AD 서비스 계정을 관리하는 방법 | Microsoft Docs"
description: "이 항목은 Azure AD 서비스 계정을 복원하는 방법을 설명합니다."
services: active-directory
keywords: "AADSTS70002, AADSTS50054, Azure AD Connect 동기화 커넥터 서비스 계정의 암호를 재설정하는 방법"
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: cfd807706ebbf0bfa6ea699129cb197f1c79db8c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure AD Connect 동기화: Azure AD 서비스 계정을 관리하는 방법
Azure AD Connector가 사용하는 서비스 계정은 무료입니다. 자격 증명을 재설정해야 할 경우 이 항목을 참조하세요. 예를 들어 전역 관리자가 PowerShell을 사용하여 실수로 서비스 계정의 암호를 재설정한 경우입니다.

## <a name="reset-the-credentials"></a>자격 증명 다시 설정
인증 문제로 인해 Azure AD Connector에서 정의된 서비스 계정으로 Azure AD에 연결할 수 없다면 암호를 재설정할 수 있습니다.

1. Azure AD Connect 동기화 서버에 로그인하고 PowerShell을 시작합니다.
2. `Add-ADSyncAADServiceAccount`.  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Azure AD 전역 관리자 자격 증명을 제공합니다.

이 cmdlet은 서비스 계정의 암호를 재설정하고 Azure AD와 동기화 엔진에서 암호를 업데이트합니다.

## <a name="known-issues-these-steps-can-solve"></a>이 단계에서 해결할 수 있다고 알려진 문제
이 섹션은 Azure AD 서비스 계정에서 다시 설정된 자격 증명에 의해 수정된 고객이 보고한 오류 목록입니다.

- - -
이벤트 6900  
서버가 암호 변경 알림을 처리하는 동안 예기치 않은 오류가 발생했습니다.  
AADSTS70002: 자격 증명의 유효성 검사 오류. AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS50054: 이전 암호가 인증에 사용되었습니다.

- - -
이벤트 659  
암호 정책 동기화 구성을 검색하는 도중 오류 발생. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: 자격 증명의 유효성 검사 오류. AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS50054: 이전 암호가 인증에 사용되었습니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

