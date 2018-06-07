---
title: Linux의 Azure App Service에 대한 FAQ | Microsoft Docs
description: Linux의 Azure App Service에 대한 FAQ.
keywords: Azure App Service, 웹앱, faq, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 8d25c70a0e5db92bca6f3970049a2e1325fe124b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-on-linux-faq"></a>Linux의 Azure App Service에 대한 FAQ

Linux의 App Service를 릴리스하면서 현재 플랫폼에 기능을 추가하고 플랫폼을 더욱 개선하기 위한 작업을 진행하고 있습니다. 이 문서는 고객이 최근에 요청한 질문에 대한 대답을 제공합니다.

질문이 있으면 문서에 남겨주세요. 최대한 신속하게 답변을 드리겠습니다.

## <a name="built-in-images"></a>기본 제공 이미지

**플랫폼에서 제공하는 기본 제공 Docker 컨테이너를 분기하려고 합니다. 이러한 파일은 어디서 찾을 수 있나요?**

[GitHub](https://github.com/azure-app-service)에서 모든 Docker 파일을 찾을 수 있습니다. [Docker Hub](https://hub.docker.com/u/appsvc/)에서 모든 Docker 컨테이너를 찾을 수 있습니다.

**런타임 스택을 구성할 때 시작 파일 섹션에 대해 예상되는 값은 무엇인가요?**

Node.js의 경우 PM2 구성 파일 또는 스크립트 파일을 지정합니다. .Net Core의 경우 컴파일된 DLL 이름을 지정해야 합니다. Ruby의 경우 앱을 초기화하려면 Ruby 스크립트를 지정할 수 있습니다.

## <a name="management"></a>관리

**Azure Portal에서 다시 시작 단추를 누르면 어떻게 되나요?**

이 작업은 Docker 다시 시작과 동일합니다.

**앱 컨테이너 가상 머신(VM)에 연결하기 위해 Secure Shell(SSH)을 사용할 수 있나요?**

예. SCM(원본 제어 관리) 사이트를 통해 수행할 수 있습니다.

> [!NOTE] 
> SSH, SFTP 또는 Visual Studio Code를 사용하여 로컬 개발 컴퓨터에서 직접 앱 컨테이너에 연결할 수도 있습니다(Node.js 앱 라이브 디버깅을 위해). 자세한 내용은 [Linux App Service의 원격 디버깅 및 SSH](https://aka.ms/linux-debug)를 참조하세요.
>

**SDK 또는 Azure Resource Manager 템플릿을 통해 Linux App Service 계획을 어떻게 만들 수 있나요?**

앱 서비스의 **예약** 필드를 *true*로 설정해야 합니다.

## <a name="continuous-integration-and-deployment"></a>지속적인 통합 및 배포

**Docker Hub에서 이미지를 업데이트한 후에도 웹앱에서 여전히 기존 Docker 컨테이너 이미지를 사용합니다. 사용자 지정 컨테이너의 지속적인 통합 및 배포를 지원하나요?**

Azure Container Registry 또는 DockerHub 이미지에 대한 연속 통합/배포를 설정하려면 [Web App for Containers를 사용한 연속 배포](./app-service-linux-ci-cd.md) 문서를 확인하세요. 개인 레지스트리의 경우 웹앱을 중지했다가 다시 시작하여 컨테이너를 새로 고칠 수 있습니다. 또는 컨테이너를 강제로 새로 고침하도록 더미 응용 프로그램을 변경 또는 추가할 수 있습니다.

**스테이징 환경이 지원되나요?**

예.

***웹 배포*를 사용하여 내 웹앱을 설정할 수 있나요?**

예. `WEBSITE_WEBDEPLOY_USE_SCM`이라는 앱 설정을 *false*로 설정해야 합니다.

**Linux 웹앱을 사용할 때 내 응용 프로그램의 Git 배포가 실패합니다. 이 문제를 어떻게 해결할 수 있나요?**

Linux 웹앱에 대한 Git 배포가 실패하면 다음 대체 옵션을 선택하여 응용 프로그램 코드를 배포할 수 있습니다.

- 지속적인 업데이트(미리 보기) 기능 사용: Azure 지속적인 업데이트를 사용하도록 Team Services Git 리포지토리 또는 GitHub 리포지토리에 앱의 소스 코드를 저장할 수 있습니다. 자세한 내용은 [Linux 웹앱에 지속적인 업데이트를 구성하는 방법](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)을 참조하세요.

- [ZIP 배포 API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) 사용: 이 API를 사용하려면 [웹앱에 SSH를 실행하고](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) 코드를 배포할 폴더로 이동합니다. 다음을 실행합니다.

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   `curl` 명령을 찾을 수 없다는 오류가 발생하면 이전 `curl` 명령을 실행하기 전에 `apt-get install curl`을 사용하여 curl을 설치해야 합니다.

## <a name="language-support"></a>언어 지원

**Node.js 응용 프로그램에서 웹 소켓을 사용하려고 합니다. 특별한 설정이나 구성이 필요한가요?**

예. 서버 쪽 Node.js 코드에서 `perMessageDeflate`를 비활성화해야 합니다. 예를 들어, socket.io를 사용하고 있다면 다음을 수행합니다.
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**컴파일되지 않은 .NET Core 앱을 지원하나요?**

예.

**작성기를 PHP 앱의 종속성 관리자로 지원하나요?**

예. Git 배포 중에 Kudu는 사용자가 PHP 응용 프로그램을 배포하고 있음을 감지해야 하고(composer.lock 파일의 존재 덕분) 자동으로 작성기 설치를 트리거합니다.

## <a name="custom-containers"></a>사용자 지정 컨테이너

**나만의 사용자 지정 컨테이너를 사용하고 있습니다. `/home/` 디렉터리에 대한 SMB 공유를 플랫폼에 탑재하려 합니다.**

`WEBSITES_ENABLE_APP_SERVICE_STORAGE` 앱 설정을 *true*로 설정하거나 앱 설정 전체를 제거하면 됩니다. 이렇게 하면 플랫폼 저장소가 변경될 때 컨테이너가 다시 시작됩니다. 

>[!NOTE]
>`WEBSITES_ENABLE_APP_SERVICE_STORAGE` 설정이 *false*인 경우 `/home/` 디렉터리는 스케일 인스턴스 간에 공유되지 않으며 여기서 작성된 파일은 다시 시작 후에 지속되지 않습니다.

**내 사용자 지정 컨테이너는 시작하는 데 시간이 오래 걸리고 플랫폼이 시작을 마무리하기 전에 컨테이너를 다시 시작합니다.**

컨테이너를 다시 시작하기 전에 플랫폼이 대기할 시간을 구성할 수 있습니다. 이를 수행하려면 `WEBSITES_CONTAINER_START_TIME_LIMIT` 앱 설정을 원하는 값으로 설정합니다. 기본값은 230초이고 최대값은 600초입니다.

**개인 레지스트리 서버 URL의 형식은 무엇인가요?**

`http://` 또는 `https://`를 포함하여 전체 레지스트리 URL을 입력합니다.

**개인 레지스트리 옵션에서 이미지 이름의 형식은 무엇인가요?**

개인 레지스트리 URL(예: myacr.azurecr.io/dotnet:latest)을 포함하여 전체 이미지 이름을 추가합니다. 사용자 지정 포트를 사용하는 이미지 이름은 [포털을 통해 입력할 수 없습니다](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). `docker-custom-image-name`을 설정하려면 [`az` 명령줄 도구](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set)를 사용합니다.

**사용자 지정 컨테이너 이미지에 포트를 두 개 이상 표시할 수 있나요?**

현재 둘 이상의 포트 노출을 지원하지 않습니다.

**사용자 고유의 저장소를 가져올 수 있나요?**

현재 자체 저장소 가져오기를 지원하지 않습니다.

**내 사용자 지정 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 SCM 사이트에서 찾을 수 없는 이유는 무엇인가요?**

SCM 사이트는 별도의 컨테이너에서 실행됩니다. 사용자가 앱 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 확인할 수 없습니다.

**내 사용자 지정 컨테이너가 포트 80 이외의 포트를 수신합니다. 해당 포트로 요청을 라우팅하도록 내 앱을 구성하려면 어떻게 합니까?**

자동 포트 검색이 있습니다. *WEBSITES_PORT*라는 앱 설정을 지정하고 예상되는 포트 번호 값을 지정할 수도 있습니다. 이전에 플랫폼은 *포트* 앱 설정을 사용했습니다. 이 앱 설정의 사용을 중단하고 *WEBSITES_PORT*를 단독으로 사용할 계획입니다.

**사용자 지정 컨테이너에서 HTTPS를 구현해야 하나요?**

아니요. 플랫폼에서는 공유 프런트 엔드에서 HTTPS 종료를 처리합니다.

## <a name="pricing-and-sla"></a>가격 및 SLA

**서비스가 일반 공급될 때 가격은 얼마인가요?**

일반 Azure App Service 가격으로 앱이 실행되는 시간만큼 청구됩니다.

## <a name="other-questions"></a>기타 질문

**응용 프로그램 설정 이름에 지원되는 문자는 무엇입니까?**

응용 프로그램 설정에 문자(A-Z, a-z), 숫자(0-9) 및 밑줄(_)만 사용할 수 있습니다.

**어디서 새 기능을 요청할 수 있나요?**

[Web Apps 사용자 의견 포럼](https://aka.ms/webapps-uservoice)에서 사용자의 아이디어를 제출할 수 있습니다. 아이디어 제목에 "[Linux]"를 붙여 주세요.

## <a name="next-steps"></a>다음 단계

* [Linux의 Azure App Service란?](app-service-linux-intro.md)
* [Azure App Service에서 스테이징 환경 설정](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Web App for Containers를 사용한 연속 배포](./app-service-linux-ci-cd.md)
