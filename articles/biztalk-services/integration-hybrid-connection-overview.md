---
title: "하이브리드 연결 개요 | Microsoft Docs"
description: "하이브리드 연결, 보안, TCP 포트 및 지원되는 구성에 대해 알아봅니다. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 819af52bb10c9ffcb7e1133437f6d0afbe6105ae
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="hybrid-connections-overview"></a>하이브리드 연결 개요

> [!IMPORTANT]
> BizTalk 하이브리드 연결은 사용 중지되고 App Service 하이브리드 연결로 대체됩니다. 기존 BizTalk 하이브리드 연결 관리 방법을 비롯한 자세한 내용은 [Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md)을 참조하세요.

하이브리드 연결을 소개하고, 지원되는 구성과 필수 TCP 포트를 설명합니다.

## <a name="what-is-a-hybrid-connection"></a>하이브리드 연결 정의
하이브리드 연결은 Azure BizTalk Services의 기능입니다. 하이브리드 연결은 방화벽 뒤에서 Azure App Service의 Web Apps 기능(이전의 Websites) 및 Azure App Service의 Mobile Apps 기능(이전의 Mobile Services)을 온-프레미스 리소스에 연결하는 쉽고 편리한 방법을 제공합니다.

![하이브리드 연결][HCImage]

하이브리드 연결은 다음과 같은 이점을 제공합니다.

* Web Apps 및 Mobile Apps는 기존 온-프레미스 데이터 및 서비스에 안전하게 액세스할 수 있습니다.
* 여러 Web Apps 또는 Mobile Apps가 하나의 하이브리드 연결을 공유하여 온-프레미스 리소스에 액세스할 수 있습니다.
* 네트워크에 액세스하려면 최소의 TCP 포트가 필요합니다.
* 하이브리드 연결을 사용하는 응용 프로그램은 하이브리드 연결을 통해 게시되는 특정 온-프레미스 리소스에만 액세스합니다.
* SQL Server, MySQL, HTTP Web API 및 대부분의 사용자 지정 웹 서비스와 같이 정적 TCP 포트를 사용하는 모든 온-프레미스 리소스에 연결할 수 있습니다.
  
  > [!NOTE]
  > 동적 포트(예: FTP 수동 모드 또는 확장 수동 모드)를 사용하는 TCP 기반 서비스는 현재 지원되지 않습니다. LDAP도 지원되지 않습니다. LDAP는 고정 TCP 포트를 사용하지만 UDP도 사용할 수 있기 때문에 지원되지 않습니다.
  > 
  > 
* Web Apps에서 지원하는 모든 프레임워크(.NET, PHP, Java, Python, Node.js) 및 Mobile Apps에서 지원하는 모든 프레임워크(Node.js, .NET)에서 사용할 수 있습니다.
* Web Apps 및 Mobile Apps는 웹이나 Mobile Apps가 로컬 네트워크에 위치하는 것처럼 정확히 동일한 방식으로 온-프레미스 리소스에 액세스할 수 있습니다. 예를 들어 온-프레미스에 사용되는 동일한 연결 문자열을 Azure에서도 사용할 수 있습니다.

또한 하이브리드 연결은 다음을 비롯하여 하이브리드 응용 프로그램에서 액세스하는 회사 리소스를 엔터프라이즈 관리자를 통해 제어하고 확인할 수 있도록 합니다.

* 관리자는 그룹 정책 설정을 사용하여 네트워크의 하이브리드 연결을 허용하고, 하이브리드 응용 프로그램을 통해 액세스할 수 있는 리소스를 지정할 수 있습니다.
* 회사 네트워크의 이벤트 및 감사 로그를 사용하여 하이브리드 연결을 통해 액세스하는 리소스를 확인할 수 있습니다.

## <a name="example-scenarios"></a>예제 시나리오
하이브리드 연결은 다음 프레임워크 및 응용 프로그램 조합을 지원합니다.

* .NET Framework에서 SQL Server 액세스
* .NET Framework에서 WebClient를 사용하여 HTTP/HTTPS 서비스 액세스
* PHP에서 SQL Server, MySQL 액세스
* Java에서 SQL Server, MySQL 및 Oracle 액세스
* Java에서 HTTP/HTTPS 서비스 액세스

하이브리드 연결을 사용하여 온-프레미스 SQL Server에 액세스할 때는 다음을 고려하세요.

