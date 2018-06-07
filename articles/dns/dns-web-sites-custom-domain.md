---
title: 웹앱에 대한 사용자 지정 DNS 레코드 만들기 | Microsoft Docs
description: Azure DNS를 사용하여 웹앱에 대한 사용자 지정 도메인 DNS 레코드를 만드는 방법입니다.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: 00a56a2683e95e70bb13acd6b936e766f044e1cd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기

Azure DNS를 사용하여 웹앱에 대한 사용자 지정 도메인을 호스트할 수 있습니다. 예를 들어, Azure 웹앱을 만드는 중이며 사용자가 contoso.com 또는 www.contoso.com을 FQDN으로 사용하여 액세스하도록 설정하려고 합니다.

이렇게 하려면, 두 개의 레코드를 만들어야 합니다.

* contoso.com을 가리키는 루트 "A" 레코드
* A 레코드를 가리키는 www 이름에 대한 "CNAME" 레코드

Azure에서 웹앱에 대한 A 레코드를 만드는 경우 웹앱의 기본 IP 주소가 변경되면 A 레코드를 수동으로 업데이트해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

시작하기 전에, 먼저 Azure DNS에서 DNS 영역을 만들고 등록 기관의 영역을 Azure DNS로 위임해야 합니다.

1. DNS 영역을 만들려면 [DNS 영역 만들기](dns-getstarted-create-dnszone.md)의 단계를 수행합니다.
2. DNS를 Azure DNS로 위임하려면 [DNS domain delegation](dns-domain-delegation.md)(DNS 도메인 위임)의 단계를 수행합니다.

영역을 만들어서 Azure DNS에 위임한 후에는, 사용자 지정 도메인에 대한 레코드를 만들 수 있습니다.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. 사용자 지정 도메인에 대한 A 레코드 만들기

A 레코드는 이름을 해당 IP 주소에 매핑하는 데 사용됩니다. 다음 예제에서는 IPv4 주소에 @을 A 레코드로 할당합니다.

### <a name="step-1"></a>1단계

A 레코드를 만들고 $rs 변수에 할당합니다.

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>2단계

할당된 $rs 변수를 사용하여 이전에 만든 레코드 집합 "\@\" 에 IPv4 값을 추가합니다. 할당된 IPv4 값은 웹앱의 IP 주소가 됩니다.

웹앱의 IP 주소를 찾으려면 [Azure App Service에서 사용자 지정 도메인 이름 구성](../app-service/app-service-web-tutorial-custom-domain.md)의 단계를 따르세요.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>3단계

레코드 집합의 변경 내용을 커밋합니다. `Set-AzureRMDnsRecordSet`를 사용하여 레코드 집합의 변경 내용을 Azure DNS로 업로드합니다.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. 사용자 지정 도메인에 대한 CNAME 레코드 만들기

Azure DNS에서 이미 도메인을 관리하고 있는 경우( [DNS 도메인 위임](dns-domain-delegation.md)참조), 다음 예제를 사용하여 contoso.azurewebsites.net에 대한 CNAME 레코드를 만들 수 있습니다.

### <a name="step-1"></a>1단계

PowerShell을 열고 새 CNAME 레코드 집합을 만든 다음 $rs 변수에 할당합니다. 이 예제에서는 "contoso.com"이라는 DNS 영역에 "time to live"가 600초인 레코드 집합 형식 CNAME이 생성됩니다.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

다음 예제는 응답입니다.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>2단계

CNAME 레코드 집합을 만든 후에는 웹앱을 가리키는 별칭 값을 만들어야 합니다.

이전에 할당된 변수 "$rs"를 통해 아래 PowerShell 명령을 사용하여 웹앱 contoso.azurewebsites.net에 대한 별칭을 만들 수 있습니다.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

다음 예제는 응답입니다.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>3단계

`Set-AzureRMDnsRecordSet` cmdlet을 사용하여 변경 내용을 커밋합니다.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

아래와 같이 nslookup으로 "www.contoso.com"을 쿼리하여 레코드가 올바르게 생성되었는지 확인할 수 있습니다.

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>웹앱에 대한 "awverify" 레코드 만들기

웹앱에 대해 A 레코드를 사용하려는 경우 사용자 지정 도메인의 소유자가 맞는지 확인하도록 검증 프로세스를 수행해야 합니다. 이 검증 단계는 "awverify"라는 특수 CNAME 레코드를 만들어 수행됩니다. 이 섹션은 A 레코드에만 적용됩니다.

### <a name="step-1"></a>1단계

"awverify" 레코드를 만듭니다. 아래 예제에서는 contoso.com에 대한 "awverify" 레코드를 만들어서 사용자 지정 도메인에 대한 소유권을 확인합니다.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

다음 예제는 응답입니다.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>2단계

"awverify" 레코드 집합이 생성되면, CNAME 레코드 집합 별칭을 할당합니다. 아래 예제에서는 CNAME 레코드 집합 별칭을 awverify.contoso.azurewebsites.net에 할당합니다.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

다음 예제는 응답입니다.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>3단계

아래 명령과 같이 `Set-AzureRMDnsRecordSet cmdlet`을 사용하여 변경 내용을 커밋합니다.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>다음 단계

[Configuring a custom domain name for App Service](../app-service/app-service-web-tutorial-custom-domain.md) (앱 서비스에 대한 사용자 지정 도메인 이름 구성)의 단계에 따라 사용자 지정 도메인을 사용하도록 웹앱을 구성합니다.
