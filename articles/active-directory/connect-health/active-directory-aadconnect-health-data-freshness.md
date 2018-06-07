---
title: "Azure AD Connect Health - 상태 서비스 데이터가 최신 경고가 아닙니다 | Microsoft Docs"
description: "이 문서에서는 \"상태 서비스 데이터가 최신이 아닙니다\" 경고의 원인과 문제를 해결하는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: zhiweiw
manager: maheshu
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요
<li>Azure AD Connect Health는 2시간 동안 서버에서 모든 데이터 지점을 받지 못하면 데이터 새로 고침 경고를 생성합니다. 경고 제목은 **상태 서비스 데이터가 최신 경고가 아닙니다**입니다. </li>
<li>Connect Health에서 2시간 동안 서버로부터 전송되는 일부 데이터 요소를 받지 못하면 **경고** 상태 경고가 발생합니다. 경고 상태 경고는 테넌트 관리자에게 이메일 알림을 트리거하지 않습니다. </li>
<li>Connect Health에서 2시간 동안 서버로부터 전송되는 모든 데이터 요소를 받지 못하면 **오류** 상태 경고가 발생합니다. 오류 상태 경고는 테넌트 관리자에게 이메일 알림을 트리거합니다. </li>

>[!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](active-directory-aadconnect-health-gdpr.md#data-retention-policy)을 따릅니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계 
* [요구 사항 섹션](active-directory-aadconnect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.


## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)
