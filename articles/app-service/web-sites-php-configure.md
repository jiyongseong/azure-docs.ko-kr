---
title: Azure App Service Web Apps에서 PHP 구성
description: Azure App Service의 Web Apps에서 기본 PHP 설치를 구성하거나 사용자 지정 PHP 설치를 추가하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: d7b832659272f01b6e939150fa89626620d5eab7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Azure App Service Web Apps에서 PHP 구성

## <a name="introduction"></a>소개

이 가이드에서는 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)에서 Web Apps에 대한 기본 제공 PHP 런타임을 구성하고 사용자 지정 PHP 런타임을 제공하며 확장을 사용하도록 설정하는 방법을 보여 줍니다. App Service를 사용하려면 [무료 평가판]에 등록해야 합니다. 이 가이드를 최대한 활용하려면 먼저 App Service에서 PHP 웹앱을 만들어야 합니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>방법: 기본 제공 PHP 버전 변경

기본적으로 PHP 5.6은 App Service 웹앱을 만들 때 설치하여 바로 사용할 수 있습니다. 사용 가능한 릴리스 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()] 함수를 호출하는 스크립트를 배포하는 것입니다.

PHP 7.0 및 PHP 7.2 버전도 사용할 수 있지만 기본적으로는 사용하도록 설정되어 있지 않습니다. PHP 버전을 업데이트하려면 다음 방법 중 하나를 따르세요.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에서 웹앱을 찾아 **설정** 단추를 클릭합니다.

    ![저장][settings-button]
1. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 새 PHP 버전을 선택합니다.

    ![응용 프로그램 설정][application-settings]
