---
title: "응용 프로그램 서비스 개요: Azure 스택 | Microsoft Docs"
description: "Azure 스택 앱 서비스의 개요"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 1884574bcb39a1cbbd95b481adabdd06ebd499a9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 개요에 대한 App Service
*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 앱 서비스를 azure은 Azure 스택을 사용할 수 있는 Microsoft Azure의 플랫폼으로-서비스 (PaaS) 제품입니다. 서비스 고객-내부 또는 외부 있습니다-웹, API 및 Azure 함수를 만들기 위한 모든 플랫폼 이나 장치의 응용 프로그램. 온-프레미스 응용 프로그램과 앱을 통합할 수 있으며 조직의 비즈니스 프로세스를 자동화 있습니다. Azure 스택 클라우드 운영자는 선택한 VM 리소스 공유 또는 전용된 Vm으로 완전히 관리 되는 가상 컴퓨터 (Vm)에서 고객 앱을 실행할 수 있습니다.

Azure 앱 서비스에는 비즈니스 프로세스를 자동화 하 고 호스팅 클라우드에 Api에 대 한 기능이 포함 됩니다. 단일 통합 서비스로 Azure 앱 서비스 사용 하면-웹 사이트, RESTful Api 및 비즈니스 프로세스-다양 한 구성 요소를 작성 합니다. 단일 솔루션으로.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Azure 스택 Azure 앱 서비스를 제공 하는 이유

다음은 App Service의 몇 가지 주요 기능입니다.
- **여러 언어 및 프레임 워크**: 응용 프로그램 서비스에 ASP.NET, Node.js, Java, PHP 및 Python에 대 한 고급 지원 합니다. 앱 서비스 Vm에서 Windows PowerShell 및 기타 스크립트 또는 실행 파일을 실행할 수도 있습니다.
- **DevOps 최적화**: 연속 통합 및 로컬 Git, GitHub, BitBucket 인 배포를 설정 합니다. 테스트 및 준비 환경을 통해 업데이트를 승격 합니다. Azure PowerShell 또는 플랫폼 간 명령줄 인터페이스 (CLI)를 사용 하 여 앱 서비스에서 앱을 관리 합니다.
- **Visual Studio 통합**: Visual Studio에서 전용된 도구를 만들고 응용 프로그램 배포 작업을 간소화 합니다.

## <a name="app-types-in-app-service"></a>App Service의 앱 유형

앱 서비스는 특정 워크 로드를 호스트 하기 위한 것이 각각 여러 응용 프로그램 형식을 제공 합니다.

- [웹 앱](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) 호스팅 웹 사이트 및 웹 응용 프로그램에 대 한 합니다.
- [API 앱](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) 호스팅 RESTful Api에 대 한 합니다.
- 이벤트 구동, 서버가 없는 작업을 호스팅하기 위한 azure 함수입니다.

Word 앱 여기 작업 실행 전용으로 사용 하는 호스팅 리소스를 참조 합니다. 예를 들어 "웹앱"의 경우 함께 브라우저에 대한 기능을 제공하는 계산 리소스와 응용 프로그램 코드를 모두 웹앱으로 생각하는 것이 익숙할 것입니다. 하지만 앱 서비스 웹 앱 호스팅 응용 프로그램 코드에 대 한 Azure 스택 제공 하는 계산 리소스입니다.

여러 앱 서비스 앱에서 다른 종류의 응용 프로그램으로 구성 될 수 있습니다. 예를 들어 응용 프로그램은 웹 프런트 엔드 이루어져 RESTful API 백 엔드를 수행할 수 있습니다.
- 프런트 엔드와 api를 단일 웹앱에 배포합니다.
- 프런트 엔드 코드를 웹앱에 배포하고 백 엔드 코드를 API 앱에 배포합니다.

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>App Service 계획의 정의

앱 서비스 리소스 공급자는 Azure 앱 서비스를 사용 하는 동일한 코드를 사용 합니다. 결과적으로, 일반적인 몇 가지 개념을 설명 하는 사항이 있습니다. 앱 서비스 가격 책정 컨테이너 응용 프로그램에 대 한 앱 서비스 계획을 라고 합니다. 앱을 유지 하기 위해 사용 하는 전용된 가상 컴퓨터의 집합을 나타냅니다. 지정된 된 구독 내에서 여러 앱 서비스 계획을 가질 수 있습니다.

Azure에서 공유 및 전용 작업 자가 있습니다. 공유 작업자 고밀도 다중 테 넌 트 응용 프로그램 호스팅를 지원 하 고 공유 작업자의 하나만 설정 되어 있습니다. 전용된 서버가 하나만 테 넌 트 별로 사용 및 세 가지 크기: 소형, 중형 및 대형 합니다. 이러한 용어를 사용 하 여 설명할 항상 온-프레미스 고객의 요구를 사용할 수 없습니다. Azure 스택 앱 서비스에서 리소스 공급자 관리자는 사용할 수 있도록 설정할 작업자 계층을 정의할 수 있습니다. 고유한 호스팅 요구 사항에 따라, 공유 작업자의 여러 집합 또는 다양 한 전용된 작업자 집합을 정의할 수 있습니다. 해당 작업자 계층 정의 사용 하 여 정의할 수 있습니다 다음 자신의 Sku 가격입니다.

## <a name="portal-features"></a>포털의 기능

Azure 스택 앱 서비스는 Azure 앱 서비스 사용 하는 백 엔드에서 true 인 동일한 UI를 사용 합니다. 일부 기능이 해제 되 고 Azure 스택 정상 작동 되지 않습니다. 아직 이러한 기능을 사용 하려면는 서비스나 Azure 관련 기대가 다음 Azure 스택의 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계


- [Azure 스택 앱 서비스를 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)
- [앱 서비스 리소스 공급자를 설치 합니다.](azure-stack-app-service-deploy.md)

또한을 수행 하려면 다른 [플랫폼 서비스 (PaaS) 서비스로](azure-stack-tools-paas-services.md)처럼는 [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md) 및 [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)합니다.
