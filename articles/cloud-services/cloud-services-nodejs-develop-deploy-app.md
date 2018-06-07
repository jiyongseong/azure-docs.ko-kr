---
title: Node.js 시작 가이드
description: 간단한 Node.js 웹 응용 프로그램을 만들고 Azure 클라우드 서비스에 배포하는 방법에 대해 알아봅니다.
services: cloud-services
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: 50951a87-fed4-48e0-bcfa-453b9e50452e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 08/17/2017
ms.author: adegeo
ms.openlocfilehash: 5d69f83aed9160f94260b71e60fcf0e15b435569
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Azure 클라우드 서비스에서 Node.js 응용 프로그램 빌드 및 배포

이 자습서에서는 Azure 클라우드 서비스에서 실행되는 간단한 Node.js 응용 프로그램을 만드는 방법을 보여줍니다. Cloud Services는 Azure에서 확장 가능한 클라우드 응용 프로그램의 구성 요소입니다. 이 클라우드 서비스는 응용 프로그램의 프런트 엔드 및 백 엔드 구성 요소의 구분과 독립적인 관리 및 확장을 허용합니다.  Cloud Services는 각 역할을 안정적으로 호스팅할 수 있는 강력한 전용 가상 머신을 제공합니다.

Cloud Services에 대한 자세한 내용 및 Azure Websites와 Virtual Machines와의 비교에 대한 자세한 내용은 [Azure Websites, Cloud Services 및 Virtual Machines 비교]를 참조하세요.

> [!TIP]
> 간단한 웹 사이트를 빌드하려는 경우 시나리오에 간단한 웹 사이트 프런트 엔드만 포함된 경우 [간단한 웹앱 사용]을 고려해 보십시오. 그러면 웹앱이 커지고 요구 사항이 변경될 때 클라우드 서비스로 쉽게 업그레이드할 수 있습니다.

이 자습서를 수행하여 웹 역할 내에서 호스트되는 간단한 웹 응용 프로그램을 빌드합니다. 계산 에뮬레이터를 사용하여 로컬에서 응용 프로그램을 테스트한 다음 PowerShell 명령줄 도구를 사용하여 배포합니다.

응용 프로그램은 간단한 "hello world" 응용 프로그램입니다.

![Hello World 웹 페이지를 표시하는 웹 브라우저][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>필수 조건
> [!NOTE]
> 이 자습서는 Azure PowerShell을 사용하며,

* [Azure PowerShell]을 설치하고 구성합니다.
* [Azure SDK for .NET 2.7]을 다운로드 및 설치합니다. 설치 설정에서 다음을 선택합니다.
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Azure 클라우드 서비스 프로젝트 만들기
기본 Node.js 스캐폴딩과 함께 새 Azure 클라우드 서비스 프로젝트를 만들려면 다음 작업을 수행하세요.

1. **시작 메뉴** 또는 **시작 화면**에서 관리자 권한으로 **Windows PowerShell**을 실행하고 **Windows PowerShell**을 검색합니다.
2. [PowerShell을 연결] 합니다.
3. 프로젝트를 만들려면 다음 PowerShell cmdlet을 입력합니다.

        New-AzureServiceProject helloworld

    ![New-AzureService helloworld 명령의 결과][The result of the New-AzureService helloworld command]

    **New-AzureServiceProject** cmdlet은 클라우드 서비스에 Node.js 응용 프로그램을 게시하기 위한 기본 구조를 생성합니다. 여기에는 Azure에 게시하는 데 필요한 구성 파일이 포함됩니다. 또한 이 cmdlet은 작업 디렉터리를 서비스에 대한 디렉터리로 변경합니다.

    Cmdlet은 다음 파일을 만듭니다.

   * **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** 및 **ServiceDefinition.csdef**는 응용 프로그램을 게시하는 데 필요한 Azure 관련 파일입니다. 자세한 내용은 [Azure에 대한 호스티드 서비스 만들기 개요](영문)를 참조하세요.
   * **deploymentSettings.json**: Azure PowerShell 배포 cmdlet에 사용되는 로컬 설정이 저장됩니다.
4. 다음 명령을 사용하여 새 웹 역할을 추가하려면

       Add-AzureNodeWebRole

   ![Add-AzureNodeWebRole 명령의 출력][The output of the Add-AzureNodeWebRole command]

   **Add-AzureNodeWebRole** cmdlet는 기본 Node.js 응용 프로그램을 만듭니다. 또한 **.csfg** 및 **.csdef** 파일을 수정하여 새 역할에 대한 구성 항목을 추가합니다.

   > [!NOTE]
   > 역할 이름을 지정하지 않으면 기본 이름이 사용됩니다. 첫번째 cmdlet 매개변수로 이름을 제공할 수 있습니다. `Add-AzureNodeWebRole MyRole`

Node.js 앱은 웹 역할에 대한 디렉터리에 있는 **server.js** 파일에 정의됩니다(기본값은 **WebRole1**). 코드는 다음과 같습니다.

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

이 코드는 클라우드 환경에서 지정된 포트 번호를 사용한다는 점을 제외하고 기본적으로 [nodejs.org] 웹사이트의 "Hello World" 예제와 동일합니다.

## <a name="deploy-the-application-to-azure"></a>Azure에 응용 프로그램 배포

> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)하거나 [무료 계정을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF)할 수 있습니다.

