---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
연결 문제가 있으면 다음 항목을 확인합니다.

- 클라이언트 인증서를 내보낸 경우 기본값을 사용하여 .pfx 파일을 내보내고 가능하다면 모든 인증서를 인증서 경로에 포함합니다. 이 값을 사용하여 인증서를 내보낼 때 루트 인증서 정보까지 함께 내보내집니다. 클라이언트 컴퓨터에 인증서가 설치되면 .pfx 파일에 포함된 루트 인증서도 클라이언트 컴퓨터에 설치됩니다. 설치된 루트 인증서 정보가 클라이언트 컴퓨터에 있어야 합니다. 이를 확인하려면 **사용자 인증서 관리**로 이동하여 **신뢰할 수 있는 루트 인증 기관\인증서**로 이동합니다. 루트 인증서가 나열되어 있는지 확인합니다. 인증이 작동하려면 루트 인증서가 있어야 합니다.

- 엔터프라이즈 CA 솔루션을 사용하여 발급된 인증서를 사용하고 인증에 문제가 발생하는 경우 클라이언트 인증서에서 인증 순서를 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**으로 이동하여 인증 목록 순서를 확인할 수 있습니다. 목록의 첫 번째 항목으로 '클라이언트 인증'이 표시되는지 확인합니다. 그렇지 않으면 목록에서 첫 번째 항목으로 클라이언트 인증을 가진 사용자 템플릿을 기반으로 하는 클라이언트 인증서를 발급해야 합니다.

- 추가 P2S 문제 해결 정보는 [P2S 연결 문제 해결](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.