---
title: 응용 프로그램 프록시 응용 프로그램에 필요한 방화벽 포트를 여는 방법 | Microsoft 문서
description: Azure AD 응용 프로그램 프록시가 올바르게 작동하기 위해 열어야 하는 포트 찾기
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 72acfbd21159e15fe237be6d509cb2c4a2b1bffd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>응용 프로그램 프록시 응용 프로그램에 필요한 방화벽 포트를 여는 방법

각 포트의 기능 및 필요한 포트의 전체 목록을 보려면 [응용 프로그램 프록시 설명서](manage-apps/application-proxy-enable.md)의 필수 조건 섹션을 참조하세요. 응용 프로그램 프록시는 아웃바운드 포트만 사용합니다.

또한 온-프레미스 네트워크에서 [커넥터 포트 테스트 도구](https://aadap-portcheck.connectorporttest.msappproxy.net/)를 열어 필요한 모든 포트가 열려 있는지 확인할 수 있습니다. 녹색 확인 표시가 많을수록 복원력이 더 뛰어난 것입니다. 

## <a name="app-proxy-regions"></a>앱 프록시 영역

다음 중 반드시 녹색이어야 하는 영역을 알려주는 방법에 대해 연구하고 있습니다. 지금은 모두 녹색인지 확인합니다. 미국 중부 지역은 현재 위치한 지역에 상관없이 필요합니다.

이 도구로 올바른 결과를 얻을 수 있는지 확인하려면 다음을 수행합니다.

-   커넥터를 설치한 서버의 브라우저에서 도구를 엽니다.

-   커넥터에 적용할 수 있는 프록시 또는 방화벽이 이 페이지에도 적용되었는지 확인합니다. 이 작업은 Internet Explorer의 **설정** -&gt; **인터넷 옵션** -&gt; **연결** -&gt; **LAN 설정**으로 이동하여 수행할 수 있습니다. 이 페이지에 "사용자 LAN에 프록시 서버 사용" 필드가 표시됩니다. 이 확인란을 선택하고 "주소" 필드에 프록시 주소를 입력합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 응용 프로그램 프록시 커넥터 이해](manage-apps/application-proxy-connectors.md)
