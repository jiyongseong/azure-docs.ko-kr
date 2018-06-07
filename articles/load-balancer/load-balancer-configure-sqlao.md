---
title: SQL Server Always On에 대해 부하 분산 장치 구성 | Microsoft Docs
description: SQL Server Always On에서 작동하도록 부하 분산 장치를 구성하며, PowerShell을 사용하여 SQL Server 구현에 대한 부하 분산 장치를 만드는 방법입니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: a0c2345b47b9103ac6a7ae998f13a12332e3907e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>SQL Server Always On에 대해 부하 분산 장치 구성



이제 SQL Server Always On 가용성 그룹은 내부 부하 분산 장치로 실행할 수 있습니다. 가용성 그룹은 고가용성 및 재해 복구를 위한 SQL Server의 주력 솔루션입니다. 가용성 그룹 수신기를 통해 클라이언트 응용 프로그램은 구성의 복제본 수에 관계없이 주 복제본에 매끄럽게 연결할 수 있습니다.

수신기(DNS) 이름은 부하가 분산된 IP 주소에 매핑됩니다. Azure Load Balancer는 들어오는 트래픽을 복제본 세트의 주 서버로 보냅니다.

SQL Server Always On(수신기) 엔드포인트에 대해 내부 부하 분산 장치 지원을 사용할 수 있습니다. 이제 수신기의 접근성을 제어할 수 있습니다. 가상 네트워크의 특정 서브넷에서 부하가 분산된 IP 주소를 선택할 수 있습니다.

수신기에서 내부 부하 분산 장치를 사용하면 다음 항목만 SQL Server 엔드포인트(예: Server=tcp:ListenerName,1433;Database=DatabaseName)에 액세스할 수 있습니다.

* 동일한 가상 네트워크에 있는 서비스 및 VM
* 연결된 온-프레미스 네트워크의 서비스 및 VM
* 상호 연결된 가상 네트워크의 서비스 및 VM

![내부 부하 분산 장치 SQL Server Always On](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>서비스에 내부 부하 분산 장치 추가

1. 다음 예제에서는 ‘Subnet-1’이라는 서브넷을 포함하는 가상 네트워크를 구성합니다.

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 각 VM에 내부 부하 분산 장치에 대한 부하가 분산된 엔드포인트를 추가합니다.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    이전 예제에서 “Sqlsvc” 클라우드 서비스에서 실행하는 “sqlsvc1” 및 “sqlsvc2”라는 두 개의 VM이 있습니다. `DirectServerReturn` 스위치로 내부 부하 분산 장치를 만든 후 내부 부하 분산 장치에 부하가 분산된 엔드포인트를 추가합니다. SQL Server는 부하가 분산된 엔드포인트를 통해 가용성 그룹에 대한 수신기를 구성할 수 있습니다.

SQL Server Always On에 대한 자세한 내용은 [Azure에서 Always On 가용성 그룹에 대한 내부 부하 분산 장치 구성](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [공용 부하 분산 장치 구성 시작](load-balancer-get-started-internet-arm-ps.md)
* [내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)
* [부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
* [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
