---
title: 클래식 Azure VM에서 Java 응용 프로그램 서버 실행
description: 이 자습서에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Windows 가상 머신을 만들고 Apache Tomcat 응용 프로그램 서버를 실행하도록 구성하는 방법을 보여 줍니다.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e13228a707e7dae4a4c2505154d01215c40b4716
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 가상 머신에서 Java 응용 프로그램 서버를 실행하는 방법
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Java 8 및 Tomcat에서 웹앱을 배포하는 Resource Manager 템플릿은 [여기](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/)를 참조하세요.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Azure에서 가상 머신을 사용하여 서버 기능을 제공할 수 있습니다. 한 예로, Java 응용 프로그램 서버(예: Apache Tomcat)를 호스트하도록 Azure에서 실행되는 가상 머신을 구성할 수 있습니다.

이 가이드를 완료하면 Azure에서 실행되는 가상 머신을 만들고 Java 응용 프로그램 서버에서 실행하도록 구성하는 방법을 이해할 수 있게 됩니다. 다음 작업을 배우고 수행합니다.

* Java 개발 키트(JDK)가 이미 설치된 가상 머신을 만드는 방법
* 가상 머신에 원격으로 로그인하는 방법
* 가상 머신에 Java 응용 프로그램 서버(Apache Tomcat)를 설치하는 방법
* 가상 머신의 끝점을 만드는 방법
* 방화벽에서 응용 프로그램 서버용 포트를 여는 방법

설치를 완료하면 Tomcat이 가상 머신에서 실행됩니다.

