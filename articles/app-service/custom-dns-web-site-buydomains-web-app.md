---
title: "Azure Web Apps에 대한 사용자 지정 도메인 이름 구입"
description: "Azure App Service에서 웹 앱으로 사용자 지정 도메인 이름을 구입하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.openlocfilehash: 152dbb6d47dfdf3bf5df945b823f64e58e7d91e2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Azure Web Apps에 대한 사용자 지정 도메인 이름 구입

App Service 도메인(미리 보기)은 Azure에서 직접 관리되는 최상위 도메인입니다. 이 도메인을 통해 [Azure Web Apps](app-service-web-overview.md)에 대한 사용자 지정 도메인을 쉽게 관리할 수 있습니다. 이 자습서에서는 App Service 도메인을 구입하고 DNS 이름을 Azure Web Apps에 할당하는 방법을 보여 줍니다.

이 문서는 Azure App Service(Web Apps, API Apps, Mobile Apps, Logic Apps)에 관한 내용입니다. Azure VM 또는 Azure Storage의 경우 [Azure VM 또는 Azure Storage에 App Service 도메인 할당](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/)을 참조하세요. Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [App Service 앱을 만들거나](/azure/app-service/) 다른 자습서를 위해 만든 앱을 사용합니다.
* [구독에 대한 지출 한도를 삭제합니다](../billing/billing-spending-limit.md#remove). 체험 구독 크레딧으로는 App Service 도메인을 구입할 수 없습니다.

## <a name="prepare-the-app"></a>앱 준비

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Azure Web Apps에서 사용자 지정 도메인을 사용하려면 웹앱의 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 유료 계층(**공유**, **기본**, **표준** 또는 **프리미엄**)이어야 합니다. 이 단계에서는 웹앱이 지원되는 가격 책정 계층에 있음을 확인합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[클래식 포털](https://portal.azure.com)을 열고 Azure 계정으로 로그인합니다.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal에서 앱으로 이동합니다.

왼쪽 메뉴에서 **App Services**를 선택한 다음 앱 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service 앱의 관리 페이지가 표시됩니다.  

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)**를 선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **무료** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **무료** 계층에서는 지원되지 않습니다. 

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service 계획이 **평가판** 계층이 아닌 경우 **가격 책정 계층 선택** 페이지를 닫고 [도메인 구입](#buy-the-domain)으로 건너뜁니다.

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

무료가 아닌 계층(**공유**, **기본**, **표준** 또는 **프리미엄**) 중에서 선택합니다. 

**선택**을 클릭합니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

다음 알림이 표시되면 강화 작업이 완료됩니다.

![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>도메인 구입

### <a name="sign-in-to-azure"></a>Azure에 로그인
[클래식 포털](https://portal.azure.com/)을 열고 Azure 계정으로 로그인합니다.

### <a name="launch-buy-domains"></a>도메인 구입 시작
**Web Apps** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인**을 선택합니다.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**사용자 지정 도메인** 페이지에서 **도메인 구입**을 클릭합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> **App Service 도메인** 섹션이 표시되지 않으면 Azure 계정에 대한 지출 한도를 제거해야 합니다([필수 구성 요소](#prerequisites) 참조).
>
>

### <a name="configure-the-domain-purchase"></a>도메인 구매 구성

**App Service 도메인** 페이지의 **도메인 검색** 상자에 구입할 도메인 이름을 입력하고 `Enter`를 입력합니다. 사용 가능한 도메인이 텍스트 상자 아래에 나타납니다. 구입하려는 도메인을 하나 이상 선택합니다.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> App Service 도메인에서 지원되는 [최상위 도메인](https://wikipedia.org/wiki/Top-level_domain)은 _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ 및 _co.in_입니다.
>
>

**연락처 정보**를 클릭하고 도메인의 연락처 정보 양식을 작성합니다. 완료한 후 **확인**을 클릭하면 App Service 도메인 페이지로 돌아갑니다.
   
모든 필수 필드를 가능한 한 정확하게 기입해야 합니다. 잘못된 연락처 정보 데이터로 인해 도메인을 구입하지 못할 수 있습니다. 

그런 다음 도메인에 대해 원하는 옵션을 선택합니다. 설명은 다음 표를 참조하세요.

| 설정 | 제안 값 | 설명 |
|-|-|-|
|개인 정보 보호 | 사용 | _체험_ 구매 가격에 포함된 "개인 정보 보호"를 선택합니다. 일부 최상위 도메인은 개인 정보 보호를 지원하지 않는 등록 기관에서 관리하며 **개인 정보 보호** 페이지에 나열됩니다. |
| 기본 호스트 이름 할당 | **www** 및 **@** | 필요한 경우 원하는 호스트 이름 바인딩을 선택합니다. 도메인 구매 작업이 완료되면 선택한 호스트 이름에서 웹앱에 액세스할 수 있습니다. 웹앱이 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) 뒤에 있는 경우 Traffic Manager는 A 레코드를 지원하지 않으므로 루트 도메인을 할당하는 옵션이 표시되지 않습니다. 도메인 구매가 완료된 후 호스트 이름 할당을 변경할 수 있습니다. |

### <a name="accept-terms-and-purchase"></a>조건에 동의하고 구매

**약관**을 클릭하여 조건 및 요금을 검토한 후 **구입**을 클릭합니다.

> [!NOTE]
> App Service 도메인은 도메인을 호스트하는 데 Azure DNS를 사용합니다. 도메인 등록 요금 외에도 Azure DNS에 대한 사용 요금이 적용됩니다. 자세한 내용은 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.
>
>

**App Service 도메인** 페이지로 돌아가서 **확인**을 클릭합니다. 작업이 진행되는 동안 다음과 같은 알림이 표시됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>호스트 이름 테스트

웹앱에 기본 호스트 이름을 할당한 경우 선택한 각 호스트 이름에 대해 성공 알림도 표시됩니다. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

또한 선택한 호스트 이름도 **사용자 지정 도메인** 페이지의 **사용자 지정 호스트 이름** 섹션에서 표시됩니다. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

호스트 이름을 테스트하려면 브라우저에서 나열된 호스트 이름으로 이동합니다. 이전 스크린샷의 예에서 _kontoso.net_ 및 _www.kontoso.net_으로 이동해 봅니다.

## <a name="assign-hostnames-to-web-app"></a>웹앱에 호스트 이름 할당

구매 과정에서 웹앱에 기본 호스트 이름을 하나 이상 할당하지 않도록 선택한 경우 또는 나열되지 않은 호스트 이름을 할당해야 하는 경우 언제든지 호스트 이름을 할당할 수 있습니다.

App Service 도메인에서 다른 웹앱으로 호스트 이름을 할당할 수도 있습니다. 이 단계는 App Service 도메인 및 웹앱이 동일한 구독에 속하는지 여부에 따라 달라집니다.

- 다른 구독: App Service 도메인에서 외부에서 구매한 도메인과 같은 웹앱으로 사용자 지정 DNS 레코드를 매핑합니다. 사용자 지정 DNS 이름을 App Service 도메인에 추가하는 방법에 대한 자세한 내용은 [사용자 지정 DNS 레코드 관리](#custom)를 참조하세요. 외부에서 구매한 도메인을 웹앱에 매핑하려면 [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)을 참조하세요. 
- 같은 구독: 다음 단계를 따릅니다.

### <a name="launch-add-hostname"></a>호스트 이름 추가 시작
**App Services** 페이지에서 호스트 이름을 할당할 웹앱 이름을 선택하고 **설정**, **사용자 지정 도메인**을 차례로 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

구매한 도메인이 **App Service 도메인** 섹션에 나열되는지 확인하되, 선택하지는 마세요. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> 같은 구독에 있는 모든 App Service 도메인은 웹앱의 **사용자 지정 도메인** 페이지에 표시됩니다. 도메인이 웹앱의 구독에 있지만 웹앱의 **사용자 지정 도메인** 페이지에서 볼 수 없는 경우 **사용자 지정 도메인** 페이지를 다시 열거나 웹 페이지를 새로 고칩니다. 또한 Azure Portal의 맨 위에서 진행 상황 또는 생성 실패에 대한 알림 벨을 확인합니다.
>
>

**호스트 이름 추가**를 선택합니다.

### <a name="configure-hostname"></a>호스트 이름 구성
**호스트 이름 추가** 대화 상자에서 App Service 도메인 또는 하위 도메인의 정규화된 도메인 이름을 입력합니다. 예: 

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

완료되면 **유효성 검사**를 선택합니다. 호스트 이름 레코드 형식이 자동으로 선택됩니다.

**호스트 이름 추가**를 선택합니다.

작업이 완료되면 할당된 호스트 이름에 대해 성공 알림이 표시됩니다.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>호스트 이름 추가 닫기
**호스트 이름 추가** 페이지에서 원하는 대로, 웹앱에 다른 호스트 이름을 할당합니다. 완료되면 **호스트 이름 추가** 페이지를 닫습니다.

이제 **사용자 지정 도메인** 페이지에 새로 할당된 호스트 이름이 표시됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>호스트 이름 테스트

브라우저에서 나열된 호스트 이름으로 이동합니다. 이전 스크린샷의 예에서 _abc.kontoso.net_으로 이동해 봅니다.

## <a name="renew-the-domain"></a>도메인 갱신

구입한 App Service 도메인은 구입 시점으로부터 1년 동안 유효합니다. 도메인은 기본적으로 다음 해의 결제 방법을 부과하여 자동으로 갱신되도록 구성됩니다. 자동 갱신을 해제하거나 도메인을 수동으로 갱신하려면 다음 단계를 따릅니다.

**Web Apps** 탭에서 웹앱의 이름을 클릭하고, **설정**을 선택한 다음, **사용자 지정 도메인**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

도메인의 왼쪽 탐색 영역에서 **도메인 갱신**을 선택합니다. 도메인에 대한 자동 갱신을 중지하려면 **끄기**를 선택한 다음 **저장**을 선택합니다. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

도메인을 수동으로 갱신하려면 **도메인 갱신**을 선택합니다. 그러나 이 단추는 도메인 만료 이전 90일 동안만 활성화되어 있습니다.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>사용자 지정 DNS 레코드 관리

Azure에서 App Service 도메인에 대한 DNS 레코드는 [Azure DNS](https://azure.microsoft.com/services/dns/)를 사용하여 관리됩니다. 외부에서 구매한 도메인처럼 DNS 레코드를 추가, 제거 및 업데이트할 수 있습니다.

### <a name="open-app-service-domain"></a>App Service 도메인 열기

Azure Portal의 왼쪽 메뉴에서 **모든 서비스** > **App Service 도메인**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

관리할 도메인을 선택합니다. 

### <a name="access-dns-zone"></a>DNS 영역 액세스

도메인의 왼쪽 메뉴에서 **DNS 영역**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

이 작업으로 Azure DNS에서 App Service 도메인의 [DNS 영역](../dns/dns-zones-records.md) 페이지가 열립니다. DNS 레코드를 편집하는 방법에 대한 자세한 내용은 [Azure Portal에서 DNS 영역을 관리하는 방법](../dns/dns-operations-dnszones-portal.md)을 참조하세요.

## <a name="cancel-purchase-delete-domain"></a>구매 취소(도메인 삭제)

App Service 도메인을 구매한 후 5일 이내에 구매를 취소하고 전액 환불 받을 수 있습니다. 5일이 지나면 App Service 도메인을 삭제할 수 있지만 환불은 불가능합니다.

### <a name="open-app-service-domain"></a>App Service 도메인 열기

Azure Portal의 왼쪽 메뉴에서 **모든 서비스** > **App Service 도메인**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

취소 또는 삭제할 도메인을 선택합니다. 

### <a name="delete-hostname-bindings"></a>호스트 이름 바인딩 삭제

도메인의 왼쪽 메뉴에서 **호스트 이름 바인딩**을 선택합니다. 모든 Azure 서비스의 호스트 이름 바인딩이 여기에 나열됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

모든 호스트 이름 바인딩이 삭제되어야 App Service 도메인을 삭제할 수 있습니다.

**...** > **삭제**를 선택하여 각 호스트 이름 바인딩을 삭제합니다. 모든 바인딩을 삭제한 후 **저장**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>취소 또는 삭제

도메인의 왼쪽 메뉴에서 **개요**를 선택합니다. 

구매한 도메인에 대한 취소 기간이 경과되지 않은 경우 **구매 취소**를 선택합니다. 그렇지 않으면 **삭제** 단추가 대신 표시됩니다. 환불하지 않고 도메인을 삭제하려면 **삭제**를 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

작업을 확인하려면 **예**를 선택합니다.

작업이 완료되면 구독에서 도메인이 해제되어 누구든지 다시 구매할 수 있게 됩니다. 

## <a name="direct-default-url-to-a-custom-directory"></a>사용자 지정 디렉터리로 기본 URL 전달

App Service는 기본적으로 웹 요청을 앱 코드의 루트 디렉터리로 보냅니다. `public`과 같은 하위 디렉터리로 보내려면 [사용자 지정 디렉터리로 기본 URL 전달](app-service-web-tutorial-custom-domain.md#virtualdir)을 참조하세요.

## <a name="more-resources"></a>추가 리소스

[FAQ: App Service 도메인(미리 보기) 및 사용자 지정 도메인](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)