---
title: Azure Resource Manager 템플릿 샘플 - App Service | Microsoft Docs
description: App Service의 Web Apps 기능용 Azure Resource Manager 템플릿 샘플
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Web Apps용 Azure Resource Manager 템플릿

다음 표는 Azure App Service의 Web Apps 기능용 Azure Resource Manager 템플릿 링크를 포함합니다. 웹앱 템플릿을 만들 때 발생하는 일반적인 오류 방지에 대한 권장 사항은 [Azure Resource Manager 템플릿으로 웹앱을 배포하는 지침](web-sites-rm-template-guidance.md)을 참조하세요.

| | |
|-|-|
|**웹앱 배포**||
| [GitHub 리포지토리에 연결된 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| GitHub에서 코드를 끌어오는 Azure 웹앱을 배포합니다. |
| [사용자 지정 배포 슬롯이 있는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| 사용자 지정 배포 슬/환경이 있는 Azure 웹앱을 배포합니다. |
|**웹앱 구성**||
| [Key Vault의 웹앱 인증서](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Azure Key Vault 비밀의 Azure 웹앱 인증서를 배포하여 SSL 바인딩에 사용합니다. |
| [사용자 지정 도메인이 있는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| 사용자 지정 호스트 이름이 있는 Azure 웹앱을 배포합니다. |
| [사용자 지정 도메인 및 SSL이 있는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| 사용자 지정 호스트 이름이 있는 Azure 웹앱을 배포하고 SSL 바인딩을 위해 Key Vault에서 웹앱 인증서를 가져옵니다. |
| [GoLang 확장이 있는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| GoLang 사이트 확장을 사용하여 Azure 웹앱을 배포합니다. 그런 다음, Azure의 GoLang에서 개발된 웹 응용 프로그램을 실행할 수 있습니다. |
| [Java 8 및 Tomcat 8이 있는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8 및 Tomcat 8을 사용하여 Azure 웹앱을 배포합니다. 그런 다음, Azure에서 Java 응용 프로그램을 실행할 수 있습니다. |
|**Linux 웹앱**||
| [MySQL을 사용하는 Linux의 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Azure Database for MySQL로 Linux에 Azure Web App을 배포합니다. |
| [PostgreSQL을 사용하는 Linux의 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Azure Database for PostgreSQL로 Linux에 Azure 웹앱을 배포합니다. |
|**연결된 리소스를 사용하는 웹앱**||
| [MySQL을 사용하는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Azure Database for MySQL로 Windows에 Azure 웹앱을 배포합니다. |
| [PostgreSQL을 사용하는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Azure Database for PostgreSQL로 Windows에 Azure 웹앱을 배포합니다. |
| [SQL Database를 사용하는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Azure 웹앱과 SQL 데이터베이스를 기본 서비스 수준에서 배포합니다. |
| [Blob Storage 연결 문자열을 사용하는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Azure Blob 저장소 연결 문자열을 사용하여 Azure 웹앱을 배포합니다. 그런 다음, 웹앱에서 Blob 저장소를 사용할 수 있습니다. |
| [Redis Cache를 사용하는 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Redis Cache로 Azure 웹앱을 배포합니다. |
|**PowerApps용 App Service Environment**||
| [App Service Environment v2 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | 가상 네트워크에 App Service Environment v2를 만듭니다. |
| [ILB 주소로 App Service Environment v2 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | 개인 내부 부하 분산 장치 주소로 가상 네트워크에 App Service Environment v2를 만듭니다. |
| [ILB App Service Environment 또는 ILB App Service Environment v2에 대한 기본 SSL 인증서 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | ILB App Service Environment 또는 ILB App Service Environment v2에 대한 기본 SSL 인증서를 구성합니다. |
| | |
