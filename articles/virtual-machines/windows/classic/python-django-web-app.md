---
title: "Windows Server Azure VM에서 Django 웹앱 | Microsoft Docs"
description: "클래식 배포 모델로 만든 Windows Server 2012 R2 Datacenter VM을 사용하여 Azure에서 Django 기반 웹 사이트를 호스트하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Windows Server VM의 Django Hello World 웹앱

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하는 [Azure Resource Manager와 클래식 배포 모델](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다. 대부분의 새로운 배포에서는 Azure Resource Manager 모델을 사용하는 것이 좋습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

이 자습서는 Azure Virtual Machines의 Windows Server에서 Django 기반 웹 사이트를 호스트하는 방법을 보여 줍니다. 이 자습서에서는 사전에 Azure 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 Django 기반 응용 프로그램을 실행할 수 있게 됩니다.

방법 배우기:

* Django를 호스트하도록 Azure 가상 컴퓨터를 설정합니다. 이 자습서에서는 **Windows Server**에서 작업을 수행하는 방법을 설명하지만, Azure에서 호스트되는 Linux VM에 동일한 작업을 수행할 수 있습니다.
* Windows에서 새 Django 응용 프로그램을 만듭니다.

자습서는 기본적인 Hello World 웹 응용 프로그램을 빌드하는 방법을 보여 줍니다. 응용 프로그램은 Azure 가상 컴퓨터에 호스트됩니다.

다음 스크린샷에 완성된 응용 프로그램이 나와 있습니다.

![Azure의 hello world 페이지가 표시된 브라우저 창][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Django를 호스트하기 위해 Azure 가상 컴퓨터 만들기 및 설정

1. Windows Server 2012 R2 Datacenter 배포를 사용하여 Azure 가상 컴퓨터를 만들려면 참조 [Azure Portal에서 Windows를 실행하는 가상 컴퓨터 만들기](tutorial.md)를 참조하세요.
2. 웹을 통해 들어오는 포트 80 트래픽을 가상 컴퓨터의 포트 80으로 보내도록 Azure를 설정합니다.
   
   1. Azure Portal에서 대시보드로 이동하고 새로 만든 가상 컴퓨터를 선택합니다.
   2. **끝점**을 클릭한 후 **추가**를 클릭합니다.

     ![끝점 추가](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. **끝점 추가** 페이지에서 **이름**에 **HTTP**를 입력합니다. 공용 및 개인 TCP 포트를 **80**에 설정합니다.

     ![이름을 입력하고 공용 및 개인 포트를 설정](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. **확인**을 클릭합니다.
     
3. 대시보드에서 VM을 선택합니다. RDP(원격 데스크톱 프로토콜)를 사용하여 새로 만든 Azure 가상 컴퓨터에 원격으로 로그인하려면 **연결**을 클릭합니다.  

> [!IMPORTANT] 
> 다음 지침에서는 사용자가 가상 컴퓨터에 올바르게 로그인한 것으로 가정합니다. 또한 로컬 컴퓨터에 없는 명령이 가상 컴퓨터에 발행하였다고 가정합니다.

## <a id="setup"> </a>Python, Django 및 WFastCGI 설치
> [!NOTE]
> Internet Explorer를 사용하여 다운로드하려면 Internet Explorer **보안 강화 구성** 설정을 구성해야 할 수 있습니다. 이 작업을 수행하려면 **시작** > **관리 도구** > **서버 관리자** > **로컬 서버**를 클릭합니다. **IE 보안 강화 구성**을 클릭한 후 **끄기**를 선택합니다.

1. [python.org][python.org]에서 최신 버전의 Python 2.7 또는 Python 3.4를 설치합니다.
2. pip를 사용하여 wfastcgi 및 django 패키지를 설치합니다.
   
    Python 2.7의 경우 다음 명령을 사용합니다.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Python 3.4의 경우 다음 명령을 사용합니다.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>FastCGI를 포함하는 IIS 설치
* FastCGI 지원을 통해 IIS(인터넷 정보 서비스)를 설치합니다. 이 설치를 실행하려면 몇 분 정도 걸릴 수 있습니다.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>새 Django 응용 프로그램 만들기
1. C:\inetpub\wwwroot에서 새 Django 프로젝트를 만들려면 다음 명령을 입력합니다.
   
   Python 2.7의 경우 다음 명령을 사용합니다.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Python 3.4의 경우 다음 명령을 사용합니다.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![New-AzureService 명령의 결과](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. `django-admin` 명령은 Django 기반 웹 사이트의 기본 구조를 생성합니다.
   
   * `helloworld\manage.py`는 Django 기반 웹 사이트의 호스팅을 시작 및 중지하는 데 도움이 됩니다.
   * `helloworld\helloworld\settings.py`에는 응용 프로그램에 대한 Django 설정이 있습니다.
   * `helloworld\helloworld\urls.py`에는 각 URL과 뷰 사이 매핑 코드가 있습니다.
3. C:\inetpub\wwwroot\helloworld\helloworld 디렉터리에서 views.py라는 새 파일을 만듭니다. 이 파일에는 “hello world” 페이지를 렌더링하는 뷰가 포함되어 있습니다. 코드 편집기에서 다음 명령을 입력합니다.
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. urls.py 파일의 콘텐츠를 다음 명령으로 바꿉니다.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>IIS 설정
1. 전역 applicationhost.config 파일에서 처리기 섹션의 잠금을 해제합니다.  이렇게 하면 web.config 파일에서 Python 처리기를 사용할 수 있습니다. 다음 명령을 추가합니다.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. WFastCGI를 활성화합니다. 이렇게 하면 Python 인터프리터 실행 파일과 wfastcgi.py 스크립트를 참조하는 전역 applicationhost.config 파일에 응용 프로그램이 추가됩니다.
   
    Python 2.7에서:
   
        C:\python27\scripts\wfastcgi-enable
   
    Python 3.4에서:
   
        C:\python34\scripts\wfastcgi-enable
3. C:\inetpub\wwwroot\helloworld에서 web.config 파일을 만듭니다. `scriptProcessor` 특성의 값은 이전 단계의 출력과 일치해야 합니다. wfastcgi 설정에 대 한 자세한 내용은 [pypi wfastcgi][wfastcgi]를 참조하세요.
   
   Python 2.7에서:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   Python 3.4에서:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Django 프로젝트 폴더를 가리키도록 IIS 기본 웹 사이트의 위치를 업데이트합니다.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. 브라우저에서 웹 페이지를 로드합니다.

![Azure의 hello world 페이지가 표시된 브라우저 창][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Azure 가상 컴퓨터 종료
이 자습서를 완료하면 자습서용으로 만든 Azure VM을 종료하거나 제거하는 것이 좋습니다. 그러면 다른 자습서에 대한 리소스가 해제되어 Azure 사용량 요금이 발생하는 것을 방지할 수 있습니다.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
