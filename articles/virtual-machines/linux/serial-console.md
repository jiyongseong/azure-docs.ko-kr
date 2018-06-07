---
title: Azure 가상 머신 직렬 콘솔 | Microsoft Docs
description: Azure 가상 머신용 양방향 직렬 콘솔입니다.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 69f5e29be77f25d649ce357dae6e3905ab2bf6b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-serial-console-preview"></a>가상 머신 직렬 콘솔(미리 보기) 


Azure의 가상 머신 직렬 콘솔은 Linux 및 Windows 가상 머신용 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 직렬 연결은 가상 머신의 COM1 직렬 포트에 연결되며, 가상 머신에 대한 액세스를 제공하고 가상 머신의 네트워크/운영 체제 상태와 관련이 없습니다. 가상 머신의 직렬 콘솔에 대한 액세스는 현재 Azure Portal을 통해서만 가능하며 가상 머신에 대해 VM 참가자 이상의 액세스 권한이 있는 사용자에게만 허용됩니다. 

> [!Note] 
> 사용 약관에 동의하게 되면 미리 보기를 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관]을 참조하세요. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 현재 이 서비스는 **공개 미리 보기** 상태이며 가상 머신의 직렬 콘솔에 대한 액세스는 글로벌 Azure 지역에서 사용할 수 있습니다.) 현재 Azure Government, Azure 독일 및 Azure 중국 클라우드에서는 직렬 콘솔을 사용할 수 없습니다.


## <a name="prerequisites"></a>필수 조건 

