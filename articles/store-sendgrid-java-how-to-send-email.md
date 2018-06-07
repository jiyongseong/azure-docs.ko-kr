---
title: SendGrid 메일 서비스를 사용하는 방법(Java) | Microsoft Docs
description: Azure에서 SendGrid 메일 서비스를 사용하여 메일을 보내는 방법을 알아봅니다. 코드 샘플은 Java로 작성되었습니다.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: cadd771a516270faf23f18a36f54fa9a1541e6d5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364902"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Java의 SendGrid를 사용하여 메일을 보내는 방법
이 가이드에서는 Azure에서 SendGrid 전자 메일 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었습니다. **전자 메일 생성**, **전자 메일 보내기**, **첨부 파일 추가**, **필터 사용**, **속성 업데이트** 등의 시나리오를 다룹니다. SendGrid 및 전자 메일 보내기에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 전자 메일 서비스 정의
SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한 [트랜잭션 전자 메일 발송], 확장성 및 실시간 분석을 제공하는 [클라우드 기반 전자 메일 서비스]입니다. 일반적인 SendGrid 사용 시나리오는 다음과 같습니다.

* 고객에게 확인 메일 자동으로 보내기
* 월간 전자 전단 및 판촉 행사를 고객에게 보내기 위한 분산 목록 관리
* 차단된 전자 메일, 고객 응답 같은 항목의 실시간 메트릭 수집
* 경향을 식별하는 데 도움이 되도록 보고서 생성
* 고객 문의 전달
* 응용 프로그램의 전자 메일 알림

자세한 내용은 <http://sendgrid.com>을 참조하세요.

## <a name="create-a-sendgrid-account"></a>SendGrid 계정 만들기
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>방법: javax.mail 라이브러리 사용
javax.mail 라이브러리를 가져오고(예: <http://www.oracle.com/technetwork/java/javamail>에서) 코드로 가져옵니다. 상위 수준에서, javax.mail 라이브러리를 사용하여 SMTP를 통해 전자 메일을 보내는 프로세스는 다음과 같습니다.

1. SMTP 서버를 포함한 SMTP 값을 지정합니다. SendGrid는 smtp.sendgrid.net입니다.

```
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. *javax.mail.Authenticator* 클래스를 확장하고 *getPasswordAuthentication* 메서드 구현에서 SendGrid 사용자 이름 및 암호를 반환합니다.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. *javax.mail.Session* 개체를 통해 인증된 전자 메일 세션을 만듭니다.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. 메시지를 만들고 **받는 사람**, **보낸 사람**, **제목** 및 내용 값을 할당합니다. [방법: 전자 메일 만들기](#how-to-create-an-email) 섹션에 나와 있습니다.
4. *javax.mail.Transport* 개체를 통해 메시지를 보냅니다. [방법: 전자 메일 보내기][#how-to-send-an-email] 섹션에 나와 있습니다.

## <a name="how-to-create-an-email"></a>방법: 전자 메일 만들기
다음은 메일 값을 지정하는 방법을 보여 줍니다.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>방법: 전자 메일 보내기
다음은 메일을 보내는 방법을 보여 줍니다.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>방법: 첨부 파일 추가
다음 코드는 첨부 파일을 추가하는 방법을 보여 줍니다.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정
SendGrid는 *필터*사용을 통해 추가 전자 메일 기능을 제공합니다. 클릭 추적, Google 분석, 구독 추적 등을 사용하도록 설정하는 것과 같이 특정 기능을 사용하도록 설정하기 위해 전자 메일 메시지에 추가할 수 있는 설정입니다. 전체 필터 목록은 [필터 설정][Filter Settings](영문)을 참조하십시오.

* 다음은 보내는 전자 메일 아래쪽에 HTML 텍스트가 표시되도록 하는 바닥글 필터 삽입 방법을 보여 줍니다.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* 필터에 대한 또 다른 예제는 클릭 추적입니다. 전자 메일 텍스트에 다음과 같은 하이퍼링크가 포함되어 있고 클릭률을 추적하려 한다고 가정하겠습니다.

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* 클릭 추적을 사용하도록 설정하려면 다음 코드를 사용합니다.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>방법: 전자 메일 속성 업데이트
일부 이메일 속성은 **set Property**를 사용하여 덮어쓰거나 **add Property**를 사용하여 추가할 수 있습니다.

예를 들어 **ReplyTo** 주소를 지정하려면 다음을 사용하십시오.

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

**참조** 받는 사람을 추가하려면 다음을 사용합니다.

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>방법: 추가 SendGrid 서비스 사용
SendGrid는 Azure 응용 프로그램에서 추가 SendGrid 기능을 활용하는 데 사용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [SendGrid API 설명서][SendGrid API documentation](영문)를 참조하십시오.

## <a name="next-steps"></a>다음 단계
SendGrid 메일 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가세요.

* Azure 배포에서 SendGrid를 사용하는 방법을 보여 주는 샘플: [Azure 배포에서 Java의 SendGrid를 사용하여 전자 메일을 보내는 방법](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API 설명서: <https://sendgrid.com/docs/API_Reference/index.html>
* Azure 고객을 위한 SendGrid 특별 제공: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[클라우드 기반 전자 메일 서비스]: https://sendgrid.com/email-solutions
[트랜잭션 전자 메일 발송]: https://sendgrid.com/transactional-email
