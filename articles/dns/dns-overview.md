---
title: "Azure DNS 개요 | Microsoft 문서"
description: "Microsoft Azure의 DNS 호스팅 서비스 개요입니다. Microsoft Azure에 도메인을 호스트하세요."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: f255fd9621ff90bfbb3ad193faa64495acf7ecd7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2017
---
# <a name="azure-dns-overview"></a>Azure DNS 개요

Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 이제 Azure DNS는 사설 DNS 도메인을 지원합니다. 자세한 내용은 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요.

![DNS 개요](./media/dns-overview/scenario.png)

## <a name="features"></a>기능

* **안정성 및 성능** - Azure DNS의 DNS 도메인은 DNS 이름 서버의 Azure 글로벌 네트워크에 호스트됩니다. 사용 가능한 가장 가까운 DNS 서버에서 각 DNS 쿼리에 응답하도록 Azure DNS에서는 애니캐스트 네트워킹을 사용합니다. 이렇게 하면 도메인에 대해 빠른 성능과 고가용성이 제공됩니다.

* **원활한 통합** - Azure DNS 서비스를 사용하여 Azure 서비스에 대한 DNS 레코드를 관리하고 외부 리소스에 DNS를 제공할 수도 있습니다. Azure DNS는 Azure Portal에 통합되며 다른 Azure 서비스와 동일한 자격 증명, 청구 및 지원 계약을 사용합니다.

* **보안** - Azure DNS 서비스는 Azure Resource Manager를 기준으로 합니다. 이 경우 역할 기반 액세스 제어, 감사 로그 및 리소스 잠금과 같은 리소스 관리자 기능의 이점을 누릴 수 있습니다. 도메인과 레코드는 Azure 포털, Azure PowerShell cmdlet 및 플랫폼 간 Azure CLI를 통해 관리할 수 있습니다. 자동 DNS 관리가 필요한 응용 프로그램은 REST API 및 SDK를 통해 서비스와 통합할 수 있습니다.

Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인을 구입하려면 타사 도메인 이름 등록 기관을 사용해야 합니다. 등록 기관은 일반적으로 소액의 연간 요금을 부과합니다. 그런 다음, DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

## <a name="pricing"></a>가격

DNS 요금 청구는 Azure에 호스트되는 DNS 영역의 수와 DNS 쿼리 수를 기준으로 합니다. 가격 책정에 대한 자세한 내용은 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.

## <a name="faq"></a>FAQ

Azure DNS에 대한 질문과 대답을 보려면 [Azure DNS FAQ](dns-faq.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

Azure DNS에 [DNS 영역을 만드는](./dns-getstarted-create-dnszone-portal.md) 방법을 알아봅니다.

Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.

