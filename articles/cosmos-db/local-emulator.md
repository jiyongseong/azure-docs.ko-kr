---
title: Azure Cosmos DB 에뮬레이터를 사용하여 로컬로 개발 | Microsoft Docs
description: Azure Cosmos DB 에뮬레이터를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 응용 프로그램을 개발하고 테스트할 수 있습니다.
services: cosmos-db
documentationcenter: ''
keywords: Azure Cosmos DB 에뮬레이터
author: David-Noble-at-work
manager: kfile
editor: ''
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: danoble
ms.openlocfilehash: 109bd61963b918f2a20c48a5bf7bd89dc353db96
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>로컬 개발 및 테스트에 Azure Cosmos DB 에뮬레이터 사용

<table>
<tr>
  <td><strong>이진 파일</strong></td>
  <td>[MSI 다운로드](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker 허브](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker 원본</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 응용 프로그램을 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터에서 응용 프로그램이 작동하는 방식에 만족하는 경우 Azure Cosmos DB 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

> [!NOTE]
> 현재 에뮬레이터의 데이터 탐색기는 SQL API 컬렉션 및 MongoDB 컬렉션만 완전히 지원합니다. 테이블, 그래프 및 Cassandra 컨테이너는 완벽히 지원되지는 않습니다. 

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * 에뮬레이터 설치
> * 요청 인증
> * 에뮬레이터에서 데이터 탐색기 사용
> * SSL 인증서 내보내기
> * 명령줄에서 에뮬레이터 호출
> * Windows용 Docker에서 에뮬레이터 실행
> * 추적 파일 수집
> * 문제 해결

Kirill Gavrylyuk가 Azure Cosmos DB 에뮬레이터를 시작하는 방법을 보여 주는 다음 비디오를 시청하는 것으로 시작하는 것이 좋습니다. 이 비디오는 DocumentDB 에뮬레이터를 참조하지만 비디오를 탭한 후에는 도구 자체의 이름이 Azure Cosmos DB 에뮬레이터로 바뀝니다. 비디오의 모든 정보는 Azure Cosmos DB 에뮬레이터에 대해 정확합니다. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>에뮬레이터의 작동 원리
Azure Cosmos DB 에뮬레이터는 신뢰도 있는 Azure Cosmos DB 서비스의 에뮬레이션을 제공합니다. JSON 문서 만들기 및 쿼리, 컬렉션 프로비전 및 확장, 저장 프로시저 및 트리거 실행을 비롯하여 Azure Cosmos DB으로 동일한 기능을 지원합니다. Azure Cosmos DB 에뮬레이터를 사용하여 응용 프로그램을 개발 및 테스트하고 Azure Cosmos DB에 대한 연결 끝점에 대한 단일 구성을 변경하여 글로벌 규모로 Azure에 배포할 수 있습니다.

실제 Azure Cosmos DB 서비스의 충실도 높은 로컬 에뮬레이션을 만들었지만 Azure Cosmos DB 에뮬레이터의 구현은 서비스의 구현과 다릅니다. 예를 들어 Azure Cosmos DB 에뮬레이터는 로컬 파일 시스템(지속성) 및 HTTPS 프로토콜 스택(연결성)과 같은 표준 OS 구성 요소를 사용합니다. 따라서 전역 복제, 한 자리 밀리초 읽기/쓰기 대기 시간, 튜닝 가능한 일관성 수준 등 Azure 인프라를 기반으로 하는 일부 기능은 Azure Cosmos DB 에뮬레이터를 통해 사용할 수 없습니다.

## <a name="differences-between-the-emulator-and-the-service"></a>에뮬레이터와 서비스 간 차이 
Azure Cosmos DB 에뮬레이터는 로컬 개발자 워크스테이션에서 실행되는 에뮬레이트된 환경을 제공하기 때문에 클라우드의 Azure Cosmos DB 계정과 기능 면에서 몇 가지 차이가 있습니다.

* Azure Cosmos DB 에뮬레이터는 단일 고정 계정과 알려진 마스터 키만 지원합니다.  Azure Cosmos DB 에뮬레이터에서는 키를 다시 생성할 수 없습니다.
* Azure Cosmos DB 에뮬레이터는 확장 가능한 서비스가 아니며 많은 컬렉션을 지원하지 않습니다.
* Azure Cosmos DB 에뮬레이터는 여러 [Azure Cosmos DB 일관성 수준](consistency-levels.md)을 시뮬레이션하지 않습니다.
* Azure Cosmos DB 에뮬레이터는 [다중 지역 복제](distribute-data-globally.md)를 시뮬레이션하지 않습니다.
* Azure Cosmos DB 에뮬레이터는 Azure Cosmos DB 서비스에서 사용할 수 있는 서비스 할당량 재정의(예: 문서 크기 제한, 향상된 분할된 컬렉션 저장소)를 지원하지 않습니다.
* Azure Cosmos DB 에뮬레이터의 복사본은 최신 Azure Cosmos DB 서비스가 포함된 가장 최근의 변경 사항이 적용된 최신 에뮬레이터가 아닐 수 있으므로 [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner)를 실행하여 응용 프로그램에 요구되는 프로덕션 처리량(RU)을 정확하게 예측해야 합니다.

## <a name="system-requirements"></a>시스템 요구 사항
Azure Cosmos DB 에뮬레이터에는 다음과 같은 하드웨어 및 소프트웨어 요구 사항이 있습니다.

* 소프트웨어 요구 사항
  * Windows Server 2012 R2, Windows Server 2016 또는 Windows 10
*   최소 하드웨어 요구 사항
  * 2GB RAM
  * 10GB의 하드 디스크 여유 공간

## <a name="installation"></a>설치
[Microsoft 다운로드 센터](https://aka.ms/cosmosdb-emulator)에서 Azure Cosmos DB 에뮬레이터를 다운로드하여 설치하거나 Windows용 Docker에서 에뮬레이터를 실행할 수 있습니다. Windows용 Docker의 에뮬레이터 사용에 대한 지침을 보려면 [Docker에서 실행](#running-on-docker)을 참조하세요. 

> [!NOTE]
> Azure Cosmos DB 에뮬레이터를 설치, 구성 및 실행하려면 컴퓨터에 대한 관리 권한이 있어야 합니다.

## <a name="running-on-windows"></a>Windows에서 실행

Azure Cosmos DB 에뮬레이터를 시작하려면 시작 단추를 선택하거나 Windows 키를 누릅니다. **Azure Cosmos DB 에뮬레이터** 입력을 시작하고 응용 프로그램 목록에서 해당 에뮬레이터를 선택합니다. 

![시작 단추를 선택하거나 Windows 키를 누르고 **Azure Cosmos DB 에뮬레이터** 입력을 시작한 후 응용 프로그램 목록에서 해당 에뮬레이터를 선택합니다.](./media/local-emulator/database-local-emulator-start.png)

에뮬레이터를 실행 하는 경우 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다. ![Azure Cosmos DB 로컬 에뮬레이터 작업 표시줄 알림](./media/local-emulator/database-local-emulator-taskbar.png)

기본적으로 Azure Cosmos DB 에뮬레이터는 포트 8081에서 수신 대기하는 로컬 컴퓨터("localhost")에서 실행됩니다.

Azure Cosmos DB 에뮬레이터는 기본적으로 `C:\Program Files\Azure Cosmos DB Emulator` 디렉터리에 설치됩니다. 명령줄에서 에뮬레이터를 시작 및 중지할 수도 있습니다. 자세한 내용은 [명령줄 도구 참조](#command-line)를 참조하세요.

## <a name="start-data-explorer"></a>데이터 탐색기 시작

Azure Cosmos DB 에뮬레이터를 시작하면 브라우저에서 Azure Cosmos DB 데이터 탐색기가 자동으로 열립니다. 주소가 [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html)으로 표시됩니다. 탐색기를 닫고 나중에 다시 열고 싶은 경우, 브라우저에서 URL을 열거나 아래와 같이 Windows 트레이 아이콘의 Azure Cosmos DB 에뮬레이터에서 실행할 수 있습니다.

![Azure Cosmos DB의 로컬 에뮬레이터 데이터 탐색기 시작 관리자](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>업데이트 확인
데이터 탐색기에 다운로드할 수 있는 새 업데이트가 있는지 표시됩니다. 

> [!NOTE]
> Azure Cosmos DB 에뮬레이터의 특정 버전에서 만든 데이터에 다른 버전에서 액세스하지 못할 수도 있습니다. 데이터를 장기간 보존하려면 Azure Cosmos DB 에뮬레이터 대신 Azure Cosmos DB 계정에 저장하는 것이 좋습니다. 

## <a name="authenticating-requests"></a>요청 인증
클라우드의 Azure Cosmos DB와 마찬가지로 Azure Cosmos DB 에뮬레이터에 대한 모든 요청을 인증해야 합니다. Azure Cosmos DB 에뮬레이터는 단일 고정 계정과 마스터 키 인증에 대해 알려진 인증 키를 지원합니다. Azure Cosmos DB 에뮬레이터에서 사용할 수 있는 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Azure Cosmos DB 에뮬레이터에서 지원하는 마스터 키는 에뮬레이터 전용입니다. Azure Cosmos DB 에뮬레이터에서 프로덕션 Azure Cosmos DB 계정 및 키를 사용할 수 없습니다. 

> [!NOTE] 
> /Key 옵션과 함께 에뮬레이터를 시작한 경우 “C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==” 대신 생성된 키를 사용합니다.

또한 Azure Cosmos DB 서비스와 마찬가지로 Azure Cosmos DB 에뮬레이터는 SSL을 통한 보안 통신만 지원합니다.

## <a name="running-on-a-local-network"></a>로컬 네트워크에서 실행

로컬 네트워크에서 에뮬레이터를 실행할 수 있습니다. 네트워크 액세스를 활성화하려면 [명령줄](#command-line-syntax)에 /AllowNetworkAccess 옵션을 지정합니다. 또한 /Key=key_string 또는 /KeyFile=file_name/Key를 지정해야 합니다. /GenKeyFile=file_name을 사용하여 미리 설정된 임의 키로 파일을 생성할 수 있습니다.  그런 다음 /KeyFile=file_name 또는 /Key=contents_of_file로 전달할 수 있습니다.

처음에 네트워크 액세스를 사용하도록 설정하려면 사용자는 에뮬레이터를 종료하고 에뮬레이터의 데이터 디렉터리(C:\Users\user_name\AppData\Local\CosmosDBEmulator)를 삭제해야 합니다.

## <a name="developing-with-the-emulator"></a>에뮬레이터를 사용한 개발
Azure Cosmos DB 에뮬레이터를 데스크톱에서 실행하는 경우 지원되는 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 또는 [Azure Cosmos DB REST API](/rest/api/cosmos-db/)를 사용하여 에뮬레이터와 상호 작용할 수 있습니다. Azure Cosmos DB 에뮬레이터에는 코드를 작성하지 않고도 SQL 및 MongoDB API 컬렉션을 만들고 문서를 확인 및 편집할 수 있는 기본 제공 데이터 탐색기도 포함되어 있습니다.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

[MongoDB에 대한 Azure Cosmos DB 프로토콜 지원](mongodb-introduction.md)을 사용할 경우에는 다음 연결 문자열을 사용하세요.

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

[Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio)와 같은 기존 도구를 사용하여 Azure Cosmos DB 에뮬레이터에 연결할 수 있습니다. 또한 [Azure Cosmos DB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool)를 사용하여 Azure Cosmos DB 에뮬레이터와 Azure Cosmos DB 서비스 간에 데이터를 마이그레이션할 수 있습니다.

> [!NOTE] 
> /Key 옵션과 함께 에뮬레이터를 시작한 경우 “C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==” 대신 생성된 키를 사용합니다.

기본적으로 Azure Cosmos DB 에뮬레이터를 사용하여 최대 25개의 단일 파티션의 컬렉션 또는 분할된 컬렉션 하나를 만들 수 있습니다. 이 값을 변경하는 방법에 대한 자세한 내용은 [PartitionCount 값 설정](#set-partitioncount)을 참조하세요.

## <a name="export-the-ssl-certificate"></a>SSL 인증서 내보내기

.NET 언어 및 런타임은 Windows 인증서 저장소를 사용하여 Azure Cosmos DB 로컬 에뮬레이터에 안전하게 연결합니다. 다른 언어의 경우 해당 언어만의 인증서 관리 및 사용 방법이 있습니다. Java는 자체의 고유 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)를 사용하고 Python은 [소켓 래퍼](https://docs.python.org/2/library/ssl.html)를 사용합니다.

Windows 인증서 저장소와 통합되지 않는 런타임 및 언어와 함께 사용할 인증서를 가져오기 위해 Windows 인증서 관리자를 사용하여 인증서를 내보내야 합니다. certlm.msc를 실행하거나 [Azure Cosmos DB 에뮬레이터 인증서 내보내기](./local-emulator-export-ssl-certificates.md)의 단계별 지침을 수행하여 이 작업을 시작할 수 있습니다. 인증서 관리자가 실행되면 아래에 표시된 대로 개인 인증서를 열고 BASE-64 인코딩 X.509(.cer) 파일로 "DocumentDBEmulatorCertificate"라는 이름의 인증서를 내보냅니다.

![Azure Cosmos DB 로컬 에뮬레이터 SSL 인증서](./media/local-emulator/database-local-emulator-ssl_certificate.png)

[Java CA 인증서 저장소에 인증서 추가](https://docs.microsoft.com/azure/java-add-certificate-ca-store)의 지침을 따라 X.509 인증서를 Java 인증서 저장소로 가져올 수 있습니다. 인증서를 인증서 저장소에 가져온 후 Java 및 MongoDB 응용 프로그램을 Azure Cosmos DB 에뮬레이터에 연결할 수 있습니다.

Python 및 Node.js SDK에서 에뮬레이터에 연결하면 SSL 확인이 비활성화됩니다.

## <a id="command-line"></a>명령줄 도구 참조
설치 위치에서 명령줄을 사용하여 에뮬레이터를 시작 및 중지하고, 옵션을 구성하고, 다른 작업을 수행할 수 있습니다.

### <a name="command-line-syntax"></a>명령줄 구문

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

옵션 목록을 보려면 명령 프롬프트에 `CosmosDB.Emulator.exe /?` 을(를) 입력합니다.

<table>
<tr>
  <td><strong>옵션</strong></td>
  <td><strong>설명</strong></td>
  <td><strong>명령</strong></td>
  <td><strong>인수</strong></td>
</tr>
<tr>
  <td>[인수 없음]</td>
  <td>기본 설정으로 Azure Cosmos DB 에뮬레이터를 시작합니다.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[도움말]</td>
  <td>지원되는 명령줄 인수 목록을 표시합니다.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Azure Cosmos DB Emulator의 상태를 가져 옵니다. 상태는 종료 코드에 의해 표시 됩니다. 1 = 시작, 2 = 실행, 3 = 정지. 부정적인 종료 코드는 오류가 발생 했음을 나타냅니다. 어떤 출력도 생성되지 않습니다.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Shutdown</td>
  <td>Azure Cosmos DB 에뮬레이터를 종료합니다.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>데이터 파일을 저장할 경로를 지정합니다. 기본값은 %LocalAppdata%\CosmosDBEmulator입니다.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: 액세스 가능한 경로</td>
</tr>
<tr>
  <td>포트</td>
  <td>에뮬레이터에 사용할 포트 번호를 지정합니다.  기본값은 8081입니다.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: 단일 포트 번호</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>MongoDB 호환성 API에 사용할 포트 번호를 지정합니다. 기본값은 10255입니다.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: 단일 포트 번호</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>직접 연결에 사용할 포트를 지정합니다. 기본값은 10251,10252,10253,10254입니다.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: 쉼표로 구분된 4개의 포트 목록</td>
</tr>
<tr>
  <td>키</td>
  <td>에뮬레이터에 대한 권한 부여 키입니다. 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>요청 속도 제한 동작을 사용하도록 지정합니다.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>요청 속도 제한 동작을 사용하지 않도록 지정합니다.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>에뮬레이터 사용자 인터페이스를 표시하지 않습니다.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>시작 시 데이터 탐색기를 표시하지 않습니다.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>분할된 컬렉션의 최대 수를 지정합니다. 자세한 내용은 [컬렉션 수 변경](#set-partitioncount)을 참조하세요.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: 허용되는 단일 파티션 컬렉션의 최대 수입니다. 기본값은 25입니다. 허용되는 최대값은 250입니다.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>분할된 컬렉션에 대한 기본 파티션 수를 지정합니다.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; 기본값은 25입니다.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>네트워크를 통해 에뮬레이터에 액세스할 수 있도록 합니다. /Key=&lt;key_string&gt; 또는 /KeyFile=&lt;file_name&gt;을 전달하여 네트워크 액세스를 활성화해야 합니다.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>또는<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>/AllowNetworkAccess가 사용되는 경우 방화벽 규칙을 조정하지 마십시오.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>새 인증 키를 생성하고 지정된 파일에 저장합니다. 생성된 키를 /Key 또는 /KeyFile 옵션과 함께 사용할 수 있습니다.</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;path to key file&gt;</td>
  <td></td>
</tr>
<tr>
  <td>일관성</td>
  <td>계정의 기본 일관성 수준을 설정합니다.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;일관성&gt;: 값은 Session, Strong, Eventual 또는 BoundedStaleness의 [일관성 수준](consistency-levels.md) 중 하나여야 합니다.  기본값은 Session입니다.</td>
</tr>
<tr>
  <td>?</td>
  <td>도움말 메시지를 표시합니다.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>컬렉션 수 변경

기본적으로 Azure Cosmos DB 에뮬레이터를 사용하여 최대 25개의 단일 파티션의 컬렉션 또는 분할된 컬렉션 하나를 만들 수 있습니다. **PartitionCount** 값을 수정하여는 최대 250개의 단일 파티션 컬렉션 또는 10개의 분할된 컬렉션을 만들거나, 합쳐서 250개의 단일 파티션을 초과하지 않는 두 컬렉션 조합을 만들 수 있습니다(분할된 컬렉션 1개 = 단일 파티션 컬렉션 25개).

현재 파티션 수가 초과된 후에 컬렉션을 만들려고 하면 에뮬레이터에서 다음 메시지와 함께 ServiceUnavailable 예외를 throw합니다.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Azure Cosmos DB 에뮬레이터에서 사용할 수 있는 컬렉션의 수를 변경하려면 다음을 수행합니다.

1. 시스템 트레이에서 **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭한 다음 **데이터 다시 설정...** 을 클릭하여 모든 로컬 Azure Cosmos DB 에뮬레이터 데이터를 삭제합니다.
2. C:\Users\user_name\AppData\Local\CosmosDBEmulator 폴더에 있는 모든 에뮬레이터 데이터를 삭제합니다.
3. **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭한 다음 **마침**을 클릭하여 열려 있는 모든 인스턴스를 종료합니다. 모든 인스턴스를 종료하는 데는 1분 정도 걸립니다.
4. 최신 버전의 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 설치합니다.
5. 250 이하의 값을 설정하여 PartitionCount 플래그로 에뮬레이터를 시작합니다. 예: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`

## <a name="controlling-the-emulator"></a>에뮬레이터 제어

에뮬레이터는 해당 서비스 상태의 시작, 중지, 제거 및 검색을 위해 PowerShell 모듈과 함께 제공 됩니다. 이를 사용하려면:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

사용자의 `PSModulesPath`에 `PSModules` 디렉터리를 놓고 다음과 같이 가져옵니다.

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

다음은 PowerShell에서 에뮬레이터를 제어하는 명령을 요약한 것입니다.

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>구문

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>설명

ServiceControllerStatus.StartPending, ServiceControllerStatus.Running, 또는 ServiceControllerStatus.Stopped 같은 ServiceControllerStatus 값 중 하나를 반환합니다.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>구문

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>설명

에뮬레이터를 시작합니다. 기본적으로 이 명령은 에뮬레이터가 요청을 수락할 준비가 될 때까지 대기합니다. 에뮬레이터가 시작하자 마자 cmdlet을 반환하고자 하는 경우 -NoWait 옵션을 사용하십시오.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>구문

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>설명

에뮬레이터를 중지합니다. 기본적으로 이 명령은 에뮬레이터가 완전히 종료할 때까지 대기합니다. 에뮬레이터가 종료를 시작하자 마자 cmdlet을 반환하고자 하는 경우 -NoWait 옵션을 사용하십시오.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>구문

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>설명

에뮬레이터를 제거하고 $env:LOCALAPPDATA\CosmosDbEmulator의 전체 콘텐츠를 선택적으로 제거합니다.
Cmdlet는 에뮬레이터가 제거되기 전에 중지되도록 합니다.

## <a name="running-on-docker"></a>Docker에서 실행

Azure Cosmos DB 에뮬레이터는 Windows용 Docker에서 실행할 수 있습니다. 이 에뮬레이터는 Oracle Linux용 Docker에서 작동하지 않습니다.

[Windows용 Docker](https://www.docker.com/docker-windows)가 설치되면 도구 모음에서 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환**을 선택하여 Windows 컨테이너로 전환합니다.

다음으로 즐겨 찾는 셸에서 다음 명령을 실행하여 Docker 허브에서 에뮬레이터 이미지를 끌어옵니다.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
이미지를 시작하려면 다음 명령을 실행합니다.

명령줄에서:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

PowerShell에서:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

응답은 다음과 유사합니다.

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

이제 클라이언트의 응답에서 끝점 및 마스터 키를 사용하고 SSL 인증서를 호스트로 가져옵니다. SSL 인증서를 가져오려면 관리자 명령 프롬프트에서 다음을 수행합니다.

명령줄에서:
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

PowerShell에서:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

에뮬레이터가 시작된 후 대화형 셸을 닫으면 에뮬레이터의 컨테이너가 종료됩니다.

데이터 탐색기를 열려면 브라우저에서 다음 URL로 이동합니다. 에뮬레이터 끝점이 위에 표시된 응답 메시지에 제공됩니다.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>문제 해결

다음은 Azure Cosmos DB 에뮬레이터에서 발생하는 문제 해결을 도와주는 팁입니다.

- 새 버전의 에뮬레이터를 설치했는데 오류가 발생하는 경우 데이터를 다시 설정합니다. 시스템 트레이에서 Azure Cosmos DB 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭한 다음, 데이터 다시 설정...을 클릭하여 데이터를 다시 설정할 수 있습니다. 오류가 해결되지 않으면 앱을 제거하고 다시 설치할 수 있습니다. 지침은 [로컬 에뮬레이터 제거](#uninstall)를 참조하세요.

- Azure Cosmos DB 에뮬레이터가 충돌하는 경우 c:\Users\user_name\AppData\Local\CrashDumps 폴더에서 덤프 파일을 수집하고, 압축하고, 전자 메일에 첨부하여 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)으로 보냅니다.

- CosmosDB.StartupEntryPoint.exe에서 충돌을 경험하는 경우 관리자 명령 프롬프트에서 다음 명령을 실행합니다. `lodctr /R` 

- 연결 문제가 발생하는 경우 [추적 파일을 수집](#trace-files)하고, 압축하고, 전자 메일에 첨부하여 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)으로 보냅니다.

- **서비스를 사용할 수 없음** 메시지가 나타나는 경우 에뮬레이터에서 네트워크 스택을 초기화하지 못했을 수 있습니다. 해당 네트워크 필터 드라이버로 인해 문제가 발생할 수 있으므로 Pulse 보안 클라이언트 또는 Juniper 네트워크 클라이언트를 설치했는지 확인합니다. 일반적으로 타사 네트워크 필터 드라이버를 제거하면 문제가 해결됩니다.

### <a id="trace-files"></a>추적 파일 수집

디버깅 추적을 수집하려면 관리 명령 프롬프트에서 다음 명령을 실행합니다.

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown` 프로그램이 종료되었는지 시스템 트레이를 확인합니다. 프로그램 종료에 1분 정도 걸릴 수 있습니다. Azure Cosmos DB 에뮬레이터 사용자 인터페이스에서 **종료**를 클릭해도 됩니다.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. 문제를 재현합니다. 데이터 탐색기가 작동하지 않는 경우 브라우저가 몇 초 간 열리고 오류를 catch할 때까지 기다리면 됩니다.
5. `CosmosDB.Emulator.exe /stoptraces`
6. `%ProgramFiles%\Azure Cosmos DB Emulator`로 이동하여 docdbemulator_000001.etl 파일을 찾습니다.
7. 재현 단계와 마찬가지로 디버깅을 위해 .etl 파일을 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)으로 보냅니다.

### <a id="uninstall"></a>로컬 에뮬레이터 제거

1. 시스템 트레이에서 Azure Cosmos DB 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭한 다음 마침을 클릭하여 로컬 에뮬레이터의 열려 있는 모든 인스턴스를 종료합니다. 모든 인스턴스를 종료하는 데는 1분 정도 걸립니다.
2. Windows 검색 상자에 **앱 및 기능**을 입력하고 **앱 및 기능(시스템 설정)** 결과를 클릭합니다.
3. 앱 목록에서 **Azure Cosmos DB 에뮬레이터**로 스크롤하여 선택하고, **제거**를 클릭한 다음, 확인하고 **제거**를 다시 클릭합니다.
4. 앱이 제거되면 C:\Users\<user>\AppData\Local\CosmosDBEmulator로 이동하여 폴더를 삭제합니다. 

## <a name="change-list"></a>변경 목록

작업 표시줄에서 로컬 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭하고 정보 메뉴 항목을 클릭하여 버전 번호를 확인할 수 있습니다.

### <a name="1220-released-on-april-20-2018"></a>1.22.0. 2018년 4월 20일 릴리스

Cosmos DB 클라우드 서비스와의 패리티를 위한 에뮬레이터 서비스 업데이트 외에도, 개선된 PowerShell 설명서와 여러 가지 버그 수정이 포함되어 있습니다.

### <a name="12106-released-on-march-27-2018"></a>1.21.0.6 - 2018년 3월 27일 릴리스

Cosmos DB 클라우드 서비스와의 패리티를 위한 에뮬레이터 서비스 업데이트 외에도, 새로운 기능 하나와 두 개의 버그 수정이 이 릴리스에 포함되어 있습니다.

#### <a name="features"></a>기능

1. 이제 Start-CosmosDbEmulator 명령에 시작 옵션이 포함됩니다.

#### <a name="bug-fixes"></a>버그 수정

1. Microsoft.Azure.CosmosDB.Emulator PowerShell 모듈은 이제 `ServiceControllerStatus` 열거형이 로드됩니다.

2. Microsoft.Azure.CosmosDB.Emulator PowerShell 모듈에는 첫 번째 릴리스에서 생략되었던 매니페스트가 포함되어 있습니다.

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4 - 2018년 2월 14일 릴리즈

이 릴리즈에 한 개의 새 기능 및 두 개의 버그 수정이 포함됐습니다. 이 문제를 발견하고 해결하는 데 도움을 준 고객에게 감사드립니다.

#### <a name="bug-fixes"></a>버그 수정

1. 에뮬레이터는 이제 1개나 2개 코어(또는 가상 Cpu)를 장착한 컴퓨터에서 작동합니다.

   Cosmos DB는 다양한 서비스를 수행하기 위해 작업을 할당합니다. 할당 작업 수는 호스트의 코어 수의 배수입니다. 코어 수가 방대한 프로덕션 환경에서 기본 배수가 잘 작동 합니다. 그러나 1개 또는 2개 프로세서가 있는 머신에서 이 배수가 적용될 경우 해당 서비스를 수행하도록 작업이 할당되지 않습니다.

   에뮬레이터에 구성 재정의를 추가해 이를 수정했습니다. 이제 1의 배수를 적용합니다. 다양한 서비스를 수행하도록 할당된 작업의 수는 호스트의 코어 수와 동일합니다.

   이 릴리스에 대해 아무 것도 하지 않았다면 이 문제를 해결할 수 있었을 것입니다. 에뮬레이터를 호스팅하는 많은 개발/테스트 환경은 1개 또는 2개의 코어를 갖습니다.

2. 에뮬레이터는 더 이상 Microsoft Visual C++ 2015 재배포 가능 패키지 설치를 요구하지 않습니다.

   Windows(데스크톱 및 서버 버전)의 새로운 설치는 이 재배포 가능 패키지를 포함하지 않습니다. 따라서 재배포 가능 이진 파일을 에뮬레이터와 함께 번들로 제공합니다.

#### <a name="features"></a>기능

대화를 나눈 고객 대부분이 에뮬레이터가 스크립트가 가능하면 좋겠다는 말을 했습니다. 따라서 이 릴리스에 몇 가지 스크립트 기능을 추가했습니다. 에뮬레이터는 자체 시작, 중지, 상태 가져오기 및 제거를 위한 PowerShell 모듈을 포함합니다`Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>1.20.91.1 - 2018년 1월 26일 릴리스

* 기본적으로 MongoDB 집계 파이프라인을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 로컬 에뮬레이터 설치
> * Windows용 Docker에서 에뮬레이터 실행
> * 요청 인증
> * 에뮬레이터에서 데이터 탐색기 사용
> * SSL 인증서 내보내기
> * 명령줄에서 에뮬레이터 호출
> * 추적 파일 수집

이 자습서에서는 무료 로컬 개발을 위해 로컬 에뮬레이터를 사용하는 방법을 살펴보았습니다. 이제 다음 자습서로 진행하여 에뮬레이터 SSL 인증서를 내보내는 방법을 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB 에뮬레이터 인증서 내보내기](local-emulator-export-ssl-certificates.md)
