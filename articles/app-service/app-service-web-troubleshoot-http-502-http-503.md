---
title: "502 잘못된 게이트웨이, 503 서비스를 사용할 수 없음 오류 수정 | Microsoft Docs"
description: "Azure 앱 서비스에서 호스트되는 웹앱의 502 잘못된 게이트웨이 및 503 서비스를 사용할 수 없음 오류를 해결합니다."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "502 잘못된 게이트웨이, 503 서비스를 사용할 수 없음, 오류 503, 오류 502"
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Azure 웹앱의 "502 잘못된 게이트웨이" 및 "503 서비스를 사용할 수 없음" HTTP 오류 해결
"502 잘못된 게이트웨이" 및 "503 서비스를 사용할 수 없음" 오류는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에서 호스트되는 웹앱에서 일반적으로 나타나는 오류입니다. 이 문서는 이러한 오류를 해결하는 데 도움이 됩니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 이동한 다음 **지원 받기**를 클릭합니다.

## <a name="symptom"></a>증상
웹앱을 찾아볼 때 HTTP "502 잘못된 게이트웨이" 또는 HTTP "503 서비스를 사용할 수 없음" 오류를 반환합니다.

## <a name="cause"></a>원인
이 문제는 응용 프로그램 수준 문제가 원인이 되는 경우가 많습니다. 예를 들어:

* 긴 요청 시간
* 높은 메모리/CPU를 사용하는 응용 프로그램
* 예외로 인해 충돌하는 응용 프로그램

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>"502 잘못된 게이트웨이" 및 "503 서비스를 사용할 수 없음" 오류를 해결하기 위한 문제 해결 단계
문제 해결은 해결하는 순서대로 세 가지로 구분할 수 있습니다.

