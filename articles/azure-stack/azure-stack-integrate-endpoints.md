---
title: Azure 데이터 센터 통합 스택-끝점 게시 | Microsoft Docs
description: 데이터 센터에서 Azure 스택 끝점을 게시 하는 방법을 알아봅니다
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: d7384d8d1d8c0378e1a9dd68a4f7b71196330b8e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736697"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure 데이터 센터 통합 스택-끝점 게시
Azure 스택 인프라 역할에 대 한 가상 IP 주소 (Vip)를 설정합니다. 해당이 Vip는 공용 IP 주소 풀에서 할당 됩니다. 각 VIP 소프트웨어 정의 네트워크 계층에서 액세스 제어 목록 (ACL)로 보호 됩니다. Acl은 솔루션을 더욱 강화 하기 위해 각기 물리적 스위치 (될 수 있습니다 앞뒤 및 BMC)도 사용 됩니다. 배포 시에 지정 된 외부 DNS 영역에서 각 끝점에 대 한 DNS 항목이 생성 됩니다.


다음 아키텍처 다이어그램에는 여러 네트워크 계층 및 Acl 보여 줍니다.

![구조적 그림](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>포트 및 프로토콜 (인바운드)

인프라 집합이 Vip는 외부 네트워크에 Azure 스택 끝점 게시에 필요 합니다. *끝점 (VIP)* 표에서 각 끝점의 필요한 포트 및 프로토콜입니다. SQL 리소스 공급자와 같은 추가 리소스 공급자를 요구 하는 끝점에 대 한 특정 리소스 공급자 배포 설명서를 참조 하십시오.

내부 인프라 Vip는 Azure 스택 게시에 필요 하지 않기 때문에 나열 되지 않습니다.

> [!NOTE]
> 사용자 Vip는 동적 이며 Azure 스택 연산자가 없는 제어를 사용한 사용자가 정의 합니다.


|끝점 (VIP)|DNS 호스트 A 레코드|프로토콜|포트|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|포털 (관리자)|Adminportal 합니다.  *&lt;지역 >.&lt; fqdn >*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure 리소스 관리자 (관리자)|Adminmanagement 합니다.  *&lt;지역 >.&lt; fqdn >*|HTTPS|443<br>30024|
|포털 (사용자)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure 리소스 관리자 (사용자)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|그래프|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|인증서 해지 목록|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 및 UDP|53|
|주요 자격 증명 모음 (사용자)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|주요 자격 증명 모음 (관리자)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|저장소 큐|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|저장소 테이블|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|저장소 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 리소스 공급자|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 리소스 공급자|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (azure 리소스 관리자)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>포트 및 Url (아웃 바운드)

Azure 스택 투명 프록시 서버만 지원합니다. 배포에서 여기서 기존 프록시 서버에 투명 프록시 업링크 허용 해야 하는 다음 포트 및 Url 아웃 바운드 통신에 대 한:


|목적|URL|프로토콜|포트|
|---------|---------|---------|---------|
|ID|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|마켓플레이스에서 배포|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|패치 및 업데이트|https://&#42;.azureedge.net|HTTPS|443|
|등록|https://management.azure.com|HTTPS|443|
|사용 현황|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net|HTTPS|443|
|Windows Defender|. wdcp.microsoft.com<br>. wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|



## <a name="next-steps"></a>다음 단계

[Azure 스택 PKI 요구 사항](azure-stack-pki-certs.md)