### <a name="download-the-azure-publishing-settings"></a>Azure 게시 설정 다운로드
응용 프로그램을 Azure에 배포하려면 먼저 Azure 구독에 대한 게시 설정을 다운로드해야 합니다.

1. 다음 Azure PowerShell cmdlet를 실행합니다.

       Get-AzurePublishSettingsFile

   그러면 브라우저를 사용하여 게시 설정 다운로드 페이지로 이동합니다. Microsoft 계정으로 로그인하라는 메시지가 표시될 수 있습니다. 그럴 경우 Azure 구독과 연결된 계정을 사용합니다.

   다운로드한 프로필을 쉽게 액세스할 수 있는 파일 위치에 저장합니다.
2. 다음 Cmdlet를 실행하여 다운로드한 게시 프로필을 가져옵니다.

       Import-AzurePublishSettingsFile [path to file]

    > [!NOTE]
    > 게시 설정을 가져온 후, 다른 사용자가 계정에 액세스할 수 있는 정보가 포함되어 있으므로 다운로드한 .publishSettings 파일 삭제를 고려합니다.

### <a name="publish-the-application"></a>응용 프로그램 게시
게시하려면 다음 명령을 실행합니다.

      $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

* **-ServiceName**은 배포에 대한 이름을 지정합니다. 이 이름은 고유해야 합니다. 그렇지 않으면 게시 프로세스가 실패합니다. **Get-Date** 명령은 이름을 고유하게 만들어야 하는 날짜/시간 문자열을 추적합니다.
* **-위치** 응용 프로그램이 호스팅될 데이터센터를 지정합니다. 사용 가능한 데이터센터 목록을 보려면 **Get-AzureLocation** cmdlet을 사용하세요.
* **-Launch** 는 브라우저 창을 열고 배포가 완료 된 후 호스티ㅡㄷ 서비스를 탐색합니다.

게시가 성공하면 다음과 같은 응답이 표시됩니다.

![The output of the Publish-AzureService command][The output of the Publish-AzureService command]

> [!NOTE]
> 응용 프로그램이 처음 게시된 후 몇 분 지나야 배포되어 사용이 가능할 수도 있습니다.

배포가 완료되면 브라우저 창이 열리고 클라우드 서비스로 이동합니다.

![hello world 페이지가 표시된 브라우저 창. URL은 Azure에 호스팅된 페이지임을 나타냅니다.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

이제 응용 프로그램이 Azure에서 실행되고 있습니다.

**Publish-AzureServiceProject** cmdlet은 다음 단계를 수행합니다.

1. 배포할 패키지를 만듭니다. 이 패키지에는 응용 프로그램 폴더의 모든 파일이 포함됩니다.
2. **저장소 계정** 이 없는 경우 새로 만듭니다. Azure 저장소 계정은 배포 중 응용 프로그램 패키지를 저장하는 데 사용됩니다. 배포가 완료된 후에는 저장소 계정을 삭제해도 안전합니다.
3. **클라우드 서비스** 가 아직 없는 경우 새로 만듭니다. **클라우드 서비스** 는 응용 프로그램이 Azure에 배포될 때 호스트되는 컨테이너입니다. 자세한 내용은 [Azure에 대한 호스티드 서비스 만들기 개요](영문)를 참조하세요.
4. 배포 패키지를 Azure에 게시합니다.

## <a name="stopping-and-deleting-your-application"></a>응용 프로그램 중지 및 삭제
응용 프로그램을 배포한 후 사용하지 않도록 설정하여 추가 비용을 방지할 수 있습니다. Azure는 사용된 서버 시간의 시간당 웹 역할 인스턴스 요금을 청구합니다. 서버 시간은 응용 프로그램이 배포된 다음에 사용되며 인스턴스가 실행되지 않고 중지된 상태인 경우에도 사용됩니다.

1. Windows PowerShell 창에서, 이전 섹션에서 만든 서비스 배포를 다음 cmdlet을 사용하여 중지합니다.

       Stop-AzureService

   서비스를 중지하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 중지되면 서비스가 중지되었다는 메시지가 표시됩니다.

   ![Stop-AzureService 명령의 상태][The status of the Stop-AzureService command]
2. 서비스를 삭제하려면 다음 cmdlet을 호출합니다.

       Remove-AzureService

   메시지가 표시되면 **Y** 를 입력하여 서비스를 삭제합니다.

   서비스를 삭제하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 삭제되면 서비스가 삭제되었다는 메시지가 표시됩니다.

   ![Remove-AzureService 명령의 상태][The status of the Remove-AzureService command]

   > [!NOTE]
   > 서비스를 삭제해도 서비스가 처음 게시될 때 만들어진 저장소 계정은 삭제되지 않으므로 사용된 저장소에 대해 계속 요금이 청구됩니다. 저장소를 전혀 사용하지 않으면 삭제하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Node.js 개발자 센터]를 참조하세요.

<!-- URL List -->

[Azure Websites, Cloud Services 및 Virtual Machines 비교]: ../app-service/choose-web-site-cloud-service-vm.md
[간단한 웹앱 사용]: ../app-service/app-service-web-get-started-nodejs.md
[Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Azure SDK for .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[PowerShell을 연결]: /powershell/azureps-cmdlets-docs#step-3-connect
[nodejs.org]: http://nodejs.org/
[Azure에 대한 호스티드 서비스 만들기 개요]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js 개발자 센터]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