1. [응용 프로그램 작동을 관찰 및 감시](#observe)
2. [데이터 수집](#collect)
3. [문제 완화](#mitigate)

[App Service Web Apps](/services/app-service/web/)는 각 단계별로 다양한 옵션을 제공합니다.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. 응용 프로그램 작동을 관찰 및 감시 
#### <a name="track-service-health"></a>서비스 상태를 추적합니다.
Microsoft Azure는 서비스가 중단되거나 성능이 저하될 때마다 경고를 표시합니다. [Azure 포털](https://portal.azure.com/)에서 서비스의 상태를 추적할 수 있습니다. 자세한 내용은 [서비스 상태 추적](../monitoring-and-diagnostics/insights-service-health.md)을 참조하세요.

#### <a name="monitor-your-web-app"></a>웹앱 모니터링
이 옵션은 응용 프로그램의 문제를 해결할 수 있게 도와줍니다. 웹앱의 블레이드에서 **요청 및 오류** 타일을 클릭합니다. **메트릭** 블레이드는 추가 가능한 모든 메트릭을 보여줍니다.

모니터링 하고자 하는 웹앱의 일부 메트릭은 다음과 같습니다.

* 평균 메모리 작업 집합
* 평균 응답 시간
* CPU 시간
* 메모리 작업 집합
* 요청

![웹앱을 모니터링하여 502 잘못된 게이트웨이 및 503 서비스를 사용할 수 없음 HTTP 오류 해결](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

자세한 내용은 다음을 참조하세요.

* [Azure App Service에서 Web Apps 모니터링](web-sites-monitor.md)
* [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. 데이터 수집
#### <a name="use-the-azure-app-service-support-portal"></a>Azure 앱 서비스 지원 포털 사용
웹앱은 HTTP 로그, 이벤트 로그, 프로세스 덤프 등등을 확인하여 사용자의 웹앱과 연관된 문제 해결 기능을 제공합니다. **http://&lt;your app name>.scm.azurewebsites.net/Support**에서 지원 포털을 사용하여 모든 정보에 액세스할 수 있습니다.

Azure 앱 서비스 지원 포털은 일반적인 문제 해결 시나리오의 세 단계를 지원하기 위해 3개의 별도 탭을 제공합니다.

1. 현재 동작을 관찰하기
2. 진단 정보 수집 및 기본 제공 분석기를 실행하여 분석하기
3. 완화

지금 문제가 발생했다면, **분석** > **진단** > **바로 진단**을 클릭하여 HTTP 로그, 이벤트 뷰어 로그, 메모리 덤프, PHP 오류 로그 및 PHP 보고서를 수집하는 진단 세션을 생성합니다.

데이터가 수집되면, 데이터에 대한 분석하고 HTML 보고서를 제공합니다.

데이터를 다운로드할 경우 기본적으로 D:\home\data\DaaS folder에 저장됩니다.

Azure 앱 서비스 지원 포털에 대한 자세한 내용은 [Azure 웹 사이트의 지원 사이트 확장에 대한 새 업데이트](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites)를 참조하세요.

#### <a name="use-the-kudu-debug-console"></a>Kudu 디버그 콘솔 사용
웹앱은 사용자 환경에 대한 정보를 얻을 수 있는 JSON 끝점과 비슷한 디버깅, 탐색, 파일 업로드할 수 있는 디버그 콘솔과 함께 제공됩니다. 이 콘솔은 *Kudu 콘솔* 또는 사용자 웹앱에서는 *SCM 대시보드*라고 합니다.

**https://&lt;Your app name>.scm.azurewebsites.net/** 링크로 이동해서 대시보드에 액세스할 수 있습니다.

Kudu가 제공하는 것은 다음과 같습니다.

* 응용 프로그램에 대한 환경 설정
* 로그 스트림
* 진단 덤프
* Powershell cmdlet 및 기본 DOS 명령을 실행할 수 있는 콘솔을 디버깅합니다.

Kudu의 또 다른 유용한 기능은 응용 프로그램에 첫 번째 예외가 발생할 경우, 메모리 덤프를 만들기 위해 Kudu와 SysInternal 도구 Procdump를 사용할 수 있습니다. 메모리 덤프는 프로세스의 스냅숏이며 웹앱의 더욱 복잡한 문제를 해결하는 데 많은 도움을 줍니다.

Kudu에서 사용 가능한 기능에 대한 자세한 내용은 [사용자가 꼭 알아야 할 Azure 웹 사이트 온라인 도구](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)를 참조하세요.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. 문제 완화
#### <a name="scale-the-web-app"></a>웹앱의 크기를 조정합니다.
Azure App Service에서 성능과 처리량의 증가를 위해 사용자가 실행하는 응용 프로그램의 크기를 조정할 수 있습니다. 웹앱의 크기를 키우려면 두 가지 사항이 수반됩니다. App Service 계획을 더 높은 가격 책정 계층으로 바꿔야 하며, 높은 가격 계층으로 전환한 후에 특정 설정을 구성해야 합니다.

크기 조정에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 크기 조정](web-sites-scale.md)을 참조하세요.

또한 둘 이상의 인스턴스에서 응용 프로그램을 실행할 수 있습니다. 더 많은 처리 기능을 제공할 만 아니라, 어느 정도의 내결함성을 제공합니다. 하나의 인스턴스에서 프로세스가 다운되면, 또 다른 인스턴스에서 계속 요청을 처리합니다.

수동 또는 자동으로 크기를 조정할 수 있습니다.

#### <a name="use-autoheal"></a>AutoHeal를 사용
AutoHeal은 사용자가 선택한 설정(예: 구성 변경, 요청, 메모리 기반 제한 또는 요청을 실행하는데 필요한 시간)에 따라 앱에 대한 작업자 프로세스를 재활용 합니다. 대부분의 경우에는 프로세스를 재활용하는 방법이 문제를 해결하는 가장 빠른 방법입니다. Azure 포털 내에서 직접 웹앱을 재시작할 수도 있지만, AutoHeal은 자동으로 재시작을 수행하게 해줍니다. 몇 개의 트리거를 웹앱의 root web.config에 추가하기만 하면 됩니다. 이 설정은 사용자의 응용 프로그램이 .Net이 아니라도 같은 방식으로 작동됩니다.

자세한 내용은 [Auto-Healing Azure 웹 사이트](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)를 참조하세요.

#### <a name="restart-the-web-app"></a>웹앱 재시작
이 방법은 일회성 문제를 해결하는 가장 간단한 방법입니다. [Azure 포털](https://portal.azure.com/)또는 웹앱의 블레이드에서 앱을 멈추거나 재시작 하는 옵션을 사용할 수 있습니다.

 ![앱을 다시 시작하여 502 잘못된 게이트웨이 및 503 서비스를 사용할 수 없음 HTTP 오류 해결](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

또한, Azure Powershell을 사용하여 웹앱을 관리할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

