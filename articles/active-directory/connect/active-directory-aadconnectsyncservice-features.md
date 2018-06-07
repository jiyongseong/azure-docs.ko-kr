---
title: Azure AD Connect 동기화 서비스 기능 및 구성 | Microsoft Docs
description: Azure AD Connect 동기화 서비스의 서비스 쪽 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: eb2a670735db8a72163967d89d0359b4b89a3e2f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150348"
---
# <a name="azure-ad-connect-sync-service-features"></a>Azure AD Connect 동기화 서비스 기능
Azure AD Connect의 동기화 기능에는 두 가지 구성 요소가 있습니다.

* **Azure AD Connect 동기화**라고 하는 온-프레미스 구성 요소(**동기화 엔진**이라고도 함)
* **Azure AD Connect 동기화 서비스**

이 항목에서는 다음 **Azure AD Connect 동기화 서비스** 기능 작동 방법 및 Windows PowerShell을 사용하여 구성할 수 있는 방법에 대해 설명합니다.

이러한 설정은 [Windows PowerShell용 Azure Active Directory 모듈](https://aka.ms/aadposh)에서 구성됩니다. Azure AD Connect에서 다운로드하여 별도로 설치합니다. 이 항목에서 설명한 cmdlet은 [2016년 3월 릴리스(빌드 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)에 도입되었습니다. 이 항목에서 설명하는 cmdlet이 없거나 동일한 결과가 생성되지 않는 경우 최신 버전을 실행하고 있는지 확인합니다.

Azure AD 디렉터리의 구성을 보려면 `Get-MsolDirSyncFeatures`를 실행합니다.  
![Get-MsolDirSyncFeatures 결과](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

이러한 설정 중 대부분은 Azure AD Connect에서만 변경할 수 있습니다.

다음 설정은 `Set-MsolDirSyncFeature`에서 구성할 수 있습니다.

| DirSyncFeature | 주석 |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |개체가 기본 SMTP 주소뿐만 아니라 userPrincipalName에 대해 가입할 수 있습니다. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |관리되는/허가된(페더레이션되지 않은) 사용자에 대한 userPrincipalName 특성을 동기화 엔진이 업데이트할 수 있습니다. |

기능을 사용하도록 설정한 후 다시 해제할 수 없습니다.

> [!NOTE]
> 2016년 8월 24일에서 *중복 특성 복원력* 기능은 새로운 Azure AD 디렉터리에 대해 기본적으로 활성화됩니다. 또한 이 기능은 이 날짜 이전에 생성된 디렉터리에서 롤아웃되고 사용하도록 설정됩니다. 디렉터리가 이 기능을 사용하도록 설정하면 전자 메일 알림을 받게 됩니다.
> 
> 

다음 설정은 Azure AD Connect에서 구성되며 `Set-MsolDirSyncFeature`으로 수정할 수 없습니다.

| DirSyncFeature | 주석 |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: 장치 쓰기 저장 사용](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect 동기화: 디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |내보내는 동안 전체 개체가 실패한 것이 아니라 또 다른 개체의 복제본인 경우 특성을 격리시킬 수 있습니다. |
| PasswordSync |[Azure AD Connect 동기화로 암호 해시 동기화 구현](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |
| UnifiedGroupWriteback |[미리 보기: 그룹 쓰기 저장](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback |현재 지원되지 않습니다. |

## <a name="duplicate-attribute-resiliency"></a>중복 특성 복원력
중복된 UPN/proxyAddresses를 가진 개체를 프로비전하는 데 실패하는 대신 중복된 특성은 "격리"되고 임시 값이 할당됩니다. 충돌이 해결되면 임시 UPN은 적절한 값으로 자동으로 변경됩니다. 자세한 내용은 [ID 동기화 및 중복 특성 복원력](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)을 참조하세요.

## <a name="userprincipalname-soft-match"></a>UserPrincipalName 소프트 일치
이 기능을 사용하도록 설정하면 [기본 SMTP 주소](https://support.microsoft.com/kb/2641663)외에도 UPN에 소프트 일치를 사용하고 항상 사용하도록 설정됩니다. Azure AD의 기존 클라우드 사용자를 온-프레미스 사용자와 일치하는 데 소프트 일치를 사용합니다.

온-프레미스 AD 계정이 클라우드에서 만든 기존 계정과 일치해야 하고 Exchange Online을 사용하지 않는 경우 이 기능은 특히 유용합니다. 이 시나리오에서는 일반적으로 클라우드에서 SMTP 특성을 설정할 이유가 없습니다.

이 기능은 새로 만든 Azure AD 디렉터리에 기본적으로 설정되어 있습니다. 다음을 실행하여 이 기능을 사용하도록 설정했는지 확인할 수 있습니다.  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Azure AD 디렉터리에서 이 기능을 사용하도록 설정하지 않은 경우 다음을 실행하여 설정할 수 있습니다.  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName 업데이트 동기화
경험상 다음 조건이 모두 충족되지 않으면 온-프레미스에서 동기화 서비스를 사용하여 UserPrincipalName 특성에 대한 업데이트는 차단되었습니다.

* 사용자가 관리됨(페더레이션되지 않음)
* 사용자에게 라이선스가 할당되지 않음

자세한 내용은 [Office 365, Azure 또는 Intune의 사용자 이름이 온-프레미스 UPN 또는 대체 로그인 ID와 일치하지 않음](https://support.microsoft.com/kb/2523192)을 참조하세요.

이 기능을 사용하도록 설정하면 userPrincipalName이 변경된 온-프레미스이고 암호 해시 동기화를 사용하는 경우 동기화 엔진이 이를 업데이트할 수 있습니다. 페더레이션을 사용하는 경우 이 기능은 지원되지 않습니다.

이 기능은 새로 만든 Azure AD 디렉터리에 기본적으로 설정되어 있습니다. 다음을 실행하여 이 기능을 사용하도록 설정했는지 확인할 수 있습니다.  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Azure AD 디렉터리에서 이 기능을 사용하도록 설정하지 않은 경우 다음을 실행하여 설정할 수 있습니다.  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

이 기능을 사용하도록 설정하면 기존 userPrincipalName 값이 그대로 유지됩니다. userPrincipalName 특성 온-프레미스의 다음 변경 시 사용자에 대한 일반 델타 동기화가 UPN을 업데이트합니다.  

## <a name="see-also"></a>참고 항목
* [Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

