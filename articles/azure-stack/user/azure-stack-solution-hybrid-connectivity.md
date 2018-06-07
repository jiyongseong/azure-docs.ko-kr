---
title: Azure 및 Azure 스택 하이브리드 클라우드 연결 구성 | Microsoft Docs
description: Azure 및 Azure 스택 하이브리드 클라우드 연결을 구성 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605198"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>자습서: 하이브리드 클라우드 연결을 Azure 및 Azure 스택 구성

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

전역 Azure 및 하이브리드 연결 패턴을 사용 하 여 Azure 스택에서 보안 리소스에 액세스할 수 있습니다.

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 온-프레미스 데이터를 개인 정보 또는 규정 요구 사항을 충족 하지만 글로벌 Azure 리소스에 대 한 액세스 권한이 유지 합니다.
> - 레거시 시스템 글로벌 Azure의 클라우드 확장 앱 배포 및 리소스를 사용 하는 동안 유지 관리 합니다.

## <a name="prerequisites"></a>필수 조건

하이브리드 연결 배포를 만들려는 몇 가지 구성 요소가 필요 합니다. 이러한 구성 요소 중 일부가 시간이 걸립니다 준비를 적절 하 게 계획 해야 할 수 있도록 합니다.

**Azure Stack**

Azure OEM/하드웨어 파트너 Azure 스택 프로덕션에 배포할 수 및 모든 사용자가 Azure 스택 개발 키트 (ASDK)를 배포할 수 있습니다.

**Azure 스택 구성 요소**

Azure 스택 연산자 해야 응용 프로그램 서비스를 배포, 계획 및 제안 테 넌 트 구독 및 만든 Windows Server 2016 이미지를 추가 합니다. 이미 있는 경우 이러한 구성 요소 중 일부를이 자습서를 시작 하기 전에 요구 사항을 충족 해야 합니다.

