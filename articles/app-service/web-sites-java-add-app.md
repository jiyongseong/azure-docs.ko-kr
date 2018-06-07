---
title: Azure App Service Web Apps에 Java 응용 프로그램 추가
description: 이 자습서에서는 페이지 또는 응용 프로그램을 Java를 사용하도록 이미 구성된 Azure App Service Web Apps의 인스턴스를 추가하는 방법을 보여줍니다.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 71009370282fcfbd71c00b30d4ea22802035714d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Azure App Service Web Apps에 Java 응용 프로그램 추가
[Azure 앱 서비스에서 Java 웹 앱 만들기](app-service-web-get-started-java.md)에 설명된 대로 [Azure App Service][Azure App Service]에서 Java 웹 앱을 초기화하면, **webapps** 폴더에 WAR을 배치하여 응용 프로그램을 업로드할 수 있습니다.

**webapps** 폴더의 탐색 경로는 Web Apps 인스턴스 설정 방법에 따라 다릅니다.

* Azure Marketplace를 사용하여 웹앱을 설정한 경우 **webapps** 폴더의 경로는 **d:\home\site\wwwroot\bin\application\_server\webapps**의 형식이며, 여기서 **application\_server**는 Web Apps 인스턴스에 적용되는 응용 프로그램 서버의 이름입니다. 
* Azure 구성 UI를 사용하여 웹앱을 설정한 경우 **webapps** 폴더의 경로는 **d:\home\site\wwwroot\webapps**의 형식입니다. 

소스 제어를 사용하여 [연속 통합 시나리오](app-service-continuous-deployment.md)에 포함된 응용 프로그램 또는 웹 페이지를 업로드할 수 있습니다. 또한 FTP는 응용 프로그램 또는 웹 페이지 업로드를 위한 옵션입니다. FTP를 통해 응용 프로그램을 배포하는 방법에 대한 자세한 내용은 [FTP를 사용한 앱 배포](app-service-deploy-ftp.md)를 참조하세요.

Tomcat 웹 앱에 대한 참고 사항: **webapps** 폴더에 WAR 파일을 업로드하면 Tomcat 응용 프로그램 서버에서 해당 파일을 추가했는지 검색한 후 자동으로 로드합니다. 파일(WAR 이외 파일)을 루트 디렉터리로 복사한 경우에는 해당 파일을 사용하기 전에 응용 프로그램 서버를 다시 시작해야 합니다. Azure에서 실행되는 Tomcat Java 웹 앱의 자동 로드 기능은 추가 중인 새 WAR 파일이나 **webapps** 폴더에 추가된 새 파일 또는 디렉터리를 기반으로 합니다. 

<a name="see-also"></a>

## <a name="see-also"></a>참고 항목
Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

[application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
