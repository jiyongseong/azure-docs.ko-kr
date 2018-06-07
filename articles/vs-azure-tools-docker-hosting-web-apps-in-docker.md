---
title: "원격 Docker 호스트에 ASP.NET Core Linux Docker 컨테이너 배포 | Microsoft Docs"
description: "Visual Studio Tools for Docker를 사용하여 Azure Docket 호스트 Linux VM에서 실행 중인 Docker 컨테이너에 ASP.NET Core 웹앱을 배포하는 방법에 대해 설명합니다."
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 60efffd9313f6972ae46fd1925d999597d3c6ba2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>원격 Docker 호스트에 ASP.NET 컨테이너 배포
## <a name="overview"></a>개요
Docker는 가상 컴퓨터와 몇 가지 측면에서 비슷하며 응용 프로그램과 서비스를 호스트하는 데 사용할 수 있는 간단한 컨테이너 엔진입니다.
이 자습서에서는 [Visual Studio Tools for Docker](https://docs.microsoft.com/dotnet/articles/core/docker/visual-studio-tools-for-docker) 확장을 사용하여 Azure에서 PowerShell을 사용하여 Docker 호스트에 ASP.NET Core 앱을 배포하는 단계에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* [Azure에서 docker-machine을 사용하는 방법](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 설명에 따라 Azure Docker 호스트 VM을 만듭니다.
* 최신 버전의 [Visual Studio](https://www.visualstudio.com/downloads/)를 설치합니다.
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)를 다운로드합니다.
* [Windows용 Docker](https://docs.docker.com/docker-for-windows/install/)를 설치합니다.

## <a name="1-create-an-aspnet-core-web-app"></a>1. ASP.NET Core 웹앱 만들기
다음 단계에서는 이 자습서에서 사용할 기본적인 ASP.NET Core 앱을 만드는 과정을 안내합니다.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Docker 지원 추가
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. DockerTask.ps1 PowerShell 스크립트 사용
1. 프로젝트의 루트 디렉터리에 대해 PowerShell 프롬프트를 엽니다. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. 실행 중인 원격 호스트의 유효성을 검사합니다. 상태 = 실행 중이 표시되어야 합니다. 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. -Build 매개 변수를 사용하여 앱 빌드
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. -Run 매개 변수를 사용하여 앱 실행
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   docker가 완료되면 다음 화면과 같은 결과가 표시되어야 합니다.
   
   ![앱 보기][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
