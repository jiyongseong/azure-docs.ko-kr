---
title: 다양 한 Azure 스택 개발 키트 환경에서 두 가상 네트워크 간의 사이트 간 VPN 연결 | Microsoft Docs
description: 클라우드 관리자를 사용 하 여 두 개의 단일 노드 Azure 스택 개발 키트 환경 간에 사이트 간 VPN 연결을 만드는 단계별 절차입니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: e6520da6b866ebddd66604dd8f27acfc5a9bcef4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>다양 한 Azure 스택 개발 키트 환경에서 두 가상 네트워크 간의 사이트 간 VPN 연결 만들기
## <a name="overview"></a>개요
이 문서는 두 개의 별도 Azure 스택 개발 키트 환경에서 두 가상 네트워크 간의 사이트 간 VPN 연결을 만드는 방법을 보여 줍니다. 연결을 구성 하는 동안 Azure 스택의 VPN 게이트웨이 작동 하는 방법을 배울 수 있습니다.

### <a name="connection-diagram"></a>연결 다이어그램
다음 다이어그램은 연결 구성 해야 모양을 완료 되 면 보여 줍니다.

![사이트 간 VPN 연결 구성](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>시작하기 전에
연결 구성을 완료 하려면를 시작 하기 전에 다음 항목이 있는지 확인 합니다.

* 두 대의 서버 및 기타 필수 구성 요소에 설명 된 대로 Azure 스택 개발 키트 하드웨어 요구 사항을 충족 하는 [빠른 시작: Azure 스택 개발 키트 평가](azure-stack-deploy-overview.md)합니다. 
* [Azure 스택 개발 키트](https://azure.microsoft.com/overview/azure-stack/try/) 배포 패키지 합니다.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Azure 스택 개발 키트 환경 배포
연결 구성을 완료 하려면 두 가지 Azure 스택 개발 키트 환경 배포 해야 합니다.
> [!NOTE] 
> 배포 하는 각 Azure 스택 개발 키트에 대해 수행 된 [배포 지침](azure-stack-run-powershell-script.md)합니다. 이 문서에서는 Azure 스택 개발 키트 환경 이라고 *POC1* 및 *POC2*합니다.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>POC1 및 POC2에 제공 하는 서비스를 준비 합니다.
POC1와 POC2 모두에서 제공 하는 서비스 사용자는 제품 구독 하 고 가상 컴퓨터를 배포할 수 있도록 준비 합니다. 참조를 제공 하는 서비스를 만드는 방법에 대 한 내용은 [Azure 스택 사용자에 게 가상 컴퓨터를 사용할 수 있도록](azure-stack-tutorial-tenant-vm.md)합니다.

## <a name="review-and-complete-the-network-configuration-table"></a>검토 하 고 네트워크 구성 테이블 완료
다음 표에서 두 스택 개발 키트 Azure 환경에 대 한 네트워크 구성을 요약 합니다. 네트워크에 대 한 특정 외부 BGPNAT 주소를 추가 하려면 테이블 뒤에 표시 되는 절차를 따르세요.

**네트워크 구성 테이블**
|   |POC1|POC2|
|---------|---------|---------|
|가상 네트워크 이름     |VNET-01|VNET-02 |
|가상 네트워크 주소 공간 |10.0.10.0/23|10.0.20.0/23|
|서브넷 이름     |서브넷-01|서브넷-02|
|서브넷 주소 범위|10.0.10.0/24 |10.0.20.0/24 |
|게이트웨이 서브넷      |10.0.11.0/24|10.0.21.0/24|
|외부 BGPNAT 주소     |         |         |

> [!NOTE]
> 예제에서는 환경에 있는 외부 BGPNAT IP 주소는 POC1에 대 한 10.16.167.195 및 POC2에 대 한 10.16.169.131입니다. Azure 스택 개발 키트 호스트 프로그램에 대 한 외부 BGPNAT IP 주소를 확인 하려면 다음 절차를 사용 하 고 이전 네트워크 구성 테이블에 추가 합니다.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>NAT VM 외부 어댑터의 IP 주소 가져오기
1. POC1에 대 한 Azure 스택 물리적 컴퓨터에 로그인 합니다.
2. 관리자 암호를 바꾸려면 다음 Powershell 코드를 편집 하 고 POC 호스트에서 코드를 실행 하십시오.

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. 이전 섹션에 표시 되는 네트워크 구성 테이블에 IP 주소를 추가 합니다.

4. POC2에 대해이 절차를 반복 합니다.

## <a name="create-the-network-resources-in-poc1"></a>POC1 네트워크 리소스를 만듭니다
이제 POC1 네트워크 리소스에 게이트웨이가 설정 하는 데 필요한를 만듭니다. 다음 지침에서는 사용자 포털을 사용 하 여 리소스를 만드는 방법을 보여 줍니다. PowerShell 코드를 사용 하 여 리소스를 만들 수 있습니다.

![리소스를 만드는 데 사용 되는 워크플로](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>테 넌 트로 로그인
서비스 관리자는 테스트 계획, 혜택 및 해당 테 넌 트를 사용할 수 있는 구독을 테 넌 트로 로그인 수 있습니다. 하나를 모를 경우 [테 넌 트 계정 만들기](azure-stack-add-new-user-aad.md) 에 로그인 합니다.

### <a name="create-the-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기
1. 사용자 포털에 로그인 하려면 테 넌 트 계정을 사용 합니다.
2. 사용자 포털에서 선택 **새로**합니다.

    ![새 가상 네트워크 만들기](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
4. **가상 네트워크**를 선택합니다.
5. 에 대 한 **이름**, **주소 공간**, **서브넷 이름**, 및 **서브넷 주소 범위**, 네트워크의 앞부분에 표시 되는 값을 사용 하 여 구성 테이블입니다.
6. **구독**, 이전에 만든 구독 나타납니다.
7. 에 대 한 **리소스 그룹**, 리소스 그룹을 만들 하거나 이미 있는 경우 하나를 선택 **기존 항목 사용**합니다.
8. 기본 위치를 확인합니다.
9. **대시보드에 고정**을 선택합니다.
10. **만들기**를 선택합니다.

### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기
1. 대시보드를 이전에 만든 VNET 01 가상 네트워크 리소스를 엽니다.
2. **설정** 블레이드를 열고 **서브넷**을 선택합니다.
3. 게이트웨이 서브넷에 가상 네트워크를 추가 하려면 선택 **게이트웨이 서브넷**합니다.
   
    ![게이트웨이 서브넷 추가](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 기본적으로 서브넷 이름 설정 **GatewaySubnet**합니다.
   게이트웨이 서브넷은 특별 합니다. 사용 해야 올바르게 작동 하려면는 *GatewaySubnet* 이름입니다.
5. **주소 범위**, 주소 인지 확인 **10.0.11.0/24**합니다.
6. 선택 **확인** 게이트웨이 서브넷을 만들려고 합니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기
1. Azure 포털에서 선택 **새로**합니다. 
2. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
3. 네트워크 리소스의 목록에서 선택 **가상 네트워크 게이트웨이**합니다.
4. **이름**, 입력 **GW1**합니다.
5. 선택 된 **가상 네트워크** 항목 가상 네트워크를 선택 합니다.
   선택 **VNET 01** 목록에서 합니다.
6. 선택 된 **공용 IP 주소** 메뉴 항목입니다. 경우는 **공용 IP 주소 선택** 블레이드가 열리고 **새로 만들기**합니다.
7. **이름**, 입력 **GW1 PiP**를 선택한 후 **확인**합니다.
8.  기본적으로에 대 한 **VPN 유형을**, **경로 기반** 을 선택 합니다.
    유지 된 **경로 기반** VPN 유형입니다.
9. **구독** 및 **위치**가 올바른지 확인합니다. 리소스를 대시보드에 고정할 수 있습니다. **만들기**를 선택합니다.

### <a name="create-the-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기
Azure Stack 평가 배포에 *로컬 네트워크 게이트웨이*를 구현하는 것은 실제 Azure 배포에 구현하는 것과 약간 다릅니다.

Azure 배포에서 로컬 네트워크 게이트웨이 Azure에서 가상 네트워크 게이트웨이에 연결 하는 데 사용 하는 온-프레미스 (테 넌 트)에 물리적 장치를 나타냅니다. 연결의 양쪽 끝이 Azure 스택 평가 배포에 가상 네트워크 게이트웨이는!

보다 일반적으로이 대해 생각 하는 방법은 로컬 네트워크 게이트웨이 리소스에 항상 연결의 다른 쪽 끝에서 원격 게이트웨이 나타냅니다 것입니다. Azure 스택 개발 키트 디자인 된 방식으로 인해 해야 네트워크 주소 변환 (NAT) 다른 Azure 스택 개발 키트의 공용 IP 주소로 VM에서 외부 네트워크 어댑터의 IP 주소를 제공 하는 로컬 네트워크 게이트웨이의. 그런 다음 양쪽 끝 올바르게 연결 되어 있는지 확인 하도록 NAT VM에 NAT 매핑을 만듭니다.


### <a name="create-the-local-network-gateway-resource"></a>로컬 네트워크 게이트웨이 리소스 만들기
1. POC1에 대 한 Azure 스택 물리적 컴퓨터에 로그인 합니다.
2. 사용자 포털에서 선택 **새로**합니다.
3. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
4. 리소스 목록에서 선택 **로컬 네트워크 게이트웨이**합니다.
5. **이름**, 입력 **POC2 GW**합니다.
6. **IP 주소**, POC2에 대 한 외부 BGPNAT 주소를 입력 합니다. 이 주소는 네트워크 구성 테이블의 앞부분에 나오는 나타납니다.
7. **주소 공간**, 나중에 만드는 POC2 VNET의 주소 공간에 대 한 입력 **10.0.20.0/23**합니다.
8. 되어 있는지 확인 프로그램 **구독**, **리소스 그룹**, 및 **위치** 한 다음 선택 **만들기**합니다.

### <a name="create-the-connection"></a>연결 만들기
1. 사용자 포털에서 선택 **새로**합니다.
2. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
3. 리소스 목록에서 선택 **연결**합니다.
4. 에 **기본 사항** 설정 블레이드에서 대 한는 **연결 유형**선택, **사이트 (IPSec)** 합니다.
5. 선택 된 **구독**, **리소스 그룹**, 및 **위치**를 선택한 후 **확인**합니다.
6. 에 **설정** 블레이드를 **가상 네트워크 게이트웨이**를 선택한 후 **GW1**합니다.
7. 선택 **로컬 네트워크 게이트웨이**를 선택한 후 **POC2 GW**합니다.
8. **연결 이름**, 입력 **POC1 POC2**합니다.
9. **공유 키 (PSK)**, 입력 **12345**를 선택한 후 **확인**합니다.
10. 에 **요약** 블레이드를 **확인**합니다.

### <a name="create-a-vm"></a>VM 만들기
VPN 연결을 통해 전송 되는 데이터의 유효성을 검사 하려면 가상 컴퓨터를 각 Azure 스택 개발 키트에 데이터를 송수신 해야 합니다. POC1에서 가상 컴퓨터를 지금 만듭니다 한 다음 가상 네트워크에 VM 서브넷에 있습니다.

1. Azure 포털에서 선택 **새로**합니다.
2. 로 이동 **마켓플레이스**를 선택한 후 **계산**합니다.
3. 가상 컴퓨터 이미지의 목록에서 선택 된 **Windows Server 2016 데이터 센터 Eval** 이미지입니다.
4. 에 **기본 사항** 블레이드, **이름**, 입력 **v m 01**합니다.
5. 유효한 사용자 이름과 암호를 입력 합니다. 만든 후 VM에 로그인 하려면이 계정을 사용 합니다.
6. 제공 된 **구독**, **리소스 그룹**, 및 **위치**, 선택한 후 **확인**합니다.
7. 에 **크기** 블레이드가이 인스턴스에 대 한 가상 컴퓨터 크기를 선택한 다음 선택 **선택**합니다.
8. 에 **설정을** 블레이드에서 기본값을 적용 합니다. 확인 된 **VNET-01** 가상 네트워크를 선택 합니다. 서브넷으로 설정 되어 있는지 확인 하십시오. **10.0.10.0/24**합니다. 그런 다음 **확인**을 선택합니다.
9. 에 **요약** 블레이드에서 설정을 검토 한 다음 선택 **확인**합니다.



## <a name="create-the-network-resources-in-poc2"></a>POC2 네트워크 리소스를 만듭니다

다음 단계는 POC2에 대 한 네트워크 리소스를 만드는 것입니다. 다음 지침에는 사용자 포털을 사용 하 여 리소스를 만드는 방법을 보여 줍니다.

### <a name="sign-in-as-a-tenant"></a>테 넌 트로 로그인
서비스 관리자는 테스트 계획, 혜택 및 해당 테 넌 트를 사용할 수 있는 구독을 테 넌 트로 로그인 수 있습니다. 하나를 모를 경우 [테 넌 트 계정 만들기](azure-stack-add-new-user-aad.md) 에 로그인 합니다.

### <a name="create-the-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기

1. 테 넌 트 계정을 사용 하 여 로그인 합니다.
2. 사용자 포털에서 선택 **새로**합니다.
3. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
4. **가상 네트워크**를 선택합니다.
5. POC2에 대 한 값을 확인 하려면 네트워크 구성 테이블의 앞부분에 표시 되는 정보를 사용 하 여 **이름**, **주소 공간**, **서브넷 이름**, 및 **서브넷 주소 범위**합니다.
6. **구독**, 이전에 만든 구독 나타납니다.
7. 에 대 한 **리소스 그룹**, 새 리소스 그룹을 만들거나 이미 있는 경우 하나를 선택 **기존 항목 사용**합니다.
8. 기본 확인 **위치**합니다.
9. **대시보드에 고정**을 선택합니다.
10. **만들기**를 선택합니다.

### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기
1. 만든 가상 네트워크 리소스를 엽니다 (**VNET 02**) 대시보드에서.
2. **설정** 블레이드를 열고 **서브넷**을 선택합니다.
3. 선택 **게이트웨이 서브넷** 를 가상 네트워크에 게이트웨이 서브넷을 추가 합니다.
4. 서브넷의 이름은 **GatewaySubnet**이라고 기본으로 설정됩니다.
   게이트웨이 서브넷은 특별하며 제대로 작동하려면 특정 이름이 있어야 합니다.
5. 에 **주소 범위** 필드에서 주소 확인 **10.0.21.0/24**합니다.
6. 선택 **확인** 게이트웨이 서브넷을 만들려고 합니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기
1. Azure 포털에서 선택 **새로**합니다.  
2. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
3. 네트워크 리소스의 목록에서 선택 **가상 네트워크 게이트웨이**합니다.
4. **이름**, 입력 **GW2**합니다.
5. 가상 네트워크를 선택 하려면 선택 **가상 네트워크**합니다. 그런 다음 선택 **VNET 02** 목록에서 합니다.
6. 선택 **공용 IP 주소**합니다. 경우는 **공용 IP 주소 선택** 블레이드가 열리고 **새로 만들기**합니다.
7. **이름**, 입력 **GW2 PiP**를 선택한 후 **확인**합니다.
8. 기본적으로에 대 한 **VPN 유형을**, **경로 기반** 을 선택 합니다.
    유지 된 **경로 기반** VPN 유형입니다.
9. **구독** 및 **위치**가 올바른지 확인합니다. 리소스를 대시보드에 고정할 수 있습니다. **만들기**를 선택합니다.

### <a name="create-the-local-network-gateway-resource"></a>로컬 네트워크 게이트웨이 리소스 만들기

1. POC2 사용자 포털에서 선택 **새로**합니다. 
4. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
5. 리소스 목록에서 선택 **로컬 네트워크 게이트웨이**합니다.
6. **이름**, 입력 **POC1 GW**합니다.
7. **IP 주소**, 네트워크 구성 테이블의 앞부분에 나열 된 POC1에 대 한 외부 BGPNAT 주소를 입력 합니다.
8. **주소 공간**, POC1에서 다음을 입력에서 **10.0.10.0/23** 의 주소 공간 **VNET 01**합니다.
9. 되어 있는지 확인 프로그램 **구독**, **리소스 그룹**, 및 **위치** 한 다음 선택 **만들기**합니다.

## <a name="create-the-connection"></a>연결 만들기
1. 사용자 포털에서 선택 **새로**합니다. 
2. 로 이동 **마켓플레이스**를 선택한 후 **네트워킹**합니다.
3. 리소스 목록에서 선택 **연결**합니다.
4. 에 **기본** 설정 블레이드에서 대 한는 **연결 유형**, 선택 **사이트 (IPSec)** 합니다.
5. 선택 된 **구독**, **리소스 그룹**, 및 **위치**를 선택한 후 **확인**합니다.
6. 에 **설정** 블레이드를 **가상 네트워크 게이트웨이**를 선택한 후 **GW2**합니다.
7. 선택 **로컬 네트워크 게이트웨이**를 선택한 후 **POC1 GW**합니다.
8. **연결 이름**, 입력 **POC2 POC1**합니다.
9. **공유 키 (PSK)**, 입력 **12345**합니다. 다른 값을 선택 하면 해당 it 기억 *해야* POC1에서 만든 공유 키에 대 한 값과 일치 합니다. **확인**을 선택합니다.
10. 검토는 **요약** 블레이드에서 다음을 선택 하 고 **확인**합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
가상 네트워크에 VM 서브넷에 저장 합니다 POC2에서 이제 가상 컴퓨터를 만듭니다.

1. Azure 포털에서 선택 **새로**합니다.
2. 로 이동 **마켓플레이스**를 선택한 후 **계산**합니다.
3. 가상 컴퓨터 이미지의 목록에서 선택 된 **Windows Server 2016 데이터 센터 Eval** 이미지입니다.
4. 에 **기본 사항** 블레이드에서 대 한 **이름**, 입력 **v m 02**합니다.
5. 유효한 사용자 이름과 암호를 입력 합니다. 만든 후 가상 컴퓨터에 로그인 하려면이 계정을 사용 합니다.
6. 제공 된 **구독**, **리소스 그룹**, 및 **위치**, 선택한 후 **확인**합니다.
7. 에 **크기** 블레이드에서 선택 가상 컴퓨터 크기를이 인스턴스에 대 한 한 다음 선택 **선택**합니다.
8. 에 **설정을** 블레이드에서 기본값을 사용할 수 있습니다. 확인 하는 **VNET 02** 가상 네트워크를 선택한 서브넷으로 설정 되어 있는지 확인 **10.0.20.0/24**합니다. **확인**을 선택합니다.
9. 설정을 검토는 **요약** 블레이드에서 다음을 선택 하 고 **확인**합니다.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>게이트웨이 순회에 각 Azure 스택 개발 키트에서 NAT 가상 컴퓨터를 구성 합니다.
Azure 스택 개발 키트 독립적인와 실제 호스트를 배포 하는 네트워크에서 분리 되므로 *외부* VIP 네트워크에 연결 된 게이트웨이 실제로 외부가 아닌 합니다. 대신, VIP 네트워크는 네트워크 주소 변환을 수행 하는 라우터 뒤 숨겨집니다. 

라우터는 호출 하는 Windows Server 가상 컴퓨터 *AzS bgpnat01*, Azure 스택 개발 키트 인프라에서 라우팅 및 원격 액세스 서비스 (RRAS) 역할을 실행 하는 합니다. 사이트 간 VPN 연결 양쪽 끝에 연결할 수 있도록 AzS bgpnat01 가상 컴퓨터에서 NAT를 구성 해야 합니다. 

VPN 연결을 구성 하려면 BGPNAT 가상 컴퓨터에서 외부 인터페이스 가장자리 게이트웨이 풀의 VIP에 매핑되는 정적 NAT 지도의 경로 만들어야 합니다. 정적 NAT 맵 경로 VPN 연결에서 각 포트에 대해 필요 합니다.

> [!NOTE]
> 이 구성은 Azure 스택 개발 키트 환경에만 필요 합니다.
> 
> 

### <a name="configure-the-nat"></a>NAT 구성
> [!IMPORTANT]
> 에 대해이 절차를 완료 해야 *둘 다* Azure 스택 개발 키트 환경입니다.

1. 확인 된 **내부 IP 주소** 다음 PowerShell 스크립트에서 사용 하도록 합니다. (GW1 및 GW2) 가상 네트워크 게이트웨이 열고 선택한 다음는 **개요** 블레이드에 대 한 값을 저장는 **공용 IP 주소** 나중에 사용할 수 있습니다.
![내부 IP 주소](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. POC1에 대 한 Azure 스택 물리적 컴퓨터에 로그인 합니다.
3. 복사 하 고 다음 PowerShell 스크립트를 편집 합니다. NAT에 각 Azure 스택 개발 키트를 구성 하려면 관리자 권한 Windows PowerShell ISE에서 스크립트를 실행 합니다. 스크립트에 값을 추가 *외부 BGPNAT 주소* 및 *내부 IP 주소* 자리 표시자.

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. POC2에 대해이 절차를 반복 합니다.

## <a name="test-the-connection"></a>연결 테스트
사이트 간 연결이 설정 되 했으므로 트래픽 흐름이 진행 되므로 얻을 수 있는지를 확인 해야 합니다. 확인을 위해 Azure 스택 개발 키트 환경 중 하나에서 만든 가상 컴퓨터 중 하나에 로그인 합니다. 그런 다음 다른 환경에서 만든 가상 컴퓨터를 ping 합니다. 

사이트 간 연결을 통해 트래픽을 보내려면 VIP 하지 원격 서브넷에 가상 컴퓨터의 직접 IP (DIP) 주소를 ping을 확인 합니다. 이 작업을 수행 하려면 연결의 반대쪽 끝에서 DIP 주소가 찾습니다. 나중에 사용할 주소를 저장 합니다.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>테 넌 트 POC1에서 VM에 로그인
1. POC1에 대 한 Azure 스택 물리적 컴퓨터에 로그인 한 다음 테 넌 트 계정을 사용 하 여 사용자 포털에 로그인 합니다.
2. 왼쪽된 탐색 모음에서 선택 **계산**합니다.
3. Vm의 목록에서 찾을 **v m 01** 는 이전에 만든 하 고 선택 합니다.
4. 가상 컴퓨터에 대 한 블레이드에서 클릭 **연결**, VM01.rdp 파일을 엽니다.
   
     ![연결 단추](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. 가상 컴퓨터를 만들 때 구성한 계정으로 로그인 합니다.
6. 관리자 권한 열고 **Windows PowerShell** 창.
7. 입력 **ipconfig /all**합니다.
8. 출력에서 찾을 **IPv4 주소**, 나중에 사용할 주소를 저장 합니다. 이 주소가 POC2에서 ping 합니다. 예제 환경의 경우 주소가 **10.0.10.4**이지만 사용자의 환경에서는 주소가 다를 수 있습니다. 내에서 속해 있어야 합니다.는 **10.0.10.0/24** 이전에 만든 서브넷입니다.
9. 가상 컴퓨터를 ping 응답을 허용 하는 방화벽 규칙을 만들려면 다음 PowerShell 명령을 실행 합니다.

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>테 넌 트 POC2에서 VM에 로그인
1. POC2에 대 한 Azure 스택 물리적 컴퓨터에 로그인 한 다음 테 넌 트 계정을 사용 하 여 사용자 포털에 로그인 합니다.
2. 왼쪽된 탐색 모음에서 **계산**합니다.
3. 가상 컴퓨터의 목록에서 찾을 **v m 02** 는 이전에 만든 하 고 선택 합니다.
4. 가상 머신 블레이드에서 **연결**을 클릭합니다.
5. 가상 컴퓨터를 만들 때 구성한 계정으로 로그인 합니다.
6. 관리자 권한 열고 **Windows PowerShell** 창.
7. 입력 **ipconfig /all**합니다.
8. 내에 포함 되는 IPv4 주소 표시 되어야 **10.0.20.0/24**합니다. 예제 환경에서의 주소는 **10.0.20.4**, 하지만 사용자의 주소는 다를 수 있습니다.
9. 가상 컴퓨터를 ping 응답을 허용 하는 방화벽 규칙을 만들려면 다음 PowerShell 명령을 실행 합니다.

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. POC2에 가상 컴퓨터에서 터널을 통해 POC1에 있는 가상 컴퓨터를 ping 합니다. 이 위해 v m 01에서 기록해 둔 DIP를 ping 합니다.
   이 예에서는 환경에서 **10.0.10.4**, 있지만 랩에서 기록해둔 주소를 ping 해야 합니다. 다음과 같은 결과가 나타납니다.
   
    ![성공적인 ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. 원격 가상 컴퓨터의 회신을 성공적으로 수행한 테스트를 나타냅니다! 가상 컴퓨터 창을 닫을 수 있습니다. 연결을 테스트 하려면 다른 종류의 파일 복사를 같은 데이터 전송을 시도할 수 있습니다.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>게이트웨이 연결을 통한 데이터 전송 통계 보기
사이트 간 연결을 통해 전달 데이터의 양을 확인 하려는 경우이 정보는에서 사용할 수는 **연결** 블레이드입니다. 이 테스트 방금 전송 받은 ping이 VPN 연결을 통해가 실제로 확인 하는 다른 방법은 이기도 합니다.

1. POC2에서 테 넌 트 가상 컴퓨터에 로그인 되어 있는 동안 사용자 포털에 로그인 하려면 테 넌 트 계정을 사용 합니다.
2. 로 이동 **모든 리소스**를 선택한 후는 **POC2 POC1** 연결 합니다. **연결** 나타납니다.
4. 에 **연결** 블레이드에 대 한 통계 **데이터에** 및 **데이터 출력** 나타납니다. 다음 스크린샷에 추가 파일 전송 되는 많은 되는 특성이 합니다. 일부 0이 아닌 값이 있는 표시 되어야 합니다.
   
    ![들어오는/나가는 데이터](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
