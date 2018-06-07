---
title: PowerShell을 사용하여 Azure DNS 사설 영역 시작 | Microsoft Docs
description: Azure DNS에 사설 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. PowerShell을 사용하여 첫 번째 사설 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>PowerShell을 사용하여 Azure DNS 사설 영역 시작

이 문서에서는 Azure PowerShell을 사용하여 첫 번째 사설 DNS 영역 및 레코드를 만드는 단계를 안내합니다.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 가상 네트워크에 사설 DNS 영역에 게시하려면 영역 내에서 레코드를 확인하도록 허용된 가상 네트워크 목록을 지정합니다.  이것을 ‘확인 가상 네트워크’라고 합니다.  또한 VM이 생성되고, IP가 변경되고, 제거될 때마다 Azure DNS가 호스트 이름 레코드를 유지 관리하게 되는 가상 네트워크를 지정할 수 있습니다.  이것을 '등록 가상 네트워크'라고 합니다.

# <a name="get-the-preview-powershell-modules"></a>미리 보기 PowerShell 모듈 가져오기
이러한 지침에서는 사설 영역 기능에 필요한 모듈을 갖고 있는지 확인하는 것을 포함하여 사용자가 이미 Azure PowerShell을 설치하고 서명한 것으로 가정합니다. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹이 생성됩니다. 다음 예제에서는 명령을 보여줍니다.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>DNS 사설 영역 만들기

DNS 영역은 ZoneType 매개 변수 값으로 "Private"을 사용하여 `New-AzureRmDnsZone` cmdlet으로 만듭니다. 다음 예제에서는 *contoso.local*이라는 DNS 영역을 *MyResourceGroup*이라는 리소스 그룹에 만들고, *MyAzureVnet*이라는 가상 네트워크에서 DNS 영역을 사용할 수 있도록 합니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

ZoneType 매개 변수를 생략하면 영역이 공용 영역으로 만들어지므로 사설 영역을 만들어야 하는 경우 이 매개 변수가 필수입니다. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Azure가 자동으로 영역에 호스트 이름 레코드를 만들어야 하는 경우 *ResolutionVirtualNetworkId* 대신 *RegistrationVirtualNetworkId* 매개 변수를 사용합니다.  가상 네트워크 등록은 자동으로 해상도에 대해 사용이 설정됩니다.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

`New-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 다음 예제에서는 리소스 그룹 “MyResourceGroup”의 DNS 영역 “contoso.com”에 상대적 이름 “db”가 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 “db.contoso.local”입니다. 레코드 형식은 “A”이고, IP 주소는 “10.0.0.4”이며, TTL은 3600초입니다.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

두 개 이상의 레코드가 있는 레코드 집합 등 다른 레코드 유형을 알아보거나 기존 레코드를 수정하려면 [Azure PowerShell을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요. 

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>DNS 사설 영역 나열

`Get-AzureRmDnsZone`에서 영역 이름을 생략하면, 리소스 그룹의 모든 영역을 열거할 수 있습니다. 이 작업은 영역 개체의 배열을 반환합니다.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

`Get-AzureRmDnsZone`에서 영역 이름 및 리소스 그룹 이름을 모두 생략하여 Azure 구독의 모든 영역을 열거할 수 있습니다.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>DNS 사설 영역 업데이트

`Set-AzureRmDnsZone`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 이 cmdlet은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 쓰기 가능한 영역 속성은 현재 [영역 리소스에 대한 Azure Resource Manager ‘태그’](dns-zones-records.md#tags) 그리고 사설 영역에 대한 'RegistrationVirtualNetworkId' 및 'ResolutionVirtualNetworkId' 매개 변수로 제한됩니다.

아래 예제에서는 한 영역에 연결된 등록 가상 네트워크를 MyNewAzureVnet이라고 하는 새로운 등록 가상 네트워크로 바꿉니다.

create와는 달리, update에 대한 ZoneType 매개 변수를 지정하면 안 됩니다. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

아래 예제에서는 한 영역에 연결된 확인 가상 네트워크를 MyNewAzureVnet이라고 하는 새로운 확인 가상 네트워크로 바꿉니다.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>DNS 사설 영역 삭제

공용 영역과 마찬가지로 `Remove-AzureRmDnsZone` cmdlet을 사용하여 DNS 사설 영역을 삭제할 수 있습니다.

> [!NOTE]
> DNS 영역을 삭제하면 영역 내의 모든 DNS 레코드도 삭제됩니다. 이 작업은 취소할 수 없습니다. DNS 영역을 사용 중인 경우 영역이 삭제되면 영역을 사용하는 서비스가 실패합니다.
>
>실수로 영역이 삭제되는 것을 방지하려면 [DNS 영역 및 레코드를 보호하는 방법](dns-protect-zones-recordsets.md)을 참조하세요.

다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 삭제합니다.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>영역 이름 및 리소스 그룹 이름을 사용하여 영역을 지정합니다.

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone 개체를 사용하여 영역을 지정합니다.

`Get-AzureRmDnsZone`에서 반환된 `$zone` 개체를 사용하여 삭제될 영역을 지정할 수 있습니다.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

영역 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>확인 메시지 표시

`New-AzureRmDnsZone`, `Set-AzureRmDnsZone` 및 `Remove-AzureRmDnsZone` cmdlet은 모두 확인 메시지를 표시하도록 지원합니다.

`$ConfirmPreference` PowerShell 기본 설정 변수 값에 `Medium` 이하의 값이 있는 경우 `New-AzureRmDnsZone` 및 `Set-AzureRmDnsZone`은 확인 메시지를 표시합니다. DNS 영역을 삭제할 경우 미치는 영향이 클 수 있으므로 `Remove-AzureRmDnsZone` cmdlet은 `$ConfirmPreference` PowerShell 변수가 `None` 이외의 값을 갖는 경우 확인 메시지를 표시합니다.

`$ConfirmPreference`의 기본값은 `High`이므로 `Remove-AzureRmDnsZone`는 기본적으로 확인 메시지를 표시합니다.

`-Confirm` 매개 변수를 사용하여 현재 `$ConfirmPreference` 설정을 재정의할 수 있습니다. `-Confirm` 또는 `-Confirm:$True`를 지정하는 경우 cmdlet은 실행하기 전에 확인을 위한 메시지를 표시합니다. `-Confirm:$False`을 지정하는 경우 cmdlet은 확인을 위한 메시지를 표시하지 않습니다.

`-Confirm` 및 `$ConfirmPreference`에 대한 자세한 내용은 [기본 설정 변수 정보](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables)를 참조하세요.


## <a name="delete-all-resources"></a>모든 리소스 삭제

이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 수행합니다.

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

사설 DNS 영역에 대해 자세히 알아보려면 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요.

Azure DNS에서 사설 영역으로 실현할 수 있는 몇 가지 일반적인 시나리오를 [사설 영역 시나리오](./private-dns-scenarios.md)에서 확인합니다.

Azure DNS에서 DNS 레코드 관리에 대한 자세한 내용은 [PowerShell을 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요.

