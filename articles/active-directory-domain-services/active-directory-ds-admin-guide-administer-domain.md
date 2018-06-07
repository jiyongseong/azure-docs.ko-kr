---
title: "Azure Active Directory Domain Services: 관리되는 도메인 관리 | Microsoft Docs"
description: "Azure Active Directory Domain Services 관리되는 도메인 관리"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 7ffe3e54b891fd74e14666c136be49132d78590b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인 관리
이 문서에서는 Azure AD(Active Directory) 도메인 서비스 관리되는 도메인을 관리하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.

1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.
4. Azure AD 도메인 서비스 관리되는 도메인을 관리하게 될 **도메인에 가입된 가상 컴퓨터** . 이러한 가상 컴퓨터가 없는 경우 [Windows 가상 컴퓨터를 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)문서에 설명된 모든 작업을 수행하세요.
5. 관리되는 도메인을 관리하려면 디렉터리의 **'AAD DC 관리자' 그룹에 속한 사용자 계정** 의 자격 증명이 필요합니다.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>관리되는 도메인에서 수행할 수 있는 관리 작업
'AAD DC 관리자' 그룹의 멤버에게는 다음과 같은 작업을 수행할 수 있는 관리되는 도메인에 대한 권한이 부여됩니다.

* 컴퓨터를 관리되는 도메인에 조인합니다.
* 관리되는 도메인에서 'AADDC 컴퓨터' 및 'AADDC 사용자' 컨테이너에 대한 기본 제공 GPO를 구성합니다.
* 관리되는 도메인에서 DNS 관리
* 관리되는 도메인에서 사용자 지정 OU(조직 구성 단위)를 만들고 관리합니다.
* 관리되는 도메인에 가입된 컴퓨터에 대한 관리 액세스 권한을 얻습니다.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>관리되는 도메인에 없는 관리자 권한
패치 적용, 모니터링 및 백업 수행과 같은 작업을 포함한 도메인 관리는 Microsoft에서 수행합니다. 따라서 도메인은 잠금 상태이고 도메인에 대해 특정 관리 작업을 수행할 수 있는 권한이 없습니다. 수행할 수 없는 작업의 몇 가지 예는 다음과 같습니다.

* 관리되는 도메인에 대해 도메인 관리자 또는 엔터프라이즈 관리자 권한을 부여하지 않습니다.
* 관리되는 도메인의 스키마를 확장할 수 없습니다.
* 원격 데스크톱을 사용하여 관리되는 도메인의 도메인 컨트롤러에 연결할 수 없습니다.
* 관리되는 도메인에 도메인 컨트롤러를 추가할 수 없습니다.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>작업 1 - 관리되는 도메인을 원격으로 관리하기 위해 도메인에 가입된 Windows Server 가상 컴퓨터 프로비전
Azure AD 도메인 서비스 관리되는 도메인을 AD PowerShell 또는 ADAC(Active Directory 관리 센터)와 같은 친숙한 Active Directory 관리 도구를 사용하여 관리할 수 있습니다. 테넌트 관리자는 원격 데스크톱을 통해 관리되는 도메인의 도메인 컨트롤러에 연결할 권한이 없습니다. 따라서 'AAD DC 관리자' 그룹의 멤버는 관리되는 도메인에 가입된 Windows Server/클라이언트 컴퓨터에서 AD 관리 도구를 사용하여 관리되는 도메인을 원격으로 관리할 수 있습니다. AD 관리 도구는 관리되는 도메인에 가입된 클라이언트 컴퓨터 및 Windows Server에서 원격 서버 관리 도구(RSAT) 선택적 기능의 일부로 설치할 수 있습니다.

첫 번째 단계는 관리되는 도메인에 가입된 Windows Server 가상 컴퓨터를 설정하는 것입니다. 지침은 [Windows Server 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)이라는 문서를 참조하세요.

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>클라이언트 컴퓨터(예: Windows 10)에서 관리되는 도메인을 원격으로 관리
이 문서의 지침에서는 AAD-DS 관리되는 도메인을 관리하기 위해 Windows Server 가상 컴퓨터를 사용합니다. 하지만 이 작업을 위해 Windows 클라이언트(예: Windows 10) 가상 컴퓨터를 사용하도록 선택할 수도 있습니다.

