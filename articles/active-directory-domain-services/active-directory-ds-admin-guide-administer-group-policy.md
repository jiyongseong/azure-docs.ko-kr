---
title: "Azure Active Directory Domain Services: 관리되는 도메인에서 그룹 정책 관리 | Microsoft Docs"
description: "Azure Active Directory Domain Services 관리되는 도메인에서 그룹 정책 관리"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 316ddc2cbd67cfafaf44318c5baebcd8da366f93
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에서 그룹 정책 관리
Azure Active Directory Domain Services는 'AADDC 사용자' 및 'AADDC 컴퓨터' 컨테이너에 대한 기본 제공 GPO(그룹 정책 개체)를 포함합니다. 이러한 기본 제공 GPO를 사용자 지정하여 관리되는 도메인에서 그룹 정책을 구성할 수 있습니다. 또한 'AAD DC 관리자' 그룹의 구성원은 관리되는 도메인에서 자체 사용자 지정 OU를 만들 수 있습니다. 사용자 지정 GPO을 만들고 이러한 사용자 지정 OU에 연결할 수도 있습니다. 'AAD DC 관리자' 그룹에 속한 사용자에게 관리되는 도메인에 대한 그룹 정책 관리 권한이 부여됩니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.

1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.
4. Azure AD 도메인 서비스 관리되는 도메인을 관리하게 될 **도메인에 가입된 가상 컴퓨터** . 이러한 가상 컴퓨터가 없는 경우 [Windows 가상 컴퓨터를 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)문서에 설명된 모든 작업을 수행하세요.
5. 관리되는 도메인에 대한 그룹 정책을 관리하려면 디렉터리의 **'AAD DC 관리자' 그룹에 속한 사용자 계정**의 자격 증명이 필요합니다.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>작업 1 - 관리되는 도메인에 대한 그룹 정책을 원격으로 관리하기 위해 도메인에 가입된 가상 컴퓨터 프로비전
Azure AD 도메인 서비스 관리되는 도메인을 AD PowerShell 또는 ADAC(Active Directory 관리 센터)와 같은 친숙한 Active Directory 관리 도구를 사용하여 원격으로 관리할 수 있습니다. 마찬가지로, 그룹 정책 관리 도구를 사용하여 관리되는 도메인에 대한 그룹 정책을 원격으로 관리할 수 있습니다.

Azure AD 디렉터리의 테넌트 관리자는 원격 데스크톱을 통해 관리되는 도메인의 도메인 컨트롤러에 연결할 권한이 없습니다. 'AAD DC 관리자' 그룹의 구성원은 관리되는 도메인에 대한 그룹 정책을 원격으로 관리할 수 있습니다. 관리되는 도메인에 가입된 Windows 서버/클라이언트 컴퓨터의 그룹 정책 도구를 사용할 수 있습니다. 그룹 정책 도구는 관리되는 도메인에 가입된 클라이언트 컴퓨터 및 Windows Server에서 그룹 정책 관리 선택적 기능의 일부로 설치할 수 있습니다.

