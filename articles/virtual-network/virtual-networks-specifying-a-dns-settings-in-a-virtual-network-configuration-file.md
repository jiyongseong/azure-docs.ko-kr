---
title: Virtual Network 구성 파일에서 DNS 설정 지정 | Microsoft Docs
description: 클래식 배포 모델에서 가상 네트워크 구성 파일을 사용하여 가상 네트워크에서 DNS 서버 설정을 변경하는 방법
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: ed7f02d3e389db3bc772c4fcb00a7b3877d60173
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>가상 네트워크 구성 파일에서 DNS 설정 지정
네트워크 구성 파일에는 도메인 이름 시스템(DNS) 설정 지정에 사용할 수 있는 두 요소 **DnsServers** 및 **DnsServerRef**가 있습니다. **DnsServers** 요소에 해당 IP 주소 및 참조 이름를 지정하여 DNS 서버 목록을 추가할 수 있습니다. 그런 다음 **DnsServerRef** 요소를 사용하여 가상 네트워크 내의 다른 네트워크 사이트에 대해 사용될 DnsServers 요소의 DNS 서버 항목을 지정할 수 있습니다.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다.

네트워크 구성 파일에는 다음과 같은 요소가 포함될 수 있습니다. 각 요소의 제목은 요소 값 설정에 대한 추가 정보를 제공하는 페이지에 연결됩니다.

> [!IMPORTANT]
> 네트워크 구성 파일 구성에 대한 자세한 내용은 [네트워크 구성 파일을 사용하여 Virtual Network 구성](virtual-networks-using-network-configuration-file.md)을 참조하세요. 네트워크 구성 파일에 포함된 각 요소에 대한 자세한 내용은 [Azure Virtual Network 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.
> 
> 

[Dns 요소](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **DnsServer** 요소 내 **이름** 특성은 **DnsServerRef** 요소에 대한 참조로만 사용됩니다. DNS 서버에 대한 호스트 이름은 표시되지 않습니다. 각 **DnsServer** 특성 값은 전체 Microsoft Azure 구독에서 고유해야 합니다.
> 
> 

[Virtual Network 사이트 요소](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Virtual Network 사이트 요소에 대해 이 설정을 지정하려면 DNS 요소에서 사전 정의되어야 합니다. Virtual Network 사이트 요소 내 DnsServerRef *이름*은 DnsServer *이름*에 대한 DNS 요소에 지정된 이름 값을 참조해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Network 구성 스키마](http://go.microsoft.com/fwlink/?LinkId=248093)이해
* [Azure 서비스 구성 스키마](https://msdn.microsoft.com/library/windowsazure/ee758710)이해
* [네트워크 구성 파일을 사용하여 가상 네트워크 구성](virtual-networks-using-network-configuration-file.md)

