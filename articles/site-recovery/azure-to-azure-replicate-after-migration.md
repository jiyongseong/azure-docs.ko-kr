---
title: Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure VM에 대해 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure 지역 간에 재해 복구를 설정하기 위해 컴퓨터를 준비하는 방법을 설명합니다.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: ponatara
ms.openlocfilehash: f64d61134f61476bcdd803f92019830b7cecf867
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Azure로 마이그레이션한 후 Azure VM에 대해 재해 복구 설정 

>[!NOTE]
> Azure Site Recovery를 사용한 Azure VM의 재해 복구는 현재 미리 보기로 제공됩니다.

[Site Recovery](site-recovery-overview.md) 서비스를 사용하여 [온-프레미스 컴퓨터를 Azure VM으로 마이그레이션](tutorial-migrate-on-premises-to-azure.md)한 후 이 문서를 사용합니다. 이 문서는 Site Recovery를 사용하여 보조 Azure 지역으로 재해 복구를 설정하기 위해 Azure VM을 준비하는 데 도움이 됩니다.



## <a name="before-you-start"></a>시작하기 전에

재해 복구를 설정하기 전에 마이그레이션이 예상대로 완료되었는지 확인합니다. 마이그레이션을 성공적으로 완료하려면 마이그레이션할 각 컴퓨터에 대해 **마이그레이션 완료** 옵션을 선택해야 합니다. 



## <a name="install-the-azure-vm-agent"></a>Azure VM 에이전트 설치

Site Recovery가 복제할 수 있도록 VM에 Azure [VM 에이전트](../virtual-machines/extensions/agent-windows.md)가 설치되어 있어야 합니다.


1. Windows를 실행하는 VM에 VM 에이전트를 설치하려면 [에이전트 설치 관리자](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 실행합니다. 설치를 완료하려면 VM에 대한 관리자 권한이 필요합니다.
2. Linux를 실행하는 VM에 VM 에이전트를 설치하려면 최신 [Linux 에이전트](../virtual-machines/extensions/agent-linux.md)를 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다. 배포 리포지토리에서 설치하는 것이 좋습니다. GitHub에서 직접 Linux VM 에이전트를 설치하지 않는 것이 좋습니다. 


## <a name="validate-the-installation-on-windows-vms"></a>Windows VM에서 설치 유효성 검사

1. Azure VM의 C:\WindowsAzure\Packages 폴더에 WaAppAgent.exe 파일이 표시되어야 합니다.
2. 파일을 마우스 오른쪽 단추로 클릭하고 **속성**에서 **세부 정보** 탭을 선택합니다.
3. **제품 버전** 필드에 2.6.1198.718 이상이 표시되는지 확인합니다.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>VMware VM 또는 물리적 서버에서 마이그레이션

온-프레미스 VMware VM(또는 물리적 서버)을 Azure로 마이그레이션하는 경우 다음에 유의하세요.

- 마이그레이션된 컴퓨터에 설치된 모바일 서비스가 v9.6 또는 이전 버전인 경우에만 Azure VM 에이전트를 설치해야 합니다.
- 모바일 서비스 버전 9.7.0.0 이상을 실행하는 Windows VM에서는 서비스 설치 관리자가 사용 가능한 최신 Azure VM 에이전트를 설치합니다. 마이그레이션 시 이러한 VM은 Site Recovery 확장을 포함하여 모든 VM 확장에 대한 에이전트 설치 필수 조건을 이미 충족합니다.
- 다음 방법 중 하나를 사용하여 Azure VM에서 모바일 서비스를 수동으로 제거해야 합니다. 복제를 구성하기 전에 VM을 다시 시작합니다.
    - Windows의 경우 제어판 > **프로그램 추가/제거**에서 **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**를 제거합니다. 관리자 권한의 명령 프롬프트에서 다음을 실행합니다.
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux의 경우 루트 사용자로 로그인합니다. 터미널에서 **/user/local/ASR**로 이동하여 다음 명령을 실행합니다.
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>다음 단계

Azure VM을 보조 지역에 [신속하게 복제](azure-to-azure-quickstart.md)합니다.
