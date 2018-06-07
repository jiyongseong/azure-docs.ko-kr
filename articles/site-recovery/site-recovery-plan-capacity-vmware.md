---
title: "Azure Site Recovery를 사용하여 Azure에 VMware 복제를 위한 용량 및 크기 조정 계획 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure에 VMware VM을 복제하는 경우 용량 및 크기 조정을 계획하려면 이 문서를 사용합니다."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2018
ms.author: rayne
ms.openlocfilehash: dbaf1e29fbf4be8ef9432842b7ea4d6511b21cbb
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 VMware 복제를 위한 용량 및 크기 조정 계획

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM 및 물리적 서버를 Azure에 복제하는 경우 용량 및 크기 조정 계획을 파악하려면 이 문서를 사용합니다.

## <a name="how-do-i-start-capacity-planning"></a>용량 계획을 시작하려면 어떻게 해야 하나요?

VMware 복제를 위해 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc)를 실행하여 복제 환경에 대한 정보를 수집합니다. [자세히 알아보세요](site-recovery-deployment-planner.md) . 호환되거나 호환되지 않는 VM, VM당 디스크 및 디스크당 데이터 변동에 대한 정보를 수집합니다. 도구는 네트워크 대역폭 요구 사항과 성공적인 복제 및 테스트 장애 조치(failover)에 필요한 Azure 인프라에 대해서도 다룹니다.

## <a name="capacity-considerations"></a>용량 고려 사항

**구성 요소** | **세부 정보** |
--- | --- | ---
**복제** | **일일 최대 변경률:** 보호된 컴퓨터는 하나의 프로세스 서버만 사용할 수 있으며 단일 프로세스 서버는 최대 2TB의 일일 최대 변경률을 처리할 수 있습니다. 따라서 2TB는 보호되는 컴퓨터에 대해 지원되는 최대 일일 데이터 변경률입니다.<br/><br/> **최대 처리량:** 복제된 컴퓨터는 Azure에서 하나의 저장소 계정에 속할 수 있습니다. 표준 저장소 계정은 초당 최대 20,000개의 요청을 처리할 수 있으며 원본 컴퓨터의 IOPS(초당 입력/출력 작업 수)를 20,000으로 유지하는 것이 좋습니다. 예를 들어 원본 컴퓨터의 디스크가 5개이고 각 디스크가 원본 컴퓨터에서 120 IOPS(8K 크기)를 생성할 경우 Azure 내에서 디스크당 IOPS 한도인 500을 초과하지 않습니다. (필요한 저장소 계정 수는 총 원본 컴퓨터 IOPS 수를 20,000으로 나눈 값입니다.)
**구성 서버** | 구성 서버는 보호된 컴퓨터에서 실행되는 모든 워크로드에 대한 일일 변경률 용량을 처리할 수 있어야 하며 데이터를 Azure storage로 지속적으로 복제할 만큼 충분한 대역폭을 보유해야 합니다.<br/><br/> 가장 좋은 방법은 보호하려는 컴퓨터와 동일한 네트워크 및 LAN 세그먼트에 구성 서버를 배치하는 것입니다. 다른 네트워크에 배치할 수도 있지만 보호하려는 컴퓨터에서 구성 서버의 L3 네트워크를 볼 수 있어야 합니다.<br/><br/> 구성 서버에 권장하는 크기는 다음 섹션의 테이블에 요약되어 있습니다.
**프로세스 서버** | 첫 번째 프로세스 서버는 기본적으로 구성 서버에 설치됩니다. 사용자 환경의 크기를 조정하는 추가 프로세스 서버를 배포할 수 있습니다. <br/><br/> 프로세스 서버는 보호된 컴퓨터에서 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화합니다. 그런 다음 Azure에 데이터를 보냅니다. 프로세스 서버 컴퓨터에는 이러한 작업을 수행할 충분한 리소스가 있어야 합니다.<br/><br/> 프로세스 서버는 디스크 기반 캐시를 사용합니다. 네트워크 병목 현상 또는 중단이 발생하는 경우 저장된 데이터 변경을 처리할 수 있도록 600GB 이상의 별도의 캐시 디스크를 사용하세요.

## <a name="size-recommendations-for-the-configuration-server"></a>구성 서버에 대한 크기 권장 사항