1. **웹앱 설정** 블레이드의 위쪽에서 **저장** 단추를 클릭합니다.

    ![구성 설정 저장][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell(Windows)

1. Azure PowerShell을 열고 계정에 로그인합니다.

        PS C:\> Connect-AzureRmAccount
1. 웹앱에 대한 PHP 버전을 설정합니다.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. PHP 버전이 설정되었습니다. 이러한 설정을 확인할 수 있습니다.

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli-20-linux-mac-windows"></a>Azure CLI 2.0(Linux, Mac, Windows)

Azure 명령줄 인터페이스를 사용하려면 컴퓨터에 [Azure CLI 2.0을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)해야 합니다.

1. 터미널을 열고 계정에 로그인합니다.

        az login

1. 지원되는 런타임 목록을 확인합니다.

        az webapp list-runtimes | grep php

1. 웹앱에 대한 PHP 버전을 설정합니다.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. PHP 버전이 설정되었습니다. 이러한 설정을 확인할 수 있습니다.

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>방법: 기본 제공 PHP 구성 변경

기본 제공 PHP 런타임에 대해 다음 단계에 따라 구성 옵션을 변경할 수 있습니다. php.ini 지시문에 대한 자세한 내용은 [php.ini 지시문 목록]을 참조하세요.

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL 구성 설정 변경

1. [.user.ini] 파일을 루트 디렉터리에 추가합니다.
1. `php.ini` 파일에 사용한 것과 동일한 구문을 사용하여 구성 설정을 `.user.ini` 파일에 추가합니다. 예를 들어, `display_errors` 설정을 켜고 `upload_max_filesize` 설정을 10M로 설정하려면 `.user.ini` 파일에 다음 텍스트를 포함합니다.

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. 웹 앱에 배포합니다.
1. 웹 앱을 다시 시작합니다. PHP가 `.user.ini` 파일을 읽는 빈도는 시스템 수준 설정인 `user_ini.cache_ttl` 설정(기본적으로 300초[5분])의 적용을 받으므로 웹앱을 다시 시작해야 합니다. 웹앱을 다시 시작하면 PHP가 `.user.ini` 파일에서 새 설정을 읽습니다.

`.user.ini` 파일을 사용하는 대신 스크립트에서 [ini_set()] 함수를 사용하여 시스템 수준 지시문이 아닌 구성 옵션을 설정할 수도 있습니다.

### <a name="changing-phpinisystem-configuration-settings"></a>PHP\_INI\_SYSTEM 구성 설정 변경

1. `PHP_INI_SCAN_DIR` 키 및 `d:\home\site\ini` 값으로 웹앱에 앱 설정을 추가합니다.
1. Kudu Console(http://&lt;site-name&gt;.scm.azurewebsite.net)을 사용하여 `d:\home\site\ini` 디렉터리에 `settings.ini` 파일을 만듭니다.
1. php.ini 파일에서 사용한 것과 동일한 구문을 사용하여 구성 설정을 `settings.ini` 파일에 추가합니다. 예를 들어 `curl.cainfo` 설정이 `*.crt` 파일을 가리키고 'wincache.maxfilesize' 설정을 512K로 설정하려면 `settings.ini` 파일에 다음 텍스트를 포함합니다.

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. 변경 내용을 로드하려면 웹앱을 다시 시작합니다.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>방법: 기본 PHP 런타임에서 확장 사용

이전 섹션에 언급된 것처럼 기본 PHP 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()]라는 스크립트를 배포하는 것입니다. 추가 확장을 사용하도록 설정하려면 다음 단계를 따릅니다.

### <a name="configure-via-ini-settings"></a>Ini 설정을 통해 구성

1. `ext` 디렉터리를 `d:\home\site` 디렉터리에 추가합니다.
1. `.dll` 확장 파일을 `ext` 디렉터리에 둡니다(예: `php_xdebug.dll`). 확장이 기본 버전의 PHP와 호환되며 VC9 및 nts(non-thread-safe)와 호환 가능해야 합니다.
1. `PHP_INI_SCAN_DIR` 키 및 `d:\home\site\ini` 값으로 웹앱에 앱 설정을 추가합니다.
1. `d:\home\site\ini`에 `extensions.ini`라는 `ini` 파일을 만듭니다.
1. php.ini 파일에서 사용한 것과 동일한 구문을 사용하여 구성 설정을 `extensions.ini` 파일에 추가합니다. 예를 들어 MongoDB 및 XDebug 확장을 사용하려면 `extensions.ini` 파일에 다음 텍스트를 포함합니다.

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. 웹앱을 다시 시작하여 변경 내용을 로드합니다.

### <a name="configure-via-app-setting"></a>앱 설정을 통해 구성

1. `bin` 디렉터리를 루트 디렉터리에 추가합니다.
1. `.dll` 확장 파일을 `bin` 디렉터리에 둡니다(예: `php_xdebug.dll`). 확장이 기본 버전의 PHP와 호환되며 VC9 및 nts(non-thread-safe)와 호환 가능해야 합니다.
1. 웹 앱에 배포합니다.
1. Azure Portal에서 웹앱을 찾아 **설정** 단추를 클릭합니다.

    ![저장][settings-button]
1. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 **앱 설정** 섹션으로 스크롤합니다.
1. **앱 설정** 섹션에서 **PHP_EXTENSIONS** 키를 만듭니다. 이 키의 값은 웹 사이트 루트 **bin\your-ext-file**에 상대적인 경로입니다.

    ![앱 설정의 확장 사용][php-extensions]
1. **웹앱 설정** 블레이드의 위쪽에서 **저장** 단추를 클릭합니다.

    ![구성 설정 저장][save-button]

**PHP_ZENDEXTENSIONS** 키를 통해 Zend 확장도 지원됩니다. 여러 확장을 사용하려면 앱 설정 값에 `.dll` 파일의 쉼표로 구분된 목록을 포함합니다.

## <a name="how-to-use-a-custom-php-runtime"></a>방법: 사용자 지정 PHP 런타임 사용

App Service Web Apps는 기본 PHP 런타임 대신, 사용자가 PHP 스크립트를 실행하도록 제공한 PHP 런타임을 사용할 수 있습니다. 이러한 런타임은 사용자가 제공한 `php.ini` 파일로 구성될 수 있습니다. Web Apps에 사용자 지정 PHP 런타임을 사용하려면 다음 단계를 따르세요.

1. 스레드로부터 안전하지 않은 VC9 또는 VC11과 호환되는 버전의 Windows용 PHP를 받아야 합니다. 최신 Windows용 PHP 릴리스는 [http://windows.php.net/download/]에서 확인할 수 있습니다. 이전 릴리스는 [http://windows.php.net/downloads/releases/archives/]의 보관 위치에서 찾을 수 있습니다.
1. 런타임에 맞게 `php.ini` 파일을 수정합니다. 시스템 수준 전용 지시문인 구성 설정은 Web Apps에서 무시됩니다. 시스템 수준 전용 지시문에 대한 자세한 내용은 [php.ini 지시문 목록](영문)을 참조하세요.
1. 경우에 따라 확장을 PHP 런타임에 추가하고 `php.ini` 파일에서 확장을 사용하도록 설정합니다.
1. 루트 디렉터리에 `bin` 디렉터리를 추가하고 PHP 런타임이 포함된 디렉터리(예: `bin\php`)를 배치합니다.
1. 웹 앱에 배포합니다.
1. Azure Portal에서 웹앱을 찾아 **설정** 단추를 클릭합니다.

    ![저장][settings-button]
1. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 **처리기 매핑** 섹션으로 스크롤합니다. `*.php`를 확장 필드에 추가하고 경로를 `php-cgi.exe` 실행 파일에 추가합니다. PHP 런타임을 응용 프로그램의 루트에 있는 `bin` 디렉터리에 배치하면 경로는 `D:\home\site\wwwroot\bin\php\php-cgi.exe`가 됩니다.

    ![처리기 매핑에 처리기 지정][handler-mappings]
1. **웹앱 설정** 블레이드의 위쪽에서 **저장** 단추를 클릭합니다.

    ![구성 설정 저장][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>방법: Azure에서 작성기 자동화를 사용하도록 설정

App Service가 PHP 프로젝트에 있는 경우 기본적으로 composer.json로 작업하지 않습니다. [Git 배포](app-service-deploy-local-git.md)를 사용하는 경우 작성기 확장을 사용하여 `git push` 중에 composer.json 처리를 사용할 수 있습니다.

> [!NOTE]
> [여기에서 App Service의 최고 수준 작성기 지원에 투표](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)할 수 있습니다.
>

1. [Azure Portal](https://portal.azure.com)의 PHP 웹앱의 블레이드에서 **도구** > **확장**을 클릭합니다.

    ![Azure에서 작성기를 자동화하도록 설정하기 위한 Azure Portal 설정 블레이드](./media/web-sites-php-configure/composer-extension-settings.png)
2. **추가**를 클릭한 다음 **작성기**를 클릭합니다.

    ![Azure에서 작성기를 자동화하도록 설정하기 위하여 작성기 확장 추가](./media/web-sites-php-configure/composer-extension-add.png)
3. **확인** 을 클릭하여 약관을 수락합니다. **확인** 을 다시 클릭하여 확장을 추가합니다.

    **설치된 확장** 블레이드에 작성기 확장이 표시됩니다.
    ![Azure에서 작성기를 자동화하도록 설정하기 위하여 약관에 동의](./media/web-sites-php-configure/composer-extension-view.png)
4. 이제 로컬 컴퓨터의 터미널 창에서 웹앱에 대해 `git add`, `git commit` 및 `git push`를 수행합니다. 작성기가 composer.json에 정의된 종속성을 설치하고 있다고 표시됩니다.

    ![Azure에서 작성기 자동화를 사용하여 GIT 배포](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [PHP 개발자 센터](/develop/php/)를 참조하세요.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
>

[무료 평가판]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 지시문 목록]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
