---
title: Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑 | Microsoft Docs
description: Azure App Service의 웹앱, 모바일 앱 백 엔드 또는 API 앱에 기존 사용자 지정 DNS 도메인 이름(베니티 도메인)을 추가하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 도메인 매핑, 도메인 이름, 기존 도메인, 호스트 이름
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 431268082b24d23289188f5422cd596dc5f37d30
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-web-apps"></a>자습서: Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑

[Azure Web Apps](app-service-web-overview.md)는 확장성 있는 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 기존 사용자 지정 DNS 이름을 Azure Web Apps에 매핑하는 방법을 보여 줍니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * CNAME 레코드를 사용하여 하위 도메인(예: `www.contoso.com`) 매핑
> * A 레코드를 사용하여 루트 도메인(예: `contoso.com`) 매핑
> * CNAME 레코드를 사용하여 와일드카드 도메인(예: `*.contoso.com`) 매핑
> * 스크립트로 도메인 매핑 자동화

**CNAME 레코드** 또는 **A 레코드**를 사용하여 사용자 지정 DNS 이름을 App Service에 매핑할 수 있습니다. 

> [!NOTE]
> 루트 도메인(예: `contoso.com`)을 제외한 모든 사용자 지정 DNS 이름에 대해 CNAME을 사용하는 것이 좋습니다.

