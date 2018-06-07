---
title: "Azure AD Synchronization Service Manager UI의 커넥터 | Microsoft Docs"
description: "Azure AD Connect의 Synchronization Service Manager에 있는 커넥터 탭을 이해합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bbbe5d0d7a7ed7065133b4bc6e5fc2dba39bf7d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Auzre AD Connect Sync Service Manager에서 커넥터 사용

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

커넥터 탭은 동기화 엔진이 연결된 모든 시스템을 관리하는 데 사용됩니다.

## <a name="connector-actions"></a>커넥터 작업
| 조치 | 주석 |
| --- | --- |
| 생성 |사용 안 함. 추가 AD 포리스트에 연결하려면 설치 마법사를 사용합니다. |
| properties |모든 도메인 및 OU 필터링에 사용 |
| [삭제](#delete) |커넥터 공간에서 데이터를 삭제하거나 포리스트에 대한 연결을 삭제하는 데 사용 |
| [실행 프로필 구성](#configure-run-profiles) |여기서는 도메인 필터링만 구성 이 작업을 사용하여 이미 구성된 실행 프로필을 볼 수 있습니다. |
| 실행 |프로필의 일회성 실행을 시작하는 데 사용 |
| 중지 |현재 프로필을 실행하는 커넥터 중지 |
| 커넥터 내보내기 |사용 안 함. |
| 커넥터 가져오기 |사용 안 함. |
| 커넥터 업데이트 |사용 안 함. |
| 스키마 새로 고침 |캐시된 스키마를 새로 고칩니다. 동기화 규칙도 업데이트되므로 대신 설치 마법사를 사용하는 것이 좋습니다. |
| [커넥터 공간 검색](#search-connector-space) |개체를 찾고 [시스템 전체에서 개체 및 해당 데이터의 흐름을 따르는](#follow-an-object-and-its-data-through-the-system)데 사용됩니다. |

### <a name="delete"></a>삭제
삭제 작업은 두 가지 작업에 사용됩니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

**커넥터 공간만 삭제** 옵션은 모든 데이터를 제거하지만 구성은 그대로 유지합니다.

**커넥터 및 커넥터 공간 삭제** 옵션은 데이터와 구성을 제거합니다. 이 옵션은 포리스트에 더 이상 연결하지 않으려는 경우에 사용됩니다.

두 옵션 모두 모든 개체를 동기화하고 메타버스 개체를 업데이트합니다. 이 작업은 장기 실행 작업입니다.

### <a name="configure-run-profiles"></a>실행 프로필 구성
이 옵션을 사용하면 커넥터에 대해 구성된 실행 프로필을 볼 수 있습니다.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>커넥터 공간 검색
커넥터 공간 검색 작업은 개체를 찾아 데이터 문제를 해결하는 데 유용합니다.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

먼저 **범위**를 선택합니다. 데이터(RDN, DN 앵커, 하위 트리) 또는 개체의 상태(다른 모든 옵션)에 따라 검색할 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
예를 들어 하위 트리 검색을 수행하는 경우 모든 개체를 하나의 OU로 가져옵니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
이 표에서 개체를 선택하고, **속성**을 선택한 다음 원본 커넥터 공간의 메타버스, 대상 커넥터 공간으로 이어지는 [흐름을 따릅니다](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="changing-the-ad-ds-account-password"></a>AD DS 계정 암호 변경
계정 암호를 변경하는 경우 동기화 서비스가 더 이상 온-프레미스 AD에 대한 변경 내용을 가져오거나 내보낼 수 없습니다.   다음이 표시될 수 있습니다.

- AD 커넥터에 대한 가져오기/내보내기 단계가 "no-start-credentials" 오류를 나타내며 실패합니다.
- Windows 이벤트 뷰어에서 응용 프로그램 이벤트 로그에는 이벤트 ID 6000 오류 및 메시지 “자격 증명이 잘못되었기 때문에 "contoso.com" 관리 에이전트를 실행하지 못했습니다.”가 포함됩니다.

이 문제를 해결하려면 다음을 사용하여 AD DS 사용자 계정을 업데이트합니다.


1. Synchronization Service Manager를 시작합니다(시작 → 동기화 서비스).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. **커넥터** 탭으로 이동합니다.
3. AD DS 계정을 사용하도록 구성된 AD 커넥터를 선택합니다.
4. 작업 아래에서 **속성**을 선택합니다.
5. 팝업 대화 상자에서 Active Directory 포리스트에 연결을 선택합니다.
6. 포리스트 이름은 해당 온-프레미스 AD를 나타냅니다.
7. 사용자 이름은 동기화에 사용되는 AD DS 계정을 나타냅니다.
8. 암호 텍스트 상자에 AD DS 계정의 새 암호를 입력합니다. ![Azure AD Connect 동기화 암호화 키 유틸리티](media/active-directory-aadconnectsync-encryption-key/key6.png)
9. 확인을 클릭하여 새 암호를 저장하고 동기화 서비스를 다시 시작하여 메모리 캐시에서 이전 암호를 제거합니다.



## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
