---
title: "Azure AD Connect 동기화: AD DS 계정 암호 변경 | Microsoft Docs"
description: "이 항목 문서에서는 AD DS 계정의 암호가 변경된 후 Azure AD Connect를 업데이트하는 방법을 설명합니다."
services: active-directory
keywords: "AD DS 계정, Active Directory 계정, 암호"
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 5bc296b594917f68f35d7d15653b7b0c60cde514
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="changing-the-ad-ds-account-password"></a>AD DS 계정 암호 변경
AD DS 계정은 Azure AD Connect가 온-프레미스 Active Directory와 통신하는 데 사용하는 사용자 계정을 나타냅니다. AD DS 계정의 암호를 변경하는 경우 Azure AD Connect 동기화 서비스를 새 암호로 업데이트해야 합니다. 그렇지 않으면 더 이상 온-프레미스 Active Directory와 올바르게 동기화될 수 없으며 다음 오류가 발생합니다.

* Synchronization Service Manager에서 온-프레미스 AD를 사용하여 가져오기 또는 내보내기 작업을 수행하면 **no-start-credentials** 오류를 나타내며 실패합니다.

* Windows 이벤트 뷰어에서 응용 프로그램 이벤트 로그에는 **이벤트 ID 6000** 오류 및 메시지 **'자격 증명이 잘못되었기 때문에 "contoso.com" 관리 에이전트를 실행하지 못했습니다.'**가 포함됩니다.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>AD DS 계정에 대한 새 암호로 동기화 서비스를 업데이트하는 방법
새 암호로 동기화 서비스를 업데이트하려면

1. Synchronization Service Manager를 시작합니다(시작 → 동기화 서비스).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. **커넥터** 탭으로 이동합니다.

3. 암호가 변경된 AD DS 계정에 해당하는 **AD 커넥터**를 선택합니다.

4. **작업** 아래에서 **속성**을 선택합니다.

5. 팝업 대화 상자에서 **Active Directory 포리스트에 연결**을 선택합니다.

6. **암호** 텍스트 상자에 AD DS 계정의 새 암호를 입력합니다.

7. **확인**을 클릭하여 새 암호를 저장하고 팝업 대화 상자를 닫습니다.

8. Windows Service Control Manager에서 Azure AD Connect 동기화 서비스를 다시 시작합니다. 이 경우 이전 암호에 대한 모든 참조는 메모리 캐시에서 제거됩니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
