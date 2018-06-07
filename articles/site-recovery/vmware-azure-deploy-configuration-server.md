---
title: Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버 배포 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버를 관리하는 방법을 설명합니다.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 2389ff6824a005db46c04bd1b45eabfd5ce50481
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188483"
---
# <a name="deploy-a-configuration-server"></a>구성 서버 배포

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 배포합니다. 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다. 이 문서에서는 VMware VM을 Azure로 복제할 때 구성 서버를 배포하는 데 필요한 단계를 안내합니다. 물리적 서버 복제를 위해 구성 서버를 설정해야 하는 경우 [이 문서에 따릅니다](physical-azure-set-up-source.md).

## <a name="prerequisites"></a>필수 조건

항상 사용 가능한 VMware VM으로 구성 서버를 배포하는 것이 좋습니다. 최소 하드웨어 요구 사항이 다음 표에 요약되어 있습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>용량 계획

구성 서버에 대한 크기 조정 요구 사항은 잠재적 데이터 변경률에 따라 다릅니다. 이 표를 가이드로 참조하세요.

| **CPU** | **메모리** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터** |
| --- | --- | --- | --- | --- |
| 8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz) |16GB |300GB |500GB 이하 |100대 미만의 컴퓨터를 복제합니다. |
| 12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz) |18GB |600GB |500GB ~ 1TB |100 ~ 150대 컴퓨터를 복제합니다. |
| 16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz) |32GB |1TB |1TB ~ 2TB |150 ~ 200대 컴퓨터를 복제합니다. |


VMware VM을 복제 하는 경우 [용량 계획 고려 사항](/site-recovery-plan-capacity-vmware.md)에 대해 자세히 알아보세요. VMware 복제를 위한 [Deployment Planner 도구](site-recovery-deployment-planner.md)를 실행합니다.



## <a name="download-the-template"></a>템플릿 다운로드

Site Recovery는 구성 서버를 고가용성 VMware VM으로 설정하기 위한 다운로드 가능한 템플릿을 제공합니다. 

1. 자격 증명 모음에서 **인프라 준비** > **원본**으로 이동합니다.
2. **원본 준비**에서 **+구성 서버**를 선택합니다.
3. **서버 추가**에서 **VMware에 대한 구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. 구성 서버에 대한 OVF(Open Virtualization) 템플릿을 다운로드합니다.

  > [!TIP]
  [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 최신 버전의 구성 서버 템플릿을 직접 다운로드할 수 있습니다.


## <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기


1. VMWare vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포**를 선택하여 OVF 템플릿 배포 마법사를 시작합니다.

     ![OVF 템플릿](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **원본 선택**에서 다운로드한 OVF의 위치를 입력합니다.
4. **세부 정보 검토**에서 **다음**을 선택합니다.
5. **이름 및 폴더 선택**과 **구성 선택**에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **저장소 선택**의 **가상 디스크 형식 선택**에서 **씩 프로비전 Eager Zeroed**를 선택합니다.
4. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
5. **완료 준비**에서 다음을 수행합니다.

    * 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침**을 선택합니다.

    * 추가 네트워크 인터페이스를 추가하려면 **배포 후 전원 켜기**의 선택을 취소한 다음, **마침**을 선택합니다. 기본적으로 구성 서버 템플릿은 단일 NIC를 사용하여 배포됩니다. 배포 후 NIC를 추가할 수 있습니다.


## <a name="add-an-additional-adapter"></a>어댑터 추가

구성 서버에 NIC를 추가하려면 자격 증명 모음에 서버를 등록하기 전에 추가하세요. 등록 후에는 어댑터를 추가할 수 없습니다.

1. VSphere 클라이언트 인벤토리에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
2. **하드웨어**에서 **추가** > **이더넷 어댑터**를 선택합니다. 그런 후 **다음**을 선택합니다.
3. 어댑터 유형 및 네트워크를 선택합니다. 
4. VM이 켜질 때 가상 NIC에 연결하려면 **전원이 켜지면 연결**을 선택합니다. 그런 다음, **다음** > **마침** > **확인**을 선택합니다.
 

## <a name="register-the-configuration-server"></a>구성 서버 등록 

1. VMWare vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.
4. 처음으로 로그인하면 Azure Site Recovery 구성 도구가 시작됩니다.
5. Site Recovery에 구성 서버를 등록하는 데 사용된 이름을 입력합니다. 그런 후 **다음**을 선택합니다.
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다.
7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 컴퓨터에 다시 로그인합니다. 구성 서버 관리 마법사가 자동으로 시작됩니다.

### <a name="configure-settings"></a>설정 구성

1. 구성 서버 관리 마법사에서 **연결 설정**을 선택합니다. 복제 트래픽을 수신하도록 NIC를 선택한 다음, **저장**을 선택합니다. 구성된 후에는 이 설정을 변경할 수 없습니다.
2. **Recovery Services 자격 증명 모음 선택**에서 Azure 구독을 선택한 다음, 관련 리소스 그룹 및 자격 증명 모음을 선택합니다.
3. **타사 소프트웨어 설치**에서 사용권 계약에 동의합니다. **다운로드 및 설치**를 선택하여 MySQL 서버를 설치합니다.
4. **VMware PowerCLI 설치**를 선택합니다. 이 단계를 수행하기 전에 모든 브라우저 창을 닫아야 합니다. 그런 다음, **계속**을 선택합니다.
5. 계속하기 전에 **어플라이언스 구성 유효성 검사**에서 필수 구성 요소가 확인됩니다.
6. **vCenter Server/vSphere ESXi 서버 구성**에 복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 입력합니다. 서버가 수신 대기하는 포트 및 자격 증명 모음에 있는 VMware 서버에 사용할 이름을 입력합니다.
7. 구성 서버가 VMware 서버에 연결하는 데 사용할 자격 증명을 입력합니다. Site Recovery는 이러한 자격 증명을 사용하여 복제에 사용 가능한 VMware VM을 자동으로 검색합니다. **추가**를 선택한 다음, **계속**을 선택합니다.
8. **가상 머신 자격 증명 구성**에서 복제가 사용되면 컴퓨터에 Azure Site Recovery 모바일 서비스를 자동으로 설치하는 데 사용할 사용자 이름 및 암호를 입력합니다. Windows 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다. Linux의 경우 루트 계정에 대한 세부 정보를 제공합니다.
9. **구성 완료**를 선택하여 등록을 완료합니다. 
10. 등록이 완료되면 Azure Portal에서 구성 서버 및 VMware 서버가 자격 증명 모음의 **원본** 페이지에 표시되는지 확인합니다. 그런 다음, **확인**을 선택하여 대상 설정을 구성합니다.


## <a name="upgrade-the-configuration-server"></a>구성 서버 업그레이드

구성 서버를 최신 버전으로 업그레이드하려면 [여기에서](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) 지정된 단계를 읽어 보세요.


## <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>다음 단계

Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
