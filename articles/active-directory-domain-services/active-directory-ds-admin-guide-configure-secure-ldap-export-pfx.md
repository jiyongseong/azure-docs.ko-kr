---
title: "Azure AD Domain Services에서 보안 LDAP(LDAPS) 구성 | Microsoft Docs"
description: "Azure AD 도메인 서비스 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: 4d495fcef89a68ca544100dcea8e6c55d4709ae0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성

## <a name="before-you-begin"></a>시작하기 전에
[작업 1: 보안 LDAP를 위한 인증서 가져오기](active-directory-ds-admin-guide-configure-secure-ldap.md)를 완료했는지 확인합니다.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>작업 2 - 보안 LDAP 인증서를 .PFX 파일로 내보내기
이 작업을 시작하기 전에 사용자는 공용 인증 기관에서 보안 LDAP 인증서를 가져오거나 자체 서명된 인증서를 만들어야 합니다.

LDAPS 인증서를 .PFX 파일로 내보내려면 다음 단계를 수행합니다.

1. **시작** 단추를 누르고 **R**을 입력합니다. **실행** 대화 상자에서 **mmc**를 입력하고 **확인**을 클릭합니다.

    ![MMC 콘솔 시작](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. **사용자 계정 컨트롤** 프롬프트에서 **예**를 클릭하고 관리자 권한으로 MMC(Microsoft Management Console)를 시작합니다.
3. **파일** 메뉴에서 **스냅인 추가/제거...**를 클릭합니다.

    ![MMC 콘솔에 스냅인 추가](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. **스냅인 추가/제거** 대화 상자에서 **인증서** 스냅인을 선택하고 **추가 >** 단추를 클릭합니다.

    ![MMC 콘솔에 인증서 스냅인 추가](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. **인증서 스냅인** 마법사에서 **컴퓨터 계정**을 선택하고 **다음**을 클릭합니다.

    ![컴퓨터 계정에 대한 인증서 스냅인 추가](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. **컴퓨터 선택** 페이지에서 **로컬 컴퓨터: (이 콘솔이 실행되고 있는 컴퓨터)**를 선택하고 **마침**을 클릭합니다.

    ![인증서 스냅인 추가 - 컴퓨터 선택](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. **스냅인 추가/제거** 대화 상자에서 **확인**을 클릭하고 인증서 스냅인을 MMC에 추가합니다.

    ![MMC에 인증서 스냅인 추가 - 완료](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. MMC 창에서 **콘솔 루트**를 클릭하여 확장합니다. 인증서 스냅인이 로드되는 것으로 표시됩니다. 확장할 **인증서(로컬 컴퓨터)**를 클릭합니다. **개인** 노드, **인증서** 노드를 차례로 클릭하여 확장합니다.

    ![개인 인증서 저장소 열기](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. 앞서 만든 자체 서명된 인증서가 표시됩니다. 인증서의 속성을 검사하여 지문이 인증서를 만들 때 PowerShell 창에 보고된 것과 일치하는지 확인할 수 있습니다.
10. 자체 서명된 인증서를 선택하고 **마우스 오른쪽 단추로 클릭**합니다. 오른쪽 클릭 메뉴에서 **모든 태스크**를 선택하고 **내보내기...**를 선택합니다.

    ![인증서 내보내기](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. **인증서 내보내기 마법사**에서 **다음**을 클릭합니다.

    ![인증서 내보내기 마법사](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. **개인 키 내보내기** 페이지에서 **예, 개인 키를 내보냅니다.**를 선택하고 **다음**을 클릭합니다.

    ![인증서 개인 키 내보내기](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > 인증서와 함께 개인 키를 내보내야 합니다. 인증서에 대한 개인 키가 없는 PFX를 제공하면 관리되는 도메인에 대한 보안 LDAP 설정에 실패합니다.
    >
    >
13. **파일 형식 내보내기** 페이지에서 내보낸 인증서에 대한 파일 형식으로 **개인 정보 교환 – PKCS #12(.PFX)**를 선택합니다.

    ![인증서 내보내기 파일 형식](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > .PFX 파일 형식만 지원됩니다. 인증서를 .CER 파일 형식으로 내보내지 마세요.
    >
    >
14. **보안** 페이지에서 **암호** 옵션을 선택하고 암호를 입력하여 .PFX 파일을 보호합니다. 다음 작업에 필요하므로 이 암호를 기억해 둡니다. **다음** 을 클릭하여 진행합니다.

    ![인증서 내보내기에 대한 암호 ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > 이 암호를 기록해 둡니다. [작업 3 - 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)에서 이 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정할 때 필요합니다.
    >
    >
15. **내보낼 파일** 페이지에서 파일 이름 및 인증서를 내보낼 위치를 지정합니다.

    ![인증서 내보내기에 대한 경로](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. 다음 페이지에서 **마침** 을 클릭하여 인증서를 PFX 파일로 내보냅니다. 인증서를 내보내면 확인 대화 상자가 표시됩니다.

    ![인증서 내보내기 완료](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>다음 단계
[작업 3 - 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
