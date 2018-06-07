---
title: Linux 가상 머신에 Apache Tomcat 설치 | Microsoft Docs
description: Linux를 실행하는 Azure Virtual Machines를 사용하여 Apache Tomcat7을 설치하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: 161a56a019f8c2c8ce5e3890e73ad5c5710e7b82
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Azure를 사용하여 Linux 가상 머신에 Tomcat7 설치
Apache Tomcat(또는 간단히 Tomcat, 이전에는 Jakarta Tomcat이라고도 함)은 ASF(Apache Software Foundation)에서 개발한 오픈 소스 웹 서버 및 서블릿 컨테이너입니다. Tomcat은 Sun Microsystems의 Java Servlet 및 JSP(JavaServer Pages) 사양을 구현합니다. Tomcat은 Java 코드를 실행할 수 있는 순수한 Java HTTP 웹 서버 환경을 제공합니다. 가장 단순한 구성의 경우 Tomcat은 단일 운영 체제 프로세스로 실행됩니다. 이 프로세스에서는 JVM(Java Virtual Machine)을 실행합니다. 브라우저에서 Tomcat으로 전송되는 모든 HTTP 요청은 Tomcat 프로세스에서 별도의 스레드로 처리됩니다.  

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델, 즉 [Azure Resource Manager 및 클래식](../../../resource-manager-deployment-model.md) 모델이 있습니다. 이 문서에서는 클래식 배포 모델을 사용하는 방법에 대해 설명합니다. 대부분의 새로운 배포에서는 Azure Resource Manager 모델을 사용하는 것이 좋습니다. Resource Manager 템플릿을 사용하여 Open JDK 및 Tomcat이 있는 Ubuntu VM을 배포하려면 [이 문서 ](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/)를 참조하세요.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

이 문서에서는 Tomcat7을 Linux 이미지에 설치하고 이를 Azure에 배포합니다.  

다음 내용을 배웁니다.  

* Azure에서 가상 머신을 만드는 방법
* Tomcat7을 위한 가상 머신을 준비하는 방법
* Tomcat7을 설치하는 방법

