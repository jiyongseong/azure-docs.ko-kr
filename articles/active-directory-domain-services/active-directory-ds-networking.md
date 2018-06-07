---
title: 'Azure AD 도메인 서비스: 네트워킹 지침 | Microsoft Docs'
description: Azure Active Directory Domain Services의 네트워킹 고려 사항
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: b40aa0e105c0e9fac9c9cab63a5b0a2a6116c4c9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD 도메인 서비스의 네트워킹 고려 사항
## <a name="how-to-select-an-azure-virtual-network"></a>Azure 가상 네트워크를 선택하는 방법
다음 지침은 Azure AD 도메인 서비스와 함께 사용할 가상 네트워크를 선택하는 데 도움을 줍니다.

### <a name="type-of-azure-virtual-network"></a>Azure 가상 네트워크 유형
* **Resource Manager 가상 네트워크**: Azure AD Domain Services는 Azure Resource Manager를 사용하여 만든 가상 네트워크에서 활성화될 수 있습니다.
* 클래식 Azure Virtual Network에서 Azure AD Domain Services를 활성화할 수 없습니다.
* 다른 가상 네트워크를 Azure AD Domain Services가 활성화된 가상 네트워크에 연결할 수 있습니다. 자세한 내용은 [네트워크 연결](active-directory-ds-networking.md#network-connectivity) 섹션을 참조하세요.

### <a name="azure-region-for-the-virtual-network"></a>가상 네트워크에 대한 Azure 지역
* Azure AD 도메인 서비스 관리되는 도메인은 서비스를 활성화하도록 선택한 가상 네트워크와 동일한 Azure 지역에 배포됩니다.
* Azure AD 도메인 서비스에서 지원하는 Azure 지역에서 가상 네트워크를 선택합니다.
* Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.

### <a name="requirements-for-the-virtual-network"></a>가상 네트워크에 대한 요구 사항
* **Azure 워크로드에 대한 근접성**: Azure AD 도메인 서비스에 액세스해야 하는 가상 머신을 현재 호스트하거나 호스트할 가상 네트워크를 선택합니다. 워크로드가 관리되는 도메인이 아닌 다른 가상 네트워크에 배포된 경우 가상 네트워크를 연결하도록 선택할 수도 있습니다.
* **사용자 지정/사용자 DNS 서버 필요**: 가상 네트워크에 대해 구성된 사용자 지정 DNS 서버가 없는지 확인합니다. 사용자 지정 DNS 서버의 예는 가상 네트워크에 배포한 Windows Server VM에서 실행 중인 Windows Server DNS의 인스턴스입니다. Azure AD Domain Services는 가상 네트워크 내에 배포된 사용자 지정 DNS 서버와 통합되지 않습니다.
* **동일한 도메인 이름의 기존 도메인**: 해당 가상 네트워크에서 동일한 도메인 이름을 사용하는 기존 도메인이 없는지 확인합니다. 예를 들어, 선택한 가상 네트워크에서 'contoso.com'이라는 도메인을 이미 사용한다고 가정합니다. 나중에 해당 가상 네트워크에서 동일한 도메인 이름(즉 'contoso.com')으로 Azure AD 도메인 서비스 관리되는 도메인을 사용하도록 설정하려고 합니다. Azure AD 도메인 서비스를 사용하도록 설정하면 오류가 발생합니다. 이 오류는 해당 가상 네트워크에서 도메인 이름이 충돌하기 때문입니다. 이 경우 다른 이름을 사용하여 Azure AD 도메인 서비스 관리되는 도메인을 설정해야 합니다. 또는 기존 도메인을 프로비전 해제한 후 Azure AD 도메인 서비스를 사용하도록 설정할 수 있습니다.

> [!WARNING]
> 이 서비스를 사용하도록 설정한 후에는 도메인 서비스를 다른 가상 네트워크로 이동할 수 없습니다.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>서브넷 선택 지침

![권장되는 서브넷 디자인](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* **Azure 가상 네트워크 내 별도 전용 서브넷**에 Azure AD Domain Services를 배포합니다.
* 관리되는 도메인 전용 서브넷에는 NSG를 적용하지 않습니다. 전용 서브넷에 NSG를 적용해야 하는 경우 **도메인을 서비스하고 관리하는 데 필요한 포트를 차단하지 마십시오**.
* 관리되는 도메인의 전용 서브넷에서 사용할 수 있는 IP 주소의 수를 지나치게 제한하지 않습니다. 이 제한으로 인해 서비스에서 두 도메인 컨트롤러를 관리되는 도메인에 사용할 수 없게 됩니다.
* 가상 네트워크의 **게이트웨이 서브넷에서 Azure AD Domain Services를 사용하지 않습니다**.
* 관리되는 도메인이 사용되도록 설정되는 서브넷에서의 아웃바운드 액세스를 차단하지 않도록 합니다.

> [!WARNING]
> NSG를 Azure AD 도메인 서비스가 활성화된 서브넷에 연결하는 경우 Microsoft의 도메인 서비스 및 관리 기능에 방해가 될 수 있습니다. 또한 Azure AD 테넌트와 관리되는 도메인 간의 동기화가 중단됩니다. **SLA는 Azure AD Domain Services에서 도메인을 업데이트하고 관리하지 못하도록 차단하는 NSG를 적용한 배포에는 적용되지 않습니다.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Azure AD Domain Services에 필요한 포트
다음 포트는 Azure AD Domain Services에서 관리되는 도메인을 서비스하고 유지하는 데 필요합니다. 이러한 포트가 관리되는 도메인을 사용하도록 설정한 서브넷에 대해 차단되어 있지 않은지 확인합니다.

| 포트 번호 | Required? | 목적 |
| --- | --- | --- |
| 443 | 필수 |Azure AD 테넌트와 동기화 |
| 5986 | 필수 | 도메인 관리 |
| 3389 | 옵션 | 도메인 관리 |
| 636 | 옵션 | 관리되는 도메인에 대한 LDAPS(Secure LDAP) 액세스 보안 |

**포트 443(Azure AD와 동기화)**
* 관리되는 도메인과 Azure AD 디렉터리를 동기화하는 데 사용됩니다.
* NSG에서 이 포트에 대한 액세스를 허용해야 합니다. 이 포트에 대한 액세스 권한이 없으면 관리되는 도메인은 Azure AD 디렉터리와 동기화되지 않습니다. 사용자는 암호 변경 내용이 관리되는 도메인과 동기화되지 않으므로 로그인할 수 없습니다.
* 이 포트에 대한 인바운드 액세스를 Azure IP 주소 범위에 속한 IP 주소로 제한할 수 있습니다. Azure IP 주소 범위는 아래 규칙에 표시된 PowerShell 범위와 다른 범위입니다.

**포트 5986(PowerShell 원격 기능)**
* 이 포트는 관리되는 도메인에서 원격으로 PowerShell을 사용하여 관리 작업을 수행하는 데 사용됩니다.
* NSG에서 이 포트를 통한 액세스를 허용해야 합니다. 이 포트에 대한 액세스 권한이 없으면 관리되는 도메인을 업데이트, 구성, 백업 또는 모니터링할 수 없습니다.
* 이 포트에 대한 인바운드 액세스를 다음 원본 IP 주소로 제한할 수 있습니다. 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* 관리되는 도메인의 도메인 컨트롤러는 일반적으로 이 포트에서 수신하지 않습니다. 서비스는 관리 또는 유지 관리 작업을 관리되는 도메인에서 수행해야 하는 경우에만 관리되는 도메인 컨트롤러에서 이 포트를 엽니다. 작업이 완료되는 즉시 서비스는 관리되는 도메인 컨트롤러에서 이 포트를 종료합니다.

**포트 3389(원격 데스크톱)**
* 이 포트는 관리되는 도메인의 도메인 컨트롤러에 대한 원격 데스크톱 연결에 사용됩니다.
* NSG를 통해 이 포트를 여는 것은 선택 사항입니다.
* 이 포트도 관리되는 도메인에서 주로 꺼진 상태로 유지됩니다. 원격으로 PowerShell을 사용하여 관리 및 모니터링 작업을 수행하므로 이 메커니즘은 지속적으로 사용되지 않습니다. 이 포트는 Microsoft가 고급 문제 해결을 위해 관리되는 도메인에 원격으로 연결해야 하는 드문 경우에만 사용됩니다. 문제 해결 작업이 완료되는 즉시 포트가 닫힙니다.

**포트 636(보안 LDAP)**
* 이 포트는 인터넷을 통한 관리되는 도메인에 대한 보안 LDAP 액세스를 사용하거나 사용하지 않도록 설정하는 데 사용됩니다.
* NSG를 통해 이 포트를 여는 것은 선택 사항입니다. 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정한 경우에만 이 포트를 엽니다.
* 이 포트에 대한 인바운드 액세스를 보안 LDAP를 통해 연결하려는 원본 IP 주소로 제한할 수 있습니다.

**아웃바운드 액세스** AAD Domain Services는 관리되는 도메인을 관리, 백업 및 모니터링하기 위해 여러 다양한 Azure 서비스에 아웃바운드로 액세스해야 합니다. 관리되는 도메인이 사용되도록 설정되는 전용 서브넷에서의 아웃바운드 액세스를 차단하지 않도록 합니다.


## <a name="network-security-groups"></a>네트워크 보안 그룹
[NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md)는 ACL(Access Control 목록)의 Virtual Network에 VM 인스턴스에 대한 허용 또는 거부 네트워크 트래픽 규칙의 목록을 포함합니다. Nsg는 서브넷 또는 서브넷 내의 개별 VM 인스턴스 중 하나와 연결될 수 있습니다. NSG를 서브넷과 연결한 경우 ACL 규칙은 해당 서브넷에 있는 모든 VM 인스턴스에 적용됩니다. 또한 개별 VM에 대한 트래픽은 해당 VM에 직접 NSG를 연결하여 추가로 제한할 수 있습니다.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Azure AD Domain Services를 사용하는 가상 네트워크에 대한 샘플 NSG
다음 표는 Azure AD Domain Services 관리되는 도메인을 사용하여 가상 네트워크에 대해 구성할 수 있는 샘플 NSG를 보여 줍니다. 이 규칙을 통해 필수 포트의 인바운드 트래픽에서 관리되는 도메인을 패치되고 업데이트된 상태로 유지하고 Microsoft에서 모니터링할 수 있도록 합니다. 기본 'DenyAll' 규칙은 인터넷의 다른 모든 인바운드 트래픽에 적용됩니다.

또한 NSG는 인터넷을 통해 보안 LDAP 액세스를 잠그는 방법을 보여 줍니다. 인터넷을 통해 관리되는 도메인에 대한 보안 LDAP 액세스를 비활성화한 경우 이 규칙을 건너뜁니다. 이 NSG에는 지정된 IP 주소 집합에서만 TCP 포트 636을 통해 인바운드 LDAPS 액세스를 허용하는 규칙 집합이 포함되어 있습니다. 지정된 IP 주소에서 인터넷을 통해 LDAPS 액세스를 허용하는 NSG 규칙은 DenyAll NSG 규칙보다 우선 순위가 높습니다.

![인터넷을 통해 LDAPS 액세스를 보안하는 예제 NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

**자세한 내용** - [네트워크 보안 그룹 만들기](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)


## <a name="network-connectivity"></a>네트워크 연결
Azure AD Domain Services 관리되는 도메인은 Azure의 단일 가상 네트워크 내에서만 활성화될 수 있습니다.

### <a name="scenarios-for-connecting-azure-networks"></a>Azure 네트워크 연결에 대한 시나리오
다음 배포 시나리오 중 하나에서 관리되는 도메인을 사용하여 Azure 가상 네트워크를 연결합니다.

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>둘 이상의 Azure 가상 네트워크에서 관리되는 도메인 사용
다른 Azure 가상 네트워크를 Azure AD Domain Services가 활성화된 Azure 가상 네트워크에 연결할 수 있습니다. 이 VPN/VNet 피어링 연결을 통해 다른 가상 네트워크에 배포된 워크로드에서 관리되는 도메인을 사용할 수 있습니다.

![클래식 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>리소스 관리자 기반 가상 네트워크에서 관리되는 도메인 사용
리소스 관리자 기반 가상 네트워크를 Azure AD 도메인 서비스가 활성화된 Azure 클래식 가상 네트워크에 연결할 수 있습니다. 이 연결을 통해 리소스 관리자 기반 가상 네트워크에 배포된 작업과 함께 관리되는 도메인을 사용할 수 있습니다.

![클래식 가상 네트워크 연결에 대한 리소스 관리자](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>네트워크 연결 옵션
* **가상 네트워크 피어링을 사용하여 VNet 간 연결**: 가상 네트워크 피어링은 Azure 백본 네트워크를 통해 동일한 지역에 있는 두 개의 가상 네트워크를 연결하는 메커니즘입니다. 두 가상 네트워크가 피어링되면 모든 연결에 대해 하나로 표시됩니다. 여전히 별도 리소스로 관리할 수는 있지만 이러한 가상 네트워크의 가상 머신은 개인 IP 주소를 사용하여 직접 서로 통신할 수 있습니다.

    ![피어링을 사용하여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [추가 정보 - 가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)

* **사이트 간 VPN 연결을 사용하여 VNet 간 연결**: 가상 네트워크를 다른 가상 네트워크에 연결(VNet 간)하는 것은 가상 네트워크를 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다.

    ![VPN Gateway를 사용하여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [추가 정보 - VPN 게이트웨이를 사용하여 가상 네트워크 연결](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>관련 콘텐츠
* [Azure 가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)
* [클래식 배포 모델에 대한 VNet 간 연결 구성](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Azure 네트워크 보안 그룹](../virtual-network/security-overview.md)
* [네트워크 보안 그룹 만들기](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
