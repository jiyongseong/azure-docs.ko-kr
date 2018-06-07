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
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
엔터프라이즈 솔루션을 사용하여 생성되는 루트 인증서를 사용하거나(권장됨) 자체 서명된 인증서를 생성할 수 있습니다. 루트 인증서를 만든 후 Base-64 인코딩된 X.509 .cer 파일로 공용 인증서 데이터(개인 키 아님)를 내보내고 공용 인증서 데이터를 Azure로 업로드합니다.

* **엔터프라이즈 인증서:** 엔터프라이즈 솔루션을 사용하는 경우 기존 인증서 체인을 사용할 수 있습니다. 사용할 루트 인증서용 .cer 파일을 가져옵니다.
* **자체 서명된 루트 인증서:** 엔터프라이즈 인증서 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 만들어야 합니다. 아래 P2S 인증서 문서 중 하나에 있는 단계를 따르는 것이 중요합니다. 그렇지 않으면 만든 인증서는 P2S 연결과 호환되지 않으며 연결을 시도하는 동안 클라이언트에 연결 오류가 수신됩니다. Microsoft Azure PowerShell, MakeCert, 또는 OpenSSL을 사용할 수 있습니다. 제공된 문서의 단계는 호환되는 인증서를 생성합니다.

  * [Windows 10 PowerShell 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Windows 10 및 PowerShell에서 인증서를 생성해야 합니다. 루트 인증서에서 생성된 클라이언트 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
  * [MakeCert 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 사용할 Windows 10 컴퓨터에 액세스할 수 없는 경우 MakeCert를 사용하여 인증서를 생성합니다. MakeCert는 사용되지 않지만, 그래도 MakeCert를 사용하여 인증서를 생성할 수 있습니다. 루트 인증서에서 생성된 클라이언트 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
