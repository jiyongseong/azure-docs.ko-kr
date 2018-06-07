---
title: 'Azure Active Directory Domain Services: 관리되는 도메인에 Ubuntu VM 가입 | Microsoft Docs'
description: Ubuntu Linux 가상 머신을 Azure AD Domain Services에 가입
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: maheshu
ms.openlocfilehash: d968548f9ac369f02f10a10d8f1ecc99c48bca60
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Azure의 Ubuntu 가상 머신을 관리되는 도메인에 가입
이 문서에서는 Ubuntu Linux 가상 컴퓨터를 Azure AD Domain Services 관리되는 도메인에 가입하는 방법을 보여 줍니다.


## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.  
1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.
4. 관리되는 도메인의 IP 주소를 가상 네트워크에 대한 DNS 서버로 구성했는지 확인합니다. 자세한 내용은 [Azure 가상 네트워크에 대한 DNS 설정을 업데이트하는 방법](active-directory-ds-getting-started-dns.md)을 참조하세요.
5. [Azure AD Domain Services 관리되는 도메인에 암호를 동기화](active-directory-ds-getting-started-password-sync.md)하는 데 필요한 단계를 완료합니다.


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Ubuntu Linux 가상 머신 프로비전
다음 방법 중 하나를 사용하여 Azure에서 Ubuntu Linux 가상 머신을 프로비전합니다.
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * **Azure AD Domain Services를 활성화한 동일한 가상 네트워크**에 가상 머신을 배포합니다.
> * Azure AD Domain Services를 활성화한 서브넷이 아닌 **다른 서브넷**을 선택합니다.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Ubuntu Linux 가상 머신에 원격으로 연결
Ubuntu 가상 머신이 Azure에서 프로비전되었습니다. 다음 작업은 VM을 프로비전할 때 만든 로컬 관리자 계정을 사용하여 가상 머신에 원격으로 연결하는 것입니다.

[Linux를 실행하는 가상 머신에 로그온하는 방법](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서의 지침을 따르세요.


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Linux 가상 컴퓨터에서 호스트 파일 구성
SSH 터미널에서 /etc/hosts 파일을 편집하고 컴퓨터의 IP 주소 및 호스트 이름을 업데이트합니다.

```
sudo vi /etc/hosts
```

호스트 파일에 다음 값을 입력합니다.

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
여기서 'contoso100.com'은 관리되는 도메인의 DNS 도메인 이름입니다. 'contoso-ubuntu'는 관리되는 도메인에 가입한 Ubuntu 가상 머신의 호스트 이름입니다.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 가상 머신에 필요한 패키지 설치
다음으로 가상 머신에서 도메인 가입에 필요한 패키지를 설치합니다. 다음 단계를 수행합니다.

1.  SSH 터미널에서 다음 명령을 입력하여 저장소에서 패키지 목록을 다운로드합니다. 이 명령은 패키지 목록을 업데이트하여 최신 버전의 패키지 및 해당 종속성에 대한 정보를 가져옵니다.

    ```
    sudo apt-get update
    ```

2. 필수 패키지를 설치하려면 다음 명령을 입력합니다.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Kerberos를 설치하는 동안 분홍색 화면이 표시됩니다. 'krb5-user' 패키지 설치는 영역 이름(모두 대문자)에 대한 메시지를 표시합니다. 설치는 /etc/krb5.conf에서 [realm] 및 [domain_realm] 섹션을 작성합니다.

    > [!TIP]
    > 관리되는 도메인의 이름이 contoso100.com이면 CONTOSO100.COM을 영역으로 입력합니다. 영역 이름을 대문자로 지정해야 합니다.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Linux 가상 머신에서 NTP(Network Time Protocol) 설정 구성
Ubuntu VM의 날짜 및 시간은 관리되는 도메인과 동기화해야 합니다. /etc/ntp.conf 파일에 관리되는 도메인의 NTP 호스트 이름을 추가합니다.

```
sudo vi /etc/ntp.conf
```

ntp.conf 파일에서 다음 값을 입력하고 파일을 저장합니다.

```
server contoso100.com
```
여기서 'contoso100.com'은 관리되는 도메인의 DNS 도메인 이름입니다.

이제 Ubuntu VM의 날짜 및 시간을 NTP 서버와 동기화하고 NTP 서비스를 시작합니다.

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 가상 컴퓨터를 관리되는 도메인에 가입
이제 필요한 패키지를 Linux 가상 머신에 설치했고 다음 작업은 가상 머신을 관리되는 도메인에 가입하는 것입니다.

1. AAD 도메인 서비스 관리되는 도메인을 검색합니다. SSH 터미널에서 다음 명령을 입력합니다.

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE] 
   > **문제 해결:** *영역 검색*을 통해 관리되는 도메인을 찾을 수 없는 경우 다음을 수행합니다.
     * 해당 도메인을 가상 머신에서 연결 가능한지 확인합니다(ping 시도).
     * 또한 관리되는 도메인을 사용할 수 있는 동일한 가상 네트워크에 가상 머신을 확실히 배포했는지 확인합니다.
     * 가상 네트워크가 관리되는 도메인의 도메인 컨트롤러를 가리키도록 DNS 서버 설정을 업데이트했는지 확인합니다.
   >