이 자습서에서는 Azure와 Azure 스택 한 기본 지식이 있다고 가정 합니다. 이 자습서를 시작 하기 전에 자세한 내용은 다음 문서를 참조 합니다.

 - [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure 스택 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
 - 만들기는 [웹 앱](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) Azure에서. 이 자습서에서 해야 하기 때문에 웹 앱 URL를 기록해 둡니다.

### <a name="azure-stack"></a>Azure Stack

 - Azure 스택 프로덕션 사용 또는 배포에서 Azure 스택 개발 키트 https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1합니다.
   >[!Note]
   >배포는 ASDK 최대 7 시간까지 걸릴, 있으므로 그에 따라 계획 수 있습니다.

 - 배포 [앱 서비스](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure 스택에 PaaS 서비스입니다.
 - [계획 및 제안 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure 스택 환경에서 합니다.
 - [테 넌 트 구독 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure 스택 환경 내에서.

### <a name="before-you-begin"></a>시작하기 전에

하이브리드 클라우드 연결을 구성 하기 전에 다음 조건을 충족 하는지 확인 합니다.

 - VPN 장치는 외부와 연결 공용 IPv4 주소가 필요합니다. Nat 뒤에이 IP 주소를 찾을 수 없습니다.
 - 모든 리소스가 동일한 지역/위치에 배포 됩니다.

#### <a name="tutorial-example-values"></a>자습서 예제 값

이 자습서의 예제에서는 다음 값을 사용 합니다. 이러한 값은 테스트 환경을 만들 또는 예제를 보다 잘 이해 하는 것에 대 한 참조를 사용할 수 있습니다. 특정 게이트웨이 설정에 대한 자세한 내용은 [VPN Gateway 설정 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)를 참조하세요.

연결 사양:

 - **VPN 유형**: 경로 기반
 - **연결 유형**: 사이트 (IPsec)
 - **게이트웨이 유형**: VPN
 - **Azure 연결 이름**: Azure-게이트웨이-AzureStack-S2SGateway (포털은 자동 채우기가이 값)
 - **Azure 스택 연결 이름**: AzureStack-게이트웨이-Azure-S2SGateway (포털은 자동 채우기가이 값)
 - **공유 키**: 모든 연결의 양쪽 모두에 값을 일치 하는 VPN 하드웨어와 호환
 - **구독**: 모든 기본 구독
 - **리소스 그룹**: 테스트 인프라

네트워크 및 서브넷 IP 주소:

| 스택 azure/Azure 연결 | 이름 | 서브넷 | IP 주소 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure 스택 vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 가상 네트워크 게이트웨이 | Azure 게이트웨이 |  |  |
| Azure 스택 가상 네트워크 게이트웨이 | AzureStack 게이트웨이 |  |  |
| Azure 공용 IP | Azure GatewayPublicIP |  | 생성 시 결정 |
| Azure 스택 공용 IP | AzureStack GatewayPublicIP |  | 생성 시 결정 |
| Azure의 로컬 네트워크 게이트웨이 | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Azure 스택 공용 IP 값 |
| Azure 스택 로컬 네트워크 게이트웨이 | Azure S2SGateway<br>10.100.102.0/23 |  | Azure 공용 IP 값 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>전역 Azure 및 Azure 스택에서 가상 네트워크 만들기

포털을 사용 하 여 가상 네트워크를 만들려면 다음 단계를 사용 합니다. 이 사용할 수 있습니다 [예제 값](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) 자습서로이 문서를 사용 하는 경우. 하지만 프로덕션 환경을 구성 하려면이 문서를 사용할 경우 설정 예를 원하는 값으로 바꿉니다.

> [!IMPORTANT]
> Azure 또는 Azure 스택 vNet 주소 공간에서 IP 주소 잠금의 겹쳐진 부분 없는지 확인 해야 합니다.

Azure에서 vNet을 만들려면:

1. 브라우저를 사용 하 여 연결할는 [Azure 포털](http://portal.azure.com/) Azure 계정으로 로그인 합니다.
2. 선택 **리소스 만들기**합니다. 에 **마켓플레이스 검색** 필드에, 입력 `virtual network`'. 찾을 **가상 네트워크** 결과 목록과 다음 선택에서 **가상 네트워크**합니다.
3. **배포 모델 선택** 목록에서 선택 **리소스 관리자**를 선택한 후 **만들기**합니다.
4. **가상 네트워크 만들기**, VNet 설정을 구성 합니다. 필수 필드 이름에 대 한 접두사로 빨간색 별표 사용 됩니다.  올바른 값을 입력 하는 경우 별표 녹색 확인 표시를 변경 합니다.

스택의 Azure vNet을 만들려면:

* (1-4)는 Azure 스택을 사용 하 여 이전 단계를 반복 **테 넌 트 포털**합니다.

## <a name="add-a-gateway-subnet"></a>게이트웨이 서브넷 추가

가상 네트워크 게이트웨이 연결 하기 전에 연결 하려는 가상 네트워크 게이트웨이 서브넷을 만든 해야 합니다. 게이트웨이 서비스는 게이트웨이 서브넷에서 지정 된 IP 주소를 사용 합니다.

에 [Azure 포털](http://portal.azure.com/), 가상 네트워크 게이트웨이를 만들려는 리소스 관리자 가상 네트워크로 이동 합니다.

1. 열려는 vNet 선택 된 **가상 네트워크** 페이지.
2. **설정**선택, **서브넷**합니다.
3. 에 **서브넷** 페이지에서 **+ 게이트웨이 서브넷** 열려는 **서브넷 추가** 페이지.

    ![게이트웨이 서브넷 추가](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. **이름** 에 서브넷 'GatewaySubnet' 값을 사용 하 여 자동으로 채워집니다. 이 값은 서브넷의 게이트웨이 서브넷으로 인식 하도록 Azure에 필요 합니다.
5. 변경 된 **주소 범위** 구성 요구 사항을 충족 한 다음 선택 제공 되는 값 **확인**합니다.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Azure 및 Azure에서 가상 네트워크 게이트웨이 만들기 스택

Azure에서 가상 네트워크 게이트웨이 만들려면 다음 단계를 사용 합니다.

1. 포털 페이지의 왼쪽에서 선택 **+** 검색 필드에 '가상 네트워크 게이트웨이'를 입력 합니다.
2. **결과**선택, **가상 네트워크 게이트웨이**합니다.
3. **가상 네트워크 게이트웨이**선택, **만들기** 열려는 **가상 네트워크 게이트웨이 만들기** 페이지.
4. **가상 네트워크 게이트웨이 만들기**, 에서처럼 사용자 네트워크 게이트웨이에 대 한 값을 지정 **자습서 예제 값**, 및 다음의 추가 값:

    - **SKU**: 기본
    - **가상 네트워크**: 이전에 만든 가상 네트워크를 선택 합니다. 만든 게이트웨이 서브넷은 자동으로 선택 됩니다.
    - **첫 번째 IP 구성**: 게이트웨이의 공용 IP입니다.
        - 선택 **게이트웨이 IP 구성 만들기**, 수를 사용 하는 **공용 IP 주소 선택** 페이지.
        - 선택 **+ 새로 만들기** 열려는 **공용 IP 주소 만들기** 페이지.
        - 입력 한 **이름** 공용 IP 주소에 대 한 합니다. SKU로 둡니다 **기본**를 선택한 후 **확인** 변경 내용을 저장 하려면.

       > [!Note]
       > 현재 VPN 게이트웨이가 동적 공용 IP 주소 할당을 지원합니다. 그러나 IP 주소는 VPN 게이트웨이에 할당 된 후 변경 됨 의미 하지 않습니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. 크기 조정, 다시 설정, 또는 기타 내부 유지 관리/업그레이드에 VPN 게이트웨이 IP 주소를 변경 하지 마십시오.

4. 게이트웨이 설정을 확인 합니다.
5. 선택 **만들기** VPN 게이트웨이 만들려고 합니다. 게이트웨이 설정 유효성을 검사 하 고 "배포 가상 네트워크 게이트웨이" 타일은 대시보드에 표시 됩니다.

   >[!Note]
   >하나의 게이트웨이를 만드는 데 최대 45분이 걸릴 수 있습니다. 완료 상태를 확인하기 위해 포털 페이지를 새로 고쳐야 할 수 있습니다.

    게이트웨이 만든 후에 포털에서 가상 네트워크를 확인 하 여 할당 된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 장치로 표시됩니다. 게이트웨이에 대 한 자세한 정보를 보려면 장치를 선택 합니다.

6. Azure 스택 배포에 대해 이전 단계 (1-5)를 반복 합니다.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Azure 및 Azure 스택에서 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. Azure 또는 Azure 스택를 참조 하 고 다음 지정할 수 있는 이름을 사이트를 지정 합니다.

- 에 대 한 연결을 만들려는 온-프레미스 VPN 장치의 IP 주소입니다.
- VPN 장치에 VPN 게이트웨이 통해 라우팅되는 IP 주소 접두사입니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다.

  >[!Note]
  >온-프레미스 네트워크 변경 되거나를 VPN 장치에 대 한 공용 IP 주소를 변경 해야 할 경우 나중에 이러한 값을 업데이트할 쉽게 있습니다.

1. 포털에서 선택 **리소스를 만들 +** 합니다.
2. 검색 상자에 입력 **로컬 네트워크 게이트웨이**을 선택한 후 **Enter** 을 찾으려고 합니다. 그러면 결과 목록이 반환됩니다.
3. 선택 **로컬 네트워크 게이트웨이**을 선택한 후 **만들기** 열려는 **로컬 네트워크 게이트웨이 만들기** 페이지.
4. **로컬 네트워크 게이트웨이 만들기**, 로컬 네트워크 게이트웨이에 대 한 값을 지정를 사용 하 여 우리의 **자습서 예제 값**합니다. 다음의 추가 값을 포함 합니다.

    - **IP 주소**:에 연결 하는 Azure 또는 Azure 스택 원하는 VPN 장치의 공용 IP 주소입니다. Azure의 주소에 연결할 수 있도록 NAT 뒤에 없는 유효한 공용 IP 주소를 지정 합니다. IP 주소를 지금 바로 없는, 자리 표시자로 예제에서 값을 사용할 수 있지만 VPN 장치의 공용 IP 주소와 자리 표시자를 바꾸고 다시 이동 해야 합니다. Azure는 올바른 주소를 제공할 때까지 장치에 연결할 수 없습니다.
    - **주소 공간**:이 로컬 네트워크를 나타내는 네트워크에 대 한 주소 범위입니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 지정한 범위에 연결 하려는 다른 네트워크의 범위와 겹치지 않는지 확인 합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 장치 IP 주소에 라우팅합니다. 예제 값 온-프레미스 사이트에 연결 하려는 경우 사용자가 직접 값을 사용 합니다.
    - **BGP 설정을 구성**: BGP를 구성할 때에 사용 합니다. 그렇지 않으면 이것을 선택하지 마십시오.
    - **구독**:에서 올바른 구독이 표시 되어 있는지 확인 합니다.
    - **리소스 그룹**: 리소스 그룹을 사용 하려면 선택 합니다. 새 리소스 그룹을 만들 하거나 이미 만든 하나를 선택 합니다.
    - **위치**:이 개체에서 생성 되는 위치를 선택 합니다. 이렇게 해야 하는 하지만의 VNet에 있는 동일한 위치를 선택 할 수 있습니다.
5. 필요한 값을 지정 하는 것이 했으면, 선택 **만들기** 로컬 네트워크 게이트웨이 만들려고 합니다.
6. Azure 스택 배포에서 이러한 단계 (1-5)를 반복 합니다.

## <a name="configure-your-connection"></a>연결 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 장치가 필요합니다. 구성한 VPN 장치를 연결으로 라고 합니다. 연결을 구성 하려면 다음을 수행 해야 합니다.

- 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
- 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다. Azure 포털을 사용 하 여 VPN 게이트웨이의 공용 IP 주소를 확인 하려면 가상 네트워크 게이트웨이와 이동한 다음 게이트웨이의 이름을 선택 합니다.

다음 단계를 사용 하 여 가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만듭니다.

1. Azure 포털에서 선택 **리소스를 만들 +** 합니다.
2. 검색할 **연결**합니다.
3. **결과**선택, **연결**합니다.
4. **연결**선택, **만들기**합니다.
5. **연결 만들기**, 다음 설정을 구성 합니다.

    - **연결 유형**: 사이트 (IPSec)을 선택 합니다.
    - **리소스 그룹**: 테스트 리소스 그룹을 선택 합니다.
    - **가상 네트워크 게이트웨이**: 만든 가상 네트워크 게이트웨이 선택 합니다.
    - **로컬 네트워크 게이트웨이**: 만든 로컬 네트워크 게이트웨이 선택 합니다.
    - **연결 이름**: 두 게이트웨이에서 값을 사용 하 여 자동으로 채워집니다.
    - **공유 키**:이 값에는 로컬 온-프레미스 VPN 장치에 사용 하는 값과 일치 해야 합니다. 'Abc123'을 사용 하 여 자습서 예제 하지만 있습니다 수 (및 해야) 사용 하 여 조금 더 복잡 한 합니다. 중요 한 점은이 값 VPN 장치를 구성할 때 지정 하는 동일한 값을 이어야 합니다.
    - **구독**, **리소스 그룹** 및 **위치**에 대한 값이 고정됩니다.

6. 선택 **확인** 사용자 연결을 만듭니다.

연결을 확인 수는 **연결** 가상 네트워크 게이트웨이의 페이지입니다. 상태에서 이동 *알 수 없는* 를 *연결*, 한 다음 *Succeeded*합니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 참조 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
