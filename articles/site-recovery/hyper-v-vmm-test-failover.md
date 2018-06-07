---
title: Azure Site Recovery를 사용하여 보조 사이트로 Hyper-V VM의 DR 훈련 실행 | Microsoft Docs
description: Azure Site Recovery를 사용하여 VMM 클라우드에서 Hyper-V VM에 대한 DR 훈련을 보조 데이터 센터로 실행하는 방법을 알아봅니다.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: c389776f62db5fd04f67ef22822e21fd4aee368f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>보조 사이트로 Hyper-V VM에 대한 DR 훈련 실행


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 Hyper-V VM에 대한 DR(재해 복구) 훈련을 보조 온-프레미스 사이트에 실행하는 방법을 설명합니다.

데이터 손실 또는 가동 중지 없이 복제 전략의 유효성을 검사하고 DR 훈련을 수행하기 위해 테스트 장애 조치(failover)를 실행합니다. 테스트 장애 조치(failover)는 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않습니다. 

## <a name="how-do-test-failovers-work"></a>장애 조치(failover) 작업은 어떻게 수행되나요?

기본 사이트에서 보조 사이트로 테스트 장애 조치(failover)를 실행합니다. 간단히 VM 장애 조치(failover)를 확인하려는 경우 보조 사이트에 아무것도 설정하지 않고 테스트 장애 조치(failover)를 실행할 수 있습니다. 앱 장애 조치(failover)가 예상대로 작동하는지 확인하려는 경우 보조 위치에서 네트워킹 및 인프라를 설정해야 합니다.
- 단일 VM 또는 [복구 계획](site-recovery-create-recovery-plans.md)에서 테스트 장애 조치(failover)를 실행할 수 있습니다.
- 네트워크 없이, 기존 네트워크를 사용하거나 자동으로 생성된 네트워크를 사용하여 테스트 장애 조치(failover)를 실행할 수 있습니다. 이러한 옵션에 대한 자세한 내용은 아래 표에 제공됩니다.
    - 네트워크 없이 테스트 장애 조치(failover)를 실행할 수 있습니다. 이 옵션은 단순히 VM에서 장애 조치(failover)할 수 있었는지를 확인하려는 경우에 유용하지만 모든 네트워크 구성을 확인할 수 없습니다.
    - 기존 네트워크를 사용하여 장애 조치(failover)를 실행합니다. 프로덕션 네트워크를 사용하지 않는 것이 좋습니다.
    - 장애 조치(failover)를 실행하고 Site Recovery에서 자동으로 테스트 네트워크를 만들도록 합니다. 이 경우 Site Recovery는 네트워크를 자동으로 만들고 테스트 장애 조치(failover)가 완료되면 정리합니다.
