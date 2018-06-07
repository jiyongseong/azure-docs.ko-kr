---
title: 웹 사이트의 가용성 및 응답성 모니터링 | Microsoft Docs
description: Application Insights에서 웹 테스트를 설정합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2018
ms.author: sdash ; mbullwin
ms.openlocfilehash: c97b45616a58035dd5a1d7e832212fb90694ccce
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>웹 사이트의 가용성 및 응답성 모니터링
웹앱 또는 웹 사이트를 서버에 배포한 후에 가용성 및 응답성을 모니터링하도록 테스트를 설정할 수 있습니다. [Azure Application Insights](app-insights-overview.md)는 전세계 지점에서 정기적인 간격으로 응용 프로그램에 웹 요청을 보냅니다. 응용 프로그램이 응답하지 않거나 느리게 응답하는 경우 사용자에게 경고할 수 있습니다.

공용 인터넷에서 액세스 가능한 모든 HTTP 또는 HTTPS 끝점에 대해 가용성 테스트를 설정할 수 있습니다. 테스트하는 웹 사이트에 아무것도 추가하지 않아도 됩니다. 자신의 사이트가 아니어도 됩니다. 종속되어 있는 REST API 서비스를 테스트할 수 있습니다.

가용성 테스트는 다음과 같은 두 종류가 있습니다.

