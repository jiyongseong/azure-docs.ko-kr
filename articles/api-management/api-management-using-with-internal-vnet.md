---
title: 내부 가상 네트워크에서 Azure API Management를 사용하는 방법 | Microsoft Docs
description: 내부 가상 네트워크에서 Azure API Management를 설정하고 구성하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: b2b690978c2d67dbf26b74ecd38a408cece91566
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>내부 가상 네트워크에서 Azure API Management를 사용하는 방법
Azure Virtual Networks에서 Azure API Management에서는 인터넷에서 액세스할 수 없는 API를 관리할 수 있습니다. 다양한 VPN 기술은 연결을 만드는 데 사용할 수 있습니다. API Management는 가상 네트워크 내의 두 가지 주요 모드로 배포됩니다.
* 외부
* 내부


API Management를 내부 가상 네트워크 모드로 배포하는 경우 모든 서비스 끝점(게이트웨이, 개발자 포털, Azure Portal, 직접 관리 및 Git)은 액세스를 제어하는 가상 네트워크 내부에서만 볼 수 있습니다. 서비스 끝점은 공용 DNS 서버에 등록되지 않습니다.

내부 모드에서 API Management를 사용하면 다음 시나리오를 달성할 수 있습니다.
* 사이트 간 연결 또는 Azure ExpressRoute VPN 연결을 사용하여 타사의 외부에서 개인 데이터 센터에 호스팅된 API에 안전하게 액세스할 수 있게 합니다.
* 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
* 단일 게이트웨이 끝점을 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다. 


## <a name="prerequisites"></a>필수 조건

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ **활성 Azure 구독**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **API Management 인스턴스** 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

## <a name="enable-vpn"> </a>내부 가상 네트워크에서 API Management 만들기
내부 가상 네트워크의 API Management 서비스는 ILB(내부 부하 분산 장치) 뒤에 호스팅됩니다.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 네트워크 연결 사용

1. [Azure Portal](https://portal.azure.com/)에서 Azure API Management 인스턴스를 찾습니다.
2. **가상 네트워크**를 선택합니다.
3. API Management 인스턴스를 가상 네트워크 내부에 배포되도록 구성합니다.

    ![내부 가상 네트워크에서 Azure API Management를 설정하는 메뉴][api-management-using-internal-vnet-menu]

4. **저장**을 선택합니다.

배포가 성공하면 대시보드에 서비스의 내부 가상 IP 주소가 표시됩니다.

![내부 가상 네트워크를 구성한 API Management 대시보드][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 가상 네트워크 연결 사용
또한 PowerShell cmdlet을 사용하여 가상 네트워크 연결을 사용할 수 있습니다.

* 가상 네트워크 내부에 API Management 서비스 만들기: [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) cmdlet을 사용하여 가상 네트워크 내에 Azure API Management 서비스를 만들고 내부 가상 네트워크 형식을 사용하도록 구성합니다.

* 가상 네트워크 내부에 기존 API Management 서비스 배포: [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) cmdlet을 사용하여 가상 네트워크 내에서 API Management 서비스를 이동하고 내부 가상 네트워크 형식을 사용하도록 구성합니다.

## <a name="apim-dns-configuration"></a>DNS 구성
API Management가 외부 가상 네트워크 모드인 경우 Azure에서 DNS를 관리합니다. 내부 가상 네트워크 모드의 경우 자체의 라우팅을 관리해야 합니다.

> [!NOTE]
> API Management 서비스는 IP 주소에서 오는 요청을 수신 대기하지 않습니다. 해당 서비스 끝점에 구성된 호스트 이름에 대한 요청에만 응답합니다. 이러한 끝점에는 게이트웨이, Azure Portal, 개발자 포털, 직접 관리 끝점 및 Git가 포함됩니다.

### <a name="access-on-default-host-names"></a>기본 호스트 이름에 대한 액세스
예를 들어 "contoso"라는 API Management 서비스를 만들면 기본적으로 다음 서비스 끝점이 구성됩니다.

   * 게이트웨이 또는 프록시: contoso.azure-api.net

   * Azure Portal 및 개발자 포털: contoso.portal.azure-api.net

   * 직접 관리 끝점: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

이러한 API Management 서비스 끝점에 액세스하려면 API Management를 배포한 가상 네트워크에 연결된 서브넷에 가상 머신을 만들 수 있습니다. 서비스의 내부 가상 IP 주소가 10.0.0.5라고 가정하면 다음과 같이 호스트 파일을 매핑할 수 있습니다(%SystemDrive%\drivers\etc\hosts).

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

그런 다음 만든 가상 머신에서 모든 서비스 끝점에 액세스할 수 있습니다. 가상 네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 DNS 레코드를 만들고 가상 네트워크의 어느 곳에서나 이러한 끝점에 액세스할 수 있습니다. 

### <a name="access-on-custom-domain-names"></a>사용자 지정 도메인 이름에 대한 액세스

   1. 기본 호스트 이름으로 API Management 서비스에 액세스하지 않으려면 다음 이미지에 표시된 대로 모든 서비스 끝점에 대해 사용자 지정 도메인 이름을 설정하면 됩니다. 

   ![API Management를 위한 사용자 지정 도메인 설정][api-management-custom-domain-name]

   2. 그런 다음 DNS 서버에 레코드를 만들어 가상 네트워크 내에서만 액세스할 수 있는 이러한 끝점에 액세스할 수 있습니다.

## <a name="routing"> </a> 라우팅
+ 서브넷 범위의 부하 분산된 개인 가상 IP 주소는 예약되며 VNet 내에서 API Management 서비스 끝점에 액세스하는 데 사용됩니다.
+ 부하 분산된 VIP(공용 IP 주소)도 포트 3443 통해서만 관리 서비스 끝점에 액세스하도록 하기 위해 예약됩니다.
+ 서브넷 IP 범위의 IP 주소(DIP)는 VNet 내의 리소스에 액세스하는 데 사용되고, 공용 IP 주소(VIP)는 VNet 외부 리소스에 액세스하는 데 사용됩니다.
+ 부하 분산된 공용 및 개인 IP 주소는 Azure Portal의 개요/기본 정보 블레이드에서 확인할 수 있습니다.

## <a name="related-content"> </a>관련 콘텐츠
자세한 내용은 다음 문서를 참조하세요.
* [가상 네트워크에서 Azure API Management를 설정하는 동안 발생하는 공통 네트워크 구성 문제][Common network configuration problems]
* [가상 네트워크 FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS에서 레코드 만들기](https://msdn.microsoft.com/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

