---
title: Azure Site Recovery를 사용하여 보조 Azure 지역에 Azure VM의 재해 복구 설정
description: Azure Site Recovery 서비스를 사용하여 다른 Azure 지역에 Azure VM의 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d707122f79b37dd6b979be09693011dead988156
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211670"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>보조 Azure 지역에 Azure VM의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 Azure VM의 보조 Azure 지역에 재해 복구를 설정하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음 만들기
> * 대상 리소스 설정 확인
> * VM에 대해 아웃바운드 액세스 설정
> * VM에 대한 복제 사용

Azure에서 Azure로의 복제는 현재 미리 보기에 있습니다.
## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [시나리오 아키텍처 및 구성 요소](concepts-azure-to-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](site-recovery-support-matrix-azure-to-azure.md)을 검토합니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

원본 지역을 제외한 모든 지역에 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **모니터링 및 관리** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
4. 리소스 그룹을 만들거나 기존 그룹을 선택합니다. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정**을 클릭하고 **만들기**를 클릭합니다.

   ![새 자격 증명 모음](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   **대시보드**의 **모든 리소스** 아래와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 추가됩니다.

## <a name="verify-target-resources"></a>대상 리소스 확인

1. Azure 구독에서 재해 복구에 사용되는 대상 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

2. 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery는 대상 VM에 대해 동일한 크기 또는 가장 가깝게 가능한 크기를 선택합니다.

## <a name="configure-outbound-network-connectivity"></a>아웃바운드 네트워크 연결 구성

Site Recovery가 예상대로 작동하려면 복제하려는 VM에서 아웃바운드 네트워크 연결의 일부를 변경해야 합니다.

- Site Recovery는 인증 프록시를 사용하여 네트워크 연결을 제어하도록 지원하지 않습니다.
- 인증 프록시가 있는 경우 복제는 사용할 수 없습니다.

### <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 Site Recovery에서 사용되는 다음 URL에 대한 액세스를 허용합니다.

| **URL** | **세부 정보** |
| ------- | ----------- |
| \*.blob.core.windows.net | VM에서 원본 지역의 캐시 저장소 계정에 데이터를 쓸 수 있도록 합니다. |
| login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. |
| \*.hypervrecoverymanager.windowsazure.com | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. |
| \*.servicebus.windows.net | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 주소 범위에 대한 아웃바운드 연결

URL 대신 IP 주소를 사용하여 아웃바운드 연결을 제어하려면 적절한 데이터 센터 범위, Office 365 주소 및 IP 기반 방화벽, 프록시 또는 NSG 규칙에 대한 서비스 엔드포인트 주소를 허용 목록에 추가합니다.

  - [Microsoft Azure 데이터 센터 IP 범위](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Windows Azure 데이터 센터 IP 범위(독일)](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Windows Azure 데이터 센터 IP 범위(중국)](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery 서비스 끝점 IP 주소](https://aka.ms/site-recovery-public-ips)

이 [스크립트](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)를 사용하여 필요한 NSG 규칙을 만들 수 있습니다.

## <a name="verify-azure-vm-certificates"></a>Azure VM 인증서 확인

복제하려는 Windows 또는 Linux VM에 최신 루트 인증서가 모두 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 Site Recovery에 VM을 등록할 수 없습니다.

- Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.

- Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다.

## <a name="set-permissions-on-the-account"></a>계정에 권한 설정

Azure Site Recovery는 Site Recovery 관리 작업을 제어하는 3가지 기본 제공 역할을 제공합니다.

- **Site Recovery 참가자** - 이 역할에는 Recovery Services 자격 증명 모음에서 Azure Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다. 그러나 이 역할의 사용자는 Recovery Services 자격 증명 모음을 만들거나 삭제할 수 없고 액세스 권한을 다른 사용자에게 할당할 수 없습니다. 이 역할은 응용 프로그램이나 전체 조직에 재해 복구를 사용하도록 설정하고 관리할 수 있는 재해 복구 관리자에게 가장 적합합니다.

- **Site Recovery 연산자** - 이 역할에는 장애 조치 및 장애 복구 작업을 실행하고 관리하는 사용 권한이 있습니다. 이 역할의 사용자는 복제를 활성화하거나 비활성화할 수 없고, 자격 증명 모음을 만들거나 삭제할 수 없으며, 새로운 인프라를 등록하거나 다른 사용자에게 액세스 권한을 할당할 수 없습니다. 이 역할은 응용 프로그램 소유자 및 IT 관리자가 지시하는 경우 가상 머신 또는 응용 프로그램을 장애 조치할 수 있는 재해 복구 운영자에게 가장 적합합니다. 재해를 해결한 후에 DR 운영자는 가상 머신을 다시 보호하고 장애 복구(failback)할 수 있습니다.

- **Site Recovery 읽기 권한자** - 이 역할은 모든 Site Recovery 관리 작업을 볼 수 있는 권한을 갖습니다. 이 역할은 현재 보호 상태를 모니터링하고 지원 티켓을 발행할 수 있는 IT 모니터링 임원에게 가장 적합합니다.

[Azure RBAC 기본 제공 역할](../role-based-access-control/built-in-roles.md)에 대해 알아보기

## <a name="enable-replication"></a>복제 사용

### <a name="select-the-source"></a>원본 선택

1. Recovery Services 자격 증명 모음에서 자격 증명 모음 이름 > **+복제**를 클릭합니다.
2. **원본**에서 **Azure**를 선택합니다.
3. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
4. VM의 **Azure 가상 머신 배포 모델**(**Resource Manager** 또는 **클래식**)을 선택합니다.
5. **원본 리소스 그룹**(Resource Manager VM의 경우) 또는 **클라우드 서비스**(클래식 VM의 경우)를 선택합니다.
6. **확인**을 클릭하여 설정을 저장합니다.

### <a name="select-the-vms"></a>VM 선택

Site Recovery는 구독 및 리소스 그룹/클라우드 서비스와 연결된 VM 목록을 검색합니다.

1. **Virtual Machines**에서 복제할 VM을 선택합니다.
2. **확인**을 클릭합니다.

### <a name="configure-replication-settings"></a>복제 설정 구성

Site Recovery는 대상 지역에 대한 기본 설정 및 복제 정책을 만듭니다. 요구 사항에 따라 설정을 변경할 수 있습니다.

1. **설정**을 클릭하여 대상 및 복제 설정을 확인합니다.
2. 기본 대상 설정을 재정의하려면 **리소스 그룹, 네트워크, 저장소 및 가용성 집합** 옆의 **사용자 지정**을 클릭합니다.

  ![설정 구성](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **대상 위치**: 재해 복구에 사용되는 대상 지역입니다. 대상 위치가 Site Recovery 자격 증명 모음의 위치와 일치하는 것이 좋습니다.

- **대상 리소스 그룹**: 장애 조치(failover) 후 Azure VM을 보유하는 대상 지역의 리소스 그룹입니다. 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다. 원본 가상 머신이 호스트되는 지역을 제외한 모든 지역이 대상 리소스 그룹의 리소스 그룹 위치가 될 수 있습니다. 

- **대상 가상 네트워크**: 장애 조치(failover) 후 VM이 배치되는 대상 지역의 네트워크입니다.
  기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가상 네트워크(및 서브넷)를 만듭니다.

- **저장소 계정 캐시**: Site Recovery는 원본 영역의 저장소 계정을 사용합니다. 원본 VM의 변경 내용이 이 계정으로 전송된 후 대상 위치에 복제됩니다.

- **대상 저장소 계정(원본 VM에 관리 디스크를 사용하지 않는 경우)**: 기본적으로 Site Recovery는 대상 지역에 새 저장소 계정을 만들어서 원본 VM 저장소 계정을 미러링합니다.

- **복제본 관리 디스크(원본 VM이 관리 디스크를 사용하는 경우)**: 기본적으로 Site Recovery는 대상 지역에 복제본 관리 디스크를 만들어서 원본 VM의 관리 디스크와 동일한 저장소 유형(표준 또는 프리미엄)을 원본 VM의 관리 디스크로 미러링합니다.

- **대상 가용성 집합**: 기본적으로 Site Recovery는 “asr” 접미사를 사용하여 대상 지역에 새 가용성 집합을 만듭니다. VM이 원본 영역에 있는 집합의 일부인 경우 가용성 집합만 추가할 수 있습니다.

기본 복제 정책 설정을 재정의하려면 **복제 정책** 옆의 **사용자 지정**을 클릭합니다.  

- **복제 정책 이름**: 정책 이름입니다.

- **복구 지점 유지**: 기본적으로 Site Recovery는 복구 지점을 24시간 동안 유지합니다. 이 값을 1-72시간 사이에서 구성할 수 있습니다.

- **앱 일치 스냅숏 빈도**: 기본적으로 Site Recovery는 4시간마다 앱 일치 스냅숏을 만듭니다. 이 값을 1-12시간 사이에서 구성할 수 있습니다. 앱 일치 스냅숏은 VM 내 응용 프로그램 데이터의 지정 시간 스냅숏입니다. VSS(볼륨 섀도 복사본 서비스)는 스냅숏을 만들 때 VM의 앱이 일관된 상태가 되도록 합니다.

- **복제 그룹**: 응용 프로그램이 여러 VM에서 다중 VM 일관성을 지켜야 하는 경우 해당 VM용 복제 그룹을 만들 수 있습니다. 선택한 VM은 기본적으로 복제 그룹에 포함되지 않습니다.

  **복제 정책** 옆의 **사용자 지정**을 클릭하고 다중 VM 일관성에 대해 **예**를 선택하면 VM이 복제 그룹에 포함됩니다. 새 복제 그룹을 만들거나 기존 복제 그룹을 사용할 수 있습니다. 복제 그룹에 포함할 VM을 선택하고 **확인**을 클릭합니다.

> [!IMPORTANT]
  복제 그룹의 모든 컴퓨터는 장애 조치(failover) 시에 충돌 일치/앱 일치 복구 지점을 공유합니다. 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. 따라서 컴퓨터가 동일한 워크로드를 실행하며 여러 컴퓨터에서 일관성을 지켜야 하는 경우에만 다중 VM 일관성을 사용해야 합니다.

> [!IMPORTANT]
  다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 20004 포트를 통한 VM 간의 내부 통신을 차단하는 방화벽 어플라이언스가 없는지 확인합니다. Linux VM을 복제 그룹에 포함하고 싶다면 특정 Linux 버전의 지침에 따라 20004 포트의 아웃바운드 트래픽을 수동으로 열어야 합니다.

### <a name="track-replication-status"></a>복제 상태 추적

1. **설정**에서 **새로 고침**을 클릭하여 최신 상태를 가져옵니다.

2. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다.

3. **설정** > **복제된 항목**에서 VM의 상태 및 초기 복제 진행률을 볼 수 있습니다. VM을 클릭하여 해당 설정으로 드릴다운합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM의 재해 복구를 구성해 보았습니다. 다음 단계는 구성을 테스트하는 것입니다.

> [!div class="nextstepaction"]
> [재해 복구 드릴 실행](azure-to-azure-tutorial-dr-drill.md)
