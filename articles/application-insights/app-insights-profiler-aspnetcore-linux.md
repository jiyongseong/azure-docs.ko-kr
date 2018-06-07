---
title: Application Insights Profiler를 사용하여 ASP.NET Core Azure Linux 웹앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하는 방법에 대한 개념 개요 및 단계별 자습서입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Application Insights Profiler를 사용하여 ASP.NET Core Azure Linux 웹앱 프로파일링

이 기능은 현재 미리 보기로 제공됩니다.

[Application Insights](app-insights-overview.md)를 사용할 때 라이브 웹 응용 프로그램의 각 메서드에서 얼마나 많은 시간이 소요되는지 알아봅니다. Application Insights Profiler는 이제 Azure App Service의 Linux에서 호스팅되는 ASP.NET Core 웹앱에서 사용할 수 있습니다. 이 가이드에서는 ASP.NET Core Linux 웹앱에 대한 프로파일러 추적을 수집하는 방법에 대한 단계별 지침을 제공합니다.

이 연습을 완료하면 앱은 이미지에 표시된 것처럼 추적과 같은 프로파일러 추적을 수집할 수 있습니다. 이 예에서는 시간이 대기하는 데 소요되었으므로 프로파일러 추적에서 특정 웹 요청이 느리다는 것을 나타냅니다. 앱의 속도를 저하시키는 코드의 *핫 경로*는 화염 아이콘으로 표시됩니다. 메서드는 **Thread.Sleep** 함수를 호출하기 때문에 **HomeController** 섹션에서 **About** 메서드는 웹앱의 속도를 저하시킵니다.

![Profiler 추적](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>필수 조건
다음 지침은 모든 Windows, Linux 및 Mac 개발 환경에 적용됩니다.

* [.NET Core SDK 2.1.2 이상](https://www.microsoft.com/net/download/windows/build)을 설치합니다.
* [시작 - Git 설치](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)의 지침에 따라 Git을 설치합니다.

## <a name="set-up-the-project-locally"></a>로컬로 프로젝트 설정

1. 컴퓨터에서 명령 프롬프트 창을 엽니다. 다음 지침은 모든 Windows, Linux 및 Mac 개발 환경에 작동합니다.

2. ASP.NET Core MVC 웹 응용 프로그램을 만듭니다.

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. 작업 디렉터리를 프로젝트에 대한 루트 폴더로 변경합니다.

4. 프로파일러 추적을 수집하도록 NuGet 패키지를 추가합니다.

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. **HomeController.cs** 섹션에서 몇 초 동안 임의로 지연하는 코드 줄을 추가합니다.

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

6. 변경 내용을 로컬 리포지토리에 저장하고 커밋합니다.

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>프로젝트를 호스팅하는 Linux 웹앱 만들기

1. Linux에서 App Service를 사용하여 웹앱 환경을 만듭니다.

    ![Linux 웹앱 만들기](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. 배포 자격 증명을 만듭니다.

    > [!NOTE]
    > 웹앱을 배포할 때 나중에 사용하기 위한 암호를 기록합니다.

    ![배포 자격 증명 만들기](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. 배포 옵션을 선택합니다. Azure Portal의 지침에 따라 웹앱에 로컬 Git 리포지토리를 설정합니다. Git 리포지토리가 자동으로 만들어집니다.

    ![Git 리포지토리 설정](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

더 많은 배포 옵션은 [이 문서](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)를 참조하세요.

## <a name="deploy-your-project"></a>프로젝트 배포

1. 명령 프롬프트 창에서 프로젝트에 대한 루트 폴더로 이동합니다. App Service에서 리포지토리를 가리키도록 Git 원격 리포지토리를 추가합니다.

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * 배포 자격 증명을 만드는 데 사용한 **사용자 이름**을 사용합니다.
    * Linux에서 App Service를 사용하여 웹앱을 만드는 데 사용한 **앱 이름**을 사용합니다.

2. 변경 내용을 Azure로 푸시하여 프로젝트를 배포합니다.

    ```
    git push azure master
    ```

다음 예제와 비슷한 결과가 나타납니다.

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>웹앱을 모니터링하는 Application Insights 추가

1. [Application Insights 리소스를 만듭니다](./app-insights-create-new-resource.md).

2. Application Insights 리소스의 **iKey** 값을 복사하고 웹앱에서 다음 설정을 지정합니다.

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![앱 설정 구성](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    앱 설정이 변경되면 사이트가 자동으로 다시 시작합니다. 새 설정이 적용된 후 프로파일러는 2분 동안 즉시 실행됩니다. 그런 다음, 프로파일러는 1시간마다 2분 동안 실행됩니다.

3. 웹 사이트에 일부 트래픽을 생성합니다. 사이트 **정보** 페이지를 여러 번 새로 고쳐서 트래픽을 생성할 수 있습니다.

4. 이벤트가 Application Insights에 집계될 때까지 2~5분 동안 기다립니다.

5. Azure Portal에서 Application Insights **성능** 창으로 이동합니다. 창의 오른쪽 아래에서 프로파일러 추적을 볼 수 있습니다.

    ![프로파일러 추적 보기](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>알려진 문제

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>프로파일러 구성 창에서 활성화 작업이 작동하지 않습니다.

> [!NOTE]
> Linux에서 App Service를 사용하여 앱을 호스팅하는 경우 Application Insights 포털의 **성능** 창에서 프로파일러를 다시 활성화할 필요가 없습니다. 프로젝트에 NuGet 패키지를 포함하고 프로파일러를 활성화하도록 웹앱 설정에서 Application Insights **iKey** 값을 설정할 수 있습니다.

[Windows용 Application Insights Profiler](./app-insights-profiler.md)에 대한 활성화 워크플로를 따르는 경우 **프로파일러 구성** 창에서 **활성화**를 선택하면 오류가 발생합니다. 활성화 작업은 Linux 환경에서 Windows 버전의 프로파일러 에이전트를 설치하려고 시도합니다.

이 문제 해결을 위해 작업 중입니다.

![성능 창의 프로파일러를 다시 활성화하려고 시도하지 마세요.](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>다음 단계
Azure App Service에 의해 호스팅되는 사용자 지정 컨테이너를 사용하는 경우 [컨테이너화된 ASP.NET Core 응용 프로그램에 대한 서비스 프로파일러 활성화](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)의 지침을 따라 Application Insights Profiler를 활성화합니다.

Application Insights GitHub 리포지토리([ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues))에 문제 또는 제안 사항을 보고합니다.
