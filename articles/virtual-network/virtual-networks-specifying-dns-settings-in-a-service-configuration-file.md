---
title: 서비스 구성 파일에서 DNS 설정 지정 | Microsoft Docs
description: 가상 네트워크에 대한 서비스 구성 파일을 사용하여 사용자 지정 DNS 설정 지정
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 009206f1e0ba848538ed2c666032a63051d062e4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>서비스 구성 파일에서 DNS 설정 지정
## <a name="dns-elements"></a>DNS 요소
서비스 구성 파일은 서비스에서 사용할 도메인 이름 시스템(DNS) 서버에 대한 IPv4 주소 목록과 함께 DnsServers 요소를 포함할 수 있습니다. 서비스 구성 파일의 설정은 네트워크 구성 파일의 설정보다 우선합니다. 자세한 내용은 [Azure 서비스 구성 스키마(.cscfg 파일)](https://msdn.microsoft.com/library/azure/ee758710.aspx)를 참조하세요.

**네트워크 구성 요소**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> **DnsServer** 요소 내 **이름** 특성은 참조 이름으로만 사용됩니다. DNS 서버에 대한 호스트 이름은 표시되지 않습니다. 각 **DnsServer** 특성 값은 전체 Microsoft Azure 구독에서 고유해야 합니다.
> 
> 

## <a name="see-also"></a>참고 항목
[Azure 서비스 구성 스키마(.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure Virtual Network 구성 스키마](http://go.microsoft.com/fwlink/?LinkId=248093)

[네트워크 구성 파일을 사용하여 Virtual Network 구성](http://go.microsoft.com/fwlink/?LinkId=248094)

[관리 포털의 Virtual Network 설정 정보](http://go.microsoft.com/fwlink/?LinkId=248092)