- 테스트 장애 조치(failover)에 대한 복구 지점을 선택해야 합니다. 
    - **가장 최근에 처리됨**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치(failover)합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.
    - **최신 앱 일치**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 응용 프로그램 일치 복구 지점으로 장애 조치(failover)합니다. 
    - **최신**: 먼저 Site Recovery 서비스로 보낸 모든 데이터를 처리한 다음 각 VM에 대한 복구 지점을 만든 후에 해당 복구 지점에 장애 조치합니다. 이 옵션은 장애 조치(failover) 후에 생성된 VM은 장애 조치(failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
    - **최신 다중 VM 처리**: 다중 VM 일치가 사용하도록 설정된 하나 이상의 VM을 포함하는 복구 계획에 사용할 수 있습니다. 이 설정이 사용하도록 설정된 VM은 최신 일반 다중 VM 일치 복구 지점으로 장애 조치(Failover)됩니다. 다른 VM은 처리된 최신 복구 지점으로 장애 조치(Failover)됩니다.
    - **최신 다중 VM 앱 일치**: 이 옵션은 다중 VM 일치가 사용하도록 설정된 하나 이상의 VM이 포함된 복구 계획에 사용할 수 있습니다. 복제 그룹에 속하는 VM은 공통된 최신 다중 VM 응용 프로그램 일관성 복구 지점으로 장애 조치(failover)됩니다. 다른 VM은 최신 응용 프로그램 일치 복구 지점으로 장애 조치(Failover)됩니다.
    - **사용자 지정**: 특정 VM을 특정 복구 지점으로 장애 조치(failover)하려면 이 옵션을 사용합니다.



## <a name="prepare-networking"></a>네트워킹 준비

테스트 장애 조치를 실행하면 표에 요약된 것처럼 테스트 복제 컴퓨터에 대한 네트워크 설정을 선택하라는 메시지가 나타납니다.

**옵션** | **세부 정보** 
--- | --- 
**없음** | 복제본 VM이 있는 호스트에 테스트 VM이 만들어집니다. 클라우드에 추가되지 않고 네트워크에 연결되지 않습니다.<br/><br/> 생성된 후에 컴퓨터를 VM 네트워크에 연결할 수 있습니다.
**기존 항목 사용** | 복제본 VM이 있는 호스트에 테스트 VM이 만들어집니다. 클라우드에 추가되지 않습니다.<br/><br/>프로덕션 네트워크에서 격리된 VM 네트워크를 만듭니다.<br/><br/>권장되는 VLAN 기반 네트워크를 사용하는 경우 이 목적을 위해 VMM에서 별도의 논리 네트워크(프로덕션에 사용되지 않음)를 만드는 것이 좋습니다. 이 논리 네트워크는 테스트 장애 조치(failover)에 대한 VM 네트워크를 만드는 데 사용됩니다.<br/><br/>논리 네트워크는 가상 머신을 호스트하는 모든 Hyper-V 서버에 있는 하나 이상의 네트워크 어댑터에 연결해야 합니다.<br/><br/>VLAN 논리 네트워크의 경우 논리 네트워크에 추가한 네트워크 사이트는 격리되어야 합니다.<br/><br/>Windows 네트워크 가상화 기반의 논리 네트워크를 사용한다면 Azure Site Recovery가 격리된 VM 네트워크를 자동으로 생성합니다. 
**네트워크 만들기** | 임시 테스트 네트워크가 **논리 네트워크**에서 지정한 설정 및 관련 네트워크 사이트를 기반으로 자동 생성됩니다.<br/><br/> 장애 조치(failover)에서 VM이 생성되었는지 확인합니다. |복구 계획에서 VM 네트워크를 두 개 이상 사용할 경우 이 옵션을 사용해야 합니다.<br/><br/> Windows 네트워크 가상화 네트워크를 사용하는 경우 이 옵션을 사용하여 복제 가상 머신의 네트워크에서 동일한 설정(서브넷 및 IP 주소 풀)으로 VM 네트워크를 자동으로 만들 수 있습니다. 테스트 장애 조치 완료 후 이러한 VM 네트워크는 자동으로 정리됩니다.<br/><br/> 복제본 가상 머신이 있는 호스트에 테스트 VM이 만들어집니다. 클라우드에 추가되지 않습니다.

### <a name="best-practices"></a>모범 사례

- 프로덕션 네트워크를 테스트하면 프로덕션 작업에서 가동 중지 시간이 발생합니다. 따라서 재해 복구 드릴을 진행 중일 때는 사용자들이 관련 앱을 사용하지 않도록 요청하세요.

- 테스트 네트워크는 테스트 장애 조치(failover)에 사용되는 VMM 논리 네트워크 유형과 일치할 필요가 없습니다. 하지만 일부 조합은 작동하지 않습니다.

     - 복제본에서 DHCP 및 VLAN 기반 격리를 사용하는 경우 복제본에 대한 VM 네트워크는 고정 IP 주소 풀이 필요하지 않습니다. 따라서 사용 가능한 주소 풀이 없으므로 테스트 장애 조치에 대한 Windows 네트워크 가상화의 사용이 작동하지 않습니다. 
        
     - 복제본 네트워크가 격리 없음을 사용하고 테스트 네트워크가 Windows 네트워크 가상화를 사용하는 경우 테스트 장애 조치(Failover)가 작동하지 않습니다. 이것은 격리 없는 네트워크에 Windows 네트워크 가상화 네트워크를 만드는 데 필요한 서브넷이 없기 때문입니다.
        
- 네트워크 매핑에 선택한 네트워크를 테스트 장애 조치(failover)용으로 사용하지 않는 것이 좋습니다.

- 장애 조치 후에 복제본 가상 머신이 매핑된 VM 네트워크에 연결된 방식은 VM네트워크가 VMM 콘솔에서 구성된 방식에 따라 다릅니다.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>격리 없음 또는 VLAN 격리로 구성된 VM 네트워크

VM 네트워크가 격리 없음 또는 VLAN 격리를 사용하여 VMM에서 구성된 경우 다음을 참조하세요.

- DHCP가 VM 네트워크에 대해 정의된 경우 복제본 가상 머신이 관련된 논리 네트워크 내 네트워크 사이트에 대해 지정된 설정을 통해 VLAN ID에 연결됩니다. 가상 머신은 사용 가능한 DHCP 서버로부터 IP 주소를 수신합니다.
- 대상 VM 네트워크에 대해 고정 IP 주소 풀을 정의할 필요가 없습니다. VM 네트워크에 대해 고정 IP 주소 풀이 사용되는 경우 복제본 가상 머신이 관련된 논리 네트워크 내 네트워크 사이트에 대해 지정된 설정을 통해 VLAN ID에 연결됩니다.
- 가상 머신은 VM 네트워크에 대해 지정된 풀로부터 IP 주소를 수신합니다. 고정 IP 주소 풀이 대상 VM 네트워크에 정의되어 있다면 IP 주소 할당이 실패합니다. 보호 및 복구에 사용하려는 원본과 대상 VMM 서버 모두에 IP 주소 풀을 만듭니다.

### <a name="vm-network-with-windows-network-virtualization"></a>Windows 네트워크 가상화를 사용하여 VM 네트워크

VM 네트워크가 Windows 네트워크 가상화를 사용하여 VMM에서 구성된 경우 다음을 참조하세요.

- 원본 VM 네트워크가 DHCP 또는 고정 IP 주소 풀을 사용하도록 구성되었는지 여부에 상관 없이 대상 VM 네트워크에 대해 고정 풀을 정의해야 합니다. 
- DHCP를 정의하는 경우 대상 VMM 서버가 DHCP 서버 역할을 하며, 대상 VM 네트워크에 대해 정의된 풀로부터 IP 주소를 제공합니다.
- 고정 IP 주소 풀의 사용이 원본 서버에 대해 정의 되어 있다면 대상 VMM 서버가 풀로부터 IP 주소를 할당합니다. 두 경우 모두, 고정 IP 주소 풀이 정의되어 있지 않으면 IP 주소 할당이 실패합니다.



## <a name="prepare-the-infrastructure"></a>인프라 준비

간단히 VM이 장애 조치(failover)할 수 있는지 확인하려는 경우 인프라 없이 테스트 장애 조치(failover)를 실행할 수 있습니다. 테스트 앱 장애 조치(failover)에 전체 DR 훈련을 수행하려는 경우 보조 사이트에서 인프라를 준비해야 합니다.

- 기존 네트워크를 사용하여 테스트 장애 조치를 실행하는 경우 해당 네트워크에서 Active Directory, DHCP 및 DNS를 준비합니다.
- VM 네트워크를 자동으로 생성하는 옵션으로 테스트 장애 조치(failover)를 실행하는 경우 테스트 장애 조치(failover)를 실행하기 전에 자동으로 만들어진 네트워크에 인프라 리소스를 추가해야 합니다. 복구 계획에서 테스트 장애 조치(failover)에 사용하려는 복구 계획의 그룹 1 전에 수동 단계를 추가하여 이를 활용할 수 있습니다. 그런 다음 테스트 장애 조치(failover)를 실행하기 전에 자동으로 만든 네트워크에 인프라 리소스를 추가합니다.


### <a name="prepare-dhcp"></a>DHCP 준비
테스트 장애 조치에 사용되는 가상 머신이 DHCP를 사용하는 경우 테스트 장애 조치의 목적을 위해 격리된 네트워크 내에 테스트 DHCP 서버를 만듭니다.


### <a name="prepare-active-directory"></a>Active Directory 준비
응용프로그램 테스트를 위해 테스트 장애 조치를 실행하려면 테스트 환경에서 프로덕션 Active Directory 환경의 복사본이 필요합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations)을 참조하세요.

