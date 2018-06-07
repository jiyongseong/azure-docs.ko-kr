---
title: Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure
description: Visual Studio를 사용하여 Azure App Service에 Azure WebJobs를 개발하고 배포하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: babe190c0865f5be4aeecb40ca48b52673c6920e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure App Service

## <a name="overview"></a>개요

이 항목에서는 Visual Studio를 사용하여 콘솔 응용 프로그램 프로젝트를 [App Service](app-service-web-overview.md)의 웹앱에 [Azure WebJob](http://go.microsoft.com/fwlink/?LinkId=390226)으로 배포하는 방법을 설명합니다. [Azure Portal](https://portal.azure.com)을 사용하여 WebJobs를 배포하는 방법에 대한 내용은 [WebJobs로 백그라운드 작업 실행](web-sites-create-web-jobs.md)을 참조하세요.

Visual Studio는 WebJob 지원 콘솔 응용 프로그램 프로젝트를 배포할 때 다음 두 가지 작업을 수행합니다.

* 웹 앱의 해당 폴더에 런타임 파일을 복사합니다(연속 WebJob의 경우 *App_Data/jobs/continuous*, 예약된 주문형 WebJob의 경우 *App_Data/jobs/triggered*).
* 특정 시간에 실행되도록 예약된 WebJob에 대해 [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) 작업을 설정합니다. (연속 WebJob에는 필요하지 않습니다.)

WebJob 지원 프로젝트에는 다음 항목이 추가됩니다.

* [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지
* 배포 및 스케줄러 설정을 포함하는 [webjob-publish-settings.json](#publishsettings) 파일 

![배포를 WebJob으로 설정하기 위해 콘솔 앱에 추가되는 내용을 보여 주는 다이어그램](./media/websites-dotnet-deploy-webjobs/convert.png)

이러한 항목을 기존 콘솔 응용 프로그램 프로젝트에 추가하거나 템플릿을 사용하여 새 WebJob 지원 콘솔 응용 프로그램 프로젝트를 만들 수 있습니다. 

프로젝트를 WebJob 자체로 배포하거나 웹 프로젝트를 배포할 때마다 자동으로 배포되도록 웹 프로젝트에 연결할 수 있습니다. 프로젝트를 연결할 수 있게 Visual Studio는 웹 프로젝트의 [webjobs-list.json](#webjobslist) 파일에 WebJob 지원 프로젝트의 이름을 포함합니다.

![웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>필수 조건

Visual Studio 2015를 사용하는 경우 [Azure SDK for .NET(Visual Studio 2015)](https://azure.microsoft.com/downloads/)을 설치합니다.

Visual Studio 2017을 사용하는 경우 [Azure 개발 워크로드](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads)를 설치합니다.

## <a id="convert"></a>기존 콘솔 응용 프로그램 프로젝트에 WebJobs 배포 사용

다음 두 가지 옵션을 사용할 수 있습니다.

* [웹 프로젝트를 사용하여 자동 배포 사용](#convertlink).

  기존 콘솔 응용 프로그램 프로젝트가 웹 프로젝트를 배포할 때 WebJob으로 자동 배포되도록 구성합니다. 관련 웹 응용 프로그램을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

* [웹 프로젝트를 제외하고 배포](#convertnolink).

  기존 콘솔 응용 프로그램 프로젝트가 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 응용 프로그램은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 응용 프로그램 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.

### <a id="convertlink"></a> 웹 프로젝트와 함께 자동 WebJob 배포 사용

1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)**을 클릭합니다.
   
    ![Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. **프로젝트 이름** 드롭다운 목록에서 WebJob으로 추가할 콘솔 응용 프로그램 프로젝트를 선택합니다.
   
    ![Add Azure WebJob(Azure WebJob 추가) 대화 상자에서 프로젝트 선택](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다. 

### <a id="convertnolink"></a> 웹 프로젝트를 제외한 WebJob 배포 사용
1. **솔루션 탐색기**에서 콘솔 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **Azure WebJob 게시...**를 클릭합니다. 
   
    ![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    [프로젝트 이름](#configure) 상자에서 프로젝트가 선택된 상태로 **Add Azure WebJob(Azure WebJob 추가)** 대화 상자가 나타납니다.
2. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다.
   
   **웹 게시** 마법사가 나타납니다.  바로 게시하지 않으려면 마법사를 닫습니다. 입력한 설정은 [프로젝트를 배포](#deploy)할 때 사용할 수 있게 저장됩니다.

## <a id="create"></a>새 WebJob 지원 프로젝트 만들기
새 WebJob 지원 프로젝트를 만들려면 [이전 섹션](#convert)에 설명된 대로 콘솔 응용 프로그램 프로젝트 템플릿을 사용하고 WebJob 배포를 사용하도록 설정할 수 있습니다. 또는 WebJob new-project 템플릿을 사용할 수도 있습니다.

* [독립 WebJob을 위해 WebJob new-project 템플릿 사용](#createnolink)
  
    프로젝트를 만든 다음 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 응용 프로그램은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 응용 프로그램 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.
* [웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용](#createlink)
  
    동일한 솔루션의 웹 프로젝트가 배포될 때 자동으로 WebJob으로 배포되도록 구성된 프로젝트를 만듭니다. 관련 웹 응용 프로그램을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

> [!NOTE]
> WebJobs new-project 새 프로젝트 템플릿은 NuGet 패키지를 자동으로 설치하고 *WebJobs SDK* 에 대한 [Program.cs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)에 코드를 포함합니다. WebJobs SDK를 사용하지 않으려면 *Program.cs*에서 `host.RunAndBlock` 문을 제거하거나 변경합니다.
> 
> 

### <a id="createnolink"></a> 독립 WebJob을 위해 WebJob new-project 템플릿 사용
1. **파일** > **새 프로젝트**를 클릭한 다음 **새 프로젝트** 대화 상자에서 **클라우드** > **Azure WebJob(.NET Framework)**을 클릭합니다.
   
    ![WebJob 템플릿을 표시하는 새 프로젝트 대화 상자](./media/websites-dotnet-deploy-webjobs/np.png)
2. 앞에 표시된 지침에 따라 [콘솔 응용 프로그램 프로젝트를 독립 WebJob 프로젝트로 만듭니다](#convertnolink).

### <a id="createlink"></a> 웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용
1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **New Azure WebJob Project(새 Azure WebJob 프로젝트)**를 클릭합니다.
   
    ![New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다.

## <a id="configure"></a>Azure WebJob 추가 대화 상자
**Azure WebJob 추가** 대화 상자에서 WebJob 이름을 입력하고 WebJob에 대한 모드 설정을 실행할 수 있습니다. 

![Add Azure WebJob(Azure WebJob 추가) 대화 상자](./media/websites-dotnet-deploy-webjobs/aaw2.png)

이 대화 상자의 필드는 Azure Portal의 **WebJob 추가** 대화 상자에 있는 필드에 해당합니다. 자세한 내용은 [WebJobs를 사용하여 백그라운드 작업 실행](web-sites-create-web-jobs.md)을 참조하세요.

> [!NOTE]
> * 명령줄 배포에 대한 자세한 내용은 [Azure WebJob의 명령줄 또는 지속적인 전송 사용](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)을 참조하세요.
> * WebJob을 배포한 다음 WebJob의 유형을 변경하고 재배포하는 경우, *webjobs-publish-settings.json* 파일을 삭제해야 합니다. 이렇게 하면 Visual Studio가 게시 옵션을 다시 표시하므로, 웹 작업의 유형을 변경할 수 있습니다.
> * WebJob을 배포하고 나중에 실행 모드를 연속에서 비연속으로 또는 그 반대로 변경하면 Visual Studio는 사용자가 WebJob을 다시 배포할 때 Azure에서 새 WebJob을 만듭니다. 다른 일정 설정을 변경하고 실행 모드를 그대로 두거나 예약형 및 주문형 간을 전환하면 Visual Studio는 새 작업을 만들지 않고 기존 작업을 업데이트합니다.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
WebJob 배포를 위해 콘솔 응용 프로그램을 구성하는 경우, Visual Studio는 [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지를 설치하고 WebJob 프로젝트의 프로젝트 *Properties* 폴더에 있는 *webjob-publish-settings.json* 파일에 일정 정보를 저장합니다. 다음은 이 파일의 예입니다.

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는 [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json)에 저장되며 이 위치에서 볼 수 있습니다.  

## <a id="webjobslist"></a>webjobs-list.json
WebJob 지원 프로젝트를 웹 프로젝트에 연결하면 Visual Studio는 WebJob 프로젝트의 이름을 웹 프로젝트의 *Properties* 폴더에 있는 *webjobs-list.json* 파일에 저장합니다. 이 목록에는 다음 예와 같은 여러 WebJob 프로젝트가 포함될 수 있습니다.

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는 [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json)에 저장되며 이 위치에서 볼 수 있습니다.

## <a id="deploy"></a>WebJob 프로젝트 배포
웹 프로젝트에 연결한 WebJob 프로젝트는 웹 프로젝트와 함께 자동으로 배포됩니다. 웹 프로젝트 배포에 대한 자세한 내용은 **방법 가이드** > **앱 배포**를 참조하세요.

WebJob 프로젝트 자체적으로 배포하려면 **솔루션 탐색기**에서 이 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure WebJob 게시...**를 클릭합니다. 

![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/websites-dotnet-deploy-webjobs/paw.png)

독립 WebJob의 경우 웹 프로젝트에 사용되는 것과 동일한 **웹 게시** 마법사가 나타나지만 변경할 수 있는 설정은 더 적습니다.