2. Kerberos를 초기화합니다. SSH 터미널에서 다음 명령을 입력합니다. 

    > [!TIP] 
    > * 'AAD DC 관리자' 그룹에 속한 사용자를 지정해야 합니다. 
    > * 도메인 이름을 대문자로 지정하지 않으면 kinit가 실패합니다.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. 컴퓨터를 도메인에 가입합니다. SSH 터미널에서 다음 명령을 입력합니다. 

    > [!TIP] 
    > 이전 단계에서 지정한 동일한 사용자 계정을 사용합니다.
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

컴퓨터가 관리되는 도메인에 성공적으로 가입되면 메시지("Successfully enrolled machine in realm")가 표시됩니다.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>SSSD 구성 업데이트 및 서비스 다시 시작
1. SSH 터미널에서 다음 명령을 입력합니다. sssd.conf 파일을 열어 다음과 같이 변경합니다.
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. **use_fully_qualified_names = True** 줄을 주석으로 처리하고 파일을 저장합니다.
    ```
    # use_fully_qualified_names = True
    ```

3. SSSD 서비스를 다시 시작합니다.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>자동 홈 디렉터리 생성 구성
사용자가 로그인한 후에 홈 디렉터리의 자동 생성을 활성화하려면 PuTTY 터미널에서 다음 명령을 입력합니다.
```
sudo vi /etc/pam.d/common-session
```
    
'session optional pam_sss.so' 아래의 이 파일에 다음 줄을 추가하고 저장합니다.
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>도메인 가입 확인
컴퓨터가 관리되는 도메인에 성공적으로 가입되었는지 여부를 확인합니다. 다른 SSH 연결을 사용하여 도메인에 가입된 Ubuntu VM에 연결합니다. 도메인 사용자 계정을 사용하고 사용자 계정이 올바른지 확인합니다.

1. SSH 터미널에서 다음 명령을 입력하고 SSH를 사용하여 도메인에 가입된 Ubuntu 가상 컴퓨터에 연결합니다. 관리되는 도메인에 속하는 도메인 계정을 사용합니다(예: 여기서는 ‘bob@CONTOSO100.COM’).
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. SSH 터미널에서 다음 명령을 입력하여 홈 디렉터리가 올바르게 초기화되었는지 확인합니다.
    ```
    pwd
    ```

3. SSH 터미널에서 다음 명령을 입력하여 그룹 멤버 자격이 올바르게 확인되었는지 확인합니다.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여
Ubuntu VM에서 멤버에게 'AAD DC Administrators' 그룹 관리 권한을 부여할 수 있습니다. sudo 파일은 /etc/sudoers에 있습니다. sudoers에 추가된 AD 그룹의 멤버는 sudo를 수행할 수 있습니다.

1. SSH 터미널에서 superuser 권한으로 로그인했는지 확인합니다. VM을 만드는 동안 지정한 로컬 관리자 계정을 사용할 수 있습니다. 다음 명령을 실행합니다.
    ```
    sudo vi /etc/sudoers
    ```

2. /etc/sudoers 파일에 다음 항목을 추가하고 저장합니다.
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. 이제 'AAD DC Administrators' 그룹의 멤버로 로그인할 수 있고 VM에 대한 관리 권한이 있어야 합니다.


## <a name="troubleshooting-domain-join"></a>도메인 가입 문제 해결
[도메인 가입 문제 해결](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) 문서를 참조하세요.


## <a name="related-content"></a>관련 콘텐츠
* [Azure AD 도메인 서비스 - 시작 가이드](active-directory-ds-getting-started.md)
* [Windows Server 가상 머신을 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux를 실행하는 가상 머신에 로그온하는 방법](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
