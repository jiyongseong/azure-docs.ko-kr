---
title: PowerShell을 사용하여 Azure DNS 시작 | Microsoft Docs
description: Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. PowerShell을 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: 050111f4a5e8459e89d049ccb879b5079ff68527
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-using-powershell"></a>PowerShell을 사용하여 Azure DNS 시작

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

이 문서에서는 Azure PowerShell을 사용하여 DNS 영역 및 레코드를 만드는 단계를 안내합니다. Azure Portal 또는 플랫폼 간 Azure CLI를 사용하여 이러한 단계를 수행할 수도 있습니다. Azure DNS는 사설 도메인 만들기도 지원합니다. 첫 번째 사설 DNS 영역 및 레코드를 만드는 방법에 대한 단계별 지침은 [PowerShell을 사용하여 Azure 사설 DNS 영역 시작](private-dns-getstarted-powershell.md)을 참조하세요.

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 아래에서는 이러한 각 단계에 대해 설명합니다.

이 지침에서는 이미 Azure PowerShell을 설치했고, 로그인했다고 가정합니다. 도움말은 [PowerShell을 사용하여 DNS 영역을 관리하는 방법](dns-operations-dnszones.md)을 참조하세요.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹이 생성됩니다. 다음은 명령을 표시합니다.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

DNS 영역은 `New-AzureRmDnsZone` cmdlet을 사용하여 생성됩니다. 다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```
Azure DNS는 이제 사설 DNS 영역(현재는 공개 미리 보기)도 지원합니다.  사설 DNS 영역에 대해 자세히 알아보려면 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요. 사설 DNS 영역을 만드는 방법은 [PowerShell을 사용하여 Azure DNS 사설 영역 시작](./private-dns-getstarted-powershell.md)을 참조하세요.

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

`New-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 다음 예제에서는 리소스 그룹 "MyResourceGroup"에서 DNS 영역 "contoso.com"에 상대적 이름 "www"가 포함된 레코드를 만듭니다. 레코드의 정규화된 이름은 “www.contoso.com”입니다. 레코드 형식은 "A"이고, IP 주소는 "1.2.3.4"이며, TTL은 3600초입니다.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

두 개 이상의 레코드가 있는 레코드 집합 등 다른 레코드 유형을 알아보거나 기존 레코드를 수정하려면 [Azure PowerShell을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요. 


## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>이름 서버 업데이트

DNS 영역 및 레코드가 적절히 설정되었다면 Azure DNS 이름 서버를 사용하도록 도메인 이름을 구성해야 합니다. 이렇게 하면 인터넷에 있는 다른 사용자가 DNS 레코드를 찾을 수 있습니다.

영역에 대한 이름 서버는 `Get-AzureRmDnsZone` cmdlet으로 지정됩니다.

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

이러한 이름 서버는 사용자가 도메인 이름을 구입한 도메인 이름 등록 기관에서 구성해야 합니다. 등록 기관에서 도메인에 대한 이름 서버를 설정하는 옵션을 제공합니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

## <a name="delete-all-resources"></a>모든 리소스 삭제

이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 수행합니다.

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.

Azure DNS에서 DNS 영역 관리에 대한 자세한 내용은 [PowerShell을 사용하여 Azure DNS에서 DNS 영역 관리](dns-operations-dnszones.md)를 참조하세요.

Azure DNS에서 DNS 레코드 관리에 대한 자세한 내용은 [PowerShell을 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요.

