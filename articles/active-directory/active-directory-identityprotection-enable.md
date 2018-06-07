---
title: "Azure Active Directory ID 보호 활성화 | Microsoft Docs"
description: "Azure Active Directory ID 보호를 활성화하는 방법에 대해 알아봅니다."
services: active-directory
keywords: "Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f7a7ffaf-76bf-4cc7-96a1-86c944275c82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 39bdc17e63d382775db17097556c15dc00c3785b
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="enabling-azure-active-directory-identity-protection"></a>Azure Active Directory ID 보호 활성화
Azure Active Directory ID 보호는 의심스러운 로그인 활동 및 잠재적인 취약성에 대한 통합된 보기를 제공하고 알림, 수정 권장 사항 및 위험 기반 정책을 통해 비즈니스를 보호하도록 도움을 주는 새로운 기능입니다. 

서비스는 무차별 암호 대입 공격(brute-force attack), 자격 증명 유출, 낯선 위치 및 감염된 장치에서 로그인과 같은 신호를 기반으로 한 최종 사용자 및 권한 있는 (관리자) ID에 대한 의심스러운 활동을 검색하여 실시간으로 이러한 활동을 방지합니다. 가장 중요한 점은 이러한 의심스러운 활동에 따라 사용자 위험 심각도가 계산되고 위험 기반 정책을 구성할 수 있으며 자동으로 조직의 ID를 보호합니다. 자세한 내용은 [Azure Active Directory ID 보호](active-directory-identityprotection.md)를 참조하세요.

이 항목은 Azure Active Directory ID 보호를 활성화하는 방법을 보여 줍니다.

## <a name="steps-to-enable-azure-active-directory-identity-protection"></a>Azure Active Directory ID 보호를 활성화하는 단계
1. 전역 관리자로 Azure Portal에 [로그온](https://ms.portal.azure.com/)합니다. 
2. Azure 포털에서 **Marketplace**를 클릭합니다.
   
    ![만들기](./media/active-directory-identityprotection-enable/01.png "만들기")
3. 응용 프로그램 목록에서 **보안 + ID**를 클릭합니다.
   
    ![만들기](./media/active-directory-identityprotection-enable/02.png "만들기")
4. **Azure AD ID 보호**를 클릭합니다.
   
    ![만들기](./media/active-directory-identityprotection-enable/03.png "만들기")
5. **Azure AD ID 보호** 블레이드에서 **만들기**를 클릭합니다.
   
    ![만들기](./media/active-directory-identityprotection-enable/04.png "만들기")

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory ID 보호](active-directory-identityprotection.md)