![Apache Tomcat을 실행하는 가상 머신][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>가상 머신을 만드는 방법
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
2. **리소스 만들기**, **계산**을 차례로 클릭한 다음, **추천 앱**에서 **모두 보기**를 클릭합니다.
3. **JDK**를 클릭한 다음 **JDK** 창에서 **JDK 8**을 클릭합니다.  
   JDK 8에서 실행할 준비가 되지 않은 레거시 응용 프로그램이 있는 경우 **JDK 6** 및 **JDK 7**을 지원하는 가상 머신 이미지를 사용할 수 있습니다.
4. JDK 8 창에서 **클래식**을 선택한 다음 **만들기**를 클릭합니다.
5. **기본 사항** 블레이드에서 다음을 수행합니다.
   1. 가상 머신의 이름을 지정합니다.
   2. **사용자 이름** 필드에 관리자의 이름을 입력합니다. 이 이름과 다음 필드에 있는 연결된 암호를 기억해 두세요. 가상 머신에 원격으로 로그인할 때 필요합니다.
   3. **새 암호** 필드에 암호를 입력하고 **암호 확인** 필드에 다시 입력합니다. 이 암호는 관리자 계정 암호입니다.
   4. 적합한 **구독**을 선택합니다.
   5. **리소스 그룹**에 대해 **새로 만들기**를 클릭하고 새 리소스 그룹의 이름을 입력합니다. 또는 **기존 항목 사용**을 클릭하고 사용 가능한 리소스 그룹 중 하나를 선택합니다.
   6. 가상 머신이 있는 위치(예: **미국 중남부**)를 선택합니다.
6. **다음**을 클릭합니다.
7. **가상 머신 이미지 크기** 블레이드에서 **A1 표준** 또는 적절한 다른 이미지를 선택합니다.
8. **선택**을 클릭합니다.

9. **설정** 블레이드에서 **확인**을 클릭합니다. Azure에서 제공하는 기본값을 사용할 수 있습니다.  
10. **요약** 블레이드에서 **확인**을 클릭합니다.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>가상 컴퓨터에 원격으로 로그인하려면 다음을 수행합니다.
1. [Azure 포털](https://portal.azure.com)에 로그온합니다.
2. **가상 머신(클래식)** 을 클릭합니다. 필요한 경우 서비스 범주 아래의 왼쪽 하단에 있는 **추가 서비스**를 클릭합니다. **가상 머신(클래식)** 항목이 **Compute** 그룹에 나열됩니다.
3. 로그인할 가상 머신의 이름을 클릭합니다.
4. 가상 머신이 시작되면 창 위쪽의 메뉴를 통해 연결할 수 있습니다.
5. **Connect**를 클릭합니다.
6. 가상 머신에 연결해야 한다는 메시지에 응답합니다. 일반적으로 연결 세부 정보가 포함된 .rdp 파일을 열거나 저장해야 합니다. .rdp 파일의 첫 줄 끝 부분으로 url:port를 복사한 다음 원격 로그인 응용 프로그램에 붙여 넣어야 할 수 있습니다.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>가상 머신에 Java 응용 프로그램 서버를 설치하는 방법
Java 응용 프로그램 서버를 가상 컴퓨터로 복사하거나 설치 관리자를 통해 Java 응용 프로그램 서버를 설치할 수 있습니다.

이 자습서에서는 설치할 Java 응용 프로그램 서버로 Tomcat을 사용합니다.

1. 가상 머신에 로그인하는 경우 [Apache Tomcat](http://tomcat.apache.org/download-80.cgi)에 대한 브라우저 세션을 엽니다.
2. **32-bit/64-bit Windows Service Installer**링크를 두 번 클릭합니다. 이 기술을 사용하면 Tomcat이 Windows 서비스로 설치됩니다.
3. 메시지가 표시되면 설치 관리자를 실행하도록 선택합니다.
4. **Apache Tomcat Setup** 마법사에서 프롬프트에 따라 Tomcat을 설치합니다. 이 자습서에서는 기본값을 그대로 사용해도 됩니다. **Apache Tomcat 설정 마법사 완료** 대화 상자가 표시되면 옵션으로 제공되는 **Apache Tomcat 실행**을 선택하여 Tomcat을 바로 시작할 수 있습니다. **Finish** 를 클릭하여 Tomcat 설치 프로세스를 완료합니다.

## <a name="to-start-tomcat"></a>Tomcat 시작 방법

가상 머신에서 명령 프롬프트를 열고 **net&nbsp;start&nbsp;Tomcat8** 명령을 실행하여 Tomcat을 수동으로 시작할 수 있습니다.

Tomcat이 실행되면 가상 머신의 브라우저에서 URL <http://localhost:8080>을 입력하여 Tomcat에 액세스할 수 있습니다.

Tomcat이 외부 컴퓨터에서 실행되는 것을 보려면 끝점을 만들고 포트를 열어야 합니다.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>가상 머신의 끝점을 만드는 방법
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **가상 머신(클래식)** 을 클릭합니다.
3. Java 응용 프로그램 서버를 실행하는 가상 머신의 이름을 클릭합니다.
4. **끝점**을 클릭합니다.
5. **추가**를 클릭합니다.
6. **끝점 추가** 대화 상자에서 다음을 수행합니다.
   1. 끝점의 이름(예: **HttpIn**)을 지정합니다.
   2. 프로토콜에서 **TCP**를 선택합니다.
   3. 공용 포트에서 **80** 을 지정합니다.
   4. 개인 포트에서 **8080** 을 지정합니다.
   5. 부동 IP 주소에서 **사용 안 함**을 선택합니다.
   6. 액세스 제어 목록을 그대로 둡니다.
   7. **확인** 단추를 클릭하여 대화 상자를 닫고 끝점을 만듭니다.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>가상 머신의 방화벽에서 포트를 여는 방법
1. 가상 머신에 로그인합니다.
2. **Windows 시작**을 클릭합니다.
3. **제어판**을 클릭합니다.
4. **시스템 및 보안**, **Windows 방화벽**, **고급 설정**을 차례로 클릭합니다.
5. **인바운드 규칙**을 선택하고 **새 규칙**을 클릭합니다.  
   ![새 인바운드 규칙][NewIBRule]
6. **규칙 유형**에 대해 **포트**를 선택하고 **다음**을 클릭합니다.  
   ![새 인바운드 규칙 포트][NewRulePort]
7. **프로토콜 및 포트** 화면에서 **TCP**를 선택하고 **8080**을 **특정 로컬 포트**로 지정한 후 **다음**을 클릭합니다.  
  ![새 인바운드 규칙][NewRuleProtocol]
8. **작업** 화면에서 **연결 허용**을 선택하고 **다음**을 클릭합니다.
   ![새 인바운드 규칙 동작][NewRuleAction]
9. **프로필** 화면에서 **도메인**, **개인** 및 **공용**이 선택되었는지 확인하고 **다음**을 클릭합니다.
   ![새 인바운드 규칙 프로필][NewRuleProfile]
10. **이름** 화면에서 규칙 이름(예: **HttpIn**)(규칙 이름은 끝점 이름과 일치하지 않아도 됨)을 지정하고 **마침**을 클릭합니다.  
    ![새 인바운드 규칙 이름][NewRuleName]

이제 외부 브라우저에서 Tomcat 웹 사이트를 볼 수 있어야 합니다. 브라우저의 주소 창에 **http://*your\_DNS\_name*.cloudapp.net** 형식의 URL을 입력합니다. 여기서 ***your\_DNS\_name***은 가상 머신을 만들 때 지정한 DNS 이름입니다.

## <a name="application-lifecycle-considerations"></a>응용 프로그램 수명 주기 고려 사항
* 사용자 고유의 WAR(웹 응용 프로그램 보관)을 만들어 **webapps** 폴더에 추가할 수 있습니다. 예를 들어 기본 JSP(Java 서비스 페이지) 동적 웹 프로젝트를 만들어 WAR 파일로 내보냅니다. 다음으로, 이 WAR을 가상 머신의 Apache Tomcat **webapps** 폴더에 복사한 후 브라우저에서 실행합니다.
* 기본적으로 Tomcat 서비스가 설치될 때 수동으로 시작되도록 설정됩니다. 서비스 스냅인을 사용하여 자동으로 시작되도록 전환할 수 있습니다. **Windows 시작**, **관리 도구**, **서비스**를 차례로 클릭하여 서비스 스냅인을 시작합니다. **Apache Tomcat** 서비스를 두 번 클릭하고 **시작 유형**을 **자동**으로 설정합니다.

    ![서비스가 자동으로 시작되도록 설정][service_automatic_startup]

    Tomcat이 자동으로 시작되도록 설정할 경우의 이점은 가상 컴퓨터가 다시 부팅되면(예를 들어 재부팅이 필요한 소프트웨어 업데이트가 설치된 후) Tomcat이 실행되기 시작된다는 점입니다.

## <a name="next-steps"></a>다음 단계
Azure Storage, Service Bus, SQL Database 등 Java 응용 프로그램에 포함할 기타 서비스에 대해 자세히 알아볼 수 있습니다. [Java 개발자 센터](https://azure.microsoft.com/develop/java/)에서 제공되는 정보를 확인하세요.

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
