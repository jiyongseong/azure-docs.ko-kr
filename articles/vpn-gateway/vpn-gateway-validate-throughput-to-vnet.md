---
title: Microsoft Azure Virtual Network에 대한 VPN 처리량 유효성 검사 | Microsoft Docs
description: 이 문서의 목적은 사용자가 온-프레미스 리소스에서 Azure 가상 머신으로의 네트워크 처리량을 유효성 검사하도록 돕는 것입니다.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: cad7719eb077d7aca9c1db5741a5fe1e0ca910a2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>가상 네트워크에 대한 VPN 처리량의 유효성을 검사하는 방법

VPN Gateway 연결을 통해 Azure 내 Virtual Network와 온-프레미스 IT 인프라 사이에 안전한 프레미스 간 연결을 설정할 수 있습니다.

이 문서에서는 온-프레미스 리소스에서 Azure VM(가상 컴퓨터)으로의 네트워크 처리량을 유효성 검사하는 방법을 보여줍니다. 문제 해결 지침도 제공합니다.

>[!NOTE]
>이 문서는 일반적인 문제를 진단하고 해결하는 데 사용됩니다. 다음 정보를 사용하여 문제를 해결할 수 없는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
>
>

## <a name="overview"></a>개요

VPN Gateway 연결에는 다음 구성 요소가 포함됩니다.