Windows Server 가상 컴퓨터를 프로비전하기 위한 첫 번째 작업은 관리되는 도메인에 가입하는 것입니다. 지침은 [Windows Server 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)이라는 문서를 참조하세요.

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>작업 2 - 가상 컴퓨터에 그룹 정책 도구 설치
도메인에 가입된 가상 컴퓨터에 그룹 정책 관리 도구를 설치하려면 다음 단계를 수행합니다.

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
9. **기능** 페이지에서 **그룹 정책 관리** 기능을 선택합니다.

    ![기능 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. **확인** 페이지에서 가상 컴퓨터에 그룹 정책 관리 기능을 설치하기 위해 **설치**를 클릭합니다. 기능 설치를 성공적으로 완료하면 **닫기**를 클릭하여 **역할 및 기능 추가** 마법사를 종료합니다.

    ![확인 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>작업 3 - 그룹 정책을 관리하는 그룹 정책 관리 콘솔 시작
도메인에 가입된 가상 컴퓨터에서 그룹 정책 관리 콘솔을 사용하여 관리되는 도메인에서 그룹 정책을 관리할 수 있습니다.

> [!NOTE]
> 관리되는 도메인의 그룹 정책을 관리하려면 'AAD DC 관리자' 그룹의 구성원이어야 합니다.
>
>

1. 시작 화면에서 **관리 도구**를 클릭합니다. 가상 컴퓨터에 설치된 **그룹 정책 관리** 콘솔이 표시됩니다.

    ![그룹 정책 관리 시작](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. **그룹 정책 관리**를 클릭하여 그룹 정책 관리 콘솔을 시작합니다.

    ![그룹 정책 콘솔](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>작업 4 - 기본 제공 그룹 정책 개체 사용자 지정
관리되는 도메인에서 'AADDC 컴퓨터' 및 'AADDC 사용자' 컨테이너 각각에 대한 두 개의 기본 제공 GPO(그룹 정책 개체)가 있습니다. 이러한 GPO를 사용자 지정하여 관리되는 도메인에서 그룹 정책을 구성할 수 있습니다.

1. **그룹 정책 관리** 콘솔에서 **포리스트: contoso100.com** 및 **도메인** 노드를 클릭하여 확장하고 관리되는 도메인에 대한 그룹 정책을 확인합니다.

    ![기본 제공 GPO](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. 이러한 기본 제공 GPO를 사용자 지정하여 관리되는 도메인에서 그룹 정책을 구성할 수 있습니다. GPO를 마우스 오른쪽 단추로 클릭하고 **편집...**을 클릭하여 기본 제공 GPO를 사용자 지정합니다. 그룹 정책 구성 편집기 도구를 사용하여 GPO를 사용자 지정할 수 있습니다.

    ![기본 제공 GPO 편집](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. 이제 **그룹 정책 관리 편집기** 콘솔을 사용하여 기본 제공 GPO를 편집할 수 있습니다. 예를 들어 다음 스크린샷은 기본 제공 'AADDC 컴퓨터' GPO를 사용자 지정하는 방법을 보여 줍니다.

    ![GPO 사용자 지정](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>작업 5 - 사용자 지정 GPO(그룹 정책 개체) 만들기
사용자 지정 그룹 정책 개체를 만들거나 가져올 수 있습니다. 사용자 지정 GPO를 관리되는 도메인에서 만든 사용자 지정 OU에 연결할 수도 있습니다. 사용자 지정 조직 구성 단위를 만드는 방법에 대한 자세한 내용은 [관리되는 도메인에서 사용자 지정 OU 만들기](active-directory-ds-admin-guide-create-ou.md)를 참조하세요.

> [!NOTE]
> 관리되는 도메인의 그룹 정책을 관리하려면 'AAD DC 관리자' 그룹의 구성원이어야 합니다.
>
>

1. **그룹 정책 관리** 콘솔에서 사용자 지정 조직 구성 단위(OU)를 클릭하여 선택합니다. OU를 마우스 오른쪽 단추로 클릭하고 **이 도메인에서 GPO를 만들어 여기에 연결...**을 클릭합니다.

    ![사용자 지정 GPO 만들기](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. 새 GPO의 이름을 지정하고 **확인**을 클릭합니다.

    ![GPO의 이름 지정](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. 새 GPO가 만들어지고 사용자 지정 OU로 연결됩니다. GPO를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **편집...**을 클릭합니다.

    ![새로 만든 GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. 새로 만든 GPO는 **그룹 정책 관리 편집기**를 사용하여 사용자 지정할 수 있습니다.

    ![새 GPO 사용자 지정](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


[그룹 정책 관리 콘솔](https://technet.microsoft.com/library/cc753298.aspx) 사용에 대한 자세한 내용은 Technet에서 확인할 수 있습니다.

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD 도메인 서비스 - 시작 가이드](active-directory-ds-getting-started.md)
* [Windows Server 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
* [그룹 정책 관리 콘솔](https://technet.microsoft.com/library/cc753298.aspx)
