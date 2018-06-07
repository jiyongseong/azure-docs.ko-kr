---
title: "Azure Relay Node API 개요 | Microsoft Docs"
description: "Relay Node API 개요"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 696e3f77a283cc31d3c8f6007a839480ae8eb984
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Relay 하이브리드 연결 Node API 개요

## <a name="overview"></a>개요

Azure Relay 하이브리드 연결에 대한 [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) Node 패키지는 ['ws'](https://www.npmjs.com/package/ws) NPM 패키지를 기반으로 하며 확장합니다. 이 패키지는 해당 기본 패키지의 모든 내보내기를 다시 내보내며 Azure Relay 서비스 하이브리드 연결 기능과 통합할 수 있는 새로운 내보내기를 추가합니다. 

`require('ws')`이 `require('hyco-ws')` 대신 이 패키지를 사용할 수 있는 기존 응용 프로그램을 사용하면 응용 프로그램이 WebSocket 연결을 "방화벽 내부"에서 로컬로 및 하이브리드 연결을 통해 동시에 수신 대기할 수 있는 하이브리드 시나리오를 수행할 수 있습니다.
  
## <a name="documentation"></a>문서화

API는 [주 'ws' 패키지에 문서화되어](https://github.com/websockets/ws/blob/master/doc/ws.md) 있습니다. 이 문서에서는 이 패키지가 해당 기준과 어떻게 다른지를 설명합니다. 

기본 패키지와 이 'hyco-ws' 간의 주요 차이점은 `require('hyco-ws').RelayedServer`를 통해 내보낸 새 서버 클래스와 몇 가지 도우미 메서드를 추가한 것입니다.

### <a name="package-helper-methods"></a>패키지 도우미 메서드

다음과 같이 사용자가 참조할 수 있는 패키지 내보내기에서 사용 가능한 몇 가지 유틸리티 메서드가 있습니다.

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

도우미 메서드는 이 패키지와 함께 사용하기 위한 것이지만 웹 또는 장치 클라이언트에서 수신기 또는 보낸 사람을 만들 수 있도록 Node 서버에서 사용할 수도 있습니다. 서버는 수명이 짧은 토큰을 포함하는 URI를 전달하여 이러한 메서드를 사용합니다. 또한 이러한 URI는 WebSocket 핸드셰이크에 대한 HTTP 헤더 설정을 지원하지 않는 일반적인 WebSocket 스택을 통해 사용할 수 있습니다. URI에 권한 부여 토큰을 포함하는 것은 그러한 라이브러리 외부 사용 시나리오에 주로 지원됩니다. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

지정된 네임스페이스 및 경로에 대한 유효한 Azure Relay 하이브리드 연결 수신기 URI를 만듭니다. 이 URI는 WebSocketServer 클래스의 릴레이 버전으로 사용할 수 있습니다.

- `namespaceName`(필수) - 사용할 Azure Relay 네임스페이스의 정규화된 도메인 이름.
- `path`(필수) - 해당 네임스페이스에서 기존 Azure Relay 하이브리드 연결의 이름.
- `token`(선택 사항) - 수신기 URI에 포함되어 있는 이전에 발급된 Relay 액세스 토큰(다음 예제 참조).
- `id`(선택 사항) - 요청의 종단 간 진단 추적을 활성화하는 추적 식별자.

`token` 값은 선택 사항이며 W3C WebSocket 스택을 사용하는 경우와 마찬가지로 WebSocket 핸드셰이크와 함께 HTTP 헤더를 보낼 수 없는 경우에만 사용해야 합니다.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

지정된 네임스페이스 및 경로에 대해 유효한 Azure Relay 하이브리드 연결 송신 URI를 만듭니다. 이 URI는 WebSocket 클라이언트와 함께 사용할 수 있습니다.

- `namespaceName`(필수) - 사용할 Azure Relay 네임스페이스의 정규화된 도메인 이름.
- `path`(필수) - 해당 네임스페이스에서 기존 Azure Relay 하이브리드 연결의 이름.
- `token`(선택 사항) - 송신 URI에 포함되어 있는 이전에 발급된 Relay 액세스 토큰(다음 예제 참조).
- `id`(선택 사항) - 요청의 종단 간 진단 추적을 활성화하는 추적 식별자.

`token` 값은 선택 사항이며 W3C WebSocket 스택을 사용하는 경우와 마찬가지로 WebSocket 핸드셰이크와 함께 HTTP 헤더를 보낼 수 없는 경우에만 사용해야 합니다.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

만료 인수가 생략된 경우 현재 인스턴스에서 지정된 시간(초) 또는 한 시간 동안 유효한 지정된 대상 URI, SAS 규칙 및 SAS 규칙 키에 대한 Azure Relay SAS(공유 액세스 서명) 토큰을 만듭니다.

- `uri`(필수) - 토큰이 발급되는 URI입니다. URI는 HTTP 체계를 사용하도록 표준화되고 쿼리 문자열 정보가 제거됩니다.
- `ruleName`(필수) - 지정된 URI에 표시된 엔터티 또는 URI 호스트 부분에 표시된 네임스페이스 중 하나에 대한 SAS 규칙 이름입니다.
- `key`(필수) - SAS 규칙에 유효한 키입니다. 
- `expirationSeconds`(선택 사항) - 생성된 토큰이 만료될 때까지의 시간(초)입니다. 지정하지 않은 경우 기본값은 1시간(3600)입니다.

발급된 토큰은 지정된 기간 동안 특정 SAS 규칙과 관련된 권한을 부여합니다.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

이 메서드는 이전에 문서화된 `createRelayToken` 메서드와 기능적으로 동일하나, 입력 URI에 추가된 토큰을 올바르게 반환합니다.

### <a name="class-wsrelayedserver"></a>클래스 ws.RelayedServer

`hycows.RelayedServer` 클래스는 로컬 네트워크에서 수신 대기하지 않는 `ws.Server` 클래스에 대한 대안이지만 Azure Relay 서비스에 대한 수신 대기를 위임합니다.

두 클래스는 대체로 계약과 호환되므로, `ws.Server` 클래스를 사용하는 기존 응용 프로그램이 릴레이된 버전을 사용하도록 쉽게 변경할 수 있습니다. 주요 차이점은 생성자와 사용 가능한 옵션입니다.

#### <a name="constructor"></a>생성자  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

`RelayedServer` 생성자는 독립 실행형 수신기이거나 기존 HTTP 수신기 프레임워크에 포함할 수 있기 때문에 `Server`와는 다른 인수 집합을 지원합니다. WebSocket 관리는 주로 Relay 서비스에 위임되었기 때문에 사용할 수 있는 옵션은 적습니다.

생성자 인수:

- `server`(필수) - 수신하는 하이브리드 연결 이름에 대한 정규화된 URI로, 일반적으로 WebSocket.createRelayListenUri() 도우미 메서드로 생성됩니다.
- `token`(필수) - 이 인수는 이전에 발급된 토큰 문자열 또는 그러한 토큰 문자열을 가져오도록 호출할 수 있는 콜백 함수를 저장합니다. 콜백 옵션은 토큰 갱신을 활성화하기 때문에 기본으로 설정되어 있습니다.

#### <a name="events"></a>이벤트

`RelayedServer` 인스턴스는 들어오는 요청을 처리하고, 연결을 설정하며, 오류 상태를 검색할 수 있도록 하는 세 가지 이벤트를 내보냅니다. 메시지를 처리하는 `connect` 이벤트를 구독해야 합니다. 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

들어오는 연결이 수락되기 바로 전에 `headers` 이벤트가 발생하여 클라이언트에 전송할 헤더를 수정할 수 있게 합니다. 

##### <a name="connection"></a>connection

```JavaScript
function(socket)
```

새로운 WebSocket 연결이 수락되면 보내집니다. 개체는 기본 패키지와 동일한 형식 `ws.WebSocket`입니다.


##### <a name="error"></a>error

```JavaScript
function(error)
```

내부 서버에서 오류를 보내면 여기서 전달됩니다.  

#### <a name="helpers"></a>도우미

릴레이된 서버를 시작하고 들어오는 연결에 즉시 가입하는 작업을 간소화하기 위해 패키지는 간단한 도우미 함수를 제공하며, 다음과 같이 예제에서도 사용되었습니다.

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

이 메서드는 생성자를 호출하여 RelayedServer의 새 인스턴스를 만든 다음, 공급된 콜백을 '연결' 이벤트로 구독합니다.
 
##### <a name="relayedconnect"></a>relayedConnect

`relayedConnect`는 함수에서 `createRelayedServer` 도우미를 간단히 미러링하면서 클라이언트 연결을 만들고 결과 소켓에 '열기' 이벤트를 구독합니다.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>다음 단계
Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.
* [Azure 릴레이란?](relay-what-is-it.md)
* [사용 가능한 Relay API](relay-api-overview.md)