- 온-프레미스 VPN 장치([유효성 검사된 VPN 장치](vpn-gateway-about-vpn-devices.md#devicetable) 목록 보기).
- 공용 인터넷
- Azure VPN Gateway
- Azure VM

다음 다이어그램에서는 VPN을 통한 온-프레미스 네트워크와 Azure Virtual Network의 논리적 연결을 보여 줍니다.

![VPN을 사용한 고객 네트워크와 MSFT 네트워크의 논리적 연결](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>최대 예상 수신/송신 계산

1.  응용 프로그램의 기준선 처리량 요구 사항을 결정합니다.
2.  Azure VPN Gateway 처리량 제한을 결정합니다. 자세한 내용은 [VPN Gateway 계획 및 설계](vpn-gateway-plan-design.md)의 “SKU 및 VPN 형식별 총 처리량” 섹션을 참조하세요.
3.  VM 크기에 대한 [Azure VM 처리량 지침](../virtual-machines/virtual-machines-windows-sizes.md)을 결정합니다.
4.  ISP(인터넷 서비스 공급자) 대역폭을 결정합니다.
5.  예상 처리량을 계산합니다((VM, Gateway, ISP)의 최소 대역폭 * 0.8).

계산된 처리량이 응용 프로그램의 기준선 처리량 요구 사항을 충족하지 않을 경우 병목 현상으로 식별한 리소스의 대역폭을 늘려야 합니다. Azure VPN Gateway의 크기를 조정하려면 [Changing a gateway SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)(게이트웨이 SKU 변경)를 참조하세요. 가상 머신의 크기를 조정하려면 [VM 크기 조정](../virtual-machines/virtual-machines-windows-resize-vm.md)을 참조하세요. 예상 인터넷 대역폭을 사용할 수 없으면 ISP에 문의해야 할 수도 있습니다.

## <a name="validate-network-throughput-by-using-performance-tools"></a>성능 도구를 사용하여 네트워크 처리량의 유효성 검사

테스트 중에 VPN 터널 처리량 포화가 발생하면 정확한 결과가 제공되지 않으므로 이 유효성 검사는 사용량이 많지 않은 시간에 수행해야 합니다.

이 테스트에 사용하는 도구는 Windows 및 Linux에서 둘 다 작동하고 클라이언트 및 서버 모드가 둘 다 있는 iPerf입니다. 이 도구는 Windows VM의 경우 3Gbps로 제한됩니다.

이 도구는 디스크에 대한 읽기/쓰기 작업을 수행하지 않습니다. 종단 간에 자체 생성된 TCP 트래픽을 생성할 뿐입니다. 클라이언트 노드와 서버 노드 간에 대역폭을 측정하는 실험을 기반으로 통계를 생성합니다. 두 노드 사이에서 테스트할 경우 한 노드는 서버 역할을 하고 다른 노드는 클라이언트 역할을 합니다. 이 테스트가 완료되면 두 노드에서 모두 업로드 및 다운로드 처리량을 둘 다 테스트하도록 역할을 전환해 보는 것이 좋습니다.

### <a name="download-iperf"></a>iPerf 다운로드
[iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)를 다운로드합니다. 자세한 내용은 [iPerf 설명서](https://iperf.fr/iperf-doc.php)를 참조하세요.

 >[!NOTE]
 >이 문서에서 설명하는 타사 제품은 Microsoft가 아닌 회사에서 제조되었습니다. Microsoft는 이러한 제품의 성능에 대한 어떠한 묵시적 또는 다른 형태의 보증도 하지 않습니다.
 >
 >

### <a name="run-iperf-iperf3exe"></a>iPerf(iperf3.exe) 실행
1. 트래픽을 허용하는 NSG/ACL 규칙을 사용하도록 설정합니다(Azure VM에서 공용 IP 주소 테스트의 경우).

2. 두 노드에서 모두 포트 5001에 대한 방화벽 예외를 사용하도록 설정합니다.

    **Windows:** 관리자 권한으로 다음 명령을 실행합니다.

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    테스트가 완료될 때 규칙을 제거하려면 이 명령을 실행합니다.

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Azure Linux:** Azure Linux 이미지에는 허용되는 방화벽이 있습니다. 포트를 수신 중인 응용 프로그램이 있으면 트래픽이 통과할 수 있습니다. 보안 설정된 사용자 지정 이미지를 사용하려면 명시적으로 열린 포트가 필요할 수 있습니다. 일반적인 Linux OS 계층 방화벽에는 `iptables`, `ufw` 또는 `firewalld`가 포함됩니다.

3. 서버 노드에서 iperf3.exe가 추출된 디렉터리로 변경합니다. 그다음에 서버 모드에서 iPerf를 실행하고 다음 명령으로 포트 5001을 수신하도록 설정합니다.

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. 클라이언트 노드에서 iperf 도구가 추출된 디렉터리로 변경하고 다음 명령을 실행합니다.

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    클라이언트는 30초 동안 포트 5001의 트래픽을 서버로 유도합니다. 표시된 '-P'는 32개의 동시 연결을 사용하여 서버 노드에 연결 중임을 나타냅니다.

    다음 화면에는 이 예제의 출력이 표시됩니다.

    ![출력](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (선택 사항) 테스트 결과를 유지하려면 이 명령을 실행합니다.

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. 이전 단계를 완료한 후 전환된 역할로 같은 단계를 실행하면 서버 노드가 클라이언트가 되고 클라이언트 노드가 서버가 됩니다.

## <a name="address-slow-file-copy-issues"></a>느린 파일 복사 문제 처리
Windows 탐색기를 사용하거나 RDP 세션을 통해 끌어서 놓으면 파일 복사가 느려질 수 있습니다. 일반적으로 이 문제의 원인은 다음 요소 중 하나이거나 둘 다에 해당합니다.

- Windows 탐색기 및 RDP와 같은 파일 복사 응용 프로그램은 파일을 복사할 때 여러 스레드를 사용하지 않습니다. 성능을 개선하기 위해 [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx)와 같은 다중 스레드 파일 복사 응용 프로그램을 통해 16개 또는 32개의 스레드를 사용하여 파일을 복사합니다. Richcopy에서 파일 복사에 사용할 스레드 수를 변경하려면 **작업** > **복사 옵션** > **파일 복사**를 클릭합니다.<br><br>
![느린 파일 복사 문제](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- 부족한 VM 디스크 읽기/쓰기 속도. 자세한 내용은 [Azure Storage 문제 해결](../storage/common/storage-e2e-troubleshooting.md)을 참조하세요.

## <a name="on-premises-device-external-facing-interface"></a>온-프레미스 장치 외부 연결 인터페이스
온-프레미스 VPN 장치 인터넷 연결 IP 주소가 Azure의 [로컬 네트워크](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) 정의에 포함된 경우 VPN, 간헐적인 연결 해제 또는 성능 문제가 표시되지 않을 수 있습니다.

## <a name="checking-latency"></a>대기 시간 확인
tracert를 통해 Microsoft Azure Edge 장치를 추적하여 홉 사이에 100ms를 초과하는 지연이 있는지 확인합니다.

온-프레미스 네트워크에서 Azure Gateway 또는 VM의 VIP에 대해 *tracert*를 실행합니다. 반환된 *만 확인하면 Azure 가장자리에 도달했음을 알 수 있습니다. 반환된 “MSN”이 포함된 DNS 이름을 확인하면 Microsoft 백본에 도달했음을 알 수 있습니다.<br><br>
![대기 시간 확인](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>다음 단계
자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [Azure Virtual Machine에 대한 네트워크 처리량 최적화](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
