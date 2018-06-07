---
title: "음성 및 SMS에 Twilio를 사용하는 방법(.NET) | Microsoft Docs"
description: "Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 .NET으로 작성되었습니다."
services: 
documentationcenter: .net
author: devinrader
manager: twilio
editor: 
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Azure에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법
이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 응용 프로그램에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

## <a id="WhatIs"></a>Twilio 정의
Twilio는 개발자가 응용 프로그램에 음성, VoIP 및 메시징을 포함할 수 있도록 하면서 비즈니스 통신의 미래를 이끌고 있습니다. 개발자는 클라우드 기반 글로벌 환경에 필요한 모든 인프라를 가상화하고, Twilio 통신 API 플랫폼을 통해 이를 공개합니다. 덕분에 응용 프로그램을 간단히 빌드하고 확장할 수 있습니다. 종량제 가격 책정의 유연성과 클라우드 안정성의 이점을 누리세요.

**Twilio 음성** 을 통해 응용 프로그램에서 전화를 걸고 받을 수 있습니다. **Twilio SMS** 를 사용하면 응용 프로그램에서 SMS 메시지를 보내고 받을 수 있습니다. **Twilio 클라이언트** 를 통해서는 전화, 태블릿 또는 브라우저에서 VoIP 통화를 하고 WebRTC를 지원할 수 있습니다.

## <a id="Pricing"></a>Twilio 가격 책정 및 특별 제공
Azure 고객은 Twilio 계정을 업그레이드할 때 [특별 제공](http://www.twilio.com/azure)(10달러의 Twilio 크레딧)을 받습니다. 이 Twilio 크레딧은 모든 Twilio 사용량에 적용될 수 있습니다. 10달러의 크레딧은 전화 번호 및 메시지 또는 통화 대상의 위치에 따라 SMS 메시지를 1,000개 보내거나 최대 1000분간 인바운드 음성을 받을 수 있는 금액입니다. [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure)에서 이 Twilio 크레딧을 충전하고 시작하십시오.

Twilio는 종량제 서비스입니다. 설치 수수료는 없으며 언제든 계정을 종료할 수 있습니다. [Twilio 가격 책정](http://www.twilio.com/voice/pricing)(영문)에서 자세한 내용을 볼 수 있습니다.

## <a id="Concepts"></a>개념
Twilio API는 응용 프로그램에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][twilio_libraries](영문)를 참조하십시오.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <a id="Verbs"></a>Twilio 동사
API는 Twilio 동사를 활용합니다. 예를 들어 **&lt;Say&gt;** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다.  기타 동사 및 기능에 대해서는 [Twilio Markup Language 설명서](http://www.twilio.com/docs/api/twiml)(영문)에서 알아보십시오.

* **&lt;Dial&gt;**: 발신자를 다른 전화에 연결합니다.
* **&lt;Gather&gt;**: 전화 키패드에 입력된 숫자를 수집합니다.
* **&lt;Hangup&gt;**: 통화를 끝냅니다.
* **&lt;Play&gt;**: 오디오 파일을 재생합니다.
* **&lt;Pause&gt;**: 지정된 시간(초) 동안 무음으로 대기합니다.
* **&lt;Record&gt;**: 발신자의 음성을 녹음하고 녹음이 포함된 파일의 URL을 반환합니다.
* **&lt;Redirect&gt;**: 통화 또는 SMS에 대한 제어를 다른 URL의 TwiML로 전송합니다.
* **&lt;Reject&gt;**: 요금을 청구하지 않고 Twilio 번호의 수신 전화를 거부합니다.
* **&lt;Say&gt;**: 텍스트를 통화에 사용되는 음성으로 변환합니다.
* **&lt;Sms&gt;**: SMS 메시지를 보냅니다.

### <a id="TwiML"></a>TwiML
TwiML은 Twilio에 통화 또는 SMS 처리 방법을 알려 주는 Twilio 동사를 사용하는 XML 기반 명령 집합입니다.

다음 예제 TwiML은 **Hello World** 텍스트를 음성으로 변환합니다.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

응용 프로그램에서 Twilio API를 호출할 때 API 매개 변수 중 하나는 TwiML 응답을 반환하는 URL입니다. 개발을 위해서 Twilio 제공 URL을 사용하여 응용 프로그램에 사용되는 TwiML 응답을 제공할 수 있습니다. 또한 TwiML 응답을 생성하는 고유한 URL을 호스트할 수도 있고, **TwiMLResponse** 개체를 사용할 수도 있습니다.

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][twiml](영문)을 참조하십시오. Twilio API에 대한 자세한 내용은 [Twilio API][twilio_api](영문)를 참조하십시오.

## <a id="CreateAccount"></a>Twilio 계정 만들기
Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][try_twilio](영문)에서 등록하십시오. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 ID 및 인증 토큰을 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하십시오. 계정 ID 및 인증 토큰은 [Twilio 계정 페이지][twilio_account](영문)의 **ACCOUNT SID** 및 **AUTH TOKEN**에서 각기 확인할 수 있습니다.

