---
title: "Azure Site Recovery로 장애 조치(failover) 후 보조 온-프레미스 사이트에 연결하도록 IP 주소 지정 설정 | Microsoft Docs"
description: "Azure Site Recovery로 장애 조치(failover) 후 보조 온-프레미스 사이트의 VM에 연결하도록 IP 주소 지정을 설정하는 방법을 설명합니다."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>장애 조치(failover) 후 보조 온-프레미스 사이트에 연결하도록 IP 주소 지정 설정

System Center VMM(Virtual Machine Manager) 클라우드의 Hyper-V VM을 보조 사이트로 장애 조치(failover)한 후 복제본 VM에 연결할 수 있어야 합니다. 이 문서는 이 작업을 수행하는 데 도움이 됩니다. 

## <a name="connection-options"></a>연결 옵션

장애 조치(failover) 후 복제본 VM의 IP 주소 지정을 처리하는 몇 가지 방법이 있습니다. 

- **장애 조치(failover) 후 동일한 IP 주소 유지**: 이 시나리오에서는 복제된 VM이 기본 VM과 동일한 IP 주소를 갖습니다. 이렇게 하면 장애 조치(failover) 후 네트워크 관련 문제가 간소화되지만 일부 인프라 작업이 필요합니다.
- **장애 조치(failover) 후 다른 IP 주소 사용**: 이 시나리오에서는 장애 조치(failover) 후 VM이 새로운 IP 주소를 얻습니다. 
 

## <a name="retain-the-ip-address"></a>IP 주소 유지

보조 사이트로 장애 조치(failover) 후에 기본 사이트의 IP 주소를 유지하려는 경우 다음을 수행할 수 있습니다.

- 기본 및 보조 사이트 사이에 확대 서브넷을 배포합니다.
- 기본 사이트에서 보조 사이트로 전체 서브넷 장애 조치(failover)를 수행합니다. IP 주소의 새 위치를 표시하도록 경로를 업데이트해야 합니다.


### <a name="deploy-a-stretched-subnet"></a>확대 서브넷 배포

확대 구성에서는 기본 사이트와 보조 사이트 둘 다에서 서브넷을 동시에 사용할 수 있습니다. 확대 서브넷에서 컴퓨터와 해당 IP(계층 3) 주소 구성을 보조 사이트로 이동하면 네트워크에서 트래픽을 새 위치로 자동으로 라우팅합니다. 

- 계층 2(데이터 링크 계층) 측면을 고려하면 확대 VLAN을 관리할 수 있는 네트워킹 장비가 필요합니다.
- VLAN을 늘이면 잠재적 장애 도메인이 두 사이트로 모두 확장됩니다. 이는 단일 장애 지점이 됩니다. 가능성은 적지만, 이러한 시나리오에서는 브로드캐스트 스톰과 같은 인시던트를 격리하지 못할 수 있습니다. 


### <a name="fail-over-a-subnet"></a>서브넷 장애 조치(failover)

전체 서브넷을 장애 조치(failover)하면 서브넷을 실제로 늘이지 않고도 확대 서브넷의 혜택을 얻을 수 있습니다. 이 솔루션에서는 원본 또는 대상 사이트 중 하나에서 서브넷을 사용할 수 있지만 두 사이트에서 동시에 사용할 수는 없습니다.

- 장애 조치(failover) 시 IP 주소 공간을 유지 관리하기 위해 라우터 인프라에 대해 프로그래밍 방식으로 정렬하여 한 사이트에서 다른 사이트로 서브넷을 이동할 수 있습니다.
- 장애 조치(failover)가 발생하면 서브넷이 연결된 VM과 함께 이동됩니다.
- 이 방법의 주요 단점은 오류 발생 시 전체 서브넷을 이동해야 한다는 것입니다.

#### <a name="example"></a>예

전체 서브넷 장애 조치(failover)의 예는 다음과 같습니다. 

- 장애 조치(failover) 전에는 기본 사이트에 서브넷 192.168.1.0/24에서 실행되는 응용 프로그램이 있습니다.
- 장애 조치(failover) 중 이 서브넷의 모든 VM이 보조 사이트로 장애 조치(failover)되며 IP 주소는 유지됩니다. 
- 서브넷 192.168.1.0/24에 있는 모든 VM이 이제 보조 사이트로 이동된 사실을 반영하여 모든 사이트 간의 경로를 수정해야 합니다.

