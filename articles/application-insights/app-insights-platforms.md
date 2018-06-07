---
title: 'Application Insights: 언어, 플랫폼 및 통합 | Microsoft Docs'
description: Application Insights에 사용할 수 있는 언어, 플랫폼 및 통합
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mbullwin; olegan
ms.openlocfilehash: 8aec8567fd15865887a5fe5ded8b45cad0290bf9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>개발자 분석: 언어, 플랫폼 및 통합
이러한 항목은 타사에서 제공하는 구현을 포함하여 이미 알고 있는 [Application Insights](app-insights-overview.md) 의 구현입니다.

## <a name="languages---officially-supported-by-application-insights-team"></a>언어 - Application Insights 팀에서 공식적으로 지원
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [JavaScript 웹 페이지](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>언어 - 커뮤니티 지원
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [다른 항목](#projects)

## <a name="platforms-and-frameworks"></a>플랫폼 및 프레임워크
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET - 이미 운영 중인 앱용](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](app-insights-mobile-center-quickstart.md)(App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android)(App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* 웹 및 작업자 역할을 모두 포함하는 [Azure Cloud Services](app-insights-cloudservices.md)
* [Azure 기능](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](app-insights-mobile-center-quickstart.md)(App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS)(App Center)
* [J2EE](app-insights-java-get-started.md)
* [J2EE - 이미 운영 중인 앱용](app-insights-java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [안전 스택](https://safe-stack.github.io/docs/template-azure-ai/)
* [스프링](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [유니버설 Windows 앱](app-insights-mobile-center-quickstart.md)(App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows 데스크톱 응용 프로그램, 서비스 및 작업자 역할](app-insights-windows-desktop.md)
* [다른 항목](#projects)

## <a name="logging-frameworks"></a>로깅 프레임워크
* [Log4Net, NLog 또는 System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J 또는 Logback](app-insights-java-trace-logs.md)
* [의미 체계 로깅(SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - [의미 체계 로깅 응용 프로그램 블록](https://msdn.microsoft.com/library/dn440729.aspx)과 통합
* [클라우드 기반 부하 테스트](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash 플러그 인](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>콘텐츠 관리 시스템
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>내보내기 및 데이터 분석
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> 사용자 고유의 SDK를 빌드합니다
아직 사용자 언어나 플랫폼에 대한 SDK가 없다면 빌드하고자 합니까? [GitHub에서 Application Insights SDK 프로젝트](https://github.com/Microsoft/AppInsights-Home)에 나열된 기존 SDK의 코드를 살펴보십시오.
