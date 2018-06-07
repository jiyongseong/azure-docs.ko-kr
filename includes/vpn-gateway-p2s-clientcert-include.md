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
ms.openlocfilehash: 8a49653b4083cbfd17656d701225dcb14f91f637
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 클라이언트 인증서는 루트 인증서에서 생성되고 각 클라이언트 컴퓨터에 설치됩니다. 유효한 클라이언트 인증서가 설치되어 있지 않고 클라이언트가 VNet에 연결하려고 하는 경우 인증이 실패합니다.

연결할 각 클라이언트에 대해 고유한 인증서를 생성하거나 여러 클라이언트에서 동일한 인증서를 사용할 수 있습니다. 고유한 클라이언트 인증서를 생성하면 단일 인증서를 해지할 수 있는 장점이 있습니다. 그렇지 않으면 여러 클라이언트가 동일한 클라이언트 인증서를 사용하고 이를 해지해야 하는 경우, 해당 인증서를 사용하여 인증하는 모든 클라이언트에 대해 새 인증서를 생성하고 설치해야 합니다.

다음 메서드를 사용하여 클라이언트 인증서를 생성할 수 있습니다.

- **엔터프라이즈 인증서:**

  - 엔터프라이즈 인증서 솔루션을 사용하는 경우, 'domain name\username' 형식이 아닌 일반 이름 값 형식 'name@yourdomain.com'으로 클라이언트 인증서를 생성합니다.
  - 클라이언트 인증서가 사용 목록의 첫 번째 항목으로 스마트 카드 로그온 등이 아닌 '클라이언트 인증'이 있는 '사용자' 인증서 템플릿을 기반으로 하는지 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**을 확인하여 인증서를 확인할 수 있습니다.

- **자체 서명 루트 인증서:** 아래 P2S 인증서 문서 중 하나에 있는 단계를 따르는 것이 중요합니다. 그렇지 않으면 만든 클라이언트 인증서는 P2S 연결과 호환되지 않으며 연결을 시도하는 동안 클라이언트에 오류가 수신됩니다. 다음 문서 중 하나의 단계가 호환 가능한 클라이언트 인증서를 생성합니다. 

  * [Windows 10 PowerShell 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Windows 10 및 PowerShell에서 인증서를 생성해야 합니다. 생성된 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
  * [MakeCert 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 사용할 Windows 10 컴퓨터에 액세스할 수 없는 경우 MakeCert를 사용하여 인증서를 생성합니다. MakeCert는 사용되지 않지만, 그래도 MakeCert를 사용하여 인증서를 생성할 수 있습니다. 생성된 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.

  위의 지침을 사용하여 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 경우 생성하는 데 사용한 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 전체 인증서 체인과 함께 .pfx로 내보내야 합니다. 클라이언트가 성공적으로 인증하는 데 필요한 루트 인증서 정보가 포함된 .pfx 파일이 만들어집니다. 인증서 내보내기 단계의 경우 [인증서 - 클라이언트 인증서 내보내기](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)를 참조하세요.