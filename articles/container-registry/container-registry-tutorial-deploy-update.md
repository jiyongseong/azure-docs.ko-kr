---
title: Azure Container Registry 자습서 - 지역 배포에 업데이트된 이미지 푸시
description: 수정된 Docker 이미지를 지역에서 복제된 Azure 컨테이너 레지스트리에 푸시한 다음 여러 지역에서 실행되는 웹앱에 자동으로 배포되는 변경 내용을 확인합니다. 3부로 구성된 시리즈 중 제3부입니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 2e9a46f2a99bc9b530ac5859068bde58bf5b5098
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-push-an-updated-image-to-regional-deployments"></a>자습서: 지역 배포에 업데이트된 이미지 푸시

세 부분으로 이루어진 자습서 시리즈의 세 번째 부분입니다. [이전 자습서](container-registry-tutorial-deploy-app.md)에서 두 개의 서로 다른 지역 웹앱 배포에 대해 지역에서 복제가 구성되었습니다. 이 자습서에서는 먼저 응용 프로그램을 수정한 다음 새 컨테이너 이미지를 빌드하고 지역에서 복제된 레지스트리를 푸시합니다. 마지막으로 웹앱 인스턴스 모두에서 Azure Container Registry webhook에 의해 자동으로 배포된 변경 내용을 확인합니다.

이 자습서는 시리즈의 마지막 부분입니다.

> [!div class="checklist"]
> * 웹 응용 프로그램 HTML 수정
> * Docker 이미지 빌드 및 태그 지정
> * Azure Container Registry에 변경 내용 푸시
> * 두 개의 서로 다른 지역에서 업데이트된 앱 보기

두 개의 *Web App for Containers* 지역 배포를 아직 구성하지 않은 경우 시리즈의 이전 자습서, [Azure Container Registry에서 웹앱 배포](container-registry-tutorial-deploy-app.md)로 돌아갑니다.

## <a name="modify-the-web-application"></a>웹 응용 프로그램 수정

이 단계에서는 Azure Container Registry에 업데이트된 컨테이너 이미지를 푸시하면 항상 볼 수 있는 웹 응용 프로그램을 변경합니다.

이전 자습서의 [GitHub에서 복제한](container-registry-tutorial-prepare-registry.md#get-application-code) 응용 프로그램 원본에서 `AcrHelloworld/Views/Home/Index.cshtml` 파일을 찾고 원하는 텍스트 편집기에서 엽니다. 기존 `<h1>` 줄 아래에 다음 줄을 추가합니다.

```html
<h1>MODIFIED</h1>
```

수정된 `Index.cshtml`은 다음과 유사하게 표시됩니다.

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>이미지 다시 빌드

이제 웹 응용 프로그램을 업데이트했으므로 해당 컨테이너 이미지를 다시 빌드합니다. 이전처럼 로그인 서버 URL을 포함하는 정규화된 이미지 이름을 사용합니다. 태그의 경우:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시하기

이제 지역에서 복제된 레지스트리에 업데이트된 *acr-helloworld* 컨테이너 이미지를 푸시합니다. 여기에서 단일 `docker push` 명령을 실행하여 *미국 서부* 및 *미국 동부* 지역 모두의 레지스트리 복제본에 업데이트된 이미지를 배포합니다.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

출력은 다음과 유사합니다.

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Webhook 로그 보기

이미지가 복제 중인 동안 트리거되는 Azure Container Registry webhook를 볼 수 있습니다.

이전 자습서에서 *Web App for Containers*에 컨테이너를 배포할 때 생성된 지역 webhook를 보려면 Azure Portal에서 컨테이너 레지스트리로 이동한 다음 **서비스** 아래에서 **Webhook**를 선택합니다.

![Azure Portal의 컨테이너 레지스트리 Webhook][tutorial-portal-01]

각 Webhook를 선택하여 해당 호출 및 응답의 기록을 봅니다. 두 Webhook의 로그에 **푸시** 작업에 대한 행이 표시됩니다. 여기에서 *미국 서부* 지역에 있는 Webhook에 대한 로그는 이전 단계에서 `docker push`에 의해 트리거되는 **푸시** 동작을 보여 줍니다.

![Azure Portal(미국 서부)의 컨테이너 레지스트리 Webhook 로그][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>업데이트된 웹앱 보기

Webhook는 두 지역 웹앱에 업데이트된 컨테이너를 자동으로 배포하는 레지스트리에 새 이미지가 푸시됐음을 Web Apps에 알립니다.

웹 브라우저에서 두 지역 웹앱 배포로 이동하여 응용 프로그램이 두 배포 모두에서 업데이트되었는지 확인합니다. 참고로 각 App Service 개요 탭의 상단 오른쪽에서 배포된 웹앱에 대한 URL을 찾을 수 있습니다.

![Azure Portal의 App Service 개요][tutorial-portal-03]

업데이트된 응용 프로그램을 보려면 App Service 개요에서 링크를 선택합니다. *미국 서부*에서 실행되는 앱의 예제 보기는 다음과 같습니다.

![미국 서부 지역에서 실행되는 수정된 웹앱의 브라우저 보기][deployed-app-westus-modified]

브라우저에서 확인하여 업데이트된 컨테이너 이미지가 *미국 동부* 배포에 배포되었는지 확인합니다.

![미국 동부 지역에서 실행되는 수정된 웹앱의 브라우저 보기][deployed-app-eastus-modified]

단일 `docker push`를 사용하여 두 지역 웹앱 배포를 업데이트했고 Azure Container Registry는 네트워크에 가까운 리포지토리에서 컨테이너 이미지를 제공했습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 지역에서 복제된 레지스트리에 새 버전의 웹 응용 프로그램 컨테이너를 업데이트 및 푸시했습니다. Azure Container Registry의 웹후크가 레지스트리 복제본에서 로컬 끌어오기를 트리거하는 업데이트를 Web App for Containers에 알렸습니다.

이 시리즈의 마지막 자습서에서 다음을 수행했습니다.

> [!div class="checklist"]
> * 웹 응용 프로그램 HTML 업데이트
> * Docker 이미지 빌드 및 태그 지정
> * Azure Container Registry에 변경 내용 푸시
> * 두 개의 서로 다른 지역에서 업데이트된 앱 보기

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png