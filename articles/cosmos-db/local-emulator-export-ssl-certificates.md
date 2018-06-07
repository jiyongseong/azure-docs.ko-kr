---
title: Azure Cosmos DB 에뮬레이터 인증서 내보내기 | Microsoft Docs
description: Windows 인증서 저장소를 사용하지 않는 언어와 런타임으로 개발할 때는 SSL 인증서를 내보내고 관리해야 합니다. 이 게시물에서는 단계별 지침을 제공합니다.
services: cosmos-db
documentationcenter: ''
keywords: Azure Cosmos DB 에뮬레이터
author: voellm
manager: kfile
editor: ''
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87d453cd544b3e913209f50e4e08b77282efab39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java, Python 및 Node.js에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기

[**에뮬레이터 다운로드**](https://aka.ms/cosmosdb-emulator)

Azure Cosmos DB 에뮬레이터는 SSL 연결 사용을 포함하여 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 게시물에서는 사용자 고유의 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)를 사용하는 Java, [소켓 래퍼](https://docs.python.org/2/library/ssl.html)을 사용하는 Python, [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)을 사용하는 Node.js처럼 Windows 인증서 저장소와 통합되지 않는 언어와 런타임에 사용할 SSL 인증서를 내보내는 방법을 보여 줍니다. 에뮬레이터에 대한 자세한 내용은 [Azure Cosmos DB 에뮬레이터를 사용하여 개발 및 테스트](./local-emulator.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 인증서 순환
> * SSL 인증서 내보내기
> * Java, Python 및 Node.js에서 인증서를 사용하는 방법 알아보기

## <a name="certification-rotation"></a>인증 회전

Azure Cosmos DB 로컬 에뮬레이터의 인증서는 에뮬레이터를 처음 실행할 때 생성되며, 두 개의 인증서가 있습니다. 하나는 로컬 에뮬레이터에 연결하는 데 사용되고, 다른 하나는 에뮬레이터 내에서 비밀을 관리하는 데 사용됩니다. 내보내려는 인증서는 "DocumentDBEmulatorCertificate"라는 이름의 연결 인증서입니다.

Windows 트레이에서 실행 중인 Azure Cosmos DB 에뮬레이터에서 아래와 같이 **데이터 다시 설정**을 클릭하여 두 인증서를 모두 다시 생성할 수 있습니다. 인증서를 다시 생성하여 Java 인증서 저장소에 설치했거나 다른 곳에서 사용한 경우에는 이 인증서를 업데이트해야 합니다. 그렇지 않으면 응용 프로그램이 더 이상 로컬 에뮬레이터에 연결되지 않습니다.

![Azure Cosmos DB 로컬 에뮬레이터 데이터 다시 설정](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Azure Cosmos DB SSL 인증서를 내보내는 방법

1. certlm.msc를 실행하여 Windows 인증서 관리자를 시작하고, 개인 -> 인증서 폴더로 이동한 다음 **DocumentDbEmulatorCertificate**라는 친숙한 이름의 인증서를 엽니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 1단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. **세부 정보**를 클릭한 다음 **확인**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 2단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. **파일에 복사...**를 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 3단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 4단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. **아니요, 개인 키를 내보내지 않습니다.**를 클릭한 후 **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 5단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. **Base-64 encoded X.509 (.CER)**를 클릭한 후 **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 6단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. 인증서에 이름을 지정합니다. 여기서는 **documentdbemulatorcert**이며, **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 7단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. **Finish**를 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 8단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Java에서 인증서를 사용하는 방법

Java 클라이언트를 사용하는 Java 응용 프로그램 또는 MongoDB 응용 프로그램을 실행하는 경우 "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>" 플래그를 전달하는 것보다 Java 기본 인증서 저장소에 인증서를 설치하는 것이 더 쉽습니다. 예를 들어 포함된 [Java Demo 응용 프로그램](https://localhost:8081/_explorer/index.html)은 기본 인증서 저장소에 따라 다릅니다.

X.509 인증서를 Java 기본 인증서 저장소로 가져 오려면 [Java CA 인증서 저장소에 인증서 추가](https://docs.microsoft.com/azure/java-add-certificate-ca-store)의 지침을 따릅니다. keytool을 실행할 때는 %JAVA_HOME% 디렉터리에서 작업하게 됩니다.

"CosmosDBEmulatorCertificate" SSL 인증서가 설치되면 응용 프로그램에서 로컬 Azure Cosmos DB 에뮬레이터에 연결하여 사용할 수 있어야 합니다. 문제가 계속 발생하면 [SSL/TLS 연결 디버그](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 문서의 지침을 따릅니다. 인증서가 %JAVA_HOME%/jre/lib/security/cacerts 저장소에 설치되지 않은 것 같습니다. 예를 들어 설치된 Java 버전이 여러 개 있는 경우 응용 프로그램이 업데이트한 것과는 다른 cacerts 저장소를 사용하고 있을 수도 있습니다.

## <a name="how-to-use-the-certificate-in-python"></a>Python에서 인증서를 사용하는 방법

기본적으로 SQL API용 [Python SDK(버전 2.0.0 이상)](sql-api-sdk-python.md)는 로컬 에뮬레이터에 연결할 때 SSL 인증서를 사용하지 않습니다. 그러나 SSL 유효성 검사를 사용하려면 [Python 소켓 래퍼 ](https://docs.python.org/2/library/ssl.html) 설명서의 예제를 따르면 됩니다.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js에서 인증서를 사용하는 방법

기본적으로 SQL API용 [Node.js SDK(버전 1.10.1 이상)](sql-api-sdk-node.md)는 로컬 에뮬레이터에 연결할 때 SSL 인증서를 사용하지 않습니다. 그러나 SSL 유효성 검사를 사용하려면 [Node.js 설명서](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)의 예제를 따르면 됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 인증서를 순환했습니다.
> * SSL 인증서를 내보냈습니다.
> * Java, Python 및 Node.js에서 인증서를 사용하는 방법을 알아보았습니다.

이제 Azure Cosmos DB 입력 바인딩 자습서를 통해 Azure Functions HTTP 트리거를 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB 입력으로 Azure Function 만들기](tutorial-functions-http-trigger.md) 