**CPU** | **메모리** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 100대 미만의 컴퓨터를 복제합니다.
12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz) | 18GB | 600GB | 500GB ~ 1TB | 100-150대 컴퓨터를 복제합니다.
16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz) | 32GB | 1TB | 1TB ~ 2TB | 150-200대 컴퓨터를 복제합니다.
다른 프로세스 서버 배포 | | | > 2TB | 200대 이상의 컴퓨터를 복제하는 경우 또는 일일 데이터 변경률이 2TB를 초과하는 경우 추가 프로세스 서버를 배포합니다.

위치:

* 각 원본 컴퓨터는 각각 100GB의 디스크 3개로 구성됩니다.
* 캐시 디스크 측정을 위해 벤치마킹 저장소로 RAID 10을 이용한 10K RPM의 8개 SAS 드라이브를 사용했습니다.

## <a name="size-recommendations-for-the-process-server"></a>프로세스 서버에 대한 크기 권장 사항

200대보다 많은 컴퓨터를 보호해야 하거나 일일 변경률이 2TB를 초과하는 경우 복제 로드를 처리할 프로세스 서버를 추가할 수 있습니다. 다음과 같이 규모를 확장할 수 있습니다.

* 구성 서버의 수를 늘립니다. 예를 들어 구성 서버를 두 대 사용할 경우 최대 400대의 컴퓨터를 보호할 수 있습니다.
* 프로세스 서버를 추가하고 추가한 프로세스 서버를 사용하여 구성 서버 대신(또는 추가로) 트래픽을 처리할 수 있습니다.

다음 테이블에서는 다음과 같은 시나리오를 설명합니다.

* 구성 서버를 프로세스 서버로 사용할 계획이 없습니다.
* 추가 프로세스 서버를 설정했습니다.
* 추가 프로세스 서버를 사용하도록 보호된 가상 머신을 구성했습니다.
* 보호된 원본 컴퓨터는 각각 100GB의 디스크 3개로 구성됩니다.

**구성 서버** | **추가 프로세스 서버** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 16GB 메모리 | 4개 vCPU(2개 소켓 * 2코어 @ 2.5GHz), 8GB 메모리 | 300GB | 250GB 이하 | 85개 이하의 컴퓨터를 복제합니다.
8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 16GB 메모리 | 8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 12GB 메모리 | 600GB | 250GB ~ 1TB | 85-150대 컴퓨터를 복제합니다.
12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz), 18GB 메모리 | 12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz), 24GB 메모리 | 1TB | 1TB ~ 2TB | 150-225대 컴퓨터를 복제합니다.

서버 크기를 조정하는 방법은 모델을 강화할지, 규모를 확장할지에 대한 선호도에 따라 달라집니다.  몇 가지 고급 구성 및 프로세스 서버를 배포하여 강화하거나 적은 리소스로 더 많은 서버를 배포하여 규모를 확장합니다. 예를 들어 220대 컴퓨터를 보호해야 하는 경우 다음 중 하나를 수행할 수 있습니다.

* vCPU 12개, 메모리 18GB인 구성 서버와 vCPU 12개, 메모리 24GB인 추가 프로세스 서버를 설치합니다. 추가 프로세스 서버만 사용하도록 보호된 컴퓨터를 구성합니다.
* 구성 서버 두 개(2 x 8 vCPU, 16GB RAM)와 추가 프로세스 서버 두 개(135 + 85 [220]개 컴퓨터를 처리할 1 x 8 vCPU 및 4 vCPU x 1)를 설치합니다. 추가 프로세스 서버만 사용하도록 보호된 컴퓨터를 구성합니다.


## <a name="control-network-bandwidth"></a>네트워크 대역폭 제어

[Deployment Planner 도구](site-recovery-deployment-planner.md)를 사용하여 복제(초기 복제 및 다음 델타)에 필요한 대역폭을 계산한 후 다음 몇 가지 옵션을 사용한 복제에 사용되는 대역폭의 양을 제어할 수 있습니다.

* **대역폭 제한**: Azure에 복제하는 VMware 트래픽이 특정 프로세스 서버를 통과합니다. 프로세스 서버로 실행되는 컴퓨터에서 대역폭을 제한할 수 있습니다.
* **대역폭 영향**: 몇 가지 레지스트리 키를 사용하면 복제에 사용되는 대역폭에 영향을 줄 수 있습니다.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** 레지스트리 값은 디스크의 데이터 전송(초기 또는 델타 복제)에 사용되는 스레드 수를 지정합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM**은 장애 복구하는 동안 데이터 전송에 사용되는 스레드 수를 지정합니다.