이미 Azure 구독이 있다고 가정합니다.  그렇지 않으면 [Azure 웹 사이트](https://azure.microsoft.com/)에서 무료 평가판을 등록할 수 있습니다. MSDN 구독이 있는 경우 [Microsoft Azure 특별 가격: MSDN, MPN 및 BizSpark 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)을 참조하세요. Azure에 대한 자세한 내용은 [Azure 정의](https://azure.microsoft.com/overview/what-is-azure/)를 참조하세요.

이 문서에서는 사용자가 Tomcat 및 Linux에 대한 기본적인 실무 지식을 갖추고 있다고 가정합니다.  

## <a name="phase-1-create-an-image"></a>1단계: 이미지 만들기
이 단계에서는 Azure에서 Linux 이미지를 사용하여 가상 머신을 만듭니다.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>1단계: SSH 인증 키 생성
SSH는 시스템 관리자에게 중요한 도구입니다. 그러나 사람이 식별할 수 있는 암호를 기반으로 하여 액세스 보안을 구성하는 것은 바람직하지 않습니다. 악의적인 사용자는 사용자 이름 및 취약한 암호를 기반으로 하는 시스템에 침입할 수 있습니다.

하지만 암호를 걱정하지 않고 원격 액세스를 허용하는 좋은 방법이 있습니다. 이 방법은 비대칭 암호화를 사용하는 인증으로 구성됩니다. 사용자의 개인 키가 인증을 부여합니다. 사용자 계정을 잠그면 암호 인증을 허용하지 않을 수도 있습니다.

이 방법의 또 다른 이점은 서버마다 다른 암호를 사용하여 로그인할 필요가 없다는 것입니다. 모든 서버에서 개인 키를 사용하여 인증할 수 있으므로 여러 암호를 기억할 필요가 없습니다.



SSH 인증 키를 생성하려면 다음 단계를 수행합니다.

1. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 위치에서 PuTTYgen을 다운로드 및 설치합니다.
2. Puttygen.exe를 실행합니다.
3. **생성** 을 클릭하여 키를 생성합니다. 이 과정에서 창의 빈 영역 위로 마우스를 이동하여 임의성을 높일 수 있습니다.  
   ![새 키 생성 단추를 보여 주는 PuTTY 키 생성기 스크린샷][1]
4. 생성 프로세스가 끝나면 Puttygen.exe에서 새 공개 키를 보여 줍니다.  
   ![새 공개 키와 개인 키 저장 단추를 보여 주는 PuTTY 키 생성기 스크린샷][2]
5. 공개 키를 선택하여 복사하고 publicKey.pem 파일에 저장합니다. 저장된 공개 키의 파일 형식은 원하는 공개 키와 다르므로 **공개 키 저장**을 클릭하지 마세요.
6. **개인 키 저장**을 클릭하고 privateKey.ppk 파일에 저장합니다.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>2단계: Azure Portal에서 이미지 만들기
1. [포털](https://portal.azure.com/)에서 작업 표시줄에서 **리소스 만들기**를 클릭하여 이미지를 만듭니다. 그런 다음, 필요에 따라 Linux 이미지를 선택합니다. 다음 예제에서는 Ubuntu 14.04 이미지를 사용합니다.
![새로 만들기 단추를 보여 주는 포털의 스크린샷][3]

2. **호스트 이름**에서 사용자와 인터넷 클라이언트가 이 가상 머신에 액세스하는 데 사용할 URL의 이름을 지정합니다. DNS 이름의 마지막 부분을 정의합니다(예: tomcatdemo). 그러면 Azure에서 tomcatdemo.cloudapp.net과 같은 URL을 생성합니다.  

3. **SSH 인증 키**에서 PuTTYgen으로 생성한 공개 키가 포함된 publicKey.pem 파일의 키 값을 복사합니다.  
![포털의 SSH 인증 키 상자][4]

4. 필요에 따라 다른 설정을 구성한 다음 **만들기**를 클릭합니다.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>2단계: Tomcat7에 대해 가상 컴퓨터 준비
이 단계에서는 Tomcat 트래픽에 대한 끝점을 구성한 다음 새 가상 머신에 연결합니다.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>1단계: 웹 액세스를 허용하도록 HTTP 포트 열기
Azure의 끝점은 공용 및 개인 포트와 함께 TCP 또는 UDP 프로토콜로 구성됩니다. 개인 포트는 가상 머신에서 서비스가 수신 대기하는 포트입니다. 공용 포트는 외부에서 Azure 클라우드 서비스가 들어오는 인터넷 기반 수신 트래픽을 수신 대기하는 포트입니다.  

8080 TCP 포트는 Tomcat에서 수신 대기하는 데 사용하는 기본 포트 번호입니다. Azure 끝점에서 이 포트를 열면 사용자와 다른 인터넷 클라이언트가 Tomcat 페이지에 액세스할 수 있습니다.  

1. 포털에서 **찾아보기** > **가상 머신**를 차례로 클릭한 다음 만든 가상 머신을 클릭합니다.  
   ![가상 머신 디렉터리의 스크린샷][5]
2. 가상 머신에 끝점을 추가하려면 **끝점** 확인란을 클릭합니다.
   ![끝점 확인란을 보여 주는 스크린샷][6]
3. **추가**를 클릭합니다.  

   1. 끝점의 경우 **끝점**에 끝점 이름을 입력한 다음 **공용 포트**에 80을 입력합니다.  

      80으로 설정하면 Tomcat에 액세스하는 데 사용되는 URL에 포트 번호를 포함할 필요가 없습니다. 예: http://tomcatdemo.cloudapp.net    

      다른 값(예: 81)으로 설정하면 Tomcat에 액세스할 수 있도록 URL에 포트 번호를 추가해야 합니다. 예: http://tomcatdemo.cloudapp.net:81/
   2. **개인 포트**에 8080을 입력합니다. 기본적으로 Tomcat은 8080 TCP 포트에서 수신 대기합니다. Tomcat의 기본 수신 대기 포트를 변경한 경우 **개인 포트**를 Tomcat 수신 대기 포트와 같도록 업데이트해야 합니다.  
      ![추가 명령, 공용 포트 및 개인 포트를 보여 주는 UI 스크린샷][7]
4. **확인** 을 클릭하여 가상 머신에 끝점을 추가합니다.

### <a name="step-2-connect-to-the-image-you-created"></a>2단계: 만든 이미지에 연결
가상 머신에 연결하기 위한 SSH 도구를 선택할 수 있습니다. 이 예제에서는 PuTTY를 사용합니다.  

1. 포털에서 가상 컴퓨터의 DNS 이름을 가져옵니다.
    1. **찾아보기** > **가상 머신**을 차례로 클릭합니다.
    2. 가상 머신의 이름을 선택한 다음 **속성**을 클릭합니다.
    3. **속성** 타일에서 **도메인 이름** 상자를 보고 DNS 이름을 가져옵니다.  

2. **SSH** 상자에서 SSH 연결의 포트 번호를 가져옵니다.  
![SSH 연결 포트 번호를 보여 주는 스크린샷][8]

3. [PuTTY](http://www.putty.org/)를 다운로드합니다.  

4. 다운로드한 후 Putty.exe 실행 파일을 클릭합니다. PuTTY 구성에서 가상 머신의 속성에서 가져온 호스트 이름과 포트 번호로 기본 옵션을 구성합니다.   
![PuTTY 구성 호스트 이름 및 포트 옵션을 보여 주는 스크린샷][9]

5. 왼쪽 창에서 **연결** > **SSH** > **인증**을 차례로 클릭한 다음 **찾아보기**를 클릭하여 privateKey.ppk 파일의 위치를 지정합니다. privateKey.ppk 파일에는 이미 이 문서의 "1단계: 이미지 만들기" 섹션의 PuTTYgen에서 생성한 개인 키가 있습니다.  
![연결 디렉터리 계층 구조와 찾아보기 단추를 보여 주는 스크린샷][10]

6. **열기**를 클릭합니다. 메시지 상자에 경고 메시지가 표시될 수도 있습니다. DNS 이름과 포트 번호를 올바르게 구성한 경우 **예**를 클릭합니다.
![알림을 보여 주는 스크린샷][11]

7. 사용자 이름을 입력하라는 메시지가 표시됩니다.  
![사용자 이름을 입력하는 위치를 보여 주는 스크린샷][12]

8. 이 문서 앞부분의 "1단계: 이미지 만들기" 섹션에서 가상 머신을 만드는 데 사용한 사용자 이름을 입력합니다. 다음과 유사한 출력이 표시됩니다.  
![인증 확인을 보여 주는 스크린샷][13]

## <a name="phase-3-install-software"></a>3단계: 소프트웨어 설치
이 단계에서는 Java Runtime Environment, Tomcat7 및 기타 Tomcat7 구성 요소를 설치합니다.  

### <a name="java-runtime-environment"></a>Java Runtime Environment
Tomcat은 Java로 작성되었습니다. JDK(Java Development Kit)에는 두 가지 종류의 JDK, 즉 OpenJDK와 Oracle JDK가 있습니다. 원하는 JDK를 선택할 수 있습니다.  

> [!NOTE]
> 두 JDK는 Java API의 클래스와 거의 동일한 코드를 갖추고 있지만 가상 시스템의 코드와는 다릅니다. OpenJDK는 개방형 라이브러리를 사용하는 반면 Oracle JDK는 폐쇄형 라이브러리를 사용합니다. Oracle JDK는 더 많은 클래스와 일부 수정된 버그를 포함하고 있지만 OpenJDK보다 더 안정적입니다.

#### <a name="install-openjdk"></a>OpenJDK 설치  

다음 명령을 사용하여 OpenJDK를 다운로드합니다.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* JDK 파일을 포함할 디렉터리를 만들려면 다음을 실행합니다.  

        sudo mkdir /usr/lib/jvm  
* JDK 파일을 /usr/lib/jvm/ 디렉터리에 추출하려면 다음을 실행합니다.  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Oracle JDK 설치


다음 명령을 사용하여 Oracle 웹 사이트에서 Oracle JDK를 다운로드합니다.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* JDK 파일을 포함할 디렉터리를 만들려면 다음을 실행합니다.  

        sudo mkdir /usr/lib/jvm  
* JDK 파일을 /usr/lib/jvm/ 디렉터리에 추출하려면 다음을 실행합니다.  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Oracle JDK를 기본 Java 가상 머신으로 설정하려면 다음을 수행합니다.  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Java 설치가 성공적인지 확인
다음과 같은 명령을 사용하여 Java Runtime Environment가 올바르게 설치되었는지 테스트할 수 있습니다.  

    java -version  

OpenJDK를 설치한 경우 다음과 같은 메시지가 표시됩니다. ![OpenJDK 설치 성공 메시지][14]

Oracle JDK를 설치한 경우 다음과 같은 메시지가 표시됩니다. ![Oracle JDK 설치 성공 메시지][15]

### <a name="install-tomcat7"></a>Tomcat7 설치
다음 명령을 사용하여 Tomcat7을 설치합니다.  

    sudo apt-get install tomcat7  

Tomcat7을 사용하지 않는 경우 이 명령의 적절한 변형을 사용합니다.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Tomcat7 설치가 성공적인지 확인
Tomcat7이 성공적으로 설치되었는지 확인하려면 Tomcat 서버의 DNS 이름을 찾습니다. 이 문서의 URL 예제는 http://tomcatexample.cloudapp.net/입니다. 다음과 같은 메시지가 표시되면 Tomcat7이 제대로 설치된 것입니다.
![Tomcat7 설치 성공 메시지][16]

### <a name="install-other-tomcat7-components"></a>기타 Tomcat7 구성 요소 설치
설치할 수 있는 다른 선택적 Tomcat 구성 요소가 있습니다.  

**sudo apt-cache search tomcat7** 명령을 사용하여 사용할 수 있는 모든 구성 요소를 확인합니다. 다음 명령을 사용하여 몇 가지 유용한 구성 요소를 설치합니다.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>4단계: Tomcat7 구성
이 단계에서는 Tomcat을 관리합니다.

### <a name="start-and-stop-tomcat7"></a>Tomcat7 시작 및 중지
Tomcat7 서버를 설치하면 자동으로 시작됩니다. 또한 다음 명령을 사용하여 시작할 수도 있습니다.   

    sudo /etc/init.d/tomcat7 start

Tomcat7을 중지하려면 다음을 수행합니다.

    sudo /etc/init.d/tomcat7 stop

Tomcat7의 상태를 보려면 다음을 수행합니다.

    sudo /etc/init.d/tomcat7 status

Tomcat 서비스를 다시 시작하려면 다음을 수행합니다. 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Tomcat7 관리
Tomcat 사용자 구성 파일을 편집하여 관리자 자격 증명을 설정할 수 있습니다. 다음 명령을 사용합니다.  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

다음은 예제입니다.  
![sudo vi 명령 출력을 보여 주는 스크린샷][17]  

> [!NOTE]
> 관리자 사용자 이름에 대한 강력한 암호를 만듭니다.  

이 파일을 편집한 후 다음 명령으로 Tomcat7 서비스를 다시 시작해야 변경 내용이 적용됩니다.  

    sudo /etc/init.d/tomcat7 restart  

브라우저를 열고 URL로 **http://<your tomcat server DNS name>/manager/html**을 입력합니다. 이 문서의 예제에서 URL은 http://tomcatexample.cloudapp.net/manager/html입니다.  

연결되면 다음과 유사한 페이지가 표시됩니다.  
![Tomcat 웹 응용 프로그램 관리자의 스크린샷][18]

## <a name="common-issues"></a>일반적인 문제
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>인터넷에서 Tomcat 및 Moodle이 있는 가상 머신에 액세스할 수 없음
#### <a name="symptom"></a>증상  
  Tomcat이 실행되고 있지만 브라우저에서 Tomcat 기본 페이지를 볼 수 없습니다.
#### <a name="possible-root-cause"></a>가능한 근본 원인   

  * Tomcat 수신 대기 포트는 Tomcat 트래픽에 대한 가상 머신 끝점의 개인 포트와 다릅니다.  

     공용 포트 및 개인 포트 끝점 설정을 확인하고, 개인 포트가 Tomcat 수신 대기 포트와 같은지 확인합니다. 가상 머신의 끝점 구성에 대한 지침은 이 문서의 "1단계: 이미지 만들기" 섹션을 참조하세요.  

     Tomcat 수신 대기 포트를 확인하려면 /etc/httpd/conf/httpd.conf(Red Hat 릴리스) 또는 /etc/tomcat7/server.xml(Debian 릴리스)을 엽니다. 기본적으로 Tomcat 수신 대기 포트는 8080입니다. 다음은 예제입니다.  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Debian 또는 Ubuntu와 같은 가상 머신을 사용하는 경우 Tomcat 수신 대기 기본 포트(예: 8081)를 변경하려면 해당 운영 체제의 포트도 열어야 합니다. 먼저 프로필을 엽니다.  

        sudo vi /etc/default/tomcat7  

     그런 다음 마지막 줄에 주석 처리를 제거하고 "아니요"를 "예"로 변경합니다.  

        AUTHBIND=yes
  2. 방화벽에서 Tomcat 수신 대기 포트를 사용하지 않도록 설정했습니다.

     로컬 호스트에서만 Tomcat 기본 페이지를 볼 수 있습니다. 문제는 주로 Tomcat에서 수신 대기하는 포트가 방화벽으로 차단되는 것입니다. w3m 도구를 사용하여 웹 페이지를 찾을 수 있습니다. 다음 명령은 w3m을 설치하고 Tomcat 기본 페이지로 이동합니다.  


        sudo yum  install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>해결 방법

  * Tomcat 수신 대기 포트가 가상 머신의 트래픽에 대한 끝점의 개인 포트와 다른 경우 개인 포트를 Tomcat 수신 대기 포트와 동일하게 변경해야 합니다.   
  2. 방화벽/iptables로 인해 문제가 발생하면 /etc/sysconfig/iptables에 다음 줄을 추가합니다. 두 번째 줄은 https 트래픽에만 필요합니다.  

      -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

      -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > 위의 줄은 -A INPUT -j REJECT --reject-with icmp-host-prohibited와 같이 액세스를 전역적으로 제한하는 줄 위에 있는지 확인합니다.



iptables를 다시 로드하려면 다음 명령을 실행합니다.

    service iptables restart

CentOS 6.3에서 테스트되었습니다.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>/var/lib/tomcat7/webapps/에 프로젝트 파일을 업로드할 때 권한이 거부됨
#### <a name="symptom"></a>증상
  SFTP 클라이언트(예: FileZilla)를 사용하여 가상 머신에 연결하고 /var/lib/tomcat7/webapps/로 이동하여 사이트를 게시하면 다음과 비슷한 오류 메시지가 표시됩니다.  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>가능한 근본 원인
  /var/lib/tomcat7/webapps 폴더에 액세스할 수 있는 권한이 없습니다.  
#### <a name="solution"></a>해결 방법  
  루트 계정에서 권한을 부여받아야 합니다. 해당 폴더의 소유권을 루트에서 컴퓨터를 프로비전할 때 사용한 사용자 이름으로 변경할 수 있습니다. 다음은 azureuser 계정 이름을 사용한 예제입니다.  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  -R 옵션을 사용하여 디렉터리 내의 모든 파일에 대해서도 권한을 적용합니다.  

  이 명령은 디렉터리에서도 작동합니다. -R 옵션은 디렉터리 내의 모든 파일과 디렉터리에 대한 권한을 변경합니다. 다음은 예제입니다.  

     sudo chown -R username:group directory  

  이 명령은 디렉터리 내에 있는 모든 파일과 디렉터리에 대한 소유권(사용자 및 그룹 둘 다)을 변경합니다.  

  다음 명령은 폴더 디렉터리의 권한만 변경하며, 디렉터리 내의 파일과 폴더는 변경하지 않습니다.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
