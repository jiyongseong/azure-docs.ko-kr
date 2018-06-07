---
title: Azure SignalR이란? | Microsoft Docs
description: Azure SignalR Service에 대해 간략히 설명합니다.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868112"
---
# <a name="what-is-azure-signalr-service"></a>Azure SignalR Service란?

Microsoft Azure SignalR Service는 현재 [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

Azure SignalR Service는 [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction)을 기반으로 하는 Azure 서비스입니다. ASP.NET Core SignalR은 HTTP를 통해 응용 프로그램에 실시간 웹 기능을 추가하는 프로세스를 간소화하는 [오픈 소스 라이브러리](https://github.com/aspnet/signalr)입니다. 이 실시간 기능을 사용하면 웹 서버에서 연결된 클라이언트에 콘텐츠 업데이트를 푸시할 수 있습니다. 결과적으로 클라이언트가 서버를 폴링하거나 업데이트에 대한 새 HTTP 요청을 제출하지 않고도 업데이트됩니다.

이 문서에서는 Azure SignalR Service에 대한 개요를 제공합니다. 시작하려면 [ASP.NET Core 빠른 시작](signalr-quickstart-dotnet-core.md)부터 시작합니다.

## <a name="what-is-signalr-service-used-for"></a>SignalR Service의 용도는? 

실시간 콘텐츠 업데이트가 필요한 응용 프로그램 종류는 많습니다. Azure SignalR Service를 사용하기에 적합한 예제 응용 프로그램 종류는 다음과 같습니다.

* 서버에서 자주 업데이트해야 하는 앱. 예를 들어 게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱이 있습니다.
* 대시보드 및 모니터링 앱. 예를 들어 회사 대시보드, 즉석 판매 업데이트 또는 여행 경고가 있습니다.
* 공동 작업 앱. 예를 들어 화이트보드 앱 및 팀 회의 소프트웨어가 있습니다.
* 알림이 필요한 앱. 소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱에서 알림을 사용합니다.

내부적으로, SignalR은 실시간 웹 응용 프로그램을 빌드하는 데 사용되는 다양한 기술에 대한 추상화입니다. [Websocket](https://wikipedia.org/wiki/WebSocket)이 최적의 전송이지만, 다른 옵션을 사용할 수 없는 경우 [SSE(Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) 및 Long Polling과 같은 다른 기술이 사용됩니다. SignalR은 서버와 클라이언트에서 지원되는 기능에 따라 적절한 전송을 자동으로 감지하고 초기화합니다.

## <a name="developing-signalr-apps"></a>SignalR 앱 개발

현재 웹 응용 프로그램에서 사용할 수 있는 SignalR에는 ASP.NET용 SignalR과 최신 버전인 ASP.NET Core SignalR의 두 가지 버전이 있습니다. Azure SignalR Service는 ASP.NET Core SignalR에 기반을 둔 Azure 관리 서비스입니다. 

ASP.NET Core SignalR은 이전 버전을 다시 생성한 것입니다. 결과적으로 ASP.NET Core SignalR은 이전 버전의 SignalR과 호환되지 않습니다. API와 동작이 서로 다릅니다. ASP.NET Core SignalR SDK는 .NET Standard이므로 .NET Framework에서도 사용할 수 있습니다. 그러나 이전 API 대신 새 API를 사용해야 합니다. SignalR을 사용하지만 ASP.NET Core SignalR 또는 Azure SignalR Service로 이동하려면 API의 차이를 처리하기 위해 코드를 변경해야 합니다.

Azure SignalR Service를 사용하면 ASP.NET Core SignalR의 서버 쪽 구성 요소가 Azure에서 호스팅됩니다. 그러나 이 기술은 ASP.NET Core를 기반으로 하여 구현되므로 [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache), [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index)를 통해 호스팅하는 동안 여러 플랫폼(Windows, Linux 및 MacOS)에서 실제 웹 응용 프로그램을 실행할 수 있습니다. 사용자 자신의 프로세스에 자체 호스팅을 사용할 수도 있습니다.

응용 프로그램이 웹 클라이언트를 실시간 콘텐츠 업데이트로 업데이트하는 최신 기능을 지원하고, 여러 플랫폼(Azure, Windows, Linux 및 MacOS)에서 실행하고, 다른 환경에서 호스팅하기 위한 것이면 Azure SignalR Service를 활용하는 것이 가장 좋습니다.


## <a name="why-not-deploy-signalr-myself"></a>SignalR을 직접 배포하지 않는 이유는?

ASP.NET Core SignalR을 백 엔드 구성 요소로 지원하는 자신만의 Azure 웹 응용 프로그램을 전체 웹 응용 프로그램에 배포하는 방법은 여전히 유효합니다.

Azure SignalR Service를 사용하는 주요 이유 중 하나는 단순성입니다. Azure SignalR Service를 사용하면 성능, 확장성, 가용성과 같은 문제를 처리할 필요가 없습니다. 이러한 문제는 99.9% 서비스 수준 계약으로 처리됩니다.

또한 WebSocket은 일반적으로 실시간 콘텐츠 업데이트를 지원하는 데 선호되는 기술입니다. 그러나 많은 수의 WebSocket 영구 연결을 부하 분산하는 것은 크기 조정 시 해결해야 하는 복잡한 문제가 됩니다. 일반적인 해결 방법으로 DNS 부하 분산, 하드웨어 부하 분산 장치 및 소프트웨어 부하 분산을 활용합니다. Azure SignalR Service는 이 문제를 처리합니다.

또 다른 이유로, 실제로 웹 응용 프로그램을 호스팅할 필요가 없습니다. 웹 응용 프로그램의 논리에서는 [서버를 사용하지 않는 컴퓨팅](https://azure.microsoft.com/overview/serverless-computing/)을 활용할 수 있습니다. 예를 들어 코드는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 트리거를 통해 호스팅되고 요청 시에만 실행될 것입니다. 코드가 요청 시에만 실행되고 클라이언트와의 긴 연결을 유지하지 않기 때문에 이 시나리오는 까다로울 수 있습니다. Azure SignalR Service는 이미 사용자를 위해 연결을 관리하므로 이러한 상황을 처리할 수 있습니다.

## <a name="how-does-it-scale"></a>크기 조정은 어떻게 합니까?

SignalR은 일반적으로 SQL Server, Azure Service Bus 또는 Redis Cache를 사용하여 크기를 조정합니다. Azure SignalR Service는 크기 조정 방식을 처리합니다. 성능과 비용은 이러한 다른 서비스를 복잡하게 처리할 필요 없이 이러한 방식과 비슷합니다. 서비스에 대한 단위 수를 업데이트하면 됩니다. 각 서비스 장치마다 최대 1,000개의 클라이언트 연결을 지원합니다.

## <a name="next-steps"></a>다음 단계
* [빠른 시작: Azure SignalR을 사용하여 채팅방 만들기](signalr-quickstart-dotnet-core.md)  
  

