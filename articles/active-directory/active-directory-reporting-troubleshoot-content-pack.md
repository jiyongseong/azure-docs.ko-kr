---
title: "Azure Active Directory 활동 로그 콘텐츠 팩 오류 문제 해결 | Microsoft Docs"
description: "Azure Active Directory 활동 콘텐츠 팩의 오류 메시지 목록 및 수정 단계를 제공합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b22f387a0338246c7586f0f0735b4612a796691a
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory 활동 로그 콘텐츠 팩 오류 문제 해결 


Azure Active Directory 미리 보기용 Power BI 콘텐츠 팩에서 작업할 경우 다음과 같은 오류가 발생할 수 있습니다. 

- [새로 고침 실패](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [데이터 원본 자격 증명을 업데이트하지 못했습니다.](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [데이터를 가져오는 데 너무 오래 걸립니다.](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
이 항목에서는 발생할 수 있는 원인 및 이러한 오류 해결 방법에 대한 정보를 제공합니다.
 
## <a name="refresh-failed"></a>새로 고침 실패 
 
**이 오류가 표시되는 방법**: Power BI 또는 새로 고침 기록 실패 상태로 인해 전자 메일을 받습니다. 


| 원인 | 해결 방법 |
| ---   | ---        |
| 콘텐츠 팩에 연결되는 사용자의 자격 증명이 다시 설정되었지만 콘텐츠 팩의 연결 설정에서 업데이트되지 않은 경우 새로 고침 실패 오류가 발생할 수 있습니다. | Power BI에서 Azure Active Directory 활동 로그 대시보드(Azure Active Directory 활동 로그)에 해당하는 데이터 집합을 찾고 일정 새로 고침을 선택하고 Azure AD 자격 증명을 입력합니다. |
| 기본 콘텐츠 팩에서 발생한 데이터 문제로 인해 새로 고침에 실패할 수 있습니다. | 지원 티켓을 파일로 저장합니다. 자세한 내용은 [Azure Active Directory를 지원하는 방법](active-directory-troubleshooting-support-howto.md)을 참조하세요.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>데이터 원본 자격 증명을 업데이트하지 못했습니다. 
 
**이 오류가 표시되는 경우**: Power BI에서 Azure Active Directory 활동 로그(미리 보기) 콘텐츠 팩에 연결하는 경우입니다. 

| 원인 | 해결 방법 |
| ---   | ---        |
| 연결된 사용자는 전역 관리자 또는 보안 판독기나 보안 관리자가 아닙니다. | 전역 관리자 또는 보안 판독기 또는 보안 관리자인 계정을 사용하여 콘텐츠 팩에 액세스합니다. |
| 사용자의 테넌트가 프리미엄 테넌트가 아니거나 적어도 프리미엄 라이선스 파일이 있는 사용자가 없습니다. | 지원 티켓을 파일로 저장합니다. 자세한 내용은 [Azure Active Directory를 지원하는 방법](active-directory-troubleshooting-support-howto.md)을 참조하세요.|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>데이터를 가져오는 데 너무 오래 걸립니다. 
 
**이 오류가 표시되는 경우**: Power BI에서 콘텐츠 팩을 연결하면 데이터 가져오기 프로세스가 Azure Active Directory 활동 로그의 대시보드를 준비하기 시작합니다. "*데이터 가져오기...*"라는 메시지가 표시됩니다.  

| 원인 | 해결 방법 |
| ---   | ---        |
| 테넌트의 크기에 따라 이 단계는 몇 분에서 최대 30분까지 걸릴 수 있습니다. | 기다려 주세요. 한 시간 내에 대시보드를 표시하도록 메시지가 변경되지 않는 경우 지원 티켓을 파일로 저장합니다. 자세한 내용은 [Azure Active Directory를 지원하는 방법](active-directory-troubleshooting-support-howto.md)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

Azure Active Directory 미리 보기용 Power BI 콘텐츠 팩을 설치하려면 [여기](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/)를 클릭합니다.