## <a id="create_app"></a>Azure 응용 프로그램 만들기
Twilio 사용 응용 프로그램을 호스트하는 Azure 응용 프로그램도 다른 Azure 응용 프로그램과 동일합니다. Twilio .NET 라이브러리를 추가하고 Twilio .NET 라이브러리를 사용하도록 역할을 구성하면 됩니다.
초기 Azure 프로젝트 만들기에 대한 자세한 내용은 [Visual Studio에서 Azure 프로젝트 만들기][vs_project]를 참조하십시오.

## <a id="configure_app"></a>Twilio 라이브러리를 사용하도록 응용 프로그램 구성
Twilio는 .NET 도우미 라이브러리 집합을 제공하며, 이 라이브러리 집합은 Twilio의 여러 가지 요소를 래핑함으로써 Twilio REST API 및 Twilio 클라이언트를 간단하고 쉽게 조작해서 TwiML 응답을 생성하는 방법을 제공합니다.

Twilio는 다음과 같이 .NET 개발자를 위한 5가지 라이브러리를 제공합니다.
라이브러리|설명
---|---
Twilio.API|친숙한 .NET 라이브러리에서 Twilio REST API를 래핑하는 핵심 Twilio 라이브러리입니다. 이 라이브러리는 .NET, Silverlight 및 Windows Phone 7에 사용할 수 있습니다.
Twilio.TwiML|TwiML 태그를 생성하는 친숙한 .NET 방법을 제공합니다.
Twilio.MVC|ASP.NET MVC를 사용하는 개발자를 위해 이 라이브러리는 TwilioController, TwiML ActionResult 및 요청 유효성 검사 특성을 포함합니다.
Twilio.WebMatrix|Microsoft의 무료 WebMatrix 개발 도구를 사용하는 개발자를 위해 이 라이브러리는 다양한 Twilio 작업에 사용할 수 있는 Razor 구문을 포함합니다.
Twilio.Client.Capability|Twilio 클라이언트 JavaScript SDK와 함께 사용할 수 있는 기능 토큰 생성기를 포함합니다.

모든 라이브러리를 사용하려면 .NET 3.5, Silverlight 4 또는 Windows Phone 7 이상이 필요합니다.

이 가이드에 제공된 샘플에서는 Twilio.API 라이브러리를 사용합니다.

