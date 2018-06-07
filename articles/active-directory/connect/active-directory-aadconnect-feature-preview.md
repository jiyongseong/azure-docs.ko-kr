---
title: "Azure AD Connect: 미리 보기의 기능 | Microsoft Docs"
description: "이 항목에서는 Azure AD Connect에서 미리 보기 상태인 기능을 더 자세하게 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: d4e76c85af7a9b90867d91a6290c4f4deea19a75
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="more-details-about-features-in-preview"></a>미리 보기 기능에 대한 자세한 내용
이 항목에서는 현재 미리 보기 상태인 기능을 사용하는 방법에 대해 설명합니다.

## <a name="group-writeback"></a>그룹 쓰기 저장
선택적 기능의 그룹 쓰기 저장에 대한 옵션을 사용하면 Exchange가 설치된 포리스트로 **Office 365 그룹** 을 쓰기 저장할 수 있습니다. 항상 클라우드에서 마스터되는 그룹입니다. Exchange 온-프레미스가 있는 경우 이러한 그룹을 온-프레미스에 쓰기 저장하여 온-프레미스 Exchange 사서함이 있는 사용자가 이 그룹에서 메일을 보내고 받을 수 있습니다.

Office 365 그룹에 대한 자세한 내용 및 사용 방법은 [여기](https://aka.ms/O365g)를 참조하세요.

Office 365 그룹은 온-프레미스 AD DS에서 배포 그룹으로 표현됩니다. 이 새 그룹 유형을 인식하려면 온-프레미스 Exchange 서버는 Exchange 2013 누적 업데이트 8(2015년 3월에 릴리스됨) 또는 Exchange 2016이어야 합니다.

**미리 보기 중 참고**

* 미리 보기에서 현재 주소록 특성이 채워지지 않습니다. 이 특성이 없으면 그룹이 GAL에 표시되지 않습니다. 이 특성을 채우는 가장 쉬운 방법은 Exchange PowerShell cmdlet `update-recipient`를 사용하는 것입니다.
* Exchange 스키마가 있는 포리스트만 그룹에 대한 유효한 대상이 됩니다. 검색된 Exchange가 없는 경우, 그룹 쓰기 저장을 사용하도록 설정할 수 없습니다.
* 현재 단일 포리스트 Exchange 조직 배포만 지원합니다. 둘 이상의 Exchange 조직 온-프레미스가 있는 경우 이러한 그룹을 다른 포리스트에 표시하려면 온-프레미스 GALSync 솔루션이 필요합니다.
* 그룹 쓰기 저장 기능은 보안 그룹 또는 배포 그룹을 처리하지 않습니다.

> [!NOTE]
> Azure AD Premium에 대한 구독은 그룹 쓰기 저장에 필요합니다.
> 
>

## <a name="user-writeback"></a>사용자 쓰기 저장
> [!IMPORTANT]
> 사용자 쓰기 저장 미리 보기 기능은 Azure AD Connect 2015년 8월 업데이트에서 제거되었습니다. 이 기능을 사용하도록 설정한 경우 사용하지 않도록 설정해야 합니다.
>
>

## <a name="next-steps"></a>다음 단계
[Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 계속 진행합니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
