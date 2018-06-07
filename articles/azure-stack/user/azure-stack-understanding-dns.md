---
title: Azure 스택에서 이해 Idn | Microsoft Docs
description: Azure 스택의 기능 및 Idn 기능 이해
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 9123160f42adea57c28dff265bd5b5dbbcbb7918
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724261"
---
# <a name="introducing-idns-for-azure-stack"></a>Azure 스택에 대 한 Idn 소개

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Idn은 외부 DNS 이름을 확인할 수 있도록 Azure 스택 네트워킹 기능 (예를 들어 http://www.bing.com.) 내부 가상 네트워크 이름 등록할 수도 있습니다. 이렇게 하면 IP 주소 대신 이름으로 같은 가상 네트워크에 Vm을 해결할 수 있습니다. 이 방법은 사용자 지정 DNS 서버 항목을 제공 하는 것을 제거 합니다. DNS에 대 한 자세한 내용은 참조는 [Azure DNS 개요](https://docs.microsoft.com/en-us/azure/dns/dns-overview)합니다.

## <a name="what-does-idns-do"></a>Idn의 기능은 무엇입니까?

Azure 스택의 Idn으로를 사용자 지정 DNS 서버 항목을 지정할 필요 없이 다음과 같은 기능을 얻을 있습니다.

- 테 넌 트 작업에 대 한 DNS 이름 확인 서비스를 공유합니다.
- 이름 확인 및 테 넌 트 가상 네트워크 내에서 DNS 등록에 대 한 권한 있는 DNS 서비스입니다.
- 테 넌 트 Vm에서에서 인터넷 이름 확인 방법에 대 한 재귀 DNS 서비스입니다. 테 넌 트가 인터넷 이름 (예를 들어 www.bing.com.)를 해결 하려면 사용자 지정 DNS 항목을 지정 하려면 더 이상 필요

사용자 고유의 DNS 상태로 전환 하 고 사용자 지정 DNS 서버를 사용할 수 있습니다. 그러나 Idn을 사용 하 여 인터넷 DNS 이름을 확인 하 고 동일한 가상 네트워크의 다른 Vm에 연결할 수, 사용자 지정 DNS 항목을 만들 필요가 없습니다.

## <a name="what-doesnt-idns-do"></a>Idn의 하지 않는 무엇입니까?

가상 네트워크 외부에서 확인 될 수 있는 이름에 대 한 DNS 레코드 만들기는 어떤 Idn을 할 수 없습니다.

Azure에서 공용 IP 주소와 연결 된 DNS 이름 레이블을 지정 하는 옵션도 있습니다. 레이블 (접두사)을 선택할 수는 있지만 Azure 공용 IP 주소를 만들 수 있는 영역을 기반으로 하는 접미사를 선택 합니다.

![DNS 이름 레이블의 예](media/azure-stack-understanding-dns-in-tp2/image3.png)

이전 그림에서 볼 수 있듯이 Azure는 "A" 레코드에서에서 만들 DNS 영역 아래에 지정 된 DNS 이름 레이블을 대 한 **westus.cloudapp.azure.com**합니다. 작성 하는 접두사와 접미사 결합 되는 [정규화 된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)는에서 확인할 수 위치는 공용 인터넷에서 합니다.

Azure 스택 지원 Idn 내부 이름 등록에 대 한 되므로 다음 수행할 수 없습니다.

- 기존 호스트 된 DNS 영역 (예를 들어 local.azurestack.external.)에서 DNS 레코드 만들기
- DNS 영역을 (예: Contoso.com.)을 만듭니다.
- 사용자 고유의 사용자 지정 DNS 영역에서 레코드를 만듭니다.
- 도메인 이름 구매를 지원 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 스택에 DNS를 사용 하 여](azure-stack-dns.md)
