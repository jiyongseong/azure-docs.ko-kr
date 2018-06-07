---
title: 'Azure Active Directory Domain Services: 관리되는 도메인에 CoreOS Linux VM 가입 | Microsoft Docs'
description: CoreOS Linux 가상 머신을 Azure AD Domain Services에 가입
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 6c7290db73645a4e1e745142f266aabc6df735df
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>CoreOS Linux 가상 머신을 관리되는 도메인에 가입
이 문서에서는 Azure에서 CoreOS Linux 가상 머신을 Azure AD Domain Services 관리되는 도메인에 가입하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.
1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.
4. 관리되는 도메인의 IP 주소를 가상 네트워크에 대한 DNS 서버로 구성했는지 확인합니다. 자세한 내용은 [Azure 가상 네트워크에 대한 DNS 설정을 업데이트하는 방법](active-directory-ds-getting-started-dns.md)을 참조하세요.
5. [Azure AD Domain Services 관리되는 도메인에 암호를 동기화](active-directory-ds-getting-started-password-sync.md)하는 데 필요한 단계를 완료합니다.


## <a name="provision-a-coreos-linux-virtual-machine"></a>CoreOS Linux 가상 머신 프로비전
다음 방법 중 하나를 사용하여 CoreOS 가상 머신을 프로비전합니다.
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

이 문서에서는 Azure의 **CoreOS Linux(안정화)** 가상 컴퓨터 이미지를 사용합니다.

> [!IMPORTANT]
> * **Azure AD Domain Services를 활성화한 동일한 가상 네트워크**에 가상 머신을 배포합니다.
> * Azure AD Domain Services를 활성화한 서브넷이 아닌 **다른 서브넷**을 선택합니다.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>새로 프로비전된 Linux 가상 머신에 원격으로 연결
CoreOS 가상 머신이 Azure에서 프로비전되었습니다. 다음 작업은 VM을 프로비전할 때 만든 로컬 관리자 계정을 사용하여 가상 머신에 원격으로 연결하는 것입니다.

[Linux를 실행하는 가상 머신에 로그온하는 방법](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서의 지침을 따르세요.


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Linux 가상 컴퓨터에서 호스트 파일 구성
SSH 터미널에서 /etc/hosts 파일을 편집하고 컴퓨터의 IP 주소 및 호스트 이름을 업데이트합니다.

```
sudo vi /etc/hosts
```

호스트 파일에 다음 값을 입력합니다.

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
여기서 'contoso100.com'은 관리되는 도메인의 DNS 도메인 이름입니다. 'contoso-coreos'는 관리되는 도메인에 가입한 CoreOS 가상 컴퓨터의 호스트 이름입니다.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Linux 가상 머신에서 SSSD 서비스 구성
다음으로, 다음 샘플과 일치하도록 ('/etc/sssd/sssd.conf')의 SSSD 구성 파일을 업데이트합니다.

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
'CONTOSO100.COM'을 관리되는 도메인의 DNS 도메인 이름으로 대체합니다. 구성 파일에서 도메인 이름은 대문자로 지정해야 합니다.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 가상 컴퓨터를 관리되는 도메인에 가입
이제 필요한 패키지를 Linux 가상 머신에 설치했고 다음 작업은 가상 머신을 관리되는 도메인에 가입하는 것입니다.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **문제 해결:** *adcli*가 관리되는 도메인을 찾을 수 없는 경우:
  * 해당 도메인을 가상 머신에서 연결 가능한지 확인합니다(ping 시도).
  * 또한 관리되는 도메인을 사용할 수 있는 동일한 가상 네트워크에 가상 머신을 확실히 배포했는지 확인합니다.
  * 가상 네트워크가 관리되는 도메인의 도메인 컨트롤러를 가리키도록 DNS 서버 설정을 업데이트했는지 확인합니다.
>

SSSD 서비스를 시작합니다. SSH 터미널에서 다음 명령을 입력합니다.
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>도메인 가입 확인
컴퓨터가 관리되는 도메인에 성공적으로 가입되었는지 여부를 확인합니다. 다른 SSH 연결을 사용하여 도메인에 가입된 CoreOS VM에 연결합니다. 도메인 사용자 계정을 사용하고 사용자 계정이 올바른지 확인합니다.

1. SSH 터미널에서 다음 명령을 입력하고 SSH를 사용하여 도메인에 가입된 CoreOS 가상 머신에 연결합니다. 관리되는 도메인에 속하는 도메인 계정을 사용합니다(예: 여기서는 ‘bob@CONTOSO100.COM’).
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. SSH 터미널에서 다음 명령을 입력하여 홈 디렉터리가 올바르게 초기화되었는지 확인합니다.
    ```
    pwd
    ```

3. SSH 터미널에서 다음 명령을 입력하여 그룹 멤버 자격이 올바르게 확인되었는지 확인합니다.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>도메인 가입 문제 해결
[도메인 가입 문제 해결](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) 문서를 참조하세요.

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD 도메인 서비스 - 시작 가이드](active-directory-ds-getting-started.md)
* [Windows Server 가상 머신을 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux를 실행하는 가상 머신에 로그온하는 방법](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