다음 그림에는 장애 조치(failover) 전과 후의 서브넷이 나와 있습니다.


**장애 조치(failover) 전**

![장애 조치(failover) 전](./media/hyper-v-vmm-networking/network-design2.png)

**장애 조치(failover) 후**

![장애 조치(failover) 후](./media/hyper-v-vmm-networking/network-design3.png)

장애 조치(failover) 후 Site Recovery는 VM의 각 네트워크 인터페이스에 대해 IP 주소를 할당합니다. 주소는 각 VM 인스턴스에 대해 관련 네트워크의 고정 IP 주소 풀에서 할당됩니다.

- 보조 사이트의 IP 주소 풀이 원본 사이트의 풀과 같은 경우 Site Recovery는 복제본 VM에 원본 VM의 동일 IP 주소를 할당합니다. IP 주소는 VMM에서 예약되지만 Hyper-V 호스트에서 장애 조치(failover) IP 주소로 설정되지는 않습니다. Hyper-V 호스트의 장애 조치(failover) IP 주소는 장애 조치(failover) 직전에 설정됩니다.
- 동일한 IP 주소를 사용할 수 없는 경우 Site Recovery가 풀에서 사용 가능한 다른 IP 주소를 할당합니다.
- VM이 DHCP를 사용하는 경우 Site Recovery는 IP 주소를 관리하지 않습니다. 보조 사이트의 DHCP 서버가 원본 사이트와 동일한 범위에서 주소를 할당할 수 있는지 확인해야 합니다.

### <a name="validate-the-ip-address"></a>IP 주소 유효성 검사

VM을 보호하도록 설정한 후에는 다음 샘플 스크립트를 사용하여 VM에 할당된 주소를 확인할 수 있습니다. 이 IP 주소는 장애 조치(failover) IP 주소로 설정되고 장애 조치(failover) 시 VM에 할당됩니다.

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>다른 IP 주소 사용

이 시나리오에서는 장애 조치(failover)되는 VM의 IP 주소가 변경됩니다. 이 솔루션의 단점은 유지 관리가 필요하다는 것입니다.  DNS 및 캐시 항목을 업데이트해야 할 수 있습니다. 이로 인해 가동 중지 시간이 발생할 수 있으며 다음과 같이 완화할 수 있습니다.

- 인트라넷 응용 프로그램의 경우 낮은 TTL 값을 사용합니다.
- DNS 서버가 제때 업데이트되도록 Site Recovery 복구 계획에서 다음 스크립트를 사용합니다. 동적 DNS 등록을 사용하는 경우에는 이 스크립트를 사용할 필요가 없습니다.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>예 

이 예제에서는 기본 사이트와 보조 사이트의 IP 주소가 다릅니다. 그리고 기본 사이트 또는 복구 사이트에서 호스트되는 응용 프로그램이 액세스할 수 있는 세 번째 사이트도 있습니다.

- 장애 조치(failover) 전에는 앱이 기본 사이트의 서브넷 192.168.1.0/24에서 호스트됩니다.
- 장애 조치(failover) 후에는 앱이 보조 사이트의 서브넷 172.16.1.0/24에 구성됩니다.
- 세 사이트 모두 서로를 액세스할 수 있습니다.
- 장애 조치(failover) 후에는 앱이 복구 서브넷에서 복구됩니다.
- 이 시나리오에서는 전체 서브넷을 장애 조치(failover)할 필요가 없으며 VPN 또는 네트워크 경로를 다시 구성하기 위해 변경해야 하는 사항이 없습니다. 장애 조치(failover) 및 일부 DNS 업데이트를 수행하면 응용 프로그램이 액세스 가능한 상태로 유지됩니다.
- DNS가 동적 업데이트를 허용하도록 구성된 경우 장애 조치(failover) 후 시작되는 VM은 새 IP 주소를 사용하여 자체 등록됩니다.

**장애 조치(failover) 전**

![다른 IP 주소 - 장애 조치(failover) 전](./media/hyper-v-vmm-networking/network-design10.png)

**장애 조치(failover) 후**

![다른 IP 주소 - 장애 조치(failover) 후](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>다음 단계

[장애 조치 실행](hyper-v-vmm-failover-failback.md)