라이브러리를 Visual Studio 2010에서 2015까지의 [NuGet 패키지 관리자 확장명을 사용하여 설치](http://www.twilio.com/docs/csharp/install) 할 수 있습니다.  소스 코드는 [GitHub][twilio_github_repo]에서 호스팅되며, 라이브러리 사용에 대한 전체 설명서가 들어 있는 Wiki를 포함합니다.

기본적으로, Microsoft Visual Studio 2010은 버전 1.2의 NuGet을 설치합니다. Twilio 라이브러리를 설치하려면 버전 1.6 이상의 NuGet이 필요합니다. NuGet 설치 또는 업데이트에 대해서는 [http://nuget.org/][nuget](영문)를 참조하십시오.

> [!NOTE]
> 최신 버전의 NuGet을 설치하려면 먼저 Visual Studio 확장 관리자를 사용하여 로드된 버전을 제거해야 합니다. 이를 위해서는 Visual Studio를 관리자 권한으로 실행해야 합니다. 관리자 권한으로 실행하지 않으면 제거 단추를 사용할 수 없습니다.
>
>

### <a id="use_nuget"></a>Visual Studio 프로젝트에 Twilio 라이브러리를 추가하려면
1. Visual Studio에서 솔루션을 엽니다.
2. **참조**를 마우스 오른쪽 단추로 클릭합니다.
3. **NuGet 패키지 관리...**
4. **온라인**을 클릭합니다.
5. 온라인 검색 상자에 *twilio*를 입력합니다.
6. Twilio 패키지에서 **설치** 를 클릭합니다.

## <a id="howto_make_call"></a>방법: 발신 전화 걸기
다음은 **CallResource** 클래스를 사용하여 발신 전화를 거는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **to** 및 **from** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **from** 번호를 확인하세요.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

**CallResource.Create** 메서드에 전달된 매개 변수에 대한 자세한 내용은 [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]를 참조하세요.

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 이 사이트 대신 고유한 사이트를 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [방법: 고유한 웹 사이트에서 TwiML 응답 제공](#howto_provide_twiml_responses)을 참조하세요.

## <a id="howto_send_sms"></a>방법: SMS 메시지 보내기
다음 스크린샷은 **MessageResource** 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 평가판 계정의 경우 Twilio에서 SMS 메시지를 보낼 **from** 번호를 자동으로 입력합니다. 코드를 실행하기 전에 Twilio 계정에 대해 **to** 번호를 확인해야 합니다.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    try
    {
        // Send an SMS message.
        var message = MessageResource.Create(
            to: new PhoneNumber("+12069419717"),
            from: new PhoneNumber("+14155992671"),
            body: "This is my SMS message.");
    }
    catch (TwilioException ex)
    {
        // An exception occurred making the REST call
        Console.WriteLine(ex.Message);
    }

## <a id="howto_provide_twiml_responses"></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공
응용 프로그램에서 Twilio API 호출을 시작하면(예: **CallResource.Create** 메서드를 통해) Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. [방법: 발신 전화 걸기](#howto_make_call)의 예제에서는 Twilio 제공 URL인 [http://twimlets.com/message][twimlet_message_url]를 사용하여 응답을 반환합니다.

> [!NOTE]
> TwiML이 웹 서비스에 사용하도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어 [http://twimlets.com/message][twimlet_message_url]를 클릭하면 빈 &lt;Response&gt; 요소가 표시됩니다. 또 다른 예로, [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World)를 클릭하면 &lt;Say&gt; 요소를 포함하는 &lt;Response&gt;요소가 표시됩니다.
>
>

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 URL 사이트를 만들 수 있습니다. HTTP 응답을 반환하는 사이트는 어떤 언어로든 만들 수 있습니다. 이 항목에서는 ASP.NET 제네릭 처리기에서 URL을 호스트한다고 가정합니다.

다음 ASP.NET 처리기는 호출 시 **Hello World** 라고 말하는 TwiML 응답을 만듭니다.

    using System.Text;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {
            public void ProcessRequest(HttpContext context)
            {
                const string twiMLResponse =
                    "<Response><Say>Hello World.</Say></Response>";
                
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = Encoding.UTF8;
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }
    
위의 예제와 같이 TwiML 응답은 단지 XML 문서입니다. Twilio.TwiML 라이브러리에는 TwiML을 자동으로 생성하는 클래스가 포함되어 있습니다. 아래 예제에서는 위와 동일한 응답을 생성하지만 **VoiceResponse** 클래스를 사용합니다.

    using System.Web;
    using Twilio.TwiML;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new VoiceResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

TwiML에 대한 자세한 내용은 [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)(영문)을 참조하십시오.

TwiML 응답을 제공하는 방법을 설정한 후에는 **CallResource.Create** 메서드로 URL을 전달할 수 있습니다. 예를 들어 MyTwiML이라는 웹 응용 프로그램을 Azure 클라우드 서비스에 배포했으며 ASP.NET 처리기의 이름이 mytwiml.ashx인 경우 다음 코드 샘플과 같이 URL을 **CallResource.Create**로 전달할 수 있습니다.

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Azure에서 ASP.NET과 함께 Twilio 사용에 대한 자세한 내용은 [Azure의 웹 역할에서 Twilio를 사용하여 전화를 거는 방법][howto_phonecall_dotnet]을 참조하십시오.

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
