---
title: Azure Cosmos DB 방화벽 지원 및 IP 액세스 제어 | Microsoft Docs
description: Azure Cosmos DB 데이터베이스 계정에서 방화벽을 지원하도록 IP 액세스 제어 정책을 사용하는 방법을 알아봅니다.
keywords: IP 액세스 제어, 방화벽 지원
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
documentationcenter: ''
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: 21274a71042c5acf38711d29a5062e9f68b6a6a0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196800"
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB 방화벽 지원
Azure Cosmos DB 데이터베이스 계정에 저장된 데이터를 보호하기 위해 Azure Cosmos DB는 강력한 HMAC(해시 기반 메시지 인증 코드)를 활용하는 암호 기반 [권한 부여 모델](https://msdn.microsoft.com/library/azure/dn783368.aspx)을 지원했습니다. 이제 Azure Cosmos DB는 암호 기반 권한 부여 모델 외에도 인바운드 방화벽 지원에 대한 정책 중심 IP 기반 액세스 제어를 지원합니다. 이 모델은 기존 데이터베이스 시스템의 방화벽 규칙과 유사하며 Azure Cosmos DB 데이터베이스 계정에 추가 보안 수준을 제공합니다. 이제 이 모델에서는 승인된 컴퓨터 및/또는 클라우드 서비스에서만 액세스할 수 있도록 Azure Cosmos DB 데이터베이스 계정을 구성할 수 있습니다. 이러한 승인된 컴퓨터 및 서비스에서 Azure Cosmos DB 리소스에 액세스하려면 여전히 호출자가 유효한 권한 부여 토큰을 제공해야 합니다.

> [!NOTE]
> 현재 방화벽 지원은 Azure Cosmos DB SQL API 및 Mongo API 계정에만 사용 가능합니다. Azure Germany 또는 Azure Government와 같은 소버린 클라우드 및 다른 API에 대해 방화벽을 구성하는 기능은 곧 제공될 예정입니다. 기존 IP 방화벽이 구성된 Azure Cosmos DB 계정에 대해 서비스 엔드포인트 ACL을 구성하려는 경우 방화벽 구성에 대해 주의하고, IP 방화벽을 제거한 다음, 서비스 엔드포인트 ACL을 구성합니다. 서비스 엔드포인트를 구성한 후 필요한 경우에 IP 방화벽을 다시 활성화할 수 있습니다.

## <a name="ip-access-control-overview"></a>IP 액세스 제어 개요
기본적으로 요청에 유효한 권한 부여 토큰이 포함되어 있으면 공용 인터넷에서 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있습니다. IP 정책 기반 액세스 제어를 구성하려면 사용자가 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 또는 IP 주소 범위를 CIDR 형식으로 제공해야 합니다. 이 구성이 적용되면 이 허용되는 목록 이외의 컴퓨터에서 보내는 모든 요청을 서버에서 차단합니다.  IP 기반 액세스 제어의 연결 처리 흐름은 다음 다이어그램에 설명되어 있습니다.

![IP 기반 액세스 제어의 연결 프로세스를 보여주는 다이어그램](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> IP 액세스 제어 정책 구성
Azure Portal에서나, [Azure CLI](cli-samples.md), [Azure Powershell](powershell-samples.md)을 통해 프로그래밍 방식으로 또는 **ipRangeFilter** 속성을 업데이트하여 [REST API](/rest/api/cosmos-db/)를 통해 IP 액세스 제어 정책을 설정할 수 있습니다. 

Azure Portal에서 IP 액세스 제어 정책을 설정하려면 Azure Cosmos DB 계정 페이지로 이동하고, 탐색 메뉴에서 **방화벽**을 클릭하고, **다음에서 액세스 허용** 값을 **선택한 네트워크**로 변경한 다음, **저장**을 클릭합니다. 

![Azure Portal에서 방화벽 페이지를 여는 방법을 보여 주는 스크린샷](./media/firewall-support/azure-portal-firewall.png)

IP 액세스 제어가 켜지면 포털에서 IP 주소 및 범위를 지정하는 기능뿐 아니라 다른 Azure 서비스 및 Azure Portal에 액세스를 사용하도록 설정하는 스위치를 제공합니다. 이러한 스위치에 대한 자세한 내용은 다음 섹션에 설명되어 있습니다.

> [!NOTE]
> Azure Cosmos DB 데이터베이스 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정하면 허용되는 IP 주소 범위 목록 이외의 컴퓨터에서 시도하는 모든 Azure Cosmos DB 데이터베이스 계정 액세스가 차단됩니다. 이 모델 때문에 액세스 제어의 무결성을 보장하기 위해 포털에서 데이터 평면 작업을 검색하는 기능도 차단됩니다.

## <a name="connections-from-the-azure-portal"></a>Azure Portal에서 연결 

프로그래밍 방식으로 IP 액세스 제어 정책을 사용하도록 설정할 경우 Azure Portal의 IP 주소를 **ipRangeFilter** 속성에 추가해야 액세스가 유지됩니다. 포털 IP 주소는 다음과 같습니다.

|지역|IP 주소|
|------|----------|
|아래 지정된 지역을 제외한 모든 지역|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|독일|51.4.229.218|
|중국|139.217.8.252|
|미국 정부|52.244.48.71|

Azure Portal에서 방화벽 설정을 **선택한 네트워크**로 변경하면 기본적으로 Azure Portal 액세스를 사용하도록 설정됩니다. 

![Azure Portal 액세스를 사용하도록 설정하는 방법을 보여 주는 스크린샷](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-other-azure-paas-services"></a>다른 Azure PaaS 서비스에서 연결 
Azure에서 Azure Stream Analytics, Azure Functions 및 Azure App Service 같은 PaaS 서비스는 Azure Cosmos DB와 함께 사용됩니다. IP 주소를 즉시 사용할 수 없는 서비스에서 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있게 하려면 Azure Cosmos DB 데이터베이스 계정과 프로그래밍 방식으로 연결된 허용되는 IP 주소 목록에 IP 주소 0.0.0.0을 추가해야 합니다. 

Azure Portal에서 방화벽 설정을 **선택한 네트워크**로 변경하면 기본적으로 다른 Azure 서비스에 대한 액세스를 사용하도록 설정됩니다. 

![Azure Portal에서 방화벽 페이지를 여는 방법을 보여 주는 스크린샷](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>현재 IP에서 연결

개발을 간소화하기 위해 Azure Portal에서는 클라이언트 컴퓨터의 IP를 식별하여 허용된 목록에 추가하여 컴퓨터에서 실행되는 앱이 Azure Cosmos DB 계정에 액세스할 수 있도록 합니다. 포털에서 볼 수 있듯이 여기서 클라이언트 IP 주소가 검색됩니다. 이것은 컴퓨터의 클라이언트 IP 주소일 수 있지만 네트워크 게이트웨이의 IP 주소일 수도 있습니다. 프로덕션으로 이동하기 전에 반드시 제거하세요.

현재 IP를 사용하려면 **내 현재 IP 추가**를 선택하여 현재 IP를 IP 목록에 추가한 다음, **저장**을 클릭합니다.

![현재 IP에 대한 방화벽 설정을 구성하는 방법을 보여 주는 스크린샷](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>클라우드 서비스에서 연결
Azure에서 클라우드 서비스는 Azure Cosmos DB를 사용하여 중간 계층 서비스 논리를 호스팅하는 일반적인 방법입니다. 클라우드 서비스에서 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있게 하려면 [IP 액세스 제어 정책을 구성](#configure-ip-policy)하여 클라우드 서비스의 공용 IP 주소를 Azure Cosmos DB 데이터베이스 계정에 연결된 허용된 IP 주소 목록에 추가해야 합니다. 이렇게 하면 클라우드 서비스의 모든 역할 인스턴스가 Azure Cosmos DB 데이터베이스 계정에 액세스할 수 있습니다. 다음 스크린샷처럼 Azure Portal에서 클라우드 서비스의 IP 주소를 검색할 수 있습니다.

![Azure Portal에 표시된 클라우드 서비스의 공용 IP 주소를 보여주는 스크린샷](./media/firewall-support/public-ip-addresses.png)

추가 역할 인스턴스를 추가하여 클라우드 서비스를 규모 확장할 때 이러한 새 인스턴스는 자동으로 Azure Cosmos DB 데이터베이스 계정에 대한 액세스 권한을 갖습니다. 동일한 클라우드 서비스에 포함되기 때문입니다.

## <a name="connections-from-virtual-machines"></a>가상 머신에서 연결
[가상 머신](https://azure.microsoft.com/services/virtual-machines/) 또는 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) 역시 Azure Cosmos DB를 사용하여 중간 계층 서비스를 호스트하는 데 사용할 수 있습니다.  가상 머신에서 액세스할 수 있도록 Azure Cosmos DB 데이터베이스 계정을 구성하려면 [IP 액세스 제어 정책을 구성](#configure-ip-policy)하여 가상 머신 및/또는 가상 머신 확장 집합의 공용 IP 주소를 Azure Cosmos DB 데이터베이스 계정에 허용되는 IP 주소 중 하나로 구성해야 합니다. 다음 스크린샷처럼 Azure Portal에서 가상 머신의 IP 주소를 검색할 수 있습니다.

![Azure Portal에 표시된 가상 머신의 공용 IP 주소를 보여주는 스크린샷](./media/firewall-support/public-ip-addresses-dns.png)

그룹에 추가 가상 컴퓨터 인스턴스를 추가하면 Azure Cosmos DB 데이터베이스 계정에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="connections-from-the-internet"></a>인터넷에서 연결
인터넷에 있는 컴퓨터에서 Azure Cosmos DB 데이터베이스 계정에 액세스하려면 컴퓨터의 클라이언트 IP 주소 또는 IP 주소 범위를 Azure Cosmos DB 데이터베이스 계정에 허용되는 IP 주소 목록에 추가해야 합니다. 

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP 액세스 제어 정책 문제 해결
### <a name="portal-operations"></a>포털 작업
Azure Cosmos DB 데이터베이스 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정하면 허용되는 IP 주소 범위 목록 이외의 컴퓨터에서 시도하는 모든 Azure Cosmos DB 데이터베이스 계정 액세스가 차단됩니다. 따라서 컬렉션 탐색 및 문서 쿼리와 같은 포털 데이터 평면 작업을 사용하려면 포털의 **방화벽** 페이지를 사용하여 Azure Portal 액세스를 명시적으로 허용해야 합니다. 

![Azure Portal에 대한 액세스를 사용하도록 설정하는 방법을 보여 주는 스크린샷](./media/firewall-support/azure-portal-firewall.png)

### <a name="sdk--rest-api"></a>SDK 및 Rest API
보안상의 이유로 허용 목록에 없는 컴퓨터에서 SDK 또는 REST API를 통해 액세스를 시도하면 추가 정보 없이 일반적인 404 찾을 수 없음 응답이 반환됩니다. Azure Cosmos DB 데이터베이스 계정에 대해 구성된 IP 허용 목록을 확인하여 Azure Cosmos DB 데이터베이스 계정에 올바른 정책 구성이 적용되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
네트워크 관련 성능 팁에 대한 정보는 [성능 팁](performance-tips.md)을 참조하세요.

