---
title: Web Apps 개요 | Microsoft Docs
description: Azure App Service로 웹 응용 프로그램을 개발 및 호스팅하는 방법에 대해 알아보세요.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 79828193e283f0dcb80035cae0c11b050a1639ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="web-apps-overview"></a>Web Apps 개요

*Azure App Service Web Apps*(또는 Web Apps만)는 웹 응용 프로그램, REST API 및 모바일 백 엔드를 호스팅하는 서비스입니다. .NET, .NET Core, Java, Ruby, Node.js, PHP 또는 Python 등 원하는 언어로 개발할 수 있습니다. Windows 기반 환경에서 응용 프로그램을 쉽게 실행하고 확장할 수 있습니다. Linux 기반 환경에 대한 자세한 내용은 [Linux의 App Service](containers/app-service-linux-intro.md)를 참조하세요. 

Web Apps는 보안, 부하 분산, 자동 크기 조정 및 자동화된 관리와 같이 Microsoft Azure의 강력한 기능을 응용 프로그램에 추가합니다. 또한 VSTS, GitHub, Docker 허브 및 기타 원본, 패키지 관리, 스테이징 환경, 사용자 지정 도메인 및 SSL 인증서의 지속적인 배포와 같은 DevOps 기능도 활용할 수 있습니다. 

App Service를 사용하면 Azure 계산 리소스에 대한 비용을 지불하게 됩니다. 사용할 계산 리소스는 Web Apps를 실행하는 _App Service 계획_에 따라 결정됩니다. 자세한 내용은 [Azure Web Apps의 App Service 계획](azure-web-sites-web-hosting-plans-in-depth-overview.md)을 참조하세요.

## <a name="why-use-web-apps"></a>Web Apps를 사용하는 이유

App Service Web Apps의 주요 기능은 다음과 같습니다.

* **여러 언어 및 프레임워크** - Web Apps는 ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP 또는 Python에 대한 고급 지원을 제공합니다. [PowerShell 및 기타 스크립트 또는 실행 파일](web-sites-create-web-jobs.md)을 백그라운드 서비스로 실행할 수도 있습니다.
* **DevOps 최적화** - Visual Studio Team Services, GitHub, BitBucket, Docker 허브 또는 Azure Container Registry를 사용하여 [연속 통합 및 배포](app-service-continuous-deployment.md)를 설정합니다. [테스트 및 스테이징 환경](web-sites-staged-publishing.md)을 통해 업데이트를 승격합니다. [Azure PowerShell](/powershell/azureps-cmdlets-docs) 또는 [플랫폼 간 CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)를 사용하여 Web Apps에서 앱을 관리합니다.
* **고가용성을 가진 글로벌 규모 조정** - 수동 또는 자동으로 규모를 [강화](web-sites-scale.md) 또는 [확장](../monitoring-and-diagnostics/insights-how-to-scale.md)합니다. Microsoft의 글로벌 데이터 센터 인프라의 모든 위치에서 앱을 호스팅하고 App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 를 사용하면 고가용성이 보장됩니다.
* **SaaS 플랫폼 및 온-프레미스 데이터에 연결** - 엔터프라이즈 시스템(예: SAP), SaaS 서비스(예: Salesforce) 및 인터넷 서비스(예: Facebook)를 위해 50개 이상의 [커넥터](../connectors/apis-list.md)에서 선택합니다. [하이브리드 연결](../biztalk-services/integration-hybrid-connection-overview.md) 및 [Azure Virtual Networks](web-sites-integrate-with-vnet.md)를 사용하여 온-프레미스 데이터에 액세스합니다.
* **보안 및 규정 준수** - App Service는 [ISO, SOC 및 PCI 규격](https://www.microsoft.com/en-us/trustcenter)입니다. [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) 또는 소셜 로그인([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) 및 [Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md))을 사용하여 사용자를 인증합니다. [IP 주소 제한](app-service-ip-restrictions.md)을 만들고 [서비스 ID를 관리](app-service-managed-service-identity.md)합니다.
* **응용 프로그램 템플릿** - [Azure Marketplace](https://azure.microsoft.com/marketplace/)(예: WordPress, Joomla 및 Drupal)의 광범위한 응용 프로그램 템플릿 목록에서 선택합니다.
* **Visual Studio 통합** - Visual Studio의 전용 도구는 생성, 배포, 디버깅 작업을 간소화합니다.
* **API 및 모바일 기능** - Web Apps는 RESTful API 시나리오에 대한 턴키 방식 CORS 지원을 제공하며, 인증, 오프라인 데이터 동기화, 푸시 알림 등을 활성화하여 모바일 앱 시나리오를 간소화합니다.
* **서버를 사용하지 않는 코드** - 인프라를 명시적으로 프로비전하거나 관리하지 않고도 요청 시에 코드 조각이나 스크립트를 실행하고, 코드에서 실제로 사용하는 계산 시간에 대해서만 비용을 지불합니다([Azure Functions](/azure/azure-functions/) 참조).

Azure는 App Service의 Web Apps 뿐만 아니라 웹 사이트와 웹 응용 프로그램 호스팅에 사용할 수 있는 다른 서비스를 제공합니다. 대부분의 시나리오의 경우 Web Apps를 사용하는 것이 좋습니다.  마이크로 서비스 아키텍처의 경우 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)을 사용하는 것이 좋습니다. 코드가 실행되는 VM을 자세히 제어해야 하는 경우 [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)를 사용하는 것이 좋습니다. 이러한 Azure 서비스 중에서 하나를 선택하는 방법에 대한 자세한 내용은 [Azure App Service, Virtual Machines, 서비스 패브릭 및 Cloud Services 비교](choose-web-site-cloud-service-vm.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

첫 번째 웹앱을 만듭니다.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby(Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
