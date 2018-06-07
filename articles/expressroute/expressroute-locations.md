---
title: '연결 공급자 및 위치: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 연결 공급자에 따라 정렬됩니다.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: pareshmu
ms.openlocfilehash: 8d17c8dee89bf0ea2a4683c3b41d2eed50c65e2c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303697"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서의 표에서는 ExpressRoute 연결 공급자, ExpressRoute 지역 범위, ExpressRoute를 통해 지원되는 Microsoft 클라우드 서비스 및 ExpressRoute SI(시스템 통합업체)에 대한 정보를 제공합니다.

## <a name="partners"></a>ExpressRoute 연결 공급자
ExpressRoute는 모든 Azure 지역 및 위치에서 지원됩니다. 다음 맵에 Azure 지역 및 ExpressRoute 위치 목록을 제공합니다. ExpressRoute 위치는 Microsoft가 여러 서비스 공급자와 피어링하는 위치를 말합니다.

![위치 맵][0]

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>지역 내 ExpressRoute 위치에 대한 Azure 지역 
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **북아메리카** |미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |애틀랜타, 시카고, 댈러스, 덴버, 라스베이거스, 로스앤젤레스, 마이애미, 뉴욕, 샌안토니오, 시애틀, 실리콘밸리, 워싱턴 DC, 몬트리올, 퀘벡 시티, 토론토 |
| **남미** |브라질 남부 |상파울루 |
| **유럽** |프랑스 중부, 프랑스 남부, 북유럽, 유럽 서부, 영국 서부, 영국 남부 |암스테르담, 더블린, 런던, 뉴포트(웨일스), 파리 |
| **아시아** |동아시아, 동남 아시아 |홍콩 특별행정구, 싱가포르, 싱가포르 2 |
| **일본** |일본 서부, 일본 동부 |오사카, 도쿄 |
| **오스트레일리아** |오스트레일리아 남동부, 오스트레일리아 동부 |멜버른, 시드니 |
| **오스트레일리아 정부** | 오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 | 
| **인도** |인도 서부, 인도 중부, 인도 남부 |첸나이, 뭄바이 |
| **대한민국** |한국 중부, 한국 남부 |부산, 서울 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>국내 클라우드에 대한 지역 및 지리적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |미국 애리조나 주 정부, 미국 아이오와 주 정부, 미국 텍사스 주 정부, 미국 버지니아 주 정부, US DoD 중부, US DoD 동부  |시카고, 댈러스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC |
| **중국** |중국 북부, 중국 동부 |베이징, 상하이 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="locations"></a>연결 공급자 위치