### <a name="prepare-dns"></a>DNS 준비
다음과 같이 테스트 장애 조치에 대한 DNS 서버를 준비합니다.

* **DHCP**: 가상 머신에서 DHCP를 사용하는 경우 테스트 DNS의 IP 주소를 테스트 DHCP 서버에서 업데이트해야 합니다. 네트워크 유형의 Windows 네트워크 가상화를 사용하는 경우 VMM 서버가 DHCP 서버로 작동합니다. 따라서 테스트 장애 조치(failover) 네트워크에서 DNS의 IP 주소가 업데이트되어야 합니다. 이 경우 가상 머신이 관련 DNS 서버에 자체 등록됩니다.
* **고정 주소**: 가상 머신이 고정 IP 주소를 사용하는 경우 테스트 장애 조치(failover)에서 테스트 DNS 서버의 IP 주소를 업데이트해야 합니다. 테스트 가상 머신의 IP 주소로 DNS를 업데이트해야 할 수 있습니다. 이러한 용도로 다음 예제 스크립트를 사용할 수 있습니다.

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

이 절차는 복구 계획에 대한 테스트 장애 조치를 실행하는 방법을 설명합니다. 또는 **Virtual Machines** 탭에서 단일 가상 머신에 대한 장애 조치를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다. **장애 조치(Failover)** > **Test 장애 조치(Failover)** 에서 의견이나 질문을 게시합니다.
2. **테스트 장애 조치** 블레이드에서 테스트 장애 조치(failover) 후에 복제 VM을 네트워크에 연결할 방법을 지정합니다.
3. **작업** 탭에서 장애 조치 진행 상황을 추적합니다.
4. 장애 조치(failover)가 완료되면 VM이 정상적으로 시작되는지 확인합니다.
5. 작업이 완료되면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다. 이 단계는 테스트 장애 조치(failover) 중에 Site Recovery에 의해 생성된 VM 및 네트워크를 삭제합니다. 