* [URL ping 테스트](#create): Azure 포털에서 만들 수 있는 간단한 테스트입니다.
* [다단계 웹 테스트](#multi-step-web-tests): Visual Studio Enterprise에서 만들고 포털에 업로드합니다.

응용 프로그램 리소스당 최대 100개의 가용성 테스트를 만들 수 있습니다.


> [!NOTE] 
> * 가용성 테스트 위치는 최근에 Azure 데이터 센터로 이동했습니다. 이 이동을 통해 Azure 데이터 센터의 증가하는 네트워크와 함께 위치를 추가할 수 있습니다.  
> * 테스트를 업데이트할 필요가 없습니다. 모든 테스트가 마이그레이션되고 새 위치에서 실행됩니다. 
>* 자세한 내용은 [서비스 업데이트](https://blogs.msdn.microsoft.com/applicationinsights-status/2018/01/24/application-insights-availability-monitoring-test-locations-updated/)를 참조하세요.

## <a name="create"></a>가용성 테스트 보고서에 대한 리소스 열기

웹앱에 **Application Insights를 이미 구성한 경우** [Azure Portal](https://portal.azure.com)에서 Application Insights 리소스를 엽니다.

**또는 새 리소스에서 보고서를 확인하려는 경우** [Azure Portal](https://portal.azure.com)로 이동한 후 Application Insights 리소스를 만듭니다.

![새로 만들기 > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

**모든 리소스** 를 클릭하여 새 리소스에 대한 개요 블레이드를 엽니다.

## <a name="setup"></a>URL ping 테스트 만들기
가용성 블레이드를 열고 테스트를 추가합니다.

![웹 사이트의 최소 URL 채우기](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **URL**은 테스트하려는 웹 페이지일 수 있지만 공용 인터넷에서 볼 수 있어야 합니다. URL에 쿼리 문자열을 포함할 수 있습니다. 따라서 데이터베이스 사용 등을 연습해 볼 수 있습니다. URL이 리디렉션으로 확인되면 최대 10개의 리디렉션을 따릅니다.
* **종속 요청 구문 분석**: 이 옵션을 선택하는 경우 테스트에서 테스트 대상 웹 페이지의 일부인 이미지, 스크립트, 스타일 파일 및 기타 파일을 요청합니다. 기록된 응답 시간에는 이러한 파일을 가져오는 데 걸리는 시간이 포함됩니다. 전체 테스트의 시간 제한 내에서 이러한 모든 리소스를 성공적으로 다운로드할 수 없는 경우 테스트에 실패합니다. 

    옵션을 선택하지 않으면 테스트는 지정한 URL에서만 파일을 요청합니다.

* **다시 시도 사용**: 이 옵션을 선택한 경우 테스트에 실패하면 잠시 후에 다시 시도합니다. 연속 된 세 번의 시도가 실패하는 경우에 실패가 보고됩니다. 후속 테스트는 일반적인 테스트 빈도로 수행됩니다. 다음 성공까지 다시 시도는 일시적으로 중단됩니다. 이 규칙은 각 테스트 위치에서 독립적으로 적용됩니다. 이 옵션을 권장합니다. 평균 실패의 약 80%는 다시 시도에서 사라집니다.

* **테스트 빈도**: 각 테스트 위치에서 테스트를 실행하는 빈도를 설정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.

* **테스트 위치** 는 서버가 URL로 웹 요청을 보내는 곳입니다. 웹 사이트의 문제와 네트워크 문제를 구분할 수 있도록 한 가지 이상을 선택합니다. 최대 16 개의 위치를 선택할 수 있습니다.

* **성공 조건**:

    **테스트 시간 제한**: 느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석**을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.

    **HTTP 응답**: 성공으로 계산되어 반환된 상태 코드입니다. 200은 일반적인 웹 페이지의 반환을 나타내는 코드입니다.

    "Welcome!"과 같은 **콘텐츠 일치** 문자열. 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다.
* **경고** 는 기본적으로 5분 동안 세 곳에서 오류가 발생하는 경우 사용자에게 전송됩니다. 한 곳에서 발생한 오류는 사이트의 문제가 아니라 네트워크에 문제가 있을 가능성이 높습니다. 그러나 다소 민감하게 작동하도록 임계값을 변경할 수 있으며 메일이 보내지는 주소도 변경할 수 있습니다.

    경고가 발생하면 호출되는 [웹후크](../monitoring-and-diagnostics/insights-webhooks-alerts.md)를 설정할 수 있습니다. 그러나 현재 쿼리 매개 변수는 속성으로 전달되지 않습니다.

### <a name="test-more-urls"></a>더 많은 URL 테스트
테스트를 더 추가 합니다. 예를 들어 홈 페이지를 테스트할 수 있을 뿐 아니라 검색을 위한 URL을 테스트하여 데이터베이스가 실행되고 있는지 확인할 수 있습니다.


## <a name="monitor"></a>가용성 테스트 결과 참조

5분 후에 **새로 고침**을 클릭하여 테스트 결과를 볼 수 있습니다. 

![홈 블레이드에 대한 요약 결과](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

산점도에서는 진단 테스트 단계의 세부 정보가 포함된 테스트 결과 샘플을 보여 줍니다. 테스트 엔진은 실패한 테스트에 대한 진단 정보를 저장합니다. 성공한 테스트의 경우 실행의 하위 집합에 대한 진단 정보가 저장됩니다. 테스트 타임스탬프, 테스트 지속 시간, 위치 및 테스트 이름을 보려면 녹색/빨간색 점 위로 마우스를 이동합니다. 테스트 결과의 세부 정보를 보려면 산점도에서 원하는 점을 클릭합니다.  

특정 테스트, 위치를 선택하거나 기간을 줄여 대상 기간에서 더 많은 결과를 볼 수 있습니다. 검색 탐색기를 사용하여 모든 실행의 결과를 확인하거나 분석 쿼리를 사용하여 이 데이터에 대한 사용자 지정 보고서를 실행합니다.

메트릭 탐색기에는 원시 결과 외에도 두 개의 가용성 메트릭이 있습니다. 

1. 가용성: 모든 테스트 실행에서 성공한 테스트의 비율입니다. 
2. 테스트 지속 시간: 모든 테스트 실행에서의 평균 테스트 지속 시간입니다.

테스트 이름, 위치에 필터를 적용하여 특정 테스트 및/또는 위치의 추세를 분석할 수 있습니다.

## <a name="edit"></a>테스트 검사 및 편집

요약 페이지에서 특정 테스트를 선택합니다. 거기에서 해당 특정 결과를 확인하고 편집하거나 일시적으로 사용하지 않도록 설정할 수 있습니다.

![웹 테스트 편집 또는 사용 안 함](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

서비스에 대한 유지 관리를 수행하는 동안 가용성 테스트 또는 관련된 경고 규칙을 사용하지 않도록 설정할 수 있습니다. 

## <a name="failures"></a>오류가 표시되는 경우
빨간 점을 클릭합니다.

![빨간 점을 클릭 합니다.](./media/app-insights-monitor-web-app-availability/open-instance-3.png)


가용성 테스트 결과에서 다음을 수행할 수 있습니다.

* 서버로부터 수신한 응답을 검사합니다.
* 실패한 요청 인스턴스를 처리하는 동안 수집된 서버 쪽 원격 분석 데이터로 실패를 진단합니다.
* Git 또는 VSTS에 문제 또는 작업 항목을 기록하고 문제를 추적합니다. 버그에는 이 이벤트에 대한 링크가 포함됩니다.
* 웹 테스트 결과를 Visual Studio에서 엽니다.

*정상으로 보이지만 실패로 보고되었습니까?* 노이즈를 줄이는 방법은 [FAQ](#qna)를 참조하세요.


> [!TIP]
> 신뢰할 수 있는 모니터링의 경우 2개 이상의 위치에서 테스트하는 것이 좋습니다.
>

## <a name="multi-step-web-tests"></a>다중 단계 웹 테스트
URL 시퀀스를 포함하는 시나리오를 모니터링할 수 있습니다. 예를 들어 판매 웹 사이트를 모니터링하는 경우 장바구니에 항목을 제대로 추가할 수 있는지 테스트할 수 있습니다.

> [!NOTE] 
> 다단계 웹 테스트에 대한 요금이 청구됩니다. [가격 체계](http://azure.microsoft.com/pricing/details/application-insights/).
> 

다단계 테스트를 만들려면 Visual Studio Enterprise를 사용하여 시나리오를 기록한 다음 Application Insights에 기록을 업로드합니다. Application Insights는 지정된 간격에 따라 시나리오를 재생하고 응답을 확인합니다.

> [!NOTE]
> * 테스트에서 코딩된 함수 또는 루프를 사용할 수 없습니다. 테스트는 .webtest 스크립트에 완전히 포함되어야 합니다. 그러나 표준 플러그 인을 사용할 수 있습니다.
> * 영어 문자만 다단계 웹 테스트에서 지원됩니다. 다른 언어로 Visual Studio를 사용하는 경우 웹 테스트 정의 파일을 영어가 아닌 문자를 번역/제외하도록 업데이트하세요.
>

#### <a name="1-record-a-scenario"></a>1. 시나리오 기록
Visual Studio Enterprise를 사용하여 웹 세션을 기록합니다.

1. 웹 성능 테스트 프로젝트를 만듭니다.

    ![Visual Studio Enterprise 버전에서 웹 성능 및 부하 테스트 템플릿으로 프로젝트를 만듭니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *웹 성능 및 부하 테스트 템플릿이 보이지 않으십니까?* - Visual Studio Enterprise를 닫습니다. **Visual Studio 설치 관리자**를 열고 Visual Studio Enterprise 설치를 수정합니다. **개별 구성 요소** 아래에서 **웹 성능 및 부하 테스트 도구**를 선택합니다.

2. .webtest 파일을 열고 기록을 시작합니다.

    ![.webtest 파일을 열고 레코드를 클릭합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. 웹사이트를 열고 장바구니에 제품을 추가하는 등 테스트에서 시뮬레이션 하려는 사용자 작업을 수행합니다. 테스트를 중지합니다.

    ![웹 테스트 레코더는 Internet Explorer에서 실행합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    시나리오를 길게 만들지 마세요. 100개 단계와 2 분으로 제한됩니다.
4. 테스트를 다음과 같이 편집합니다.

   * 받은 텍스트 및 응답 코드를 확인하는 유효성 검사를 추가합니다.
   * 불필요한 상호 작용을 모두 제거합니다. 사진이나 광고 또는 추적 사이트에 대한 종속 요청을 제거할 수도 있습니다.

     테스트 스크립트만 편집할 수 있습니다. 사용자 지정 코드를 추가하거나 다른 웹 테스트를 호출할 수는 없습니다. 테스트에 루프를 삽입하지 마세요. 표준 웹 테스트 플러그 인을 사용할 수 있습니다.
5. Visual Studio에서 테스트를 실행하여 제대로 작동하는지 확인합니다.

    웹 test runner에서 웹 브라우저가 열리고 기록한 작업을 반복합니다. 예상대로 작동하는지 확인합니다.

    ![Visual Studio에서 .webtest 파일을 열고 실행을 클릭합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Application Insights에 웹 테스트를 업로드
1. Application Insights 포털에서 웹 테스트를 만듭니다.

    ![웹 테스트 블레이드에서 추가를 선택합니다.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. 다단계 테스트를 선택하고 .webtest 파일을 업로드합니다.

    ![다단계 웹 테스트를 선택합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    ping 테스트와 같은 방식으로 테스트 위치, 빈도, 경고 매개 변수를 설정합니다.

#### <a name="3-see-the-results"></a>3. 결과 확인

단일 URL 테스트의 경우와 마찬가지로 테스트 결과 및 발생한 오류를 확인합니다.

또한 Visual Studio에서 보려면 테스트 결과를 다운로드할 수 있습니다.

#### <a name="too-many-failures"></a>오류가 너무 많나요?

* 실패의 일반적인 이유는 테스트를 너무 오래 실행하기 때문입니다. 2분 이내로 실행해야 합니다.

* 테스트가 성공하려면 스크립트, 스타일 시트, 이미지 등을 포함하는 페이지의 모든 리소스가 제대로 로드되어야 한다는 점을 잊지 마세요.

* 웹 테스트가 .webtest 스크립트에 완전히 포함되어야 합니다. 코딩된 함수는 테스트에 사용할 수 없습니다.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>다단계 테스트에 시간 및 난수 연결
외부 피드에서 주식과 같이 시간에 따라 변하는 데이터를 가져오는 도구를 테스트한다고 가정합니다. 웹 테스트를 기록할 때 특정 시간을 사용해야 하지만 테스트 매개 변수로 StartTime 및 EndTime을 설정할 수 있습니다.

![매개 변수를 가진 웹 테스트입니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

테스트를 실행하면 EndTime이 항상 현재 시간이 되고 StartTime은 15분이 됩니다.

웹 테스트 플러그 인을 사용하여 시간을 매개 변수화할 수 있습니다.

1. 원하는 각 가변 매개 변수 값에 대한 웹 테스트 플러그 인을 추가합니다. 웹 테스트 도구 모음에서 **웹 테스트 플러그 인 추가**를 선택합니다.

    ![웹 테스트 플러그인 추가를 선택하고 형식을 선택합니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    이 예에서는 날짜 시간 플러그인의 두 인스턴스를 사용합니다. 한 인스턴스는 "15분 전"이고 다른 하나는 "지금"입니다.
2. 각 플러그인의 속성을 엽니다. 이름을 지정하고 현재 시간을 사용하도록 설정합니다. 둘 중 하나에 대해 Add Minutes = -15를 설정합니다.

    ![이름을 설정하고, 현재 시간을 사용하며 시간을 추가합니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. 웹 테스트 매개 변수에 {{플러그 인 이름}}을(를) 사용하여 플러그 인 이름을 참조합니다.

    ![테스트 매개 변수에서 {{플러그인 이름}}을(를) 사용합니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

이제 테스트를 포털에 업로드합니다. 테스트의 모든 실행에 동적 값을 사용합니다.

## <a name="dealing-with-sign-in"></a>로그인 처리
사용자가 앱에 로그인하면 로그인 뒤에 페이지를 테스트할 수 있도록 로그인을 시뮬레이션하기 위한 여러 옵션이 있습니다. 앱에서 제공하는 보안의 형식에 따라 사용하는 방법이 달라집니다.

모든 경우에 테스트하기 위해 응용 프로그램에 계정을 만들어야 합니다. 웹 테스트가 실제 사용자에 영향을 줄 가능성이 발생하지 않도록 가능하면 이 테스트 계정의 사용 권한을 제한합니다.

### <a name="simple-username-and-password"></a>간단한 사용자 이름 및 암호
일반적인 방법으로 웹 테스트를 기록합니다. 우선 쿠키를 삭제합니다.

### <a name="saml-authentication"></a>SAML 인정
웹 테스트에 사용할 수 있는 SAML 플러그 인을 사용할 수 있습니다.

### <a name="client-secret"></a>클라이언트 암호
앱에 클라이언트 암호를 포함하는 로그인 경로가 있는 경우 해당 경로를 사용합니다. AAD(Azure Active Directory)는 클라이언트 암호 로그인을 제공하는 서비스의 예입니다. AAD에서 클라이언트 암호는 앱 키입니다.

앱 키를 사용하는 Azure 웹앱의 샘플 웹 테스트는 다음과 같습니다.

![클라이언트 암호 샘플](./media/app-insights-monitor-web-app-availability/110.png)

1. 클라이언트 암호(AppKey)를 사용하여 AAD에서 토큰을 가져옵니다.
2. 응답에서 전달자 토큰을 추출합니다.
3. 인증 헤더에서 전달자 토큰을 사용하여 API를 호출합니다.

웹 테스트는 실제 클라이언트여야 합니다. 즉, AAD에 고유한 앱이 있고 clientId + appkey를 사용합니다. 테스트 대상 서비스는 AAD에 고유한 앱을 가집니다. 이 앱의 appID URI는 "리소스" 필드의 웹 테스트에 반영됩니다.

### <a name="open-authentication"></a>공개 인증
공개 인증의 예는 Microsoft 또는 Google 계정으로 로그인하는 것입니다. OAuth를 사용하는 많은 앱은 클라이언트 암호 대안을 제공하므로 첫 번째 방법으로 해당 가능성을 조사해야 합니다.

테스트가 OAuth를 사용하여 로그인해야 하는 경우 일반적인 방법은 다음과 같습니다.

* Fiddler와 같은 도구를 사용하여 웹 브라우저, 인증 사이트 및 응용 프로그램 간의 트래픽을 검사합니다.
* 다른 컴퓨터 또는 브라우저를 사용하거나 긴 간격으로 두 개 이상의 로그인을 수행합니다(토큰이 만료되도록 허용).
* 다른 세션을 비교하여 인증 사이트에서 다시 전달된 토큰을 식별하고 이는 로그인한 후에 앱 서버에 전달됩니다.
* Visual Studio Online을 사용하여 웹 테스트 기록
* 토큰을 매개 변수화하고 토큰이 인증자에서 반환되면 매개 변수를 설정하고 사이트에 대한 쿼리에서 사용합니다.
  (Visual Studio는 테스트를 매개 변수화하려고 하지만 올바르게 토큰을 매개 변수화하지 않습니다.)


## <a name="performance-tests"></a>성능 테스트
웹 사이트에 부하 테스트를 실행할 수 있습니다. 가용성 테스트와 같이 전 세계의 이 지점에서 간단한 요청 또는 다단계 요청을 보낼 수 있습니다. 가용성 테스트와는 달리 많은 요청이 전송되어 여러 동시 사용자를 시뮬레이션합니다.

개요 블레이드에서 **설정**, **성능 테스트**를 엽니다. 테스트를 만들 경우 Visual Studio Team Services 계정에 연결하거나 만들어야 합니다.

테스트가 완료되면 응답 시간 및 성공률이 표시됩니다.


![성능 테스트](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> 성능 테스트 결과를 살펴 보려면 [라이브 스트림](app-insights-live-stream.md) 및 [프로필](app-insights-profiler.md)을 사용합니다.
>

## <a name="automation"></a>Automation
* [PowerShell 스크립트를 사용하여 가용성 테스트를 자동으로 설정](app-insights-powershell.md#add-an-availability-test)합니다.
* 경고가 발생하면 호출되는 [웹후크](../monitoring-and-diagnostics/insights-webhooks-alerts.md)를 설정합니다.

## <a name="qna"></a>질문이 있습니까? 문제가 있습니까?
* *프로토콜 위반 오류로 인한 일시적 테스트 오류인가요?*

    이 오류("프로토콜 위반, CR 뒤에 LF가 와야 함")는 서버(또는 종속성)와 관련된 문제를 나타내며 잘못된 헤더가 응답에 설정된 경우에 발생합니다. 부하 분산 장치 또는 CDN으로 인해 발생할 수 있습니다. 특히 일부 헤더는 줄 끝을 표시하는 데 CRLF를 사용하지 않았을 수 있으며 이는 HTTP 사양을 위반하기 때문에 .NET WebRequest 수준에서 유효성 검사가 실패합니다. 위반한 헤더를 찾기 위해 응답을 검사합니다.
    
    참고: HTTP 헤더의 유효성 검사가 완화된 브라우저에서는 URL이 실패하지 않을 수 있습니다. 이 문제에 대한 자세한 설명은 http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/ 블로그 게시물을 참조하세요.  
* *사이트는 괜찮아 보이지만 테스트 실패가 표시됩니다.*

    * 모든 이미지, 스크립트, 스타일 시트 및 페이지에 의해 로드된 다른 파일을 확인합니다. 그 중 하나라도 실패하면, 기본 html 페이지가 확인을 로드하는 경우에도 테스트는 실패로 보고됩니다. 테스트가 이러한 리소스 오류에 둔감해지도록 하려면 테스트 구성에서 "종속 요청 구문 분석"을 선택 취소합니다. 

    * 일시적인 네트워크 문제 등으로 인해 노이즈가 발생할 가능성을 줄이려면 "Enable retries for test failures"(테스트 실패 시 다시 시도 사용) 구성을 선택해야 합니다. 또한 더 많은 위치에서 테스트하고 그에 따라 경고 규칙 임계값을 관리하여 과도한 경고를 유발하는 특정 위치의 문제를 방지할 수 있습니다.
    
* *테스트 실패를 진단할만한 관련 서버 쪽 원격 분석 데이터가 표시되지 않습니다.*
    
    서버 쪽 응용 프로그램에 대해 Application Insights를 설정한 경우, [샘플링](app-insights-sampling.md)이 작동 중이기 때문일 수 있습니다.
* *웹 테스트에서 코드를 호출할 수 있나요?*

    번호 테스트 단계는 .webtest 파일에 포함되어야 합니다. 또한 다른 웹 테스트를 호출하거나 루프를 사용할 수 없습니다. 그러나 몇 가지 유용한 플러그 인이 있습니다.
* *HTTPS가 지원됩니까?*

    TLS 1.1 및 TLS 1.2를 지원합니다.
* *"웹 테스트" 및 "가용성 테스트" 간의 차이가 있나요?*

    두 용어는 같은 의미로 참조할 수 있습니다. 가용성 테스트는 다단계 웹 테스트 외에 단일 URL ping 테스트를 포함한 보다 일반적인 용어입니다.
* *방화벽 뒤에 실행되는 내부 서버에서 가용성 테스트를 사용하려 합니다.*

    가능한 해결 방법으로 다음 두 가지가 있습니다.
    
    * [웹 테스트 에이전트의 IP 주소](app-insights-ip-addresses.md)에서 들어오는 요청을 허용하도록 방화벽을 구성합니다.
    * 내부 서버를 정기적으로 테스트하는 사용자 고유의 코드를 작성합니다. 방화벽 뒤의 테스트 서버에서 코드를 백그라운드 프로세스로 실행합니다. 테스트 프로세스는 코어 SDK 패키지의 [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API를 사용하여 Application Insights에 결과를 보낼 수 있습니다. 이렇게 하려면 테스트 서버에 Application Insights 수집 끝점에 대한 송신 액세스 권한이 있어야 하지만, 들어오는 요청을 허용하는 대체 방법보다 보안 위험이 훨씬 낮습니다. 결과는 가용성 웹 테스트 블레이드에 표시되지 않지만, 분석 탐색기, 검색 탐색기 및 메트릭 탐색기에는 가용성 결과로 표시됩니다.
* *다중 단계 웹 테스트 업로드 실패*

    300K의 크기 제한이 있습니다.

    루프는 지원되지 않습니다.

    다른 웹 테스트에 대한 참조는 지원되지 않습니다.

    데이터 원본은 지원되지 않습니다.
* *다중 단계 테스트가 완료되지 않습니다.*

    테스트당 100개 요청의 제한이 있습니다.

    2분 이상 실행되는 경우 테스트가 중지됩니다.
* *클라이언트 인증서로 테스트를 실행하는 방법*

    죄송합니다만, 지원되지 않습니다.


## <a name="next"></a>다음 단계
[진단 로그 검색][diagnostic]

[문제 해결][qna]

[웹 테스트 에이전트의 IP 주소](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
