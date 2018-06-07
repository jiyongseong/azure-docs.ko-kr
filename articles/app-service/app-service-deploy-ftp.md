---
title: FTP/S를 사용하여 Azure App Service에 앱 배포 | Microsoft Docs
description: FTP 또는 FTPS를 사용하여 Azure App Service에 앱을 배포하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S를 사용하여 앱에 Azure App Service에 배포

이 문서는 FTP 또는 FTPS를 사용하여 웹앱, 모바일 앱 백 엔드 또는 API 앱을 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법을 보여 줍니다.

앱에 대한 FTP/S 끝점은 이미 활성화되어 있습니다. FTP/S 배포를 사용하도록 설정하는 데 필요한 구성은 없습니다.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>1단계: 배포 자격 증명 설정

앱에 대한 FTP 서버에 액세스하려면 먼저 배포 자격 증명이 필요합니다. 

배포 자격 증명을 설정하거나 다시 설정하려면 [Azure App Service 배포 자격 증명](app-service-deployment-credentials.md)을 참조하세요. 이 자습서는 사용자 수준의 자격 증명 사용 방법을 보여 줍니다.

## <a name="step-2-get-ftp-connection-information"></a>2단계: FTP 연결 정보 가져오기

1. [Azure Portal](https://portal.azure.com)에서 앱의 [리소스 페이지](../azure-resource-manager/resource-group-portal.md#manage-resources)를 엽니다.
2. 왼쪽 탐색 메뉴에서 **개요**를 선택하고 **P/배포 사용자**, **FTP 호스트 이름** 및 **FTPS 호스트 이름** 값을 적어둡니다. 

    ![FTP 연결 정보](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > FTP 서버에 올바른 컨텍스트를 제공하기 위해 Azure Portal에 표시된 **FTP/배포 사용자** 값에는 앱 이름이 포함됩니다.
    > 왼쪽 탐색 메뉴에서 **속성**을 선택하면 같은 정보를 찾을 수 있습니다. 
    >
    > 또한 배포 암호는 표시되지 않습니다. 배포 암호를 잊은 경우 [1단계](#step1)로 이동한 후 배포 암호를 다시 설정합니다.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>3단계: Azure에 파일 배포

1. FTP 클라이언트([Visual Studio](https://www.visualstudio.com/vs/community/) 또는 [FileZilla](https://filezilla-project.org/download.php?type=client))에서 수집한 연결 정보를 사용하여 앱에 연결합니다.
3. 파일 및 해당 디렉터리 구조를 Azure의 [**/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(또는 WebJobs의 경우 **/site/wwwroot/App_Data/Jobs/** 디렉터리)에 복사합니다.
4. 앱의 URL을 찾아 앱이 제대로 실행하는지 확인합니다. 

> [!NOTE] 
> [Git 기반 배포](app-service-deploy-local-git.md)와 달리, FTP 배포에서는 다음과 같은 배포 자동화를 지원하지 않습니다. 
>
> - 종속성 복원(예: NuGet, NPM, PIP 및 Composer Automation)
> - .NET 이진 파일 컴파일
> - web.config 생성([Node.js 예제](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) 참조)
> 
> 로컬 컴퓨터에서 이러한 필요한 파일을 수동으로 생성한 후 앱과 함께 배포합니다.
>
>

## <a name="enforce-ftps"></a>FTPS 적용

보안 향상을 위해 SSL을 통한 FTP만 허용해야 합니다. FTP 배포를 사용하지 않을 경우 FTP 및 FTPS를 둘 다 사용하지 않도록 설정할 수도 있습니다.

[Azure Portal](https://portal.azure.com)의 앱 리소스 페이지에서 왼쪽 탐색 창에 있는 **앱 설정**을 선택합니다.

암호화되지 않은 FTP를 사용하지 않도록 설정하려면 **FTPS만**을 선택합니다. FTP 및 FTPS를 둘 다 완전히 사용하지 않도록 설정하려면 **사용 안 함**을 선택합니다. 완료되면 **저장**을 클릭합니다.

![FTP/S 사용 안 함](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>FTP 배포 문제 해결

- [FTP 배포 문제를 어떻게 해결할 수 있나요?](#how-can-i-troubleshoot-ftp-deployment)
- [FTP를 수행할 수 없으며 코드를 게시할 수 없습니다. 이 문제는 어떻게 해결할 수 있나요?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP 배포 문제를 어떻게 해결할 수 있나요?

FTP 배포 문제를 해결하는 첫 번째 단계는 런타임 응용 프로그램 문제에서 배포 문제를 격리하는 것입니다.

배포 문제가 발생하면 일반적으로 앱에 파일이 배포되지 않거나 잘못된 파일이 배포됩니다. 이 문제는 FTP 배포를 조사하거나 대체 배포 경로(예: 소스 제어)를 선택하여 해결할 수 있습니다.

런타임 응용 프로그램 문제가 발생하면 일반적으로 앱에 올바른 파일 집합이 배포되기는 하지만 앱이 올바르지 않게 동작합니다. 런타임의 코드 동작에 중점을 두고 구체적인 실패 경로를 조사하여 문제를 해결할 수 있습니다.

배포 또는 런타임 문제를 확인하려면 [배포 문제 및 런타임 문제](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)를 참조하세요.

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP를 수행할 수 없으며 코드를 게시할 수 없습니다. 이 문제는 어떻게 해결할 수 있나요?
올바른 호스트 이름 및 [자격 증명](#step-1--set-deployment-credentials)을 입력했는지 확인합니다. 또한 컴퓨터의 다음 FTP 포트가 방화벽에 의해 차단되지 않는지 확인합니다.

- FTP 제어 연결 포트: 21
- FTP 데이터 연결 포트: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?
Azure App Service는 활성 및 수동 모드를 통한 연결을 모두 지원합니다. 배포 컴퓨터는 일반적으로 방화벽 뒤에 있으므로(운영 체제에서 또는 가정용/회사 네트워크의 일부로) 수동 모드가 선호됩니다. [WinSCP 설명서의 예제](https://winscp.net/docs/ui_login_connection)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

고급 배포 시나리오에 대해서는 [Git를 사용하여 Azure에 배포](app-service-deploy-local-git.md)를 시도하세요. Azure로의 Git 기반 배포를 수행하면 버전 제어, 패키지 복원, MSBuild 등을 수행할 수 있습니다.

## <a name="more-resources"></a>추가 리소스

* [Azure App Service 배포 자격 증명](app-service-deploy-ftp.md)