라이브 사이트 및 해당 DNS 도메인 이름을 App Service로 마이그레이션하려면 [활성 DNS 이름을 Azure App Service로 마이그레이션](app-service-custom-domain-name-migrate.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [App Service 앱을 만들거나](/azure/app-service/) 다른 자습서를 위해 만든 앱을 사용합니다.
* 도메인 이름을 구입하고 도메인 공급자(예: GoDaddy)의 DNS 레지스트리에 대한 액세스 권한이 있는지 확인합니다.

  예를 들어 `contoso.com` 및 `www.contoso.com`에 대한 DNS 항목을 추가하려면 `contoso.com` 루트 도메인에 대한 DNS 설정을 구성할 수 있어야 합니다.

  > [!NOTE]
  > 기존 도메인 이름이 없으면 [Azure Portal을 사용하여 도메인을 구입](custom-dns-web-site-buydomains-web-app.md)하는 것이 좋습니다. 

## <a name="prepare-the-app"></a>앱 준비

사용자 지정 DNS 이름을 웹앱에 매핑하려면 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 유료 계층(**공유**, **기본**, **표준** 또는 **프리미엄**)이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Azure에 로그인

[클래식 포털](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal에서 앱으로 이동합니다.

왼쪽 메뉴에서 **App Services**를 선택한 다음 앱 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service 앱의 관리 페이지가 표시됩니다.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)** 를 선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **무료** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **무료** 계층에서는 지원되지 않습니다. 

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service 계획이 **무료** 계층이 아닌 경우 **가격 책정 계층 선택** 페이지를 닫고 [CNAME 레코드 매핑](#cname)으로 건너뜁니다.

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

무료가 아닌 계층(**공유**, **기본**, **표준** 또는 **프리미엄**) 중에서 선택합니다. 

**선택**을 클릭합니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

다음 알림이 표시되면 강화 작업이 완료됩니다.

![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>CNAME 레코드 매핑

자습서 예제에서는 `www` 하위 도메인(예: `www.contoso.com`)에 대한 CNAME 레코드를 추가합니다.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

CNAME 레코드를 추가하여 하위 도메인을 앱의 기본 호스트 이름(`<app_name>.azurewebsites.net`, 여기서 `<app_name>`은 사용자 앱의 이름)에 매핑합니다.

`www.contoso.com` 도메인 예제의 경우 `www` 이름을 `<app_name>.azurewebsites.net`에 매핑하는 CNAME 레코드를 추가합니다.

CNAME을 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure에서 CNAME 레코드 매핑 사용

Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

앱의 **사용자 지정 도메인** 페이지에서 정규화된 사용자 지정 DNS 이름(`www.contoso.com`)을 목록에 추가합니다.

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

`www.contoso.com`과 같이 CNAME 레코드를 추가한 정규화된 도메인 이름을 입력합니다. 

**유효성 검사**를 선택합니다.

**호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 형식**이 **CNAME (www.example.com 또는 하위 도메인)** 으로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데에는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

![확인 오류](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>A 레코드 매핑

자습서의 예제에서는 루트 도메인(예: `contoso.com`)에 대한 A 레코드를 추가합니다. 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>앱의 IP 주소 복사

A 레코드를 매핑하려면 앱의 외부 IP 주소가 필요합니다. 이 IP 주소는 Azure Portal에서 보여 주는 해당 앱의 **사용자 지정 도메인** 페이지에서 찾을 수 있습니다.

Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**사용자 지정 도메인** 페이지에서 앱의 IP 주소를 복사합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>A 레코드 만들기

앱에 A 레코드를 매핑하려면 App Service에 다음 **두** 개의 DNS 레코드가 필요합니다.

- 앱의 IP 주소에 매핑할 **A** 레코드
- 앱의 기본 호스트 이름(`<app_name>.azurewebsites.net`)에 매핑할 **TXT** 레코드 - App Service에서 구성할 때만 이 레코드를 사용하여 사용자 지정 도메인을 소유하고 있는지 확인합니다. App Service에서 사용자 지정 도메인의 유효성을 검사하고 해당 도메인을 구성한 후에는 이 TXT 레코드를 삭제할 수 있습니다. 

`contoso.com` 도메인 예제의 경우 다음 표에 따라 A 및 TXT 레코드를 만듭니다(`@`는 일반적으로 루트 도메인을 나타냄). 

| 레코드 형식 | 호스트 | 값 |
| - | - | - |
| A | `@` | [앱의 IP 주소 복사](#info)에서 가져온 IP 주소 |
| TXT | `@` | `<app_name>.azurewebsites.net` |

레코드를 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![DNS 레코드 페이지](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>앱에서 A 레코드 매핑 사용

Azure Portal에서 해당 앱의 **사용자 지정 도메인** 페이지로 돌아가서 정규화된 사용자 지정 DNS 이름(예: `contoso.com`)을 목록에 추가합니다.

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

`contoso.com`과 같이 A 레코드를 구성한 정규화된 도메인 이름을 입력합니다.

**유효성 검사**를 선택합니다.

**호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 형식**이 **A 레코드(example.com)** 로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데에는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 A 레코드](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

![확인 오류](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>와일드카드 도메인 매핑

이 자습서의 예제에서는 CNAME 레코드를 추가하여 [와일드카드 DNS 이름](https://en.wikipedia.org/wiki/Wildcard_DNS_record)(예: `*.contoso.com`)을 App Service 앱에 매핑합니다. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

CNAME 레코드를 추가하여 와일드카드 이름을 앱의 기본 호스트 이름(`<app_name>.azurewebsites.net`)에 매핑합니다.

`*.contoso.com` 도메인 예제의 경우 CNAME 레코드에서는 `*` 이름을 `<app_name>.azurewebsites.net`에 매핑합니다.

CNAME을 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>앱에서 CNAME 레코드 매핑 사용

이제 와일드카드 이름과 일치하는 모든 하위 도메인을 앱에 추가할 수 있습니다(예: `sub1.contoso.com`과 `sub2.contoso.com`은 `*.contoso.com`과 일치함). 

Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

와일드카드 도메인과 일치하는 정규화된 도메인 이름(예: `sub1.contoso.com`)을 입력한 다음 **유효성 검사**를 선택합니다.

**호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 형식**이 **CNAME 레코드 (www.example.com 또는 하위 도메인)** 으로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데에는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

**+** 아이콘을 다시 선택하여 와일드카드 도메인과 일치하는 다른 호스트 이름을 추가합니다. 예를 들어 `sub2.contoso.com`을 추가합니다.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>브라우저에서 테스트

이전에 구성한 DNS 이름(예: `contoso.com`, `www.contoso.com`, `sub1.contoso.com` 및 `sub2.contoso.com`)을 찾습니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>404 오류 “웹 사이트를 찾을 수 없음” 해결

사용자 지정 도메인의 URL를 찾아볼 때 HTTP 404(찾을 수 없음) 오류가 나타나는 경우, <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>을 사용하여 앱의 IP 주소로 도메인이 지정되어 있는지 확인합니다. 그렇지 않은 경우 다음 이유 중 하나가 원인일 수 있습니다.

- 구성된 사용자 지정 도메인에 A 레코드 및/또는 CNAME 레코드가 없습니다.
- 브라우저 클라이언트가 도메인의 이전 IP 주소를 캐시했습니다. 캐시 및 테스트 DNS 확인을 다시 지웁니다. Windows 컴퓨터에서 캐시를 `ipconfig /flushdns`로 지웁니다.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>사용자 지정 디렉터리로 기본 URL 전달

기본적으로 App Service는 웹 요청을 사용자 앱 코드의 루트 디렉터리로 전달합니다. 그러나 특정 웹 프레임워크는 루트 디렉터리에 시작하지 않습니다. 예를 들어 [Laravel](https://laravel.com/)은 `public` 하위 디렉터리에서 시작합니다. `contoso.com` DNS 예제를 계속하려면 그러한 앱은 `http://contoso.com/public`에서 액세스할 수도 있지만, 대신 실제로 `http://contoso.com`을 `public` 디렉터리로 전달할 수 있습니다. 이 단계는 DNS 확인을 포함하지는 않으나, 가상 디렉터리를 사용자 지정합니다.

이를 수행하려면 웹앱 페이지의 왼쪽 탐색에서 **응용 프로그램 설정**을 선택합니다. 

페이지의 하단에서 기본적으로 루트 가상 디렉터리`/`는 앱 코드의 루트 디렉터리인 `site\wwwroot`를 가리킵니다. 예를 들어 대신 `site\wwwroot\public`을 가리키도록 변경하고 변경 내용을 저장합니다. 

![가상 디렉터리 사용자 지정](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

작업이 완료되면 앱은 루트 경로(예를 들어 http://contoso.com))에서 오른쪽 페이지를 반환해야 합니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/overview)을 사용하여 스크립트를 통해 사용자 지정 도메인의 관리를 자동화할 수 있습니다. 

### <a name="azure-cli"></a>Azure CLI 

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

자세한 내용은 [웹앱에 사용자 지정 도메인 매핑](scripts/app-service-cli-configure-custom-domain.md)을 참조하세요. 

### <a name="azure-powershell"></a>Azure PowerShell 

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

자세한 내용은 [Web App에 사용자 지정 도메인 할당](scripts/app-service-powershell-configure-custom-domain.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * CNAME 레코드를 사용하여 하위 도메인 매핑
> * A 레코드를 사용하여 루트 도메인 매핑
> * CNAME 레코드를 사용하여 와일드카드 도메인 매핑
> * 스크립트로 도메인 매핑 자동화

다음 자습서로 이동하여 사용자 지정 SSL 인증서를 웹앱에 바인딩하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](app-service-web-tutorial-custom-ssl.md)