다음 테이블에서는 서비스 공급자별 위치를 보여 줍니다. 위치별 사용 가능한 공급자를 보려는 경우 [위치별 서비스 공급자](expressroute-locations-providers.md#locations)를 참조하세요.


### <a name="production-azure"></a>프로덕션 Azure
| **서비스 공급자** | **Microsoft Azure** | **Office 365 및 Dynamics 365** | **위치** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Airtel](http://www.airtel.in/business/connexion)** | 지원됨 | 지원됨 | 첸나이, 뭄바이 |
| **[Aryaka Networks](http://www.aryaka.com/)** |지원됨 |지원됨 |암스테르담, 댈러스, 홍콩, 실리콘밸리, 싱가포르, 도쿄, 워싱턴 DC |
| **[Ascenty Data Cente](https://ascenty.com/solucoes/conectividade-e-interconexoes/Microsoft-express-route/)** |서비스 예정 |서비스 예정 |상파울루 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 토론토, 워싱턴 DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |지원됨 |지원됨 |몬트리올, 토론토, 퀘벡 시티 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |지원됨 |지원됨 |암스테르담, 홍콩 특별행정구, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[C3ntro](https://c3ntro.com/)** |서비스 예정 |서비스 예정 |마이애미 |
| **CDC** | 지원됨 | 지원됨 | 캔버라, 캔버라2 |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |지원됨 |지원됨 |라스베이거스, 뉴욕, 샌안토니오, 실리콘 밸리, 도쿄, 토론토 |
| **China Telecom Global** |지원됨 |지원되지 않음 |홍콩 |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |지원됨 |지원됨 |댈러스, 몬트리올, 토론토 |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |지원됨 |지원됨 |암스테르담, 더블린, 런던, 파리, 도쿄 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |지원됨 |지원됨 |시카고, 덴버, 로스앤젤레스, 뉴욕, 실리콘밸리, 워싱턴 DC |
| **eir** |지원됨 |지원됨 |더블린|
| **Epsilon Global Communications** |지원됨 |지원됨 |싱가포르 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원됨 |암스테르담, 애틀랜타, 시카고, 댈러스, 홍콩, 런던, 로스앤젤레스, 멜버른, 마이애미, 뉴욕, 오사카, 파리, 상 파울로, 시애틀, 실리콘밸리, 싱가포르, 시드니, 도쿄, 토론토, 워싱턴 DC |
| **euNetworks** |지원됨 |지원됨 |암스테르담, 런던 |
| **GÉANT** |지원됨 |지원됨 |암스테르담 |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | 지원됨| 지원됨 | 첸나이, 뭄바이 |
| **[InterCloud](https://www.intercloud.com/)** |지원됨 |지원됨 |암스테르담, 런던, 파리, 싱가포르, 워싱턴 DC |
| **Internet2** |지원됨 |지원됨 |워싱턴 DC |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |지원됨 |지원됨 |오사카, 도쿄 |
| **Internet Solutions - 클라우드 연결** |지원됨 |지원됨 |암스테르담, 런던 |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |지원됨 |지원됨 |암스테르담, 더블린, 런던, 파리 |
| **[IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)**|지원됨 |지원됨 | 실리콘밸리, 토론토 |
| **Jisc** |지원됨 |지원됨 |런던 |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |지원됨 |지원됨 |서울 |
| **[KPN](http://www.kpn.com/cloudconnect)** | 지원됨 | 지원됨 | 암스테르담 | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 뉴포트, 상파울루, 시애틀, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **LG CNS** |지원됨 |지원됨 |부산, 서울 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 덴버, 더블린, 홍콩, 라스베이거스, 런던, 로스앤젤레스, 멜버른, 마이애미, 뉴욕, 퀘벡 시티, 샌안토니오, 시애틀, 실리콘밸리, 싱가포르, 싱가포르 2, 시드니, 토론토, 워싱턴 DC |
| **MTN** |지원됨 |지원됨 |런던 |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |지원됨 |지원됨 |마이애미, 상파울루 |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |지원됨 |지원됨 |뉴포트(웨일스) |
| **NEXTDC** |지원됨 |지원됨 |멜버른, 시드니 |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |지원됨 |지원됨 |암스테르담, 홍콩, 런던, 로스앤젤레스, 오사카, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |지원됨 |지원됨 |도쿄 |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |지원됨 |지원됨 |오사카 |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |지원됨 |지원됨 |멜버른+, 시드니 |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |지원됨 |지원됨 |암스테르담, 홍콩, 런던, 파리, 실리콘밸리, 싱가포르, 시드니, 워싱턴 DC |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |지원됨 |지원됨 |시카고, 실리콘밸리 |
| **PCCW Global Limited** |지원됨 |지원됨 |홍콩 |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |지원됨 |지원됨 |서울 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |지원됨 |지원됨 |첸나이, 뭄바이 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |지원됨 |지원됨 |싱가포르, 싱가포르 2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |지원됨 |지원됨 |워싱턴 DC |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |지원됨 |지원됨 |암스테르담, 첸나이, 홍콩 특별행정구, 런던, 뭄바이, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |지원됨 |지원됨 |암스테르담, 더블린, 런던 |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |지원됨 |지원됨 |암스테르담, 상파울루 |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |지원됨 |지원됨 |런던 |
| **Telenor** |지원됨 |지원됨 |암스테르담, 런던 |
| **[Telia Carrier](https://www.teliacarrier.com/our-services/Connectivity/Cloud-Connect.html?title=Cloud%20Connect)** | 지원됨 | 지원됨 |런던, 워싱턴 DC |
| **Telmex Uninet**| 서비스 예정 | 서비스 예정 | 댈러스+ |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Telus](http://www.telus.com)** |지원됨 |지원됨 |몬트리올, 토론토 |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |지원됨 |지원됨 |상파울루 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |지원됨 |지원됨 |암스테르담, 시카고, 달라스, 홍콩 특별행정구, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |지원됨 |지원되지 않음 |런던 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 로스앤젤레스, 몬트리올, 뉴욕, 실리콘밸리, 토론토, 워싱턴 DC |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environment"></a>국가별 클라우드 환경

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |시카고, 워싱턴 DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |시카고, 뉴욕+, 실리콘밸리, 워싱턴 DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 | 지원됨 | 시카고, 댈러스 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 워싱턴 DC |

### <a name="china"></a>중국
| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **China Telecom** |지원됨 |지원되지 않음 |베이징, 상하이 |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)를 참조하세요.

### <a name="germany"></a>독일
| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |지원됨 |지원되지 않음 |베를린+, 프랑크푸르트 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |지원됨 |지원되지 않음 |베를린 |
| **Interxion** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨  | 지원되지 않음 | 베를린 |
| **T 시스템** |지원됨 |지원되지 않음 |베를린 |

## <a name="connectivity-through-exchange-providers"></a>Exchange 공급자를 통한 연결

연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix 클라우드 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md)의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-additional-service-providers"></a>추가 서비스 공급자를 통한 연결

| **연결 공급자** | **Exchange** | **위치** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |싱가포르 |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | 토론토, 몬트리올 |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |시애틀 |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |뉴욕, 워싱턴 DC |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |도쿄 |
| **[Axtel](http://alestra.mx/landing/expressrouteazure/)** |Equinix |댈러스|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | 런던 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | 암스테르담, 프랑크푸르트, 런던, 싱가포르, 워싱턴 DC |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 도쿄 |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | 댈러스 |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | 프랑크푸르트, 함부르크 |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | 몬트리올, 토론토 |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | 댈러스, 실리콘밸리, 워싱턴 DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 댈러스 |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | 런던, 싱가포르, 워싱턴 DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 암스테르담 |
| **[지수 E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 런던 |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 암스테르담 |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | 워싱턴 DC |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | 런던, 슬라우 |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | 토론토 |
| **LGA Telecom** |Equinix |싱가포르|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | 시카고, 뉴욕, 워싱턴 DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |홍콩 |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 시드니 |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | 암스테르담 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 워싱턴 DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | 런던 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | 암스테르담, 프랑크푸르트 |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | 암스테르담 |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | 암스테르담, 더블린, 런던, 파리 |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 프랑크푸르트 |  
| **Rogers** | Cologix, Equinix | 몬트리올, 토론토 |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | 런던 | 
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | 암스테르담 |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | 토론토 | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | 댈러스, 로스앤젤레스 |  
| **[United Information Highway(UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | 싱가포르 |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | 뉴욕 |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | 시카고, 실리콘밸리, 워싱턴 DC |
| **Zain** |Equinix |런던|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | 마드리드 |
| **[Zirro](https://zirro.com/services/)**| Equinix | 몬트리올, 토론토 |

## <a name="connectivity-through-datacenter-providers"></a>데이터 센터 공급자를 통한 연결
| **공급자** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire 데이터 센터](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 데이터 센터](http://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>NREN(National Research and Education Networks)을 통한 연결

| **공급자**|
| --- |
| **AARNET**| 
| **DeIC(GÉANT 경유)**|
| **GARR(GÉANT 경유)**|
| **GÉANT**|
| **HEAnet(GÉANT 경유)**|
| **Internet2**|
| **JISC**|
| **RedIRIS(GÉANT 경유)**|
| **SINET**|
| **Surfnet(GÉANT 경유)**|

* 연결 공급자가 목록에 없는 경우 위에 나열된 ExpressRoute Exchange 파트너 중 하나에 연결되어 있는지 확인해 주세요.

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 개인 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **시스템 통합 업체** | **대륙** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | 유럽 |
| **[Avanade Inc.](http://www.avanade.com/)** | 아시아, 유럽, 북아메리카, 남미 |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | 유럽
| **[Ensyst](http://www.ensyst.com.au)** | 아시아
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | 북아메리카 |
| **[FlexManage](http://www.flexmanage.com/cloud)** | 북아메리카 |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | 유럽 |
| **[Lightstream](https://www.ltstream.com/expressroute)** | 북아메리카 |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | 오스트레일리아 |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | 오스트레일리아 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 유럽(독일) |
| **[Nelite](http://nelite.com/)** | 유럽 |
| **[새 서명](https://www.newsignature.co.uk/)** | 유럽 |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | 아시아 |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | 유럽 |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 북아메리카 |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | 북아메리카 |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | 유럽 |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | 오스트레일리아 |


## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"