### <a name="throttle-bandwidth"></a>대역폭 제한

1. 프로세스 서버 역할을 하는 컴퓨터에서 Azure Backup MMC 스냅인을 엽니다. 기본적으로 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 폴더에 Backup의 바로 가기가 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.

    ![속성을 변경하는 Azure Backup MMC 스냅인의 스크린샷](./media/site-recovery-vmware-to-azure/throttle1.png)
3. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 초당 512Kbps~1,023Mbps입니다.

    ![Azure Backup 속성 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet를 사용하여 제한을 설정할 수도 있습니다. 다음은 샘플입니다.

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 은 제한이 필요 없다는 뜻입니다.

### <a name="influence-network-bandwidth-for-a-vm"></a>VM의 네트워크 대역폭에 영향

1. VM의 레지스트리에서 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**으로 이동합니다.
   * 복제 디스크의 대역폭 트래픽에 영향을 주려면 **UploadThreadsPerVM** 값을 수정하거나 키가 없는 경우 키를 새로 만듭니다.
   * Azure에서 장애 복구(failback) 트래픽에 대한 대역폭에 영향을 주려면 **DownloadThreadsPerVM** 값을 수정합니다.
2. 기본값은 4입니다. "과도하게 프로비전된" 네트워크에서는 이러한 레지스트리 키를 기본값에서 변경해야 합니다. 최대값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.


## <a name="deploy-additional-process-servers"></a>추가 프로세스 서버 배포

배포 규모를 200대 이상으로 규모 확장해야 하거나 총 이탈률이 2TB를 초과하는 경우 트래픽 볼륨을 처리할 추가 프로세스가 필요합니다. 다음 지침에 따라 프로세스 서버를 설정합니다. 서버를 설정한 후 이를 사용하도록 원본 컴퓨터를 마이그레이션할 수 있습니다.

1. **Site Recovery 서버**에서 구성 서버를 클릭한 다음 **프로세스 서버**를 클릭합니다.

    ![프로세스 서버를 추가하는 Site Recovery 서버 옵션의 스크린샷](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. **서버 형식**에서 **프로세스 서버(온-프레미스)**를 클릭합니다.

    ![프로세스 서버 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Site Recovery 통합 설치 프로그램 파일을 다운로드하고 실행하여 프로세스 서버를 설치합니다. 이렇게 하면 자격 증명 모음에도 등록됩니다.
4. **시작하기 전에**에서 **배포 규모 확장을 위해 추가 프로세스 서버 추가**를 선택합니다.
5. 구성 서버를 [설정](#step-2-set-up-the-source-environment) 했을 때 수행한 것과 동일한 방식으로 마법사를 완료합니다.

    ![Azure Site Recovery 통합 설치 마법사의 스크린샷](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. **구성 서버 세부 정보**에서 구성 서버의 IP 주소 및 암호를 지정합니다. 암호를 가져오려면 구성 서버에서 **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe –n**을 실행합니다.

    ![구성 서버 세부 정보 페이지의 스크린샷](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>새 프로세스 서버를 사용하도록 컴퓨터 마이그레이션
1. **설정** > **Site Recovery 서버**에서 구성 서버를 클릭한 다음 **프로세스 서버**를 확장합니다.

    ![프로세스 서버 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 현재 사용 중인 프로세스 서버를 마우스 오른쪽 버튼으로 클릭하고 **스위치**를 클릭합니다.

    ![구성 서버 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. **대상 프로세스 서버 선택**에서 사용할 새 프로세스 서버를 선택한 다음 서버가 처리할 가상 머신을 선택합니다. 서버에 대한 정보를 얻으려면 정보 아이콘을 클릭합니다. 부하 결정을 할 때 도움이 되도록 선택된 각 가상 머신을 새 프로세스 서버로 복제하는 데 필요한 평균 공간이 표시됩니다. 새 프로세스 서버로 복제를 시작하려면 확인 표시를 클릭합니다.


## <a name="next-steps"></a>다음 단계

[Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) 다운로드 및 실행
