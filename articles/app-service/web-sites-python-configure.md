---
title: "Azure App Service Web Apps에서 Python 구성"
description: "이 자습서에서는 Azure App Service Web Apps에서 기본 WSGI(Web Server Gateway Interface) 규격 Python 응용 프로그램을 제작 및 구성하는 옵션을 설명합니다."
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: fa5f9afbc595f06bd41e8670fab7730b610f570e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Azure App Service Web Apps에서 Python 구성
이 자습서에서는 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)에서 기본 WSGI(Web Server Gateway Interface) 규격 Python 응용 프로그램을 제작 및 구성하는 옵션을 설명합니다.

또한 가상 환경 및 requirements.txt를 사용한 패키지 설치 등 Git 배포의 추가 기능에 대해 설명합니다.

## <a name="bottle-django-or-flask"></a>Bottle, Django 또는 Flask
Azure Marketplace에는 Bottle, Django 및 Flask 프레임워크용 템플릿이 들어 있습니다. Azure App Service에서 첫 번째 웹앱을 개발하는 경우 Azure Portal에서 신속하게 만들 수 있습니다.

* [Bottle을 사용하여 웹앱 만들기](https://portal.azure.com/#create/PTVS.Bottle)
* [Django를 사용하여 웹앱 만들기](https://portal.azure.com/#create/PTVS.Django)
* [Flask를 사용하여 웹앱 만들기](https://portal.azure.com/#create/PTVS.Flask)

## <a name="web-app-creation-on-azure-portal"></a>Azure Portal에서 웹앱 만들기
이 자습서에서는 기존 Azure 구독 및 Azure Portal에 대한 액세스 권한이 있다고 가정합니다.

기존 웹앱이 없는 경우 [Azure Portal](https://portal.azure.com)에서 웹앱을 만들 수 있습니다. 왼쪽 위 모서리에서 **리소스 만들기** > **웹 + 모바일** > **웹 응용 프로그램**을 클릭합니다.

## <a name="git-publishing"></a>Git 게시
[Azure App Service에 로컬 Git 배포](app-service-deploy-local-git.md)의 지침에 따라 새로 만든 웹앱에 대한 Git 게시를 구성합니다. 이 자습서에서는 Git을 사용하여 Python 웹앱을 만들고 관리하며 Azure App Service에 게시합니다.

Git 게시가 설정되면 Git 리포지토리가 생성되고 웹앱과 연결됩니다. 리포지토리의 URL이 표시되고 로컬 개발 환경에서 클라우드로 데이터를 보내는 데 사용할 수 있습니다. Git을 통해 응용 프로그램을 게시하려면 Git 클라이언트가 설치되어 있는지 확인하고 제공된 지침에 따라 웹앱 콘텐츠를 Azure App Service에 게시합니다.

## <a name="application-overview"></a>응용 프로그램 개요
다음 섹션에서는 다음 파일을 만듭니다. 이러한 파일은 Git 리포지토리의 루트에 배치해야 합니다.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>WSGI 처리기
WSGI는 웹 서버와 Python 간 인터페이스를 정의하는 [PEP 3333](http://www.python.org/dev/peps/pep-3333/) (영문)에 설명된 Python 표준입니다. WSGI는 Python을 사용하여 다양한 웹 응용 프로그램 및 프레임워크 쓰기에 대해 표준화된 인터페이스를 제공합니다. 오늘날 인기 있는 Python 웹 프레임워크는 WSGI를 사용합니다. Azure App Service Web Apps는 그러한 프레임워크에 대한 지원을 제공하며, 고급 사용자는 사용자 지정 처리기가 WSGI 사양 지침을 따르는 경우 고유한 프레임워크를 제작할 수도 있습니다.

다음은 사용자 지정 처리기를 정의하는 `app.py`의 예입니다.

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

`python app.py`를 사용하여 이 응용 프로그램을 로컬로 실행한 다음 웹 브라우저에서 `http://localhost:5555`로 이동합니다.

## <a name="virtual-environment"></a>가상 환경
이전의 예제 앱에는 외부 패키지가 필요 없지만 응용 프로그램에 일부 외부 패키지가 필요할 수 있습니다.

외부 패키지 종속성을 관리하도록 도와주기 위해 Azure Git 배포에서는 가상 환경 만들기를 지원합니다.

Azure에서는 리포지토리의 루트에서 requirements.txt를 발견한 경우 `env`라는 가상 환경으로 자동으로 만듭니다. 이는 최초 배포 또는 선택한 Python 런타임이 변경된 이후의 모든 배포 중에 발생합니다.

개발을 위해 가상 환경을 로컬로 만들 수 있지만 이를 Git 리포지토리에 포함하지 마세요.

## <a name="package-management"></a>패키지 관리
requirements.txt에 나열된 패키지는 pip를 사용하여 가상 환경에 자동으로 설치됩니다. 이는 모든 배포에서 발생하지만 패키지가 이미 설치된 경우에는 pip에서 설치를 건너뜁니다.

예제 `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Python 버전
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

예제 `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
web.config 파일을 만들어 서버에서 요청을 처리하는 방법을 지정해야 합니다.

web.x.y.config 파일(여기서 x.y는 선택한 Python 런타임과 일치)이 리포지토리에 있으면 Azure에서 해당 파일을 web.config로 자동으로 복사합니다.

다음 web.config 예제는 다음 섹션에 설명된 가상 환경 프록시 스크립트를 기반으로 합니다.  이는 위 예제 `app.py` 에서 사용된 WSGI 처리기에 적용됩니다.

Python 2.7용 예제 `web.config` :

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Python 3.4용 예제 `web.config` :

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


고정 파일은 성능 향상을 위해 Python 코드를 거치지 않고 웹 서버에서 직접 처리됩니다.

이전의 예제에서 디스크에 있는 고정 파일의 위치는 URL 내의 위치와 일치해야 합니다. 이는 `http://pythonapp.azurewebsites.net/static/site.css`에 대한 요청 시 `\static\site.css`에서 디스크의 파일이 제공됨을 의미합니다.

`WSGI_ALT_VIRTUALENV_HANDLER` 는 지정한 WSGI 처리기의 위치입니다. 이전의 예제에서는 처리기가 루트 폴더의 `app.py`에 있는 `wsgi_app`이라는 함수이므로 `app.wsgi_app`입니다.

`PYTHONPATH` 를 사용자 지정할 수 있지만 requirements.txt에서 모든 종속성을 지정하여 가상 환경에 설치한 경우에는 변경해서는 안 됩니다.

## <a name="virtual-environment-proxy"></a>가상 환경 프록시
다음 스크립트는 WSGI 처리기를 검색하고 가상 환경을 활성화하며 오류를 기록하는 데 사용됩니다. 이 스크립트는 수정 없이 범용적으로 사용됩니다.

`ptvs_virtualenv_proxy.py`의 내용:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []

        site.main()

        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Git 배포 사용자 지정
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>문제 해결 - 패키지 설치
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>문제 해결 - 가상 환경
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Python 개발자 센터](/develop/python/)를 참조하세요.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 
