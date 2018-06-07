---
title: SendGrid 메일 서비스를 사용하는 방법(Node.js) | Microsoft Docs
description: Azure에서 SendGrid 메일 서비스를 사용하여 메일을 보내는 방법을 알아봅니다. 코드 샘플은 Node.js API를 사용하여 작성되었습니다.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Node.js에서 SendGrid를 사용하여 메일을 보내는 방법
이 가이드에서는 Azure에서 SendGrid 전자 메일 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. **전자 메일 생성**, **전자 메일 보내기**, **첨부 파일 추가**, **필터 사용**, **속성 업데이트** 등의 시나리오를 다룹니다. SendGrid 및 전자 메일 보내기에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 전자 메일 서비스 정의
SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한 [트랜잭션 전자 메일 발송], 확장성 및 실시간 분석을 제공하는 [클라우드 기반 전자 메일 서비스]입니다. 일반적인 SendGrid 사용 시나리오는 다음과 같습니다.

* 고객에게 확인 메일 자동으로 보내기
* 월간 전자 전단 및 판촉 행사를 고객에게 보내기 위한 분산 목록 관리
* 차단된 전자 메일, 고객 응답 같은 항목의 실시간 메트릭 수집
* 경향을 식별하는 데 도움이 되도록 보고서 생성
* 고객 문의 전달
* 응용 프로그램의 전자 메일 알림

자세한 내용은 [https://sendgrid.com](https://sendgrid.com)을 참조하세요.

## <a name="create-a-sendgrid-account"></a>SendGrid 계정 만들기
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>SendGrid Node.js 모듈 참조
Node.js용 SendGrid 모듈은 다음 명령을 사용하여 NPM(Node Package Manager)을 통해 설치할 수 있습니다.

    npm install sendgrid

설치 후에는 다음 코드를 사용하여 응용 프로그램에서 이 모듈을 요청할 수 있습니다.

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

SendGrid 모듈은 **SendGrid** 및 **Email** 함수를 내보냅니다.
**SendGrid**는 Web API를 통해 전자 메일을 보내는 역할을 맡고, **Email**은 전자 메일 메시지를 캡슐화합니다.

## <a name="how-to-create-an-email"></a>방법: 전자 메일 만들기
SendGrid 모듈을 사용하여 전자 메일 메시지를 만들려면 먼저 Email 함수로 전자 메일 메시지를 만든 후, SendGrid 함수를 사용하여 이 메시지를 보내면 됩니다. 다음 예제는 Email 함수를 사용하여 새 메시지를 만드는 과정을 보여 줍니다.

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

또한 html 속성을 설정하여 HTML 메시지를 지원하는 클라이언트를 위해 HTML 메시지를 지정할 수도 있습니다. 예: 

    html: This is a sample <b>HTML<b> email message.

텍스트 속성과 html 속성을 모두 설정하면 HTML 메시지를 지원할 수 없는 클라이언트에서 텍스트 콘텐츠로 안정적으로 대체됩니다.

Email 함수에서 지원하는 모든 속성에 대한 자세한 내용은 [sendgrid-nodejs][sendgrid-nodejs]를 참조하세요.

## <a name="how-to-send-an-email"></a>방법: 전자 메일 보내기
Email 함수를 사용하여 전자 메일 메시지를 만든 후에는 SendGrid에서 제공하는 Web API를 사용하여 해당 메시지를 보낼 수 있습니다. 

### <a name="web-api"></a>Web API
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> 위 예제에서는 email 개체 및 콜백 함수를 전달하고 있지만, email 속성을 직접 지정하여 send 함수를 바로 호출할 수도 있습니다. 예:   
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>방법: 첨부 파일 추가
**files** 속성에서 파일 이름 및 경로를 지정하여 메시지에 첨부 파일을 추가할 수 있습니다. 다음 예제에서는 첨부 파일을 보내는 방법을 보여 줍니다.

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> **files** 속성을 사용할 경우, 해당 파일은 [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile)을 통해 액세스할 수 있어야 합니다. 첨부하려는 파일이 Blob 컨테이너 등의 Azure Storage에서 호스트되는 경우, 먼저 파일을 로컬 저장소 또는 Azure 드라이브에 복사해야 **files** 속성을 사용하여 해당 파일을 첨부 파일로 보낼 수 있습니다.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>방법: 필터를 사용하여 바닥글 및 추적을 사용하도록 설정
SendGrid는 필터 사용을 통해 추가 전자 메일 기능을 제공합니다. 클릭 추적, Google 분석, 구독 추적 등을 사용하도록 설정하는 것과 같이 특정 기능을 사용하도록 설정하기 위해 전자 메일 메시지에 추가할 수 있는 설정입니다. 전체 필터 목록은 [필터 설정][Filter Settings](영문)을 참조하십시오.

필터는 **filters** 속성을 사용하여 메시지에 적용할 수 있습니다.
각 필터는 필터별 설정을 포함하는 해시에 의해 지정됩니다.
다음 예에서는 바닥글 및 클릭 추적 필터를 보여 줍니다.

### <a name="footer"></a>바닥글
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### <a name="click-tracking"></a>클릭 추적
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## <a name="how-to-update-email-properties"></a>방법: 전자 메일 속성 업데이트
일부 이메일 속성은 **set*Property***를 사용하여 덮어쓰거나 **add*Property***를 사용하여 추가할 수 있습니다. 예를 들어 다음을 사용하여 받는 사람을 더 추가할 수 있습니다.

    email.addTo('jeff@contoso.com');

또는 다음을 사용하여 필터를 설정할 수 있습니다.

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

자세한 내용은 [sendgrid-nodejs][sendgrid-nodejs]를 참조하세요.

## <a name="how-to-use-additional-sendgrid-services"></a>방법: 추가 SendGrid 서비스 사용
SendGrid는 Azure 응용 프로그램에서 추가 SendGrid 기능을 활용하는 데 사용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [SendGrid API 설명서][SendGrid API documentation](영문)를 참조하십시오.

## <a name="next-steps"></a>다음 단계
SendGrid 전자 메일 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* SendGrid Node.js 모듈 리포지토리: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API 설명서: <https://sendgrid.com/docs>
* Azure 고객을 위한 SendGrid 특별 제공: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[클라우드 기반 전자 메일 서비스]: https://sendgrid.com/email-solutions
[트랜잭션 전자 메일 발송]: https://sendgrid.com/transactional-email
