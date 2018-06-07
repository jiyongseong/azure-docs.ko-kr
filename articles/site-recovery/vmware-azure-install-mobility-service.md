---
title: 모바일 서비스(VMware/Azure 물리적 서버) 설치 | Microsoft Docs
description: 모바일 서비스 에이전트를 설치하여 Azure Site Recovery를 사용하여 온-프레미스 VMware VM 및 실제 서버를 보호하는 방법에 대해 알아봅니다.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 3279dbc2eeecd3a3f0f36a47d8dd51ef4f3f503f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33942138"
---
# <a name="install-the-mobility-service"></a>모바일 서비스 설치 

Site Recovery 모바일 서비스는 Azure에 복제하려는 VMware VM 및 실제 서버에 설치됩니다. 서비스는 컴퓨터에서 데이터 쓰기를 캡처하여 프로세스 서버로 전달합니다. Azure에 복제하려는 모든 컴퓨터에 모바일 서비스(VMware VM 또는 물리적 서버)를 배포합니다. 다음 방법을 사용하여 보호하려는 서버 및 VMware VM에 모바일 서비스를 배포할 수 있습니다.


* [System Center Configuration Manager와 같은 소프트웨어 배포 도구를 사용하여 설치](vmware-azure-mobility-install-configuration-mgr.md)
* [Azure Automation 및 자동화 DSC(필요한 상태 구성)를 사용하여 설치](vmware-azure-mobility-deploy-automation-dsc.md)
* [UI에서 수동으로 설치](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [명령 프롬프트에서 수동으로 설치](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Site Recovery 강제 설치를 사용하여 설치](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> 9.7.0.0 버전부터 Mobility Service 설치 관리자는 Windows VM에 사용 가능한 최신 [Azure VM 에이전트](../virtual-machines/extensions/features-windows.md#azure-vm-agent)도 설치합니다. 컴퓨터가 Azure로 장애 조치되는 경우에는 VM 확장 사용에 대한 에이전트 설치 필수 조건을 충족합니다.

## <a name="prerequisites"></a>필수 조건
서버에 모바일 서비스를 수동으로 설치하기 전에 이러한 필수 조건 단계를 완료합니다.
1. 구성 서버에 로그인한 후 관리자로 명령 프롬프트 창을 엽니다.
2. 디렉터리를 Bin 폴더로 변경하고 암호 파일을 만듭니다.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 암호 파일을 안전한 위치에 저장합니다. 이 파일은 Mobility Service를 설치하는 동안 사용됩니다.
4. 지원되는 모든 운영 체제에 대한 모바일 서비스 설치 관리자는 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 폴더에 있습니다.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>모바일 서비스 설치 관리자와 운영 체제 매핑

| 설치 관리자 파일 템플릿 이름| 운영 체제 |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1(64비트) </br> Windows Server 2012(64비트) </br> Windows Server 2012 R2(64비트) </br> Windows Server 2016(64비트) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux(RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6.9(64비트 전용) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9(64비트 전용) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux(RHEL) 7.1, 7.2, 7.3(64비트 전용) </br> CentOS 7.0, 7.1, 7.2, 7.3(64비트 전용) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3(64비트만 해당)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4(64비트만 해당)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5(64비트만 해당)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04(64비트만 해당)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 서버(64비트 전용)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7(64비트 전용)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8(64비트 전용)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>GUI를 사용하여 수동으로 모바일 서비스 설치

>[!IMPORTANT]
> 구성 서버를 사용하여 Azure 구독/지역 간에 Azure IaaS 가상 머신을 복제하는 경우 명령줄 기반 설치 메서드를 사용합니다.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>명령 프롬프트에서 수동으로 모바일 서비스 설치

### <a name="command-line-installation-on-a-windows-computer"></a>Windows 컴퓨터에서 명령줄 설치
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Linux 컴퓨터에서 명령줄 설치
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Azure Site Recovery에서 강제 설치를 사용하여 모바일 서비스 설치
Site Recovery를 사용하여 Mobility Service의 강제 설치를 수행할 수 있습니다. 모든 대상 컴퓨터는 다음과 같은 전제 조건을 충족해야 합니다.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Mobility Service를 설치한 후 Azure Portal에서 **+복제**를 선택하여 이러한 VM 보호를 시작합니다.

## <a name="update-mobility-service"></a>모바일 서비스를 업데이트합니다.

> [!WARNING]
> 보호된 서버에서 Mobility Service의 업데이트를 시작하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

1. Azure Portal에서 *자격 증명 모음 이름* > **복제된 항목 보기**로 이동합니다.
2. 구성 서버를 최신 버전으로 업데이트한 경우 “새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. 이 알림을 선택하여 가상 머신 선택 페이지를 엽니다.
4. Mobile Service를 업그레이드하려는 가상 머신을 선택하고 **확인**을 선택합니다.

     ![복제된 항목 VM 목록](.\media\vmware-azure-install-mobility-service\update-okpng.png)

선택한 가상 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다.

> [!NOTE]
> Mobility Service를 설치하는 데 사용되는 계정의 암호를 업데이트하는 방법에 대해 [자세히 알아봅니다](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Windows Server 컴퓨터에서 모바일 서비스 제거
Windows Server 컴퓨터에서 모바일 서비스를 제거하려면 다음 방법 중 하나를 사용합니다.

### <a name="uninstall-by-using-the-gui"></a>GUI를 사용하여 제거
1. 제어판에서 **프로그램**을 선택합니다.
2. **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**를 선택한 다음 **제거**를 선택합니다.

### <a name="uninstall-at-a-command-prompt"></a>명령 프롬프트에서 제거
1. 관리자로 명령 프롬프트 창을 엽니다.
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Linux 컴퓨터에서 모바일 서비스 제거
1. Linux 서버에서 **루트** 사용자로 로그인합니다.
2. 터미널에서 /user/local/ASR로 이동합니다.
3. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

    ```
    uninstall.sh -Y
    ```