![테스트 장애 조치](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> 테스트 장애 조치(failover) 중에 가상 머신에 제공된 IP 주소는 이 IP 주소가 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우, 가상 머신이 계획되거나 계획되지 않은 장애 조치(failover)에 대해 수신하게 되는 동일한 IP 주소입니다. 동일한 IP 주소를 테스트 장애 조치(failover) 네트워크에서 사용할 수 없는 경우 가상 머신은 테스트 장애 조치(failover) 네트워크에서 사용 가능한 다른 IP 주소를 받게 됩니다.



### <a name="run-a-test-failover-to-a-production-network"></a>프로덕션 네트워크로 테스트 장애 조치(failover) 실행

네트워크 매핑 중에 지정한 프로덕션 복구 사이트 네트워크에 테스트 장애 조치(failover)를 실행하지 않는 것이 좋습니다. 그러나 장애 조치된 VM에서 종단 간 네트워크 연결의 유효성을 검사해야 하는 경우 다음 사항에 유의하세요.

* 테스트 장애 조치(failover)를 수행할 때 기본 VM이 종료되어 있는지 확인합니다. 이렇게 하지 않으면 동일한 ID의 두 가상 머신은 동시에 동일한 네트워크에서 실행됩니다. 이 경우 원치 않는 결과가 발생할 수 있습니다.
* 테스트 장애 조치(failover) VM에 수행하는 모든 변경 내용은 테스트 장애 조치(failover) 가상 머신을 정리할 때 손실됩니다. 즉, 이러한 변경 내용이 기본 VM에 다시 복제되지 않습니다.
* 이러한 방법으로 테스트를 수행하면 프로덕션 응용 프로그램이 가동 중지되는 결과를 가져옵니다. DR 훈련이 진행 중인 동안에는 응용 프로그램을 사용하지 말라고 응용 프로그램 사용자에게 요청합니다.  


## <a name="next-steps"></a>다음 단계
DR 훈련을 성공적으로 실행한 후에는 [전체 장애 조치(failover)를 실행](site-recovery-failover.md)할 수 있습니다.



