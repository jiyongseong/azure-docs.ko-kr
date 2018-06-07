---
title: 'Azure AD Connect: 기본 설정을 사용하여 시작 | Microsoft Docs'
description: Azure AD Connect용 설치 마법사를 다운로드, 설치 및 실행하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: mtillman
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: billmath
ms.openlocfilehash: 024736ed76e9b268e85db3ecc62b853d6c276fbf
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>기본 설정을 사용하여 Azure AD Connect 시작
인증을 위한 단일 포리스트 토폴로지 및 **암호 해시 동기화**가 있는 경우 Azure AD Connect [Express 설정](active-directory-aadconnectsync-implement-password-hash-synchronization.md)을 사용합니다. **Express 설정** 은 기본 옵션이며 가장 일반적으로 배포된 시나리오에 사용됩니다. 몇 번의 클릭만으로 온-프레미스 디렉터리를 클라우드로 확장할 수 있습니다.

Azure AD Connect 설치를 시작하기 전에 [Azure AD Connect를 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771)하고 [Azure AD Connect: 하드웨어 및 필수 구성 요소](active-directory-aadconnect-prerequisites.md)의 필수 구성 요소 단계를 완료하도록 합니다.

Express 설정이 토폴로지와 일치하지 않는 경우 다른 시나리오는 [관련 설명서](#related-documentation) 를 참조하세요.

## <a name="express-installation-of-azure-ad-connect"></a>Azure AD Connect의 빠른 설치
[비디오](#videos) 섹션에서 실행 중인 다음 단계를 확인할 수 있습니다.

1. Azure AD Connect를 설치하려는 서버에 로컬 관리자로 로그인합니다. 동기화 서버로 설정할 서버에서 수행해야 합니다.
2. **AzureADConnect.msi**를 찾아서 두 번 클릭합니다.
3. 시작 화면에서 사용권 계약에 동의하는 상자를 선택하고 **계속**을 클릭합니다.  
4. 기본 설정 화면에서 **Use express settings**(기본 설정 사용)를 클릭합니다.  
   ![Azure AD Connect 시작](./media/active-directory-aadconnect-get-started-express/express.png)
5. Azure AD에 연결 화면에서 Azure AD에 대한 전역 관리자의 사용자 이름 및 암호를 입력합니다. **다음**을 클릭합니다.  
   ![Azure AD에 연결](./media/active-directory-aadconnect-get-started-express/connectaad.png)  
   오류가 발생하고 연결에 문제가 있는 경우 [연결 문제 해결](active-directory-aadconnect-troubleshoot-connectivity.md)을 참조하세요.
6. AD DS에 연결 화면에서 엔터프라이즈 관리자 계정의 사용자 이름 및 암호를 입력합니다. NetBios 또는 FQDN 형식으로 도메인 부분을 입력할 수 있습니다(예: FABRIKAM\administrator 또는 fabrikam.com\administrator). **다음**을 클릭합니다.  
   ![AD DS에 연결](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. [필수 구성 요소](active-directory-aadconnect-prerequisites.md)에서 [도메인 확인](../active-directory-domains-add-azure-portal.md)을 완료하지 않은 경우 [**Azure AD 로그인 구성**](active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) 페이지가 표시됩니다.
   ![확인되지 않은 도메인](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
   이 페이지를 표시하는 경우 **추가되지 않음** 및 **확인되지 않음**으로 표시된 모든 도메인을 검토합니다. 사용한 해당 도메인을 Azure AD에서 확인하도록 합니다. 도메인을 확인한 경우 새로 고침 기호를 클릭합니다.
8. 구성 준비 화면에서 **설치**를 클릭합니다.
   * 선택적으로 구성 준비 완료 페이지에서 **구성이 완료되자마자 동기화 프로세스를 시작합니다.** 확인란의 선택을 취소할 수 있습니다. [필터링](active-directory-aadconnectsync-configure-filtering.md)같은 추가적인 구성을 수행하려면 이 확인란을 선택하지 말아야 합니다. 이 옵션에 대한 선택을 취소하면, 마법사가 동기화를 구성하지만 스케줄러는 비활성 상태로 유지합니다. [설치 마법사를 다시 실행](active-directory-aadconnectsync-installation-wizard.md)하여 사용자가 수동으로 활성화할 때까지 실행되지 않습니다.
   * **구성이 완료되자마자 동기화 프로세스를 시작합니다.** 확인란이 선택된 상태로 두면 즉시 모든 사용자, 그룹 및 연락처의 Azure AD로 전체 동기화가 트리거됩니다. 
   * 온-프레미스 Active Directory에 Exchange가 있는 경우 [**Exchange 하이브리드 배포**](https://technet.microsoft.com/library/jj200581.aspx)를 사용하는 옵션도 있습니다. Exchange 사서함을 클라우드와 온-프레미스에 동시에 두려면 이 옵션을 활성화합니다.
     ![Azure AD Connect 구성 준비 완료](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. 설치가 완료되면 **끝내기**를 클릭합니다.
10. 설치가 완료된 후 로그아웃하고 동기화 서비스 관리자 또는 동기화 규칙 편집기를 사용하기 전에 다시 로그인합니다.

## <a name="videos"></a>동영상
빠른 설치 사용에 대한 비디오는 다음을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>다음 단계
Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](active-directory-aadconnect-whats-next.md)할 수 있습니다.

[자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md), [실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md)를 설치하여 사용할 수 있는 이러한 기능에 대해 자세히 알아봅니다.

공통 항목인 [스케줄러 및 동기화를 트리거하는 방법](active-directory-aadconnectsync-feature-scheduler.md)에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

## <a name="related-documentation"></a>관련 설명서

| 항목 | 링크 |
| --- | --- |
| Azure AD Connect 개요 | [Azure Active Directory와 온-프레미스 디렉터리 통합](active-directory-aadconnect.md)
| 사용자 지정된 설정을 사용하여 설치 | [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) |
| DirSync에서 업그레이드 | [Azure AD Sync 도구(DirSync)에서 업그레이드](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
| 설치에 사용되는 계정 | [Azure AD Connect 자격 증명 및 사용 권한에 대한 자세한 정보](active-directory-aadconnect-accounts-permissions.md) |
