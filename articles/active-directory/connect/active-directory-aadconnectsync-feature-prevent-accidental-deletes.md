---
title: "Azure AD Connect 동기화: 실수로 인한 삭제 방지 | Microsoft Docs"
description: "이 항목에서는 Azure AD Connect의 실수로 인한 삭제 방지 기능을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 57ce7b2fcece751b1386ef1d57762ad8d1c27c62
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect 동기화: 실수로 인한 삭제 방지
이 항목에서는 Azure AD Connect의 실수로 인한 삭제 방지 기능을 설명합니다.

Azure AD Connect를 설치하면 실수로 인한 삭제 방지가 기본적으로 사용되며 삭제 수가 500개를 초과하는 내보내기를 허용하지 않도록 구성됩니다. 이 기능은 다수의 사용자 및 다른 개체에 영향을 주는 실수에 의한 구성 변경 및 온-프레미스 디렉터리 변경을 방지하기 위한 것입니다.

## <a name="what-is-prevent-accidental-deletes"></a>실수로 인한 삭제를 방지하는 기능
다수의 삭제가 다음을 포함하는 경우의 일반적인 시나리오입니다.

* 전체 [OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) 또는 [도메인](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering)을 선택하지 않은 [필터링](active-directory-aadconnectsync-configure-filtering.md)으로 변경합니다.
* OU의 모든 개체가 삭제됩니다.
* OU 이름이 변경되면 OU의 모든 개체가 동기화 범위를 벗어난 것으로 간주됩니다.

Azure Active Directory Connect와 함께 설치된 AD Sync 모듈의 일부인 `Enable-ADSyncExportDeletionThreshold`를 사용하는 PowerShell로 500개 개체의 기본값을 변경할 수 있습니다. 조직의 규모에 맞게 이 값을 구성해야 합니다. 동기화 스케줄러가 30분마다 실행되므로 이 값은 30분 내에 표시되는 삭제 수입니다.

Azure AD로 내보내도록 스테이징된 삭제 수가 너무 많을 경우 내보내기가 중지되며 다음과 같은 메일을 받게 됩니다.

![실수로 인한 삭제 방지 메일](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *안녕하세요. (기술 담당자). (시간)에 ID 동기화 서비스에서 삭제 수가 (조직 이름)에 대해 구성된 삭제 임계값을 초과했음을 검색했습니다. 총 (개수)개 개체가 이 ID 동기화 실행에서 삭제를 위해 전송되었습니다. 이는 구성된 삭제 임계값인 (개수)개 개체에 도달했거나 초과했습니다. 진행하기 전에 사용자가 이러한 삭제가 처리되어야 한다는 확인을 제공해야 합니다. 이 메일 메시지에 나열된 오류에 대한 자세한 내용은 실수로 인한 삭제 방지를 참조하세요.*
>
> 

또한 프로파일 내보내기에 대한 **Synchronization Service Manager** UI를 찾아보면 `stopped-deletion-threshold-exceeded` 상태를 볼 수 있습니다.
![실수로 인한 삭제 방지 동기화 서비스 관리자 UI](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

예상된 경우가 아니라면 조사하여 수정 작업을 수행합니다. 삭제되는 개체를 확인하려면 다음을 수행합니다.

1. 시작 메뉴에서 **동기화 서비스** 를 시작합니다.
2. **커넥터**로 이동합니다.
3. **Azure Active Directory**유형의 커넥터를 선택합니다.
4. 오른쪽에 있는 **작업**에서 **커넥터 공간 검색**을 선택합니다.
5. **범위** 아래의 팝업에서 **다음 이후 연결이 끊어짐**을 선택하고 과거 시간을 선택합니다. **검색**을 클릭합니다. 이 페이지는 삭제되는 모든 개체의 보기를 제공합니다. 각 항목을 클릭하면 개체에 대한 추가 정보를 얻을 수 있습니다. **열 설정**을 클릭하여 그리드에 표시되는 특성을 더 추가할 수도 있습니다.

![커넥터 공간 검색](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

모든 삭제를 진행하려면 다음을 수행합니다.

1. 현재 삭제 임계값을 검색하려면 PowerShell cmdlet `Get-ADSyncExportDeletionThreshold`를 실행합니다. Azure AD 전역 관리자 계정 및 암호를 제공합니다. 기본값은 500입니다.
2. 일시적으로 이 보호를 해제하고 삭제를 진행할 수 있도록 하려면 PowerShell cmdlet `Disable-ADSyncExportDeletionThreshold`를 실행합니다. Azure AD 전역 관리자 계정 및 암호를 제공합니다.
   ![자격 증명](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Azure Active Directory Connector를 선택한 상태로 **실행** 작업, **내보내기**를 차례로 선택합니다.
4. 보호를 다시 사용하도록 설정하려면 PowerShell cmdlet `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`를 실행합니다. 현재 삭제 임계값을 검색할 때 500을 알게 된 값으로 바꿉니다. Azure AD 전역 관리자 계정 및 암호를 제공합니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
