---
title: .NET에서 Azure Relay 하이브리드 연결 Websocket 시작 | Microsoft Docs
description: Azure Relay 하이브리드 연결 Websocket에 대한 C# 콘솔 응용 프로그램을 작성합니다.
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 5b900f70721f8642e27daf242295fbda77d7df6f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>.NET에서 Relay 하이브리드 연결 Websocket 시작
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

이 자습서에서는 [Azure Relay 하이브리드 연결](relay-what-is-it.md#hybrid-connections)에 대한 소개를 제공합니다. Microsoft .NET을 사용하여 해당 수신기 응용 프로그램에 메시지를 보내는 클라이언트 응용 프로그램을 만드는 방법에 대해 알아봅니다. 

## <a name="what-will-be-accomplished"></a>수행될 작업
하이브리드 연결에는 클라이언트 구성 요소와 서버 구성 요소가 모두 필요합니다. 이 자습서에서는 다음 단계를 수행하여 두 개의 콘솔 응용 프로그램을 만듭니다.

1. Azure Portal을 사용하여 Relay 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 해당 네임스페이스에 하이브리드 연결을 만듭니다.
3. 메시지를 수신하는 서버(수신기) 콘솔 응용 프로그램을 작성합니다.
4. 메시지를 보내는 클라이언트(발신자) 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Visual Studio 2015 이상](http://www.visualstudio.com) - 이 자습서의 예제에서는 Visual Studio 2017을 사용합니다.
* Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Azure Portal을 사용하여 네임스페이스 만들기
Relay 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 하이브리드 연결 만들기](#2-create-a-hybrid-connection-using-the-azure-portal)로 이동합니다.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Azure Portal을 사용하여 하이브리드 연결 만들기
하이브리드 연결을 이미 만든 경우 [서버 응용 프로그램 만들기](#3-create-a-server-application-listener)로 이동합니다.

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. 서버 응용 프로그램(수신기) 만들기
Visual Studio에서 메시지를 릴레이로부터 수신 대기하고 받을 C# 콘솔 응용 프로그램을 작성합니다.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. 클라이언트 응용 프로그램(보낸 사람) 만들기
Visual Studio에서 메시지를 릴레이로 보낼 C# 콘솔 응용 프로그램을 작성합니다.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. 응용 프로그램 실행
1. 서버 응용 프로그램을 실행합니다.
2. 클라이언트 응용 프로그램을 실행하고 일부 텍스트를 입력합니다.
3. 서버 응용 프로그램 콘솔에서 클라이언트 응용 프로그램에 입력된 텍스트가 표시되는지 확인합니다.

![응용 프로그램 실행](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

축하합니다. 종단 간 하이브리드 연결 응용 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계

* [릴레이 FAQ](relay-faq.md)
* [네임스페이스 만들기](relay-create-namespace-portal.md)
* [Node 시작](relay-hybrid-connections-node-get-started.md)

