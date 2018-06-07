Azure 가상 컴퓨터(VM)는 재부팅 작업을 시작하는 명확한 이유 없이 재부팅할 수 있습니다. 이 문서에서는 VM을 재부팅할 수 있는 작업 및 이벤트를 나열하고 예기치 않은 재부팅 문제를 방지하거나 그러한 문제의 영향을 줄이는 방법에 대한 정보를 제공합니다.

## <a name="configure-the-vms-for-high-availability"></a>고가용성을 위한 VM 구성
Azure에서 실행되는 응용 프로그램이 VM 재부팅 및 가동 중지 시간으로부터 영향을 받지 않도록 하는 가장 좋은 방법은 높은 사용 가능성을 위해 VM을 구성하는 것입니다.

응용 프로그램에 이러한 수준의 중복성을 제공하기 위해 여러 개의 VM을 가용성 집합으로 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 VM을 사용할 수 있고 99.95% [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)가 충족되도록 합니다.

사용 가능성 집합에 대한 자세한 내용은 다음 문서를 참조하세요.

- [VM의 가용성 관리](../articles/virtual-machines/windows/manage-availability.md)
- [VM의 가용성 구성](../articles/virtual-machines/windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Resource Health 정보 
Azure Resource Health는 개별 Azure 리소스의 상태를 노출하고 문제 해결을 위한 실행 가능한 지침을 제공하는 서비스입니다. 서버 또는 인프라 요소에 직접 액세스할 수는 없는 클라우드 환경에서는 문제 해결에 소비하는 시간을 줄이는 것이 Resource Health의 목표입니다. 특히 문제의 원인이 응용 프로그램에 있는지 Azure 플랫폼 내 이벤트에 있는지 판단하는 데 소비하는 시간을 줄이는 것이 목표입니다. 자세한 내용은 [Resource Health 이해 및 사용](../articles/resource-health/resource-health-overview.md)을 참조하세요.

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>VM을 재부팅할 수 있는 작업 및 이벤트

### <a name="planned-maintenance"></a>계획된 유지 보수
Microsoft Azure는 VM의 기반이 되는 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 전 세계적으로 주기적인 업데이트를 수행합니다. 메모리 보존 업데이트를 비롯한 이러한 많은 업데이트는 VM 또는 클라우드 서비스에 영향을 주지 않고 수행됩니다.

그러나 일부 업데이트는 재부팅이 필요하지 않습니다. 이러한 경우 인프라 패치를 적용하는 동안 VM은 종료되었다가 다시 시작됩니다.

Azure 계획된 유지 관리의 정의와 계획된 유지 관리가 Linux 가상 컴퓨터의 사용 가능성에 주는 영향에 대해 이해하려면 여기에 나열된 문서를 참조하세요. 이 문서는 Azure 계획된 유지 관리 프로세스 및 영향을 줄이기 위해 계획된 유지 관리를 예약하는 방법에 대한 배경을 제공합니다.

- [Azure에서 VM에 대한 계획된 유지 관리](../articles/virtual-machines/windows/planned-maintenance.md)
- [Azure VM에 계획된 유지 관리 예약 방법](../articles/virtual-machines/windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>메모리 유지 업데이트   
Microsoft Azure의 이 업데이트 클래스는 사용자가 실행 중인 VM에는 아무런 영향도 주지 않습니다. 이러한 많은 업데이트는 실행 중인 인스턴스를 방해하지 않고 업데이트할 수 있는 구성 요소 또는 서비스에 대한 업데이트입니다. 일부는 VM을 재부팅하지 않고 적용할 수 있는 호스트 운영 체제의 플랫폼 인프라 업데이트입니다.

이러한 메모리 보존 업데이트는 바로 실시간 마이그레이션을 가능하게 하는 기술을 통해 수행됩니다. 업데이트가 진행되는 동안 VM은 *일시 중지* 상태에 있게 됩니다. 이 상태는 기본 호스트 운영 체제가 필요한 업데이트 및 패치를 받는 동안 RAM의 메모리를 유지합니다. VM은 일시 중지 후 30초 내에 다시 시작됩니다. VM이 다시 시작된 후 시계는 자동으로 동기화됩니다.

일시 중지 기간이 짧기 때문에 이 메커니즘을 통한 업데이트 배포는 VM에 미치는 영향이 크게 줄어듭니다. 그러나 모든 업데이트를 이러한 방식으로 배포할 수는 없습니다. 

다중 인스턴스 업데이트(가용성 집합의 VM인 경우)는 한 번에 하나의 업데이트 도메인에 적용됩니다.

> [!NOTE]
> 이전 커널 버전이 설치된 Linux 컴퓨터는 이 업데이트 메서드 중에 커널 패닉의 영향을 받습니다. 이 문제를 방지하려면 커널 버전을 3.10.0-327.10.1 이상으로 업데이트합니다. 자세한 내용은 [호스트 노드 업그레이드 후에 3.10 기반 커널 패닉인 Azure Linux VM](https://support.microsoft.com/help/3212236)을 참조하세요.     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>사용자 시작 재부팅 또는 종료 작업
 
Azure Portal, Azure PowerShell, 명령줄 인터페이스 또는 API 다시 설정을 수행한 경우 [Azure 활동 로그](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에서 이벤트를 확인할 수 있습니다.

VM의 운영 체제에서 작업을 수행하는 경우 시스템 로그에서 이벤트를 찾을 수 있습니다.

일반적으로 VM을 재부팅하는 다른 시나리오에는 여러 구성 변경 작업이 있습니다. 보통 특정 작업을 실행하면 VM의 재부팅이 발생할 수 있음을 나타내는 경고 메시지가 표시됩니다. VM 크기 조정 작업, 관리 계정의 암호 변경, 고정 IP 주소 설정 등을 예로 들 수 있습니다.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center 및 Windows 업데이트
Azure Security Center는 누락된 운영 체제 업데이트가 있는지 매일 Windows 및 Linux VM을 모니터링합니다. 보안 센터는 Windows VM에서 구성된 서비스에 따라 Windows 업데이트 또는 WSUS(Windows Server Update Services)에서 사용 가능한 보안 및 중요 업데이트의 목록을 검색합니다. Security Center는 또한 Linux 시스템에서 최신 업데이트를 확인합니다. VM이 시스템 업데이트를 누락하는 경우 Security Center는 시스템 업데이트 적용을 권장합니다. 이러한 시스템 업데이트의 응용 프로그램은 Azure Portal에서 Security Center를 통해 제어됩니다. 일부 업데이트를 적용한 후 VM을 재부팅해야 할 수 있습니다. 자세한 내용은 [Azure Security Center의 시스템 업데이트 적용](../articles/security-center/security-center-apply-system-updates.md)을 참조하세요.

온-프레미스 서버와 같이 이러한 컴퓨터는 사용자가 관리하도록 되어 있으므로, Azure는 Windows 업데이트에서 Windows Azure VM으로 업데이트를 푸시하지 않습니다. 그러나 고객은 자동 Windows 업데이트 설정을 활성화 상태로 유지하는 것이 좋습니다. Windows 업데이트에서 업데이트를 자동으로 설치하면 업데이트가 적용된 후에 재부팅될 수도 있습니다. 자세한 내용은 [Windows 업데이트 FAQ](https://support.microsoft.com/help/12373/windows-update-faq)를 참조하세요.

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>VM의 가용성에 영향을 주는 다른 경우
Azure에서 VM의 사용을 적극적으로 일시 중단한 다른 경우가 있습니다. 이 작업을 수행하기 전에 전자 메일 알림을 받으므로 기본 문제를 해결할 수 있습니다. VM 사용 가능성에 영향을 주는 문제의 예로는 보안 위반 및 결제 방법의 만료가 있습니다.

### <a name="host-server-faults"></a>호스트 서버 오류 
VM은 Azure 데이터 센터 내에서 실행되는 실제 서버에서 호스트됩니다. 물리적 서버는 다른 Azure 구성 요소 외에도 호스트 에이전트를 호출하는 에이전트를 실행합니다. 실제 서버에서 이러한 Azure 소프트웨어 구성 요소가 응답하지 않는 경우 모니터링 시스템은 호스트 서버를 재부팅하도록 트리거하여 복구를 시도합니다. VM은 일반적으로 5분 내에 다시 사용할 수 있게 되고 이전과 동일한 호스트에서 실시간으로 계속됩니다.

서버 오류는 일반적으로 하드 디스크 또는 반도체 드라이브의 오류와 같은 하드웨어 오류로 인해 발생합니다. Azure는 이러한 상황을 지속적으로 모니터링하고 기본 버그를 식별하며 완화가 구현되고 테스트된 후에 공개합니다.

일부 호스트 서버 오류가 해당 서버에만 발생하기 때문에 VM을 다른 호스트 서버에 수동으로 다시 배포하여 반복되는 VM 재부팅 상황을 개선할 수 있습니다. VM의 세부 정보 페이지에서 **다시 배포** 옵션을 사용하거나 Azure Portal에서 VM을 중지하고 다시 시작하여 이 작업을 트리거할 수 있습니다.

### <a name="auto-recovery"></a>자동 복구
어떤 이유로든 호스트 서버를 재부팅할 수 없는 경우 Azure 플랫폼은 자동 복구 작업을 시작하여 추가로 조사하기 위해 오류가 발생한 호스트 서버를 순환 순서에서 제거합니다. 

해당 호스트의 모든 VM은 다른 정상 호스트 서버에 자동으로 다시 배치됩니다. 이 프로세스는 15분 이내에 일반적으로 완료됩니다. 자동 복구 프로세스에 대해 자세히 알아보려면 [VM의 자동 복구](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines)를 참조하세요.

### <a name="unplanned-maintenance"></a>계획되지 않은 유지 관리
가끔 Azure 운영 팀은 Azure 플랫폼의 전반적인 상태를 확인하기 위한 유지 관리 활동을 수행해야 할 수 있습니다. 이 동작은 VM 사용 가능성에 영향을 줄 수 있고 일반적으로 앞에서 설명한 대로 동일한 자동 복구 작업이 발생합니다.  

계획되지 않은 유지 관리는 다음을 포함합니다.

- 긴급 노드 조각 모음
- 긴급 네트워크 스위치 업데이트

### <a name="vm-crashes"></a>VM 충돌
VM 자체 내의 문제로 인해 VM이 다시 시작할 수 있습니다. VM에서 실행되는 작업 부하 또는 역할은 게스트 운영 체제 내에서 버그 검사를 트리거할 수 있습니다. 충돌에 대한 이유를 확인하는 도움말은 시스템 및 Windows VM에 대한 응용 프로그램 로그와 Linux VM에 대한 직렬 로그에서 확인합니다.

### <a name="storage-related-forced-shutdowns"></a>Storage 관련 강제 종료
Azure의 VM은 Azure Storage 인프라에서 호스팅되는 운영 체제 및 데이터 저장소에 대한 가상 디스크를 사용합니다. VM 및 연결된 가상 디스크 간의 사용 가능성 또는 연결이 120초 이상 영향을 받을 때마다 Azure 플랫폼은 데이터 손상을 방지하기 위해 VM을 강제 종료합니다. 저장소 연결이 복원된 후에 VM의 전원은 자동으로 다시 켜집니다. 

강제 종료 기간은 5분 정도로 짧지만 훨씬 길어질 수 있습니다. 다음은 저장소 관련 강제 종료와 연결된 특정 사례 중 하나입니다. 

**IO 제한 초과**

초당 I/O 작업(IOPS)의 볼륨이 디스크의 I/O 제한을 초과하기 때문에 I/O 요청이 지속적으로 제한되는 경우 VM은 일시적으로 종료될 수 있습니다. (표준 디스크 저장소는 500IOPS로 제한됩니다.) 이 문제를 완화하려면 워크로드에 따라 디스크 스트라이프를 사용하거나 게스트 VM 내에서 저장소 공간을 구성합니다. 자세한 내용은 [Storage 성능이 최적화되도록 Azure VM 구성](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)을 참조하세요.

최대 80,000IOPS의 Azure Premium Storage를 통해 더 높은 IOPS 제한을 사용할 수 있습니다. 자세한 내용은 [고성능 Premium Storage](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.

### <a name="other-incidents"></a>다른 인시던트
드문 경우이지만, 확산된 문제가 Azure 데이터 센터의 여러 서버에 영향을 줄 수 있습니다. 이 문제가 발생한 경우 Azure 팀은 영향을 받는 구독에 전자 메일 알림을 보냅니다. 현재 지속적인 중단 및 과거 인시던트 상태가 있는지 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/) 및 Azure Portal을 통해 확인할 수 있습니다.
