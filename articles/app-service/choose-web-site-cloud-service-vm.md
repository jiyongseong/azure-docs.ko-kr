---
title: Azure App Service, Virtual Machines, Service Fabric 및 Cloud Services 비교 | Microsoft Docs
description: 웹 응용 프로그램을 호스트하는 데 Azure App Service, Virtual Machines, Service Fabric 및 Cloud Services 중에서 선택하는 방법에 대해 알아봅니다.
services: app-service\web, virtual-machines, cloud-services
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: f930cec984a8b92e00ec613ce3bba91a40518911
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Azure App Service, Virtual Machines, Service Fabric 및 Cloud Services 비교
## <a name="overview"></a>개요
Azure는 [Azure App Service][Azure App Service], [Virtual Machines][Virtual Machines], [Service Fabric][Service Fabric] 및 [Cloud Services][Cloud Services] 등 웹 사이트를 호스트하는 여러 가지 방법을 제공합니다. 이 문서에서는 각 옵션에 대해 파악하고 웹 응용 프로그램에 적합한 옵션을 선택하는 데 도움이 되는 정보를 제공합니다.

대부분의 웹앱에서는 Azure App Service가 가장 적합합니다. Azure 웹 사이트에서는 배포와 관리 기능이 플랫폼에 통합되고, 높은 트래픽 부하를 처리하기 위해 사이트를 빠르게 확장할 수 있으며, 기본 제공 부하 분산 및 Traffic Manager가 고가용성을 제공합니다. [온라인 마이그레이션 도구](https://www.migratetoazure.net/)를 사용하여 기존 사이트를 Azure App Service로 쉽게 이동하거나, 웹 응용 프로그램 갤러리에서 오픈 소스 앱을 사용하거나, 원하는 프레임워크와 도구를 사용하여 새 사이트를 만들 수 있습니다. [WebJobs][WebJobs] 기능을 사용하면 백그라운드 작업 처리 기능을 App Service 웹앱에 쉽게 추가할 수 있습니다.

Service Fabric은 새 앱을 만들거나 마이크로 서비스 아키텍처를 사용하도록 기존 앱을 다시 작성하는 경우에 적합합니다. 컴퓨터의 공유 풀에서 실행하는 앱은 작게 시작하고 필요에 따라 수백 또는 수천 대의 컴퓨터로 대규모로 확장할 수 있습니다. 상태 저장 서비스를 통해 일관되고 안정적으로 앱 상태를 손쉽게 저장하고 Service Fabric은 서비스 분할, 크기 조정 및 가용성을 자동으로 관리합니다.  Service Fabric은 OWIN(Open Web Interface for .NET) 및 ASP.NET Core를 사용하여 WebAPI를 지원합니다.  App Service에 비해 Service Fabric은 기본 인프라에 대한 더 많은 제어 기능 또는 직접 액세스를 지원합니다. 서버로 원격으로 연결하거나 서버 시작 작업을 구성할 수 있습니다. Cloud Services는 제어 수준과 사용 편이성에서 Service Fabric과 비슷하지만 이제는 레거시 서비스이므로 Service Fabric을 새로운 개발에 사용하는 것이 좋습니다.

App Service 또는 Service Fabric에서 실행하려면 기존 응용 프로그램을 크게 수정해야 하는 경우에는 클라우드로의 마이그레이션을 간소화하기 위해 Virtual Machines를 선택할 수 있습니다. 그러나 VM을 올바르게 구성, 보호 및 유지 관리하려면 Azure App Service와 Service Fabric에 비해 훨씬 많은 시간과 IT 전문 지식이 필요합니다. Azure Virtual Machines를 고려 중인 경우 VM 환경 패치/업데이트/관리에 필요한 지속적인 유지 관리 작업을 고려해야 합니다. Azure Virtual Machines는 IaaS(Infrastructure-as-a-Service)이지만 App Service 및 Service Fabric은 PaaS(Platform-as-a-Service)입니다. 

## <a name="features"></a>기능 비교
다음 표에서는 최상의 옵션 선택에 도움이 되도록 App Service, Cloud Services, Virtual Machines 및 Service Fabric의 기능을 비교합니다. 각 옵션의 SLA에 대한 최신 정보는 [Azure Service Level Agreements(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

| 기능 | App Service(웹앱) | Cloud Services(웹 역할) | Virtual Machines | Service Fabric | 메모 |
| --- | --- | --- | --- | --- | --- |
| 빠른 배포 |X | | |X |응용 프로그램이나 응용 프로그램 업데이트를 클라우드 서비스로 배포하거나 VM을 만들려면 최소한 몇 분이 걸리지만 응용 프로그램을 웹앱으로 배포하는 데는 몇 초밖에 걸리지 않습니다. |
| 다시 배포하지 않고 대규모 컴퓨터로 확장 |X | | |X | |
| 웹 서버 인스턴스는 콘텐츠와 구성을 공유하므로 확장 시 다시 배포하거나 다시 구성하지 않아도 됩니다. |X | | |X | |
| 여러 배포 환경(프로덕션 및 스테이징) |X |X | |X |Service Fabric을 사용하면 앱에 대해 여러 환경을 유지하거나 여러 버전의 앱을 함께 배포할 수 있습니다. |
| 자동 OS 업데이트 관리 |X |X | | |POA(패치 오케스트레이션 응용 프로그램)를 통해 부분적으로 사용 가능하며 향후 완전히 지원될 예정입니다. |
| 원활한 플랫폼 전환(32비트와 64비트 간을 쉽게 이동) |X |X | | | |
| GIT, FTP를 사용하여 코드 배포 |X | |X | | |
| 웹 배포를 사용하여 코드 배포 |X | |X | |Cloud Services에서는 웹 배포를 사용하여 개별 역할 인스턴스로 업데이트를 배포할 수 있습니다. 그러나 역할의 초기 배포에는 웹 배포를 사용할 수 없으며 업데이트에 웹 배포를 사용하는 경우에는 각 역할 인스턴스로 업데이트를 개별 배포해야 합니다. 프로덕션 환경의 클라우드 서비스 SLA를 충족하려면 여러 인스턴스가 필요합니다. |
| WebMatrix 지원 |X | |X | | |
| Service Bus, 저장소, SQL Database 등의 서비스에 대한 액세스 |X |X |X |X | |
| 다중 계층 아키텍처의 웹 또는 웹 서비스 계층 호스트 |X |X |X |X | |
| 다중 계층 아키텍처의 중간 계층 호스트 |X |X |X |X |App Service 웹앱은 REST API 중간 계층을 쉽게 호스트할 수 있고, [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) 기능은 백그라운드 처리 작업을 호스트할 수 있습니다. 전용 웹 사이트에서 WebJobs를 실행하여 계층을 독립적으로 확장할 수 있습니다. |
| 통합된 MySQL-as-a-Service 지원 |X |X | | | |
| ASP.NET, 클래식 ASP, Node.js, PHP, Python 지원 |X |X |X |X |Service Fabric에서는 [ASP.NET 5](../service-fabric/service-fabric-reliable-services-communication-aspnetcore.md)를 사용하여 웹 프런트 엔드를 만들거나 모든 유형의 응용 프로그램(Node.js, Java 등)을 [게스트 실행 파일](../service-fabric/service-fabric-guest-executables-introduction.md)로 배포할 수 있습니다. |
| 다시 배포하지 않고 여러 인스턴스로 규모 확장 |X |X |X |X |Virtual Machines는 여러 인스턴스로 규모 확장될 수 있지만 이러한 컴퓨터에서 실행되는 서비스가 이 규모 확장을 처리하도록 작성되어야 합니다. 컴퓨터 간에 요청을 라우팅하는 부하 분산 장치를 구성하고 유지 관리 또는 하드웨어 오류로 인해 모든 인스턴스가 동시에 다시 시작되지 않도록 선호도 그룹을 만들어야 합니다. |
| SSL 지원 |X |X |X |X |App Service 웹앱의 경우 사용자 지정 도메인 이름에 대한 SSL은 기본 및 표준 모드에서만 지원됩니다. 웹앱에 SSL을 사용하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 SSL 인증서 구성](app-service-web-tutorial-custom-ssl.md)을 참조하세요. |
| Visual Studio 통합 |X |X |X |X | |
| 원격 디버깅 |X |X |X | | |
| TFS를 사용하여 코드 배포 |X |X |X |X | |
| [Azure Virtual Network](/azure/virtual-network/)를 사용한 네트워크 격리 |X |X |X |X |[Azure Websites Virtual Network 통합](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| [Azure Traffic Manager](/azure/traffic-manager/) 지원 |X |X |X |X | |
| 통합된 끝점 모니터링 |X |X |X | | |
| 서버에 대한 원격 데스크톱 액세스 | |X |X |X | |
| 사용자 지정 MSI 설치 | |X |X |X |Service Fabric을 사용하면 모든 실행 파일을 [게스트 실행 파일](../service-fabric/service-fabric-guest-executables-introduction.md)로 호스트하거나 VM에 모든 응용 프로그램을 설치할 수 있습니다. |
| 시작 작업 정의/실행 기능 | |X |X |X | |
| ETW 이벤트 수신 가능 | |X |X |X | |

## <a name="scenarios"></a>시나리오 및 권장 사항
아래에는 몇 가지 일반적인 응용 프로그램 시나리오와 권장 사항 및 각 시나리오에 가장 적합한 Azure 웹 호스팅 옵션이 나와 있습니다.

* [온-프레미스 자산과 통합된 비즈니스 응용 프로그램을 실행하기 위한 데이터베이스 백 엔드 및 백그라운드 처리 기능이 있는 웹 프런트 엔드가 필요한 경우](#onprem)
* [효율적으로 확장 가능하며 전 세계적으로 제공할 수 있는 회사 웹 사이트를 호스트할 안정적인 방법이 필요한 경우](#corp)
* [IIS6 응용 프로그램을 Windows Server 2003에서 실행 중인 경우](#iis6)
* [작은 비즈니스 소유자가 사이트를 저렴하게 호스트할 방법을 찾고 있지만 미래 성장도 염두에 두고 있는 경우](#smallbusiness)
* [웹 또는 그래픽 디자이너가 고객을 위해 웹 사이트를 디자인 및 구축하려는 경우](#designer)
* [웹 프런트 엔드가 있는 다중 계층 응용 프로그램을 클라우드로 마이그레이션하는 경우](#multitier)
* [고도로 사용자 지정된 Windows 또는 Linux 환경을 사용하는 응용 프로그램을 클라우드로 이동하려는 경우](#custom)
* [사이트가 오픈 소스 소프트웨어를 사용하며 Azure에서 호스트하려는 경우](#oss)
* [LOB(기간 업무) 응용 프로그램을 회사 네트워크에 연결해야 하는 경우](#lob)
* [모바일 클라이언트를 위해 REST API 또는 웹 서비스를 호스트하려는 경우](#mobile)

### <a id="onprem"></a>온-프레미스 자산과 통합된 비즈니스 응용 프로그램을 실행하기 위한 데이터베이스 백 엔드 및 백그라운드 처리 기능이 있는 웹 프런트 엔드가 필요한 경우
Azure App Service는 복잡한 비즈니스 응용 프로그램에 적합한 솔루션입니다. 부하 분산된 플랫폼에서 자동으로 확장되고 Active Directory를 통해 보호되며 온-프레미스 리소스에 연결되는 앱을 개발할 수 있습니다. 또한 최고 수준의 포털과 API를 통해 이러한 앱을 쉽게 관리할 수 있으며 앱 정보 도구를 사용하여 고객이 앱을 사용하는 방법을 파악할 수 있습니다. [Webjobs][Webjobs] 기능을 사용하면 웹 계층의 일부분으로 백그라운드 프로세스와 작업을 실행할 수 있으며 하이브리드 연결 및 VNET 기능을 사용하면 온-프레미스 리소스에 쉽게 다시 연결할 수 있습니다. Azure App Service는 웹앱에 대해 9의 SLA를 제공하며 다음 작업을 수행할 수 있게 해줍니다.

* 자동 복구 및 패치되는 클라우드 플랫폼에서 응용 프로그램을 안정적으로 실행할 수 있습니다.
* 전 세계 데이터 센터 네트워크로 자동 확장할 수 있습니다.
* 재해 복구를 위한 백업 및 복원을 수행할 수 있습니다.
* ISO, SOC2 및 PCI 호환성을 제공할 수 있습니다.
* Active Directory와 통합할 수 있습니다.

### <a id="corp"></a> 효율적으로 확장 가능하며 전 세계적으로 제공할 수 있는 회사 웹 사이트를 호스트할 안정적인 방법이 필요한 경우
Azure App Service는 회사 웹 사이트를 호스트하는 데 적합한 솔루션입니다. 전 세계 데이터 센터 네트워크의 요구를 충족하도록 웹앱을 빠르고 쉽게 확장할 수 있습니다. Azure 웹 사이트는 로컬 연결, 내결함성 및 지능형 트래픽 관리 기능을 제공합니다. 이러한 모든 기능이 최고 수준의 관리 도구가 포함된 플랫폼에서 제공되므로 사이트 상태와 사이트 트래픽을 빠르고 쉽게 파악할 수 있습니다. Azure App Service는 웹앱에 대해 999의 SLA를 제공하며 다음 작업을 수행할 수 있게 해줍니다.

* 자동 복구 및 패치되는 클라우드 플랫폼에서 웹 사이트를 안정적으로 실행할 수 있습니다.
* 전 세계 데이터 센터 네트워크로 자동 확장할 수 있습니다.
* 재해 복구를 위한 백업 및 복원을 수행할 수 있습니다.
* 통합된 도구로 로그 및 트래픽을 관리할 수 있습니다.
* ISO, SOC2 및 PCI 호환성을 제공할 수 있습니다.
* Active Directory와 통합할 수 있습니다.

### <a id="iis6"></a> IIS6 응용 프로그램을 Windows Server 2003에서 실행 중인 경우
Azure App Service에서는 이전의 IIS6 응용 프로그램 마이그레이션과 관련된 인프라 비용 문제를 쉽게 해결할 수 있습니다. Microsoft는 호환성을 확인하고 수행해야 하는 변경을 식별할 수 있는 [간편한 마이그레이션 도구와 자세한 마이그레이션 지침](https://www.migratetoazure.net/)을 제작했습니다. Visual Studio, TFS 및 일반 CMS 도구와의 통합을 통해 IIS6 응용 프로그램을 쉽게 클라우드로 직접 배포할 수 있습니다. 배포 후에는 Azure Portal에서 제공하는 유용한 관리 도구를 사용하여 응용 프로그램을 축소해 비용을 관리하고 필요에 따라 수요 충족을 위해 응용 프로그램을 확장할 수 있습니다. 마이그레이션 도구를 통해 다음을 수행할 수 있습니다.

* 레거시 Windows Server 2003 웹 응용 프로그램을 클라우드로 빠르고 쉽게 마이그레이션할 수 있습니다.
* 연결된 SQL Database를 온-프레미스에 유지하여 하이브리드 응용 프로그램을 만들 수 있습니다.
* 레거시 응용 프로그램과 함께 SQL Database를 자동으로 이동할 수 있습니다.

### <a id="smallbusiness"></a>작은 비즈니스 소유자가 사이트를 저렴하게 호스트할 방법을 찾고 있지만 미래 성장도 염두에 두고 있는 경우
Azure App Service는 처음에 무료로 사용한 후 필요할 때 기능을 추가할 수 있으므로 이 시나리오에 적합한 솔루션입니다. 각 무료 웹앱에는 Azure에서 제공하는 도메인(*your_company*.azurewebsites.net)이 제공되며 플랫폼에는 통합 배포 및 관리 도구와 작업을 쉽게 시작할 수 있는 응용 프로그램 갤러리가 포함되어 있습니다. 사용자 수요 증가에 따라 사이트를 발전시킬 수 있는 다른 여러 서비스 및 확장 옵션도 있습니다. Azure App Service를 통해 다음 작업을 수행할 수 있습니다.

* 무료 계층으로 시작한 후 필요에 따라 확장할 수 있습니다.
* 응용 프로그램 갤러리를 사용하여 널리 사용되는 WordPress 등의 웹 응용 프로그램을 빠르게 설정할 수 있습니다.
* 필요에 따라 Azure 서비스와 기능을 응용 프로그램에 추가할 수 있습니다.
* HTTPS를 사용하여 웹앱 보안을 유지할 수 있습니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a> 웹 또는 그래픽 디자이너가 고객을 위해 웹 사이트를 디자인 및 구축하려는 경우
웹 개발자와 디자이너의 경우 다양한 프레임워크 및 도구와 쉽게 통합되고, Git 및 FTP용 배포 지원을 포함하며, Visual Studio 및 SQL Database와 같은 도구 및 서비스와 긴밀하게 통합되는 Azure App Service가 적합합니다. App Service를 통해 다음 작업을 수행할 수 있습니다.

* [자동화된 작업][scripting](영문)을 위해 명령줄 도구를 사용할 수 있습니다.
* [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] 및 [Python][Python] 등의 인기 있는 언어로 작업할 수 있습니다.
* 매우 높은 기능까지 확장하기 위해 세 가지 확장 수준을 선택할 수 있습니다.
* [SQL Database][sqldatabase], [Service Bus][servicebus], [저장소][Storage] 등의 다른 Azure 서비스 및 MySQL, MongoDB 등 [Azure 스토어][azurestore]의 파트너 제공 항목과 통합할 수 있습니다.
* Visual Studio, Git, WebMatrix, WebDeploy, TFS, FTP 등의 도구와 통합할 수 있습니다.

### <a id="multitier"></a>웹 프런트 엔드가 있는 다중 계층 응용 프로그램을 클라우드로 마이그레이션하는 경우
데이터베이스에 연결되는 웹 서버 등의 다중 계층 응용 프로그램을 실행하는 경우에는 Azure SQL Database와 긴밀하게 통합되는 Azure App Service가 적합한 옵션입니다. WebJob 기능을 사용하여 백 엔드 프로세스를 실행할 수도 있습니다.

서버에 원격 접속하거나 서버 시작 작업을 구성하는 기능 등 서버 환경에 대한 추가 제어 기능이 필요한 경우에는 하나 이상의 계층에 대해 Service Fabric을 선택합니다.

자체 컴퓨터 이미지를 사용하거나 Service Fabric에서는 구성할 수 없는 서버 소프트웨어 또는 서비스를 실행하려는 경우에는 하나 이상의 계층에 대해 Virtual Machines를 선택합니다.

### <a id="custom"></a>고도로 사용자 지정된 Windows 또는 Linux 환경을 사용하는 응용 프로그램을 클라우드로 이동하려는 경우
응용 프로그램에 소프트웨어 및 운영 체제의 복잡한 설치나 구성이 필요한 경우 Virtual Machines가 최상의 솔루션일 수 있습니다. Virtual Machines를 통해 다음을 수행할 수 있습니다.

* 가상 컴퓨터 갤러리를 사용하여 Windows, Linux 등의 운영 체제에서 시작한 후 응용 프로그램 요구 사항에 맞게 사용자 지정할 수 있습니다.
* Azure의 가상 컴퓨터에서 실행되도록 기존 온-프레미스 서버의 사용자 지정 이미지를 만들고 업로드할 수 있습니다.

### <a id="oss"></a>사이트가 오픈 소스 소프트웨어를 사용하며 Azure에서 호스트하려는 경우
App Service에서 오픈 소스 프레임워크가 지원되는 경우 응용 프로그램에 필요한 언어와 프레임워크는 자동으로 구성됩니다. App Service를 통해 다음 작업을 수행할 수 있습니다.

* [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] 및 [Python][Python] 등의 여러 인기 있는 오픈 소스 언어를 사용할 수 있습니다.
* WordPress, Drupal, Umbraco, DNN 및 다른 여러 타사 웹 응용 프로그램을 설정할 수 있습니다.
* 기존 응용 프로그램을 마이그레이션하거나 응용 프로그램 갤러리에서 새로 만들 수 있습니다.

App Service에서 오픈 소스 프레임워크가 지원되지 않는 경우에는 나머지 Azure 웹 호스팅 옵션 중 하나에서 해당 프레임워크를 실행할 수 있습니다. Virtual Machines를 선택하는 경우 Windows 또는 Linux 기반일 수 있는 컴퓨터 이미지에 소프트웨어를 설치 및 구성합니다.

### <a id="lob"></a>회사 네트워크에 연결해야 하는 LOB(기간 업무) 애플리케이션이 있는 경우
LOB(기간 업무) 애플리케이션을 만들려는 경우 웹 사이트에 회사 네트워크의 서비스 또는 데이터에 대한 직접 액세스 권한이 필요할 수 있습니다. App Service, 서비스 패브릭 및 Virtual Machines에서 [Azure Virtual Network 서비스](/azure/virtual-network/)를 사용하면 이와 같은 직접 액세스가 가능합니다. App Service에서 [VNET 통합 기능](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)을 사용하면 Azure 응용 프로그램을 회사 네트워크에서와 같이 실행할 수 있습니다.

### <a id="mobile"></a>모바일 클라이언트를 위해 REST API 또는 웹 서비스를 호스트하려는 경우
HTTP 기반 웹 서비스를 사용하면 모바일 클라이언트를 포함하여 다양한 클라이언트를 지원할 수 있습니다. ASP.NET Web API와 같은 프레임워크는 REST 서비스를 더 쉽게 만들고 소비할 수 있도록 Visual Studio와 통합됩니다.  이러한 서비스는 웹 끝점에서 노출되므로 Azure의 모든 웹 호스팅 기법을 사용하여 이 시나리오를 지원할 수 있습니다. 그러나 REST API를 호스트하는 데 적합한 옵션은 App Service입니다. App Service를 통해 다음 작업을 수행할 수 있습니다.

* [모바일 앱](../app-service-mobile/app-service-mobile-value-prop.md) 또는 API 앱을 빠르게 만들어 전 세계에 분산된 Azure 데이터 센터 중 하나에서 HTTP 웹 서비스를 호스팅할 수 있습니다.
* 기존 서비스를 마이그레이션하거나 새 서비스를 만들 수 있습니다.
* 단일 인스턴스로 가용성에 대한 SLA를 달성하거나 여러 전용 컴퓨터로 규모를 확장할 수 있습니다.
* 게시된 사이트를 사용하여 모바일 클라이언트를 비롯한 모든 HTTP 클라이언트에 REST API를 제공할 수 있습니다.

> [!NOTE]
> 계정을 등록하기 전에 Azure App Service를 시작하려면 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>로 이동합니다. Azure App Service에서 무료로 단기 스타터 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a id="nextsteps"></a> 다음 단계
세 가지 웹 호스팅 옵션에 대한 자세한 내용은 [Azure 소개](../fundamentals-introduction-to-azure.md)를 참조하세요.

응용 프로그램에 대해 선택한 옵션 사용을 시작하려면 다음 리소스를 참조하세요.

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Azure Virtual Machines](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
