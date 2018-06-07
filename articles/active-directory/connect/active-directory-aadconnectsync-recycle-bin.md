---
title: "Azure AD Connect 동기화: AD 휴지통 사용 | Microsoft Docs"
description: "이 항목에서는 Azure AD connect에서 AD 휴지통 기능을 사용하도록 권장합니다."
services: active-directory
keywords: "AD 휴지통, 실수로 삭제, 원본 앵커"
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 45bff0585eb6f1990a7300ae3d16632eab78a83c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect 동기화: AD 휴지통 사용
Azure AD와 동기화되는 온-프레미스 Active Directory에 대해 AD 휴지통 기능을 사용하도록 설정하는 것이 좋습니다. 

온-프레미스 AD 사용자 개체를 실수로 삭제하고 이 기능을 사용하여 복원하면 Azure AD가 해당 Azure AD 사용자 개체를 복원합니다.  AD 휴지통 기능에 대한 자세한 내용은 [삭제된 Active Directory 개체 복원 시나리오 개요](https://technet.microsoft.com/library/dd379542.aspx) 문서를 참조하세요.

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>AD 휴지통 사용의 이점
이 기능은 다음을 수행하여 Azure AD 사용자 개체를 복원하는 데 도움을 줍니다.

* 온-프레미스 AD 사용자 개체를 실수로 삭제한 경우 해당 Azure AD 사용자 개체는 다음 동기화 주기에서 삭제됩니다. 기본적으로 Azure AD는 삭제된 Azure AD 사용자 개체를 30일 동안 일시 삭제 상태로 유지합니다.

* 온-프레미스 AD 휴지통 기능을 사용하도록 설정한 경우 원본 앵커 값을 변경하지 않고 삭제된 온-프레미스 AD 사용자 개체를 복원할 수 있습니다. 복구된 온-프레미스 AD 사용자 개체가 Azure AD와 동기화되면 Azure AD는 일시 삭제된 해당 Azure AD 사용자 개체를 복원합니다. 원본 앵커 특성에 대한 자세한 내용은 [Azure AD Connect: 설계 개념](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor)을 참조하세요.

* 온-프레미스 AD 휴지통 기능을 사용하도록 설정하지 않은 경우 삭제된 개체를 대신할 AD 사용자 개체를 만들어야 할 수 있습니다. Azure AD Connect 동기화 서비스가 원본 앵커 특성에 대한 시스템 생성 AD 특성(예: ObjectGuid)을 사용하도록 구성된 경우 새로 만든 AD 사용자 개체는 삭제된 AD 사용자 개체와 같은 원본 앵커 값을 갖지 않습니다. 새로 만든 AD 사용자 개체가 Azure AD와 동기화되면 Azure AD는 일시 삭제된 Azure AD 사용자 개체를 복원하지 않고 새 Azure AD 사용자 개체를 만듭니다.

> [!NOTE]
> 기본적으로 Azure AD는 Azure AD 사용자 개체를 영구적으로 삭제하기 전에 30일 동안 일시 삭제 상태로 유지합니다. 그러나 관리자가 이러한 개체의 삭제를 가속화할 수 있습니다. 개체가 영구적으로 삭제되면 온-프레미스 AD 휴지통 기능을 사용하도록 설정한 경우에도 더 이상 복구할 수 없습니다.



## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
