---
title: C를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: C를 사용하여 Azure Event Hubs로 이벤트 전송
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 12/4/2017
ms.author: sethm
ms.openlocfilehash: 430135113f4b26db6690feb6bc6ddcc0700c6d11
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>C를 사용하여 Azure Event Hubs로 이벤트 전송

## <a name="introduction"></a>소개
Event Hubs는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [Event Hubs 개요](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-overview)를 참조하세요.

이 자습서에서는 C 언어의 콘솔 응용 프로그램을 사용하여 이벤트를 이벤트 허브로 전송하는 방법을 설명합니다. 이벤트 수신에 대해 알아보려면 왼쪽의 목차에서 해당 수신 언어를 클릭합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* C 개발 환경. 이 자습서에서는 Ubuntu 14.04를 사용하는 Azure Linux VM에 gcc 스택이 있다고 가정합니다.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="send-messages-to-event-hubs"></a>Event Hubs에 메시지 보내기
이 섹션에서는 이벤트 허브로 이벤트를 보내는 C 앱을 작성하는 방법을 보여 줍니다. 코드는 [Apache Qpid 프로젝트](http://qpid.apache.org/)의 Proton AMQP 라이브러리를 사용합니다. 이 방법은 [이 샘플](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)에 나온 C에서 AMQP와 함께 Service Bus 큐와 토픽을 사용하는 방법과 유사합니다. 자세한 내용은 [Qpid Proton 설명서](http://qpid.apache.org/proton/index.html)를 참조하세요.

1. [Qpid AMQP Messenger 페이지](https://qpid.apache.org/proton/messenger.html)에서 환경에 맞는 Qpid Proton 설치에 대한 지침을 따릅니다.
2. Proton 라이브러리를 컴파일하려면 다음 패키지를 설치합니다.
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. [Qpid Proton 라이브러리](http://qpid.apache.org/proton/index.html)(영문)를 다운로드하고 다음과 같이 압축을 풉니다.
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. 빌드 디렉터리를 만들고 컴파일하고 설치합니다.
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. 작업 디렉터리에서 다음 코드가 포함된 **sender.c** 라는 새 파일을 만듭니다. SAS 키/이름, 이벤트 허브 이름 및 네임스페이스 값을 대체해야 합니다. 또한 이전에 만든 **SendRule** 도 URL로 인코드된 버전의 키로 대체합니다. [여기](http://www.w3schools.com/tags/ref_urlencode.asp)(영문)에서 URL로 인코드할 수 있습니다.
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. 파일을 컴파일합니다. **gcc**를 사용하면 다음과 같습니다.
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > 이 코드에서는 발신 창 1을 사용하여 메시지가 최대한 빨리 출력되도록 합니다. 응용 프로그램에서는 메시지를 일괄 처리하여 처리량을 늘리는 것이 좋습니다. 이 환경과 다른 환경 및 바인딩이 제공되는 플랫폼(현재 Perl, PHP, Python 및 Ruby)에서 Qpid Proton 라이브러리를 사용하는 방법에 대한 자세한 내용은 [Qpid AMQP Messenger 페이지](https://qpid.apache.org/proton/messenger.html)를 참조하세요.


## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