TechNet의 지침에 따라 Windows 클라이언트 가상 컴퓨터에 [원격 서버 관리 도구(RSAT)를 설치](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) 할 수 있습니다.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>작업 2 - 가상 컴퓨터에 Active Directory 관리 도구 설치
도메인에 가입된 가상 컴퓨터에 Active Directory 관리 도구를 설치하려면 다음 단계를 수행합니다. [원격 서버 관리 도구 설치 및 사용](https://technet.microsoft.com/library/hh831501.aspx)에 대한 자세한 내용은 TechNet을 참조하세요.

1. Azure Portal로 이동합니다. 왼쪽 패널에서 **모든 리소스**를 클릭합니다. 작업 1에서 만든 가상 컴퓨터를 찾아 클릭합니다.
2. 개요 탭에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜(.rdp) 파일이 생성되고 다운로드됩니다.

    ![Windows 가상 컴퓨터에 연결](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 클릭합니다. 로그인 프롬프트에서 'AAD DC 관리자' 그룹에 속한 사용자의 자격 증명을 사용합니다. 예를 들어 여기서는 'bob@domainservicespreview.onmicrosoft.com'을 사용합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 예 또는 계속을 클릭하여 연결을 진행합니다.
4. 시작 화면에서 **서버 관리자**를 엽니다. 서버 관리자 창의 가운데 창에서 **역할 및 기능 추가** 를 클릭합니다.

    ![가상 컴퓨터에서 서버 관리자 시작](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. **역할 및 기능 추가 마법사**의 **시작하기 전에** 페이지에서 **다음**을 클릭합니다.

    ![시작하기 전에 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. **설치 유형** 페이지에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고 **다음**을 클릭합니다.

    ![설치 유형 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. **서버 선택** 페이지에서 서버 풀의 현재 가상 컴퓨터를 선택하고 **다음**을 클릭합니다.

    ![서버 선택 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. **서버 역할** 페이지에서 **다음**을 클릭합니다. 서버에 어떠한 역할도 설치하지 않으므로 이 페이지는 건너뜁니다.
9. **기능** 페이지에서 **원격 서버 관리 도구** 노드를 클릭하여 확장한 후 **역할 관리 도구** 노드를 클릭하여 확장합니다. 역할 관리 도구 목록에서 **AD DS 및 AD LDS 도구** 기능을 선택합니다.

    ![기능 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. **확인** 페이지에서 **설치**를 클릭하여 가상 컴퓨터에 AD 및 AD LDS 도구 기능을 설치합니다. 기능 설치를 성공적으로 완료하면 **닫기**를 클릭하여 **역할 및 기능 추가** 마법사를 종료합니다.

    ![확인 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>작업 3 - 관리되는 도메인에 연결 및 탐색
이제 도메인에 가입된 가상 컴퓨터에 AD 관리 도구가 설치되었으므로 이러한 도구를 사용하여 관리되는 도메인을 탐색하고 관리할 수 있습니다.

> [!NOTE]
> 관리되는 도메인을 관리하려면 'AAD DC 관리자' 그룹의 구성원이어야 합니다.
>
>

1. 시작 화면에서 **관리 도구**를 클릭합니다. 가상 컴퓨터에 설치된 AD 관리 도구가 표시됩니다.

    ![서버에 설치된 관리 도구](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. **Active Directory 관리 센터**를 클릭합니다.

    ![Active Directory 관리 센터](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. 도메인을 탐색하려면 왼쪽 창에서 도메인 이름(예: 'contoso100.com')을 클릭합니다. 각각 'AADDC 컴퓨터' 및 'AADDC 사용자'라는 두 개의 컨테이너를 확인할 수 있습니다.

    ![ADAC - 도메인 보기](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. **AADDC 사용자** 라는 컨테이너를 클릭하면 관리되는 도메인에 속하는 모든 사용자 및 그룹이 표시됩니다. 이 컨테이너에 표시되는 Azure AD 테넌트의 사용자 계정 및 그룹이 나타납니다. 이 예에서는 'bob'이라는 사용자에 대한 사용자 계정 및 'AAD DC 관리자'라는 그룹이 이 컨테이너에 제공되는 것을 알 수 있습니다.

    ![ADAC - 도메인 사용자](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. **AADDC 컴퓨터** 라는 컨테이너를 클릭하면 이 관리되는 도메인에 가입된 컴퓨터가 표시됩니다. 도메인에 가입된 현재 가상 컴퓨터에 대한 항목이 표시됩니다. Azure AD 도메인 서비스 관리되는 도메인에 가입된 모든 컴퓨터에 대한 컴퓨터 계정이 이 'AADDC 컴퓨터' 컨테이너에 저장됩니다.

    ![ADAC - 도메인 가입 컴퓨터](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD 도메인 서비스 - 시작 가이드](active-directory-ds-getting-started.md)
* [Windows Server 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [원격 서버 관리 도구 배포](https://technet.microsoft.com/library/hh831501.aspx)
