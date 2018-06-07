---
title: Azure 스택의 DNS | Microsoft Docs
description: Azure 스택에 DNS를 사용 하 여
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="using-dns-in-azure-stack"></a>Azure 스택에 DNS를 사용 하 여

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 다음 이름을 DNS (도메인) 기능을 지원 합니다.

* DNS 호스트 이름 확인
* DNS 영역 및 API를 사용 하 여 레코드 생성 및 관리

## <a name="support-for-dns-hostname-resolution"></a>DNS 호스트 이름 확인에 대 한 지원

공용 IP 리소스에 대 한 DNS 도메인 이름 레이블을 지정할 수 있습니다. Azure 스택 사용 하 여 *domainnamelabel.location*. cloudapp.azurestack.external 레이블 이름 및 Azure 스택의 주소이 공용 IP에 지도 대 한 DNS 서버를 관리 합니다.

예를 들어 있는 공용 IP 리소스를 만들 경우 **contoso** 로컬 Azure 스택 위치에 있는 도메인 이름 레이블과 [정규화 된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **contoso.local.cloudapp.azurestack.external** 리소스의 공용 IP 주소로 확인 합니다. Azure 스택에서 공용 IP 주소를 가리키는 CNAME 레코드는 사용자 지정 도메인을 만드는이 FQDN을 사용할 수 있습니다.

이름 확인에 대 한 자세한 내용은 참조는 [DNS 확인](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 문서.

> [!IMPORTANT]
> 도메인 이름 레이블을 만들 Azure 스택 위치 내에서 고유 해야 합니다.

다음 화면 캡처 프로그램은 **공용 IP 주소 만들기** 포털을 사용 하 여 공용 IP 주소를 만들기 위한 대화 상자.

![공용 IP 주소 만들기](media/azure-stack-whats-new-dns/image01.png)

**예제 시나리오**

부하 분산 장치는 웹 응용 프로그램에서 요청을 처리 해야 합니다. 뒤에 부하 분산 장치 하나 이상의 가상 컴퓨터에서 실행 중인 웹 사이트입니다. 부하 분산 된 웹 사이트는 IP 주소 대신 DNS 이름을 사용 하 여 액세스할 수 있습니다.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>DNS 영역 및 API를 사용 하 여 레코드 만들기 및 관리

수 만들고 DNS 영역 및 Azure 스택의 레코드를 관리 합니다.

Azure 스택 Azure의 DNS Api와 일치 하는 Api를 사용 하 여 Azure의 같은 DNS 서비스를 제공 합니다.  Azure 스택 dns에서 도메인을 호스트 하 여 동일한 자격 증명, Api 및 도구를 사용 하 여 DNS 레코드를 관리할 수 있습니다. 동일한 대금 청구를 사용 하 고 다른 Azure 서비스를 지원 수도 있습니다.

Azure 스택 DNS 인프라는 Azure의 보다 더욱 간결 합니다. Azure 스택 배포의 위치와 크기에 영향을 줍니다 DNS 범위, 배율 및 성능. 즉, 성능, 가용성, 글로벌 메일 및 고가용성 배포 마다 달라질 수 있습니다.

## <a name="comparison-with-azure-dns"></a>Azure DNS와 비교

Azure 스택의 DNS를 azure에서 DNS 유사 하지만을 이해 해야 하는 주요 예외가 있습니다.

* **AAAA 레코드를 지원 하지 않습니다.**

    Azure 스택 IPv6 주소를 지원 하지 않으므로 azure 스택 AAAA 레코드를 지원 하지 않습니다.  이것이 Azure에서 DNS 및 Azure 스택 간의 주요 차이점입니다.
* **다중 테 넌 트가 아닙니다.**

    Azure 스택에서 DNS 서비스는 다중 테 넌 트입니다. 각 테 넌 트 동일한 DNS 영역을 만들 수 없습니다. 첫 번째 구독 영역을 만들려고 시도 성공 하 고 이후 요청 실패.  알려진된 문제 및 Azure와 Azure 스택 DNS 간의 주요 차이점 않습니다. 이 문제는 향후 릴리스에서 해결 됩니다.
* **태그, 메타 데이터 및 Etags**

    Azure 스택 태그, 메타 데이터, Etag, 및 제한을 처리 하는 방법에서 약간의 차이가 있습니다.

Azure DNS에 대 한 자세한 참조 [DNS 영역 및 기록](../../dns/dns-zones-records.md)합니다.

### <a name="tags-metadata-and-etags"></a>태그, 메타 데이터 및 Etags

**태그**

Azure 스택 DNS Azure 리소스 관리자 태그를 사용 하 여 DNS 영역 리소스를 지원 합니다. DNS 레코드 다음에 설명 된 대로 설정 하는 대신 'metadata'가 지원 되지만 태그 DNS 레코드 집합에서 지원 하지 않습니다.

**Metadata**

레코드 집합 태그에는 대신 Azure 스택 DNS 'metadata'를 사용 하 여 레코드 집합에 주석 지정을 지원 합니다. 태그와 유사한 메타데이터를 사용하면 각 레코드 집합에 이름-값 쌍을 연결할 수 있습니다. 예를 들어이 각 레코드 집합의 목적으로 기록해 두면 유용할 수 있습니다. 태그와 달리, 메타데이터는 Azure 청구서를 필터링하여 표시하는 데 사용할 수 없으며 Azure Resource Manager 정책에서 지정할 수 없습니다.

**Etag는**

두 사용자 또는 두 프로세스가 동시에 DNS 레코드 수정을 시도한다고 가정합니다. 어느 쪽이 성공할까요? 그리고 성공한 사용자 자신이 다른 사용자가 만든 변경 내용을 덮어썼다는 것을 알고 있을까요?

Azure 스택 DNS Etag를 사용 하 여 안전 하 게 같은 리소스에 대 한 동시 변경 처리. Etag는 Azure 리소스 관리자 '태그'에서 구분 됩니다. 각 DNS 리소스(영역 또는 레코드 집합)에는 Etag가 연결되어 있습니다. 리소스를 검색할 때마다 해당 Etag도 검색됩니다. 리소스를 업데이트할 때 다시 전달 하기 위한 Etag Azure 스택 DNS를 확인할 수 있도록 Etag 서버 일치 선택할 수 있습니다. 리소스를 업데이트할 때마다 Etag가 다시 생성되므로 Etag 불일치는 동시 변경이 발생했음을 나타냅니다. 새 리소스를 만들 때도 Etag를 사용하여 리소스가 존재하지 않는지 확인합니다.

기본적으로 Azure 스택 DNS PowerShell Etag를 사용 하 여 영역에 대 한 동시 변경 차단 하 고 레코드 집합입니다. 선택적 *-Overwrite* 스위치를 사용하여 Etag 검사를 무시할 수 있으며, 이 경우 발생한 동시 변경 내용을 덮어쓰게 됩니다.

Azure 스택 DNS REST API 수준에서 Etag는 HTTP 헤더를 사용 하 여 지정 됩니다. 해당 동작은 다음 표에 나와 있습니다.

| 헤더 | 동작|
|--------|---------|
| 없음   | PUT 항상 성공(Etag 검사 안 함)|
| If 일치| 리소스가 있고 Etag가 일치하는 경우에만 PUT 성공|
| If-match *| 리소스가 있는 경우에만 PUT 성공|
| If-none-match *| 리소스가 없는 경우에만 PUT 성공|

### <a name="limits"></a>제한

Azure 스택 DNS를 사용 하는 경우는 다음과 같은 기본 제한 값에 적용 됩니다.

| 리소스| 기본 제한|
|---------|--------------|
| 구독당 영역| 100|
| 영역당 레코드 집합| 5,000|
| 레코드 집합당 레코드| 20|

## <a name="next-steps"></a>다음 단계

[Azure 스택에 대 한 Idn 소개](azure-stack-understanding-dns.md)