* 가상 머신에 [부트 진단](boot-diagnostics.md)을 사용하도록 설정되어 있어야 합니다. 
* 직렬 콘솔을 사용하는 계정에는 VM에 대한 [참가자 역할](../../role-based-access-control/built-in-roles.md)과 [부트 진단](boot-diagnostics.md) 저장소 계정이 있어야 합니다. 
* Linux 배포판 설정에 대한 자세한 내용은 [Linux용 직렬 콘솔 액세스](#accessing-serial-console-for-linux)를 참조하세요.


## <a name="open-the-serial-console"></a>직렬 콘솔 열기
가상 머신의 직렬 콘솔은 [Azure Portal](https://portal.azure.com)을 통해서만 액세스할 수 있습니다. 다음은 포털을 통해 가상 머신의 직렬 콘솔에 액세스하는 단계입니다. 

  1. Azure 포털 열기
  2. 왼쪽 메뉴에서 가상 머신을 선택합니다.
  3. 목록에서 VM을 클릭합니다. VM에 대한 개요 페이지가 열립니다.
  4. 지원 및 문제 해결 섹션까지 아래로 스크롤하여 직렬 콘솔(미리 보기) 옵션을 클릭합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> 직렬 콘솔에는 암호가 구성된 로컬 사용자가 필요합니다. 현재 SSH 공개 키로 구성된 VM만 직렬 콘솔에 액세스할 수 없습니다. 암호가 있는 로컬 사용자를 만들려면 [VM 액세스 확장](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)의 단계를 수행하여 암호가 있는 로컬 사용자를 만듭니다.

### <a name="disable-feature"></a>기능 사용 중지
특정 VM의 부트 진단 설정을 비활성화하여 VM의 직렬 콘솔 기능을 비활성화할 수 있습니다.

## <a name="serial-console-security"></a>직렬 콘솔 보안 

### <a name="access-security"></a>액세스 보안 
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [VM 참가자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 이상의 액세스가 있는 사용자로 제한됩니다. AAD 테넌트에 Multi-Factor Authentication이 필요한 경우에는 직렬 콘솔에 대한 액세스 권한에도 MFA가 필요합니다. 해당 액세스가 [Azure Portal](https://portal.azure.com)을 통해 진행되기 때문입니다.

### <a name="channel-security"></a>채널 보안
주고 받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.  

>[!CAUTION] 
콘솔에 대한 액세스 암호가 기록되지 않은 상태에서 콘솔 내에서 실행되는 명령에 암호, 비밀, 사용자 이름 또는 기타 다른 형태의 PII(개인 식별 정보)가 포함되거나 출력되면, 표시되는 다른 모든 텍스트와 함께 해당 정보가 가상 머신 부트 진단 로그에 기록됩니다. 이것은 직렬 콘솔에 구현된 스크롤백 기능의 일부입니다. 이러한 로그는 순환되며 진단 저장소 계정에 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 하지만, 비밀 및/또는 PII가 포함될 수 있는 모든 작업에 대해서는 SSH 콘솔을 사용하는 것이 가장 좋습니다. 

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우에는, 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 연결됩니다. 이것은 첫 번째 사용자가 자리에서 일어나서 물리적 콘솔을 떠나고 새로운 사용자가 자리에 앉는 것과 유사한 방식입니다.

>[!CAUTION] 
즉, 연결이 끊긴 사용자는 로그아웃되지 않습니다. 연결 해제 시 로그아웃을 강제 적용하는 기능은(SIGHUP 또는 유사한 메커니즘을 통해) 아직 계획 중입니다. Windows의 경우 SAC에 자동 시간 제한을 사용하도록 설정되어 있지만 Linux에서는 터미널 시간 제한 설정을 구성할 수 있습니다. 이렇게 하려면 콘솔에 로그인할 때 사용한 사용자의 .bash_profile 또는 .profile에 `export TMOUT=600`을 추가하여 10분 후에 세션 시간이 초과되도록 합니다.

### <a name="disable-feature"></a>기능 사용 중지
특정 VM의 부트 진단 설정을 비활성화하여 VM의 직렬 콘솔 기능을 비활성화할 수 있습니다.

## <a name="common-scenarios-for-accessing-serial-console"></a>직렬 콘솔에 액세스하는 일반적인 시나리오 
시나리오          | 직렬 콘솔의 작업                |  OS 적용 가능성 
:------------------|:-----------------------------------------|:------------------
FSTAB 파일 손상 | 키를 입력하여 계속하고 텍스트 편집기를 사용하여 fstab 파일 수정 [fstab 문제 해결 방법](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) 참조 | Linux 
잘못된 방화벽 규칙 | 직렬 콘솔에 액세스하여 iptables 또는 Windows 방화벽 규칙 수정 | Linux/Windows 
파일 시스템 손상/검사 | 직렬 콘솔 액세스 및 파일 시스템 복구 | Linux/Windows 
SSH/RDP 구성 문제 | 직렬 콘솔 액세스 및 설정 변경 | Linux/Windows 
네트워크 시스템 잠금| 포털을 통해 직렬 콘솔에 액세스하여 시스템 관리 | Linux/Windows 
부팅 로더와 상호 작용 | 직렬 콘솔을 통해 GRUB/BCD 액세스 | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Linux의 직렬 콘솔에 액세스
직렬 콘솔이 제대로 작동하려면 게스트 운영 체제가 콘솔 메시지를 읽고 직렬 포트에 쓰도록 구성되어야 합니다. 대부분의 [Azure Linux 보증 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)에는 직렬 콘솔이 기본적으로 구성되어 있습니다. 직렬 콘솔 섹션에서 포털을 클릭하기만 하면 콘솔에 액세스할 수 있습니다. 

### <a name="access-for-redhat"></a>RedHat에 대한 액세스 
Azure에서 사용 가능한 RedHat 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. Red Hat에서 단일 사용자 모드를 사용하려면 루트 사용자를 사용하도록 설정해야 합니다. 루트 사용자는 기본적으로 사용하지 않도록 설정되어 있습니다. 단일 사용자 모드를 활성화해야 하는 경우에는 다음 지침을 따르세요.

1. SSH를 통해 Red Hat 시스템에 로그인
2. 루트 사용자의 암호를 사용하도록 설정 
 * `passwd root`(강력한 루트 암호 설정)
3. 루트 사용자가 ttyS0을 통해서만 로그인 할 수 있는지 확인
 * `edit /etc/ssh/sshd_config` 및 PermitRootLog in이 no로 설정되어 있는지 확인
 * ttyS0을 통한 로그인만 허용하도록 `edit /etc/securetty file` 

이제 시스템이 단일 사용자 모드로 부팅되면 루트 암호를 통해 로그인할 수 있습니다.

RHEL 7.4+ 또는 6.9+의 경우 GRUB 프롬프트에서 단일 사용자 모드를 활성화할 수 있습니다. 지침은 [여기](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)를 참조하세요.

### <a name="access-for-ubuntu"></a>Ubuntu에 대한 액세스 
Azure에서 사용 가능한 Ubuntu 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. 시스템이 단일 사용자 모드로 부팅되면 추가 자격 증명 없이 액세스할 수 있습니다. 

### <a name="access-for-coreos"></a>CoreOS에 대한 액세스
Azure에서 사용 가능한 CoreOS 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. 필요한 경우 GRUB 매개 변수를 변경하여 단일 사용자 모드로 시스템을 부팅할 수 있으며 `coreos.autologin=ttyS0`을 추가하면 코어 사용자가 로그인하여 직렬 콘솔에서 사용할 수 있습니다. 

### <a name="access-for-suse"></a>SUSE에 대한 액세스
Azure에서 사용 가능한 SLES 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. Azure에서 이전 버전의 SLES를 사용하는 경우[기술 자료 문서](https://www.novell.com/support/kb/doc.php?id=3456486)를 참고하여 직렬 콘솔을 사용하도록 설정합니다. SLES 12 SP3+의 최신 이미지도 시스템이 비상 모드로 부팅하는 경우 직렬 콘솔을 통한 액세스를 허용합니다.

### <a name="access-for-centos"></a>CentOS에 대한 액세스
Azure에서 사용 가능한 CentOS 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. 단일 사용자 모드의 경우, 위의 Red Hat 이미지와 유사한 지침을 따르세요. 

### <a name="access-for-oracle-linux"></a>Oracle Linux에 대한 액세스
Azure에서 사용 가능한 Oracle Linux 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. 단일 사용자 모드의 경우, 위의 Red Hat 이미지와 유사한 지침을 따르세요.

### <a name="access-for-custom-linux-image"></a>사용자 지정 Linux 이미지에 대한 액세스
사용자 지정 Linux VM 이미지에 대해 직렬 콘솔을 사용하도록 설정하려면 /etc/inittab에서 콘솔 액세스를 사용하도록 설정하여 ttyS0에서 터미널을 실행합니다. 다음은 inittab 파일에 이것을 추가하는 예제입니다. 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>오류
대부분의 오류는 본래 일시적이며 연결을 다시 시도하면 문제가 해결됩니다. 아래 표에는 오류 및 해결 방법 목록이 있습니다. 

오류                            |   해결 방법 
:---------------------------------|:--------------------------------------------|
'<VMNAME>'에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. | VM에 [부트 진단](boot-diagnostics.md)이 사용되도록 설정되어 있는지 확인합니다. 
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. | 직렬 콘솔에 액세스하려면 가상 머신이 시작된 상태여야 합니다.
직렬 콘솔 VM을 사용하는 데 필요한 권한이 없습니다. VM 참가자 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.
부트 진단 저장소 계정인 '<STORAGEACCOUNTNAME>'에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 저장소 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.

## <a name="known-issues"></a>알려진 문제 
직렬 콘솔 액세스는 아직 미리 보기 단계이며, 알려진 문제를 해결하기 위해 노력하고 있습니다. 아래에는 이러한 문제가 가능한 해결 방법이 나열되어 있습니다. 

문제                           |   해결 방법 
:---------------------------------|:--------------------------------------------|
가상 머신 확장 집합 인스턴스 직렬 콘솔에 옵션이 없습니다. |  미리 보기 단계에서는 가상 머신 확장 집합 인스턴스의 직렬 콘솔에 대한 액세스가 지원되지 않습니다.
연결 배너가 표시된 후 Enter를 눌러도 로그인 프롬프트가 표시되지 않습니다. | [Enter를 누르면 아무 작업도 수행되지 않습니다.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>질문과 대답 
**Q. 피드백을 보내려면 어떻게 해야 하나요?**

a. 으로 이동하여 피드백을 제공해 주세요https://aka.ms/serialconsolefeedback. 또는(대안으로) azserialhelp@microsoft.com을 통해서 또는 http://feedback.azure.com의 가상 머신 범주에 피드백을 보내주세요.

**Q. ”기존 콘솔의 OS 유형 Windows가 요청된 Linux의 OS 유형과 충돌됩니다”라는 오류가 발생합니다**.

a. 이것은 알려진 문제이며, 해결하려면 bash 모드에서 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 열고 다시 시도하세요.

**Q. 직렬 콘솔에 액세스할 수 없습니다. 지원 사례를 어디에서 제출할 수 있나요?**

a. 이 미리 보기 기능은 Azure 미리 보기 약관을 통해 제공됩니다. 이 기능에 대한 지원은 위에 언급된 채널을 통해 처리하는 것이 가장 좋습니다. 

## <a name="next-steps"></a>다음 단계
* [Windows](../windows/serial-console.md) VM에서도 직렬 콘솔 사용 가능
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아보기