* SQL Express 명명된 인스턴스는 정적 포트를 사용하도록 구성해야 합니다. 기본적으로 SQL Express 명명된 인스턴스는 동적 포트를 사용합니다.
* SQL Express 기본 인스턴스는 정적 포트를 사용하지만 TCP를 사용하도록 설정해야 합니다. 기본적으로 TCP는 사용되도록 설정되어 있지 않습니다.
* 클러스터링 또는 가용성 그룹을 사용할 때는 현재 `MultiSubnetFailover=true` 모드가 지원되지 않습니다.
* `ApplicationIntent=ReadOnly` 은(는) 현재 지원되지 않습니다.
* Azure 응용 프로그램 및 온-프레미스 SQL Server에서 지원하는 종단 간 권한 부여 방법으로 SQL 인증이 필요할 수 있습니다.

## <a name="security-and-ports"></a>보안 및 포트
하이브리드 연결은 SAS(공유 액세스 서명) 권한 부여를 사용하여 Azure 응용 프로그램 및 온-프레미스 하이브리드 연결 관리자에서 하이브리드 연결로의 연결에 대한 보안을 유지합니다. 응용 프로그램과 온-프레미스 하이브리드 연결 관리자에 대해 별도의 연결 키가 만들어집니다. 이러한 연결 키는 별도로 롤오버되고 해지됩니다.

하이브리드 연결은 응용 프로그램과 온-프레미스 하이브리드 연결 관리자로 원활하고 안전하게 키를 배포합니다.

[하이브리드 연결 만들기 및 관리](integration-hybrid-connection-create-manage.md)(영문)를 참조하세요.

*응용 프로그램 권한 부여가 하이브리드 연결과는 별개임*. 적절한 어떤 권한 부여 방법도 사용될 수 있습니다. 권한 부여 방법은 Azure 클라우드와 온-프레미스 구성 요소에서 지원되는 종단 간 권한 부여 방법에 따라 좌우됩니다. 예를 들어 Azure 응용 프로그램은 온-프레미스 SQL Server에 액세스합니다. 이 시나리오에서 SQL 권한 부여는 종단 간에 지원되는 권한 부여 방법일 수 있습니다.

#### <a name="tcp-ports"></a>TCP 포트
하이브리드 연결에는 개인 네트워크의 아웃바운드 TCP 또는 HTTP 연결만 필요합니다. 방화벽 포트를 열거나 네트워크에 대한 모든 인바운드 연결을 허용하도록 네트워크 경계 구성을 변경할 필요가 없습니다.

하이브리드 연결에는 다음과 같은 TCP 포트가 사용됩니다.

| 포트 | 필요한 이유 |
| --- | --- |
| 9350 - 9354 |이러한 포트는 데이터 전송에 사용됩니다. Service Bus 릴레이 관리자는 포트 9350를 조사하여 TCP 연결을 사용할 수 있는지 확인합니다. 사용 가능한 경우 포트 9352도 사용 가능한 것으로 가정합니다. 데이터 트래픽이 포트 9352를 통해 이동합니다. <br/><br/>이러한 포트에 아웃 바운드 연결을 허용합니다. |
| 5671 |데이터 트래픽에 포트 9352를 사용하는 경우 포트 5671을 제어 채널로 사용합니다. <br/><br/>이 포트에 아웃 바운드 연결을 허용합니다. |
| 80, 443 |이러한 포트는 Azure에 일부 데이터를 요청하는 데 사용됩니다. 또한 포트 9352 및 5671를 사용할 수 없는 경우 *다음으로* 포트 80 및 443가 데이터 전송 및 제어 채널로 사용되는 대체(fallback) 포트입니다.<br/><br/>이러한 포트에 아웃 바운드 연결을 허용합니다. <br/><br/>**참고** 다른 TCP 포트 대신 이를 대체(fallback) 포트로 사용하는 것이 좋습니다. HTTP/WebSocket이 데이터 채널에 대한 기본 TCP 대신 프로토콜로 사용됩니다. 성능이 저하될 수 있습니다. |

## <a name="next-steps"></a>다음 단계
[Create and manage Hybrid Connections](integration-hybrid-connection-create-manage.md)

## <a name="see-also"></a>참고 항목
[Microsoft Azure의 BizTalk Services를 관리하기 위한 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Editions 차트](biztalk-editions-feature-chart.md)  
[BizTalk 서비스 만들기](biztalk-provision-services.md)  
[BizTalk Services: 대시보드, 모니터 및 크기 조정 탭](biztalk-dashboard-monitor-scale-tabs.md)  

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
