---
title: "Azure Portal을 사용하여 Azure DNS 시작 | Microsoft Docs"
description: "Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure Portal을 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: 22bf52f7452f182510c3714f7d1c2ca884446953
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure DNS 시작

> [!div class="op_single_selector"]
> * [Azure 포털](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

이 문서에서는 Azure Portal을 사용하여 DNS 영역 및 레코드를 만드는 단계를 안내합니다. Azure PowerShell 또는 플랫폼 간 Azure CLI를 사용하여 이러한 단계를 수행할 수도 있습니다.

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 각 단계는 다음 단계에 설명되어 있습니다.

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

1. Azure 포털에 로그인합니다.
2. 허브 메뉴에서 **리소스 만들기 > 네트워킹 >**을 클릭한 다음, **DNS 영역**을 클릭하여 **DNS 영역 만들기** 페이지를 엽니다.

    ![DNS 영역](./media/dns-getstarted-portal/openzone650.png)

4. **DNS 영역 만들기** 페이지에서 다음 값을 입력한 다음 **만들기**를 클릭합니다.


   | **설정** | **값** | **세부 정보** |
   |---|---|---|
   |**Name**|contoso.com|DNS 영역의 이름|
   |**구독**|[구독 이름]|DNS 영역을 만들 구독을 선택합니다.|
   |**리소스 그룹**|**새로 만들기:** contosoDNSRG|리소스 그룹을 만듭니다. 리소스 그룹 이름은 선택한 구독 내에서 고유해야 합니다. 리소스 그룹에 대해 자세히 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 개요 문서를 참조하세요.|
   |**위치**:|미국 서부||

> [!NOTE]
> 리소스 그룹은 리소스 그룹의 위치를 나타내며 DNS 영역에 영향을 미치지 않습니다. DNS 영역 위치는 항상 "전역"이며 표시되지 않습니다.

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

다음 예제에서는 새로운 'A' 레코드를 만드는 과정을 안내합니다. 다른 레코드 유형을 알아보고 기존 레코드를 수정하려면 [Azure Portal을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-portal.md)를 참조하세요. 

1. DNS 영역을 만든 후 Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 클릭합니다. 모든 리소스 페이지에서 **contoso.com** DNS 영역을 클릭합니다. 선택한 구독에 이미 여러 개의 리소스가 있는 경우 **이름을 기준으로 필터링...**에 **contoso.com**을 입력합니다. DNS 영역에 간편하게 액세스할 수 있는 상자입니다.

1. **DNS 영역** 페이지의 위쪽에서 **+ 레코드 집합**을 클릭하여 **레코드 집합 추가** 페이지를 엽니다.

1. **레코드 집합 추가** 페이지에서 다음 값을 입력하고 **확인**을 클릭합니다. 이 예에서는 A 레코드를 만들 것입니다.

   |**설정** | **값** | **세부 정보** |
   |---|---|---|
   |**Name**|www|레코드 이름|
   |**형식**|A| 만들 DNS 레코드 유형, 사용할 수 있는 값은 A, AAAA, CNAME, MX, NS, SRV, TXT 및 PTR입니다.  레코드 유형에 대한 자세한 내용은 [DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하세요.|
   |**TTL**|1|DNS 요청의 Time-to-Live입니다.|
   |**TTL 단위**|시간|TTL 값에 대한 시간 측정입니다.|
   |**IP 주소**|ipAddressValue| 이 값은 DNS 레코드가 확인하는 IP 주소입니다.|

## <a name="view-records"></a>레코드 보기

DNS 영역 페이지의 아래쪽에서 DNS 영역에 대한 레코드를 볼 수 있습니다. 모든 영역에 생성된 기본 DNS 및 SOA 레코드와 사용자가 생성한 모든 새 레코드가 표시됩니다.

![영역](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>이름 서버 업데이트

DNS 영역 및 레코드가 적절히 설정되었다면 Azure DNS 이름 서버를 사용하도록 도메인 이름을 구성해야 합니다. 이렇게 하면 인터넷에 있는 다른 사용자가 DNS 레코드를 찾을 수 있습니다.

영역에 대한 이름 서버는 Azure 포털에 제공됩니다.

![영역](./media/dns-getstarted-portal/viewzonens500.png)

이러한 이름 서버는 사용자가 도메인 이름을 구입한 도메인 이름 등록 기관에서 구성해야 합니다. 등록 기관에서 도메인의 이름 서버를 설정하는 옵션을 제공합니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

## <a name="delete-all-resources"></a>모든 리소스 삭제

이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 완료합니다.

1. Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 클릭합니다. 모든 리소스 페이지에서 **MyResourceGroup** 리소스 그룹을 클릭합니다. 선택한 구독에 이미 여러 개의 리소스가 있는 경우 **이름을 기준으로 필터링...**에 **MyResourceGroup**을 입력합니다. 리소스 그룹에 간편하게 액세스할 수 있는 상자입니다.
1. **MyResourceGroup** 페이지에서 **삭제** 단추를 클릭합니다.
1. 포털에서 삭제할 리소스 그룹의 이름을 입력하여 리소스 그룹 삭제를 확인해야 합니다. **삭제**를 클릭하고, 리소스 그룹 이름으로 *MyResourceGroup*을 입력한 다음 **삭제**를 클릭합니다. 리소스 그룹을 삭제하면 리소스 그룹 내 모든 리소스가 삭제되므로 리소스 그룹을 삭제하기 전에 리소스 그룹의 콘텐츠를 항상 확인해야 합니다. 포털에서 리소스 그룹 내 포함된 모든 리소스가 삭제된 다음 리소스 그룹 자체가 삭제됩니다. 이 프로세스는 몇 분 정도 걸립니다.


## <a name="next-steps"></a>다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.

Azure DNS에서 DNS 레코드 관리에 대한 자세한 내용은 [Azure Portal을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-portal.md)를 참조하세요.

