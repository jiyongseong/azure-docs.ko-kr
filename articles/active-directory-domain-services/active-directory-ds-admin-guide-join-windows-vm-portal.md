---
title: "Azure Active Directory Domain Services: 관리되는 도메인에 Windows Server VM 가입 | Microsoft Docs"
description: "Windows Server 가상 머신을 Azure AD DS에 가입"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 7b5c23f1f4b6180d8b664f1371ccfd8a075572e6
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server 가상 머신을 관리되는 도메인에 가입
이 문서에서는 Azure Portal을 사용하여 Windows Server 가상 머신을 배포하는 방법을 보여줍니다. 그런 후 가상 머신을 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 가입하는 방법을 보여줍니다.

## <a name="step-1-create-a-windows-server-virtual-machine"></a>1단계: Windows Server 가상 머신 만들기
Azure AD DS가 설정된 가상 네트워크에 가입할 Windows 가상 머신을 만들려면 다음 단계를 수행합니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
2. 왼쪽 창 맨 위에서 **새로 만들기**를 클릭합니다.
3. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다.

    ![Windows Server 2016 Datacenter 링크](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. 마법사의 **기본 사항** 창에서 가상 머신의 기본 설정을 구성합니다.

    ![기본 사항 창](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > 여기서 가상 머신에 로그인하는 데 사용되는 로컬 관리자 계정에 대한 사용자 이름 및 암호를 입력합니다. 무차별 암호 대입 공격에 대해 가상 머신을 보호하기 위해 강력한 암호를 선택합니다. 여기에 도메인 사용자 계정 자격 증명을 입력하지 마세요.
    >

5. 가상 머신의 **크기**를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다.

    !["크기 선택" 창](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. **설정** 창에서 Azure AD DS 관리되는 도메인을 배포할 가상 네트워크를 선택합니다. 관리되는 도메인이 배포된 서브넷이 아닌 다른 서브넷을 선택합니다. 기타 설정은 기본값으로 유지하고 **확인**을 선택합니다.

    ![가상 머신의 가상 네트워크 설정](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **적합한 가상 네트워크 및 서브넷을 선택합니다.**
    >
    > 관리되는 도메인을 배포한 가상 네트워크 또는 가상 네트워크 피어링을 사용하여 연결된 가상 네트워크 중 하나를 선택합니다. 연결되지 않은 가상 네트워크를 선택하면 관리되는 도메인에 가상 컴퓨터를 조인할 수 없습니다.
    >
    > 관리되는 도메인을 전용 서브넷에 배포하는 것이 좋습니다. 따라서 관리되는 도메인을 활성화한 서브넷을 선택하지 않습니다.

7. 기타 설정은 기본값으로 유지하고 **확인**을 선택합니다.
8. **구매** 페이지에서 설정을 검토한 다음, **확인**을 클릭하여 가상 머신을 배포합니다.
9. Azure Portal 대시보드에 VM 배포를 고정합니다.

    ![완료된](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. 배포가 완료되면 **개요** 페이지에서 VM에 대한 정보를 볼 수 있습니다.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>2단계: 로컬 관리자 계정을 사용하여 Windows Server 가상 머신에 연결
다음으로, 새로 만든 Windows Server 가상 머신에 연결하고 도메인에 가입합니다. 가상 머신을 만들 때 지정한 로컬 관리자 자격 증명을 사용합니다.

가상 머신에 연결하려면 다음 단계를 수행합니다.

1. **개요** 창에서 **연결**을 선택합니다.  
    원격 데스크톱 프로토콜(.rdp) 파일이 생성되고 다운로드됩니다.

    ![Windows 가상 머신에 연결](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
3. 로그온 프롬프트에서, 가상 머신을 만드는 동안 지정한 **로컬 관리자 자격 증명**을 입력합니다(예: *localhost\mahesh*).
4. 로그인 프로세스 중에 인증서 경고가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속 진행합니다.

새로 만든 Windows 가상 머신에 로컬 관리자 자격 증명으로 로그온됩니다. 다음 단계는 가상 컴퓨터를 도메인에 가입하는 것입니다.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>3단계: Windows Server 가상 머신을 Azure AD DS 관리되는 도메인에 가입
Windows Server 가상 머신을 Azure AD DS 관리되는 도메인에 가입하려면 다음 단계를 수행합니다.

1. "2단계"에서 보여드린 것처럼 Windows Server VM에 연결합니다. **시작** 화면에서 **서버 관리자**를 엽니다.
2. **서버 관리자** 창의 왼쪽 창에서 **로컬 서버**를 클릭합니다.

    ![가상 머신의 서버 관리자 창](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. **속성**에서 **작업 그룹**을 선택합니다. 
4. **시스템 속성** 창에서 **변경**을 선택하여 도메인을 가입합니다.

    ![시스템 속성 창](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. **도메인** 상자에서 Azure AD DS 관리되는 도메인의 이름을 지정하고 **확인**을 선택합니다.

    ![가입할 도메인 지정](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. 도메인에 가입하려면 자격 증명을 입력하라는 메시지가 표시됩니다. *Azure AD DC 관리자 그룹에 속한 사용자*의 자격 증명을 지정해야 합니다. 이 그룹의 멤버만 관리되는 도메인에 컴퓨터를 가입할 수 있습니다.

    ![자격 증명을 지정하는 Windows 보안 창](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. 다음 방법 중 하나로 자격 증명을 지정할 수 있습니다.

   * **UPN 형식**: (권장) Azure AD에 구성된 대로 사용자 계정에 대한 UPN(사용자 계정 이름) 접미사를 지정합니다. 이 예제에서 사용자 *bob*의 UPN 접미사는 *bob@domainservicespreview.onmicrosoft.com*입니다.

   * **SAMAccountName 형식:** SAMAccountName 형식으로 계정 이름을 지정할 수 있습니다. 이 예제에서 사용자 *bob*은 *CONTOSO100\bob*을 입력해야 합니다.

     > [!TIP]
     > **UPN 형식을 사용하여 자격 증명을 지정하는 것이 좋습니다.**
     >
     > 사용자의 UPN 접두사가 너무 긴 경우(예: *joehasareallylongname*) SAMAccountName이 자동으로 생성될 수 있습니다. Azure AD 테넌트에서 여러 사용자가 동일한 UPN 접미사(예: *bob*)를 사용하는 경우 SAMAccountName 형식이 서비스에서 자동으로 생성될 수 있습니다. 이 경우 UPN 형식을 사용하여 도메인에 안전하게 로그온할 수 있습니다.
     >

8. 성공적으로 도메인을 가입하면 다음과 같은 시작 메시지가 표시됩니다.

    ![도메인 시작](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. 도메인 가입을 완료하려면 가상 머신을 다시 시작합니다.

## <a name="troubleshoot-joining-a-domain"></a>도메인 가입 문제 해결
### <a name="connectivity-issues"></a>연결 문제
가상 머신이 도메인을 찾을 수 없는 경우 다음 방법 중 하나를 시도해봅니다.

* 가상 머신이 Azure AD DS가 설정된 가상 네트워크와 동일한 가상 네트워크에 연결되어 있는지 확인합니다. 연결되지 않은 경우 가상 머신을 도메인에 연결할 수 없으므로 도메인에 가입할 수 없습니다.

* 가상 머신이 다른 가상 네트워크에 있는 경우 이 가상 네트워크가 Azure AD DS가 설정된 가상 네트워크에 연결되는지 확인합니다.

* 관리되는 도메인의 도메인 이름을 사용하여 도메인에 ping을 시도합니다(예: *ping contoso100.com*). 이렇게 할 수 없는 경우 Azure AD DS가 설정된 페이지에 표시된 도메인의 IP 주소에 ping을 시도합니다(예: *ping 10.0.0.4*). IP 주소에는 ping을 수행할 수 있지만 도메인에는 수행할 수 없는 경우 DNS가 잘못 구성되었을 수 있습니다. 도메인의 IP 주소를 가상 네트워크에 대한 DNS 서버로 구성했는지 확인합니다.

* 가상 머신에서 DNS 확인자 캐시를 플러시합니다(*ipconfig /flushdns*).

도메인 가입을 위한 자격 증명을 묻는 창이 표시되면 연결 문제는 없는 것입니다.

### <a name="credentials-related-issues"></a>자격 증명 관련 문제
자격 증명 문제가 있고 도메인에 가입할 수 없는 경우 다음 방법을 시도해 봅니다.

* UPN 형식을 사용하여 자격 증명을 지정해 봅니다. 테넌트에서 여러 사용자가 동일한 UPN 접두사를 사용하거나 UPN 접두사가 지나치게 긴 경우 사용자 계정에 대한 SAMAccountName이 자동으로 생성될 수 있습니다. 따라서 사용자 계정에 대한 SAMAccountName 형식은 온-프레미스 도메인에서 사용하거나 예상하는 것과 다를 수 있습니다.

* *AAD DC 관리자* 그룹에 속한 사용자 계정의 자격 증명을 사용해 봅니다.

* 시작 가이드에 설명된 단계에 따라 [암호 동기화를 사용하도록 설정](active-directory-ds-getting-started-password-sync.md)했는지 확인합니다.

* 로그인하려면 Azure AD에 구성된 대로 사용자의 UPN(예: *bob@domainservicespreview.onmicrosoft.com*)을 사용해야 합니다.

* 시작 가이드에 지정된 대로 암호 동기화가 완료될 때까지 충분히 기다려야 합니다.

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD DS 시작 가이드](active-directory-ds-getting-started.md)
* [Azure AD DS 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
