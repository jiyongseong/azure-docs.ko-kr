---
title: Azure Traffic Manager의 성능 저하 상태 문제해결
description: 성능 저하 상태로 표시할 때 Traffic Manager 문제를 해결하는 방법입니다.
services: traffic-manager
documentationcenter: ''
author: chadmath
manager: cshepard
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: f5b2f471b13db67411f15f32abad5afc644c04ba
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777733"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Azure Traffic Manager의 성능 저하 상태 문제 해결

이 문서에서는 성능 저하 상태를 보여 주는 Azure Traffic Manager 프로필 문제를 해결하는 방법을 설명합니다. 이 시나리오의 경우 사용자의 일부 .cloudapp.net 호스티드 서비스를 가리키는 Traffic Manager 프로필을 구성했다는 점을 고려합니다. Traffic Manager의 상태가 **성능 저하됨** 상태를 표시하는 경우 하나 이상의 끝점 상태가 **성능 저하됨**일 수 있습니다.

![성능 저하 끝점 상태](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Traffic Manager의 상태가 **비활성** 상태를 표시하는 경우 두 끝점이 모두 **비활성**일 수 있습니다.

![비활성 Traffic Manager 상태](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Traffic Manager 이해 프로브

* Traffic Manager는 프로브가 프로브 경로에서 다시 HTTP 200 응답을 수신하는 경우에만 끝점을 온라인 상태로 변경할 것을 고려합니다. 다른 200 이외의 응답은 오류입니다.
* 리디렉션된 URL이 200을 반환하더라도 30x 리디렉션은 실패합니다.
* HTTP 검색의 경우 인증서 오류는 무시됩니다.
* 200가 반환되면 검색 경로의 실제 콘텐츠는 문제가 되지 않습니다. "/favicon.ico"와 같은 정적 콘텐츠에 대한 URL을 검색하는 작업은 일반적인 기술입니다. 응용 프로그램이 정상적인 경우더라도 ASP 페이지와 같은 동적 콘텐츠는 항상 200을 반환하지 않을 수 있습니다.
* 모범 사례는 사이트 가동 또는 중지를 결정하기 위한 충분한 논리가 있는 경로로 검색 경로를 설정하는 것입니다. 이전 예제에서는 "/favicon.ico"로 경로를 설정하여 w3wp.exe가 응답하는지 테스트합니다. 이 검색에서는 웹 응용 프로그램 상태가 정상인지 나타낼 수 있습니다. 사이트의 상태를 확인하는 논리를 포함하는 "/Probe.aspx"와 같이 경로를 설정하는 것이 더 좋습니다. 예를 들어, CPU 사용률에 성능 카운터를 사용하거나 실패한 요청 수를 측정할 수 있습니다. 또는 데이터베이스 리소스 또는 세션 상태에 액세스하여 웹 응용 프로그램이 작동하는지 확인할 수 있습니다.
* 프로필의 모든 끝점의 성능이 저하된 경우 Traffic Manager는 모든 끝점을 정상으로 처리하고 트래픽을 모든 끝점으로 라우팅합니다. 이 동작을 통해 검색 메커니즘과 관련된 문제가 서비스의 전체 가동 중단을 일으키지 않도록 합니다.

## <a name="troubleshooting"></a>문제 해결

검색 실패 문제를 해결하려면 프로브 URL에서 HTTP 상태 코드 반환을 보여 주는 도구가 필요합니다. 원시 HTTP 응답을 보여 주는 사용 가능한 도구가 많이 있습니다.

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

또한 Internet Explorer에서 F12 디버깅 도구의 네트워크 탭을 사용하여 HTTP 응답을 확인할 수 있습니다.

이 예에서는 검색 URL인 http://watestsdp2008r2.cloudapp.net:80/Probe에서 응답을 확인하려고 합니다. 다음 PowerShell 예는 이러한 문제를 보여 줍니다.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

예제 출력:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

리디렉션 응답을 수신했습니다. 앞서 설명한 대로 200 이외의 StatusCode는 실패로 간주됩니다. Traffic Manager는 끝점 상태를 오프라인으로 변경합니다. 이 문제를 해결하려면 웹 사이트 구성을 확인하여 적절한 StatusCode가 프로브 경로에서 반환될 수 있는지 확인합니다. Traffic Manager 검색이 200을 반환하는 경로를 가리키도록 다시 구성합니다.

검색이 HTTPS 프로토콜을 사용하는 경우 테스트 중 SSL/TLS 오류를 방지하기 위해 확인하는 인증서를 사용하지 않도록 설정해야 합니다. 다음 PowerShell 문은 현재 PowerShell 세션에 대한 인증서 유효성 검사를 사용하지 않습니다.

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>다음 단계

[Traffic Manager 트래픽 라우팅 방법 정보](traffic-manager-routing-methods.md)

[Traffic Manager란?](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Traffic Manager 작업(REST API 참조)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager cmdlet][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
