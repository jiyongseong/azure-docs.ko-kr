---
title: Azure Stream Analytics의 출력 이해
description: 이 문서에서는 분석 결과에 대한 Power BI를 포함하여 Azure Stream Analytics에서 사용할 수 있는 데이터 출력 옵션을 설명합니다.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: e14c4671669bc00e52c84c821a5229d26b2ba1c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211374"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics의 출력 이해
이 문서에서는 Azure Stream Analytics 작업에 사용할 수 있는 다양한 유형의 출력을 설명합니다. 출력을 사용하여 Stream Analytics 작업의 결과를 저장할 수 있습니다. 출력 데이터를 사용하여 추가 비즈니스 분석 및 데이터의 데이터 웨어하우징을 수행할 수 있습니다. 

Stream Analytics 쿼리를 디자인할 때 [INTO 절](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics)을 사용하여 출력의 이름을 참조하세요. 작업당 단일 출력 또는 쿼리에서 여러 INTO 절을 제공하여 필요한 경우 스트리밍 작업당 여러 출력을 사용할 수 있습니다.

Stream Analytics 작업 출력을 만들고, 편집하고 테스트하기 위해 [Azure Portal](stream-analytics-quick-create-portal.md#configure-output-to-the-job), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-output) 및 [Visual Studio](stream-analytics-tools-for-visual-studio.md)를 사용할 수 있습니다.

일부 출력 형식은 처리량을 최적화하기 위해 [분할](#partitioning) 및 다양한 [출력 일괄 처리 크기](#output-batch-size)를 지원합니다.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics는 [Azure Data Lake 저장소](https://azure.microsoft.com/services/data-lake-store/)를 지원합니다. Azure 데이터 레이크 저장소는 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Data Lake 저장소를 사용하면 작동 및 예비 분석에 대해 모든 크기, 형식 및 수집 속도의 데이터를 저장할 수 있습니다. Stream Analytics에는 Data Lake Store에 액세스할 수 있는 권한이 필요합니다.

### <a name="authorize-an-azure-data-lake-store-account"></a>Azure Data Lake Store 계정 권한 부여

1. Azure Portal에서 출력으로 Data Lake 저장소를 선택하는 경우 기존 Data Lake Store에 대한 연결 권한을 부여하라는 메시지가 나타납니다.  

   ![Data Lake Store 권한 부여](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Data Lake Store에 대한 액세스 권한이 이미 있는 경우 **지금 권한 부여**를 선택하고 **권한 부여로 리디렉션**을 나타내는 페이지가 나타납니다. 권한 부여에 성공하면 Data Lake Store 출력을 구성할 수 있는 페이지가 표시됩니다.

3. Data Lake 저장소 계정을 인증하면 사용자가 Data Lake 저장소 출력에 대한 속성을 구성할 수 있습니다. 다음 테이블은 속성 이름 및 해당 설명의 목록으로 Data Lake 저장소 출력을 구성합니다.

   ![Data Lake Store 권한 부여](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

| 속성 이름 | 설명 | 
| --- | --- |
| 출력 별칭 | 쿼리 출력을 Data Lake Store로 보내기 위해 쿼리에 사용되는 식별 이름입니다. | 
| 계정 이름 | 출력을 보내는 Data Lake Storage 계정의 이름 구독에서 사용할 수 있는 Data Lake Store 계정 드롭다운 목록이 표시됩니다. |
| 경로 접두사 패턴 | 지정된 Data Lake 저장소 계정 내에서 파일을 작성하는 데 사용되는 파일 경로입니다. {date} 및 {time} 변수 인스턴스를 하나 이상 지정할 수 있습니다.</br><ul><li>예제 1: folder1/logs/{date}/{time}</li><li>예제 2: folder1/logs/{date}</li></ul>파일 경로 패턴에 후행 "/"가 포함되어 있지 않으면 파일 경로의 마지막 패턴이 파일 이름 접두사로 처리됩니다. </br></br>이 경우 새 파일이 만들어집니다.<ul><li>출력 스키마의 변경</li><li>작업의 외부 또는 내부 다시 시작</li></ul> |
| 날짜 형식 | 선택 사항입니다. 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD |
|시간 형식 | 선택 사항입니다. 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV 및 Avro를 지원합니다.| 
| Encoding | CSV 또는 JSON 형식을 사용하는 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.|
| 구분 기호 | CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.|
| 형식 | JSON 직렬화에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 줄로 구분된 JSON을 사용하는 것이 좋습니다. 이 경우 출력 파일에 쓰는 동안 특수한 처리가 필요하지 않기 때문입니다.|

### <a name="renew-data-lake-store-authorization"></a>Data Lake Store 권한 부여 갱신
작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우에는 Data Lake Store 계정을 다시 인증해야 합니다. 재인증을 하지 않으면 작업은 출력 결과를 생성하지 않고 재 권한 부여에 대한 필요성을 나타내는 오류가 작업 로그에 표시됩니다. 현재 Data Lake 저장소 출력을 포함하는 모든 작업에 대해 90일 마다 인증 토큰을 수동으로 새로 고쳐야 하는 제한 사항이 있습니다. 

권한 부여를 갱신하기 위해 작업을 **중지**하고 Data Lake Store 출력으로 이동하여 **권한 갱신** 링크를 클릭하면 **권한 부여 리디렉션 중...** 을 나타내는 페이지가 잠깐 나타납니다. 페이지가 자동으로 닫히고 성공하면 **권한 부여를 성공적으로 갱신했습니다**가 표시됩니다. 페이지의 맨 아래에서 **저장**을 클릭해야 하고 데이터 손실을 방지하도록 **마지막으로 중지된 시간**에서 작업을 다시 시작하여 진행할 수 있습니다.

![Data Lake Store 권한 부여](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 를 사용할 수 있습니다. Azure SQL Database의 기존 테이블에 Stream Analytics 작업을 기록합니다.  테이블 스키마는 작업에서 출력되는 필드 및 해당 형식과 정확히 일치해야 합니다. SQL Database 출력 옵션을 통해 [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)를 출력으로 지정할 수도 있습니다. 다음 테이블은 SQL Database 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 데이터베이스 | 출력을 보내는 데이터베이스의 이름입니다. |
| 서버 이름 | SQL Database 서버 이름입니다. |
| 사용자 이름 | 데이터베이스에 쓸 수 있는 액세스 권한이 있는 사용자 이름입니다. |
| 암호 | 데이터베이스에 연결하는 암호입니다. |
| 테이블 | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자를 구분하며 이 테이블의 스키마는 작업 출력에서 생성되는 필드의 수 및 해당 형식과 정확히 일치해야 합니다. |

> [!NOTE]
> 현재, Stream Analytics의 작업 출력에 대해 Azure SQL Database 제품을 사용할 수 있습니다. 그러나 데이터베이스가 연결된 SQL Server를 실행하는 Azure Virtual Machine은 지원되지 않습니다. 후속 릴리스에서는 변경될 수 있습니다.
> 

## <a name="blob-storage"></a>Blob 저장소
클라우드에서 대량의 구조화되지 않은 데이터를 저장하는 경우 Blob 저장소는 비용 효율적이고 확장성 있는 솔루션을 제공합니다.  Azure Blob 저장소 및 사용법에 대한 소개 내용은 [Blob 사용 방법](../storage/blobs/storage-dotnet-how-to-use-blobs.md)설명서를 참조하세요.

다음 테이블은 Blob 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 | 
| --- | --- |
| 출력 별칭 | 쿼리 출력을 이 Blob 저장소로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| Storage 계정 | 출력을 보내는 저장소 계정의 이름 |
| Storage 계정 키 | 저장소 계정과 연결된 비밀 키입니다. |
| 저장소 컨테이너 | 컨테이너는 Microsoft Azure Blob service에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob service에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. |
| 경로 패턴 | 선택 사항입니다. 지정된 컨테이너 내에서 Blob를 작성하는 데 사용되는 파일 경로 패턴입니다. </br></br> 경로 패턴에서 Blob가 작성된 빈도를 지정하기 위해 날짜 시간 변수 인스턴스 중 하나 이상을 사용하도록 선택할 수도 있습니다. </br> {date}, {time} </br> </br>[미리 보기](https://aka.ms/ASAPreview)에 등록한 경우 필드 이름이 영숫자이고 공백, 하이픈 및 밑줄을 포함할 수 있는지에 따라 이벤트 데이터에서 파티션 Blob으로 하나의 사용자 지정 {필드} 이름을 지정할 수도 있습니다. 사용자 지정 필드에 대한 제한은 다음을 포함합니다. <ul><li>대/소문자 구문 없음(열 "ID" 및 열 "id" 간 다를 수 없음)</li><li>중첩된 필드는 허용되지 않음(대신 "평면화" 필드에 대한 작업 쿼리에서 별칭 사용)</li><li>식은 필드 이름으로 사용할 수 없음</li></ul>예제: <ul><li>예제 1: cluster1/logs/{date}/{time}</li><li>예제 2: cluster1/logs/{date}</li><li>예제 3(미리 보기): cluster1/{client_id}/{date}/{time}</li><li>예제 4(미리 보기): cluster1/{myField} 여기서 쿼리는 SELECT data.myField AS myField FROM 입력</li></ul><BR> 파일 명명은 다음 규칙을 따릅니다. </br> {경로 접두사 패턴}/schemaHashcode_Guid_Number.extension </br></br> 예제 출력 파일: </br><ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li><li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul><br/>
| 날짜 형식 | 선택 사항입니다. 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD |
| 시간 형식 | 선택 사항입니다. 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다.  JSON, CSV 및 Avro를 지원합니다.
| Encoding | CSV 또는 JSON 형식을 사용하는 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호 | CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON 직렬화에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 줄로 구분된 JSON을 사용하는 것이 좋습니다. 이 경우 출력 파일에 쓰는 동안 특수한 처리가 필요하지 않기 때문입니다. |

BLOB 저장소를 출력으로 사용하면 다음과 같은 경우 BLOB에 새 파일이 만들어집니다.

* 파일이 허용되는 최대 블록 수(현재 50,000)를 초과합니다. 허용된 최대 블록 수는 허용된 최대 Blob 크기에 도달하지 않고 도달할 수 있습니다. 예를 들어 출력 속도가 높으면 블록당 더 많은 바이트를 볼 수 있으며 파일 크기는 더 커집니다. 출력 속도가 낮으면 각 블록의 데이터가 줄어들고 파일 크기도 작아집니다.
* 출력의 스키마가 변경되고 출력 형식에 고정 스키마(CSV 및 Avro)가 필요한 경우  
* 작업이 다시 시작되는 경우 사용자가 외부적으로 또는 시스템 유지 관리 또는 오류 복구의 경우 내부적으로 중지하고 시작합니다.  
* 쿼리가 완전히 분할되고 각 출력 파티션에 대해 새 파일이 만들어진 경우  
* 저장소 계정의 파일이나 컨테이너가 사용자에 의해 삭제된 경우  
* 출력 시간이 경로 접두사 패턴을 사용하여 분할되고 쿼리가 다음 시간으로 이동할 때 새 BLOB이 사용되는 경우
* 출력이 사용자 지정 필드에 따라 분할되는 경우 존재하지 않는 경우 파티션 키당 새 Blob이 만들어집니다.
*   출력이 파티션 키 카디널리티가 8000을 초과하는 사용자 지정 필드에 따라 분할되는 경우 파티션 키당 새 Blob이 만들어질 수도 있습니다.

## <a name="event-hub"></a>이벤트 허브
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 서비스는 확장성이 뛰어난 게시-구독 이벤트 수집기입니다. 초당 수 백만의 이벤트를 수집할 수 있습니다. 출력으로 이벤트 허브를 사용하는 한 가지 예는 Stream Analytics 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우입니다.

이벤트 허브 데이터 스트림을 출력으로 구성하는 데 필요한 몇 개의 매개 변수가 있습니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 | 쿼리 출력을 이 이벤트 허브로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 이벤트 허브 네임스페이스 |이벤트 허브 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 Event Hub를 만들 때 이벤트 허브 네임스페이스도 만들었습니다. |
| 이벤트 허브 이름 | 이벤트 허브 출력의 이름입니다. |
| 이벤트 허브 정책 이름 | 이벤트 허브 구성 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 이벤트 허브 정책 키 | 이벤트 허브 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 선택키입니다. |
| 파티션 키 열 [선택 사항] | 이 열에는 이벤트 허브 출력에 대한 파티션 키가 포함됩니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다.  JSON, CSV 및 Avro를 지원합니다. |
| Encoding | CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 | CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON 직렬화에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 줄로 구분된 JSON을 사용하는 것이 좋습니다. 이 경우 출력 파일에 쓰는 동안 특수한 처리가 필요하지 않기 때문입니다. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/)를 Stream Analytics 작업의 출력으로 사용하여 분석 결과에 대한 풍부한 시각화 환경을 제공할 수 있습니다. 운영 대시보드, 보고서 생성 및 메트릭 제어 보고에 이 기능을 이용할 수 있습니다.

### <a name="authorize-a-power-bi-account"></a>Power BI 계정 권한 부여
1. Azure Portal에서 Power BI를 출력으로 선택하는 경우 기존 Power BI 사용자를 인증하거나 새로운 Power BI 계정을 생성하라는 메시지가 나타납니다.  
   
   ![Power BI 사용자 권한 부여](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2. 아직 없다면 새 계정을 만들고 지금 권한 부여를 클릭합니다.  다음과 같은 페이지가 표시됩니다.
   
   ![Azure 계정 Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3. 이 단계에서 Power BI 출력에 대한 권한을 부여할 회사 또는 학교 계정을 제공합니다. 아직 Power BI에 등록하지 않은 경우 지금 등록을 선택합니다. Power BI에 사용하는 회사 또는 학교 계정은 현재 로그인되어 있는 Azure 구독 계정과 다를 수 있습니다.

### <a name="configure-the-power-bi-output-properties"></a>Power BI 출력 속성 구성
Power BI 계정의 인증을 설정하면 사용자가 Power BI 출력에 대한 속성을 구성할 수 있습니다. 다음 테이블은 속성 이름 및 해당 설명의 목록으로 Power BI 출력을 구성합니다.

| 속성 이름 | description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 PowerBI 출력으로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 그룹 작업 영역 |다른 Power BI 사용자와 데이터를 공유할 수 있게 하려면 Power BI 계정 내에서 그룹을 선택하거나 그룹에 작성하지 않으려면 "내 작업 영역"을 선택할 수 있습니다.  기존 그룹을 업데이트하려면 Power BI 인증을 갱신해야 합니다. |
| 데이터 집합 이름 |Power BI 출력에 사용할 데이터 집합 이름을 제공합니다. |
| 테이블 이름 |Power BI 출력의 데이터 집합 아래에 테이블 이름을 제공합니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 집합에 하나의 테이블만 있을 수 있습니다. |

Power BI 출력 및 대시보드 구성에 대한 연습은 [Azure Stream Analytics 및 Power BI](stream-analytics-power-bi-dashboard.md) 문서를 참조하세요.

> [!NOTE]
> Power BI 대시보드에 명시적으로 데이터 집합 및 테이블을 만들지 마세요. 작업을 시작하고 작업이 Power BI에 출력을 보내기 시작하면 데이터 집합 및 테이블은 자동으로 채워집니다. 한편, 작업 쿼리에서 결과를 생성하지 않으면 데이터 집합 및 테이블은 생성되지 않습니다. 이 Stream Analytics 작업에서 제공한 것과 동일한 이름의 데이터 집합과 테이블이 Power BI에 이미 있는 경우에는 기존 데이터를 덮어쓰므로 주의합니다.
> 

### <a name="schema-creation"></a>스키마 생성
Azure Stream Analytics는 사용자를 대신하여 Power BI 데이터 집합 및 테이블을 만듭니다(아직 없는 경우). 다른 모든 경우에는 테이블이 새 값으로 업데이트됩니다. 현재 데이터 집합 내에 테이블이 하나만 존재할 수 있다는 제한이 있습니다.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Stream Analytics에서 Power BI로 데이터 형식 변환
Azure Stream Analytics는 출력 스키마가 변경되면 런타임 시 동적으로 데이터 모델을 업데이트합니다. 열 이름 변경, 열 형식 변경 및 열 추가 또는 제거 작업이 모두 추적됩니다.

다음 표는 POWER BI 데이터 집합 및 테이블이 존재하지 않는 경우 [Stream Analytics 데이터 형식](https://msdn.microsoft.com/library/azure/dn835065.aspx)에서 Power BI [EDM(엔터티 데이터 모델) 형식](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/)으로의 데이터 형식 변환을 보여 줍니다.

Stream Analytics에서 | Power BI로
-----|-----|------------
bigint | Int64
nvarchar(max) | 문자열
Datetime | DateTime
float | Double
레코드 배열 | 문자열 형식, 상수 값 "IRecord" 또는 "IArray"

### <a name="schema-update"></a>스키마 업데이트
Stream Analytics는 출력의 첫 번째 이벤트 집합을 기반으로 데이터 모델 스키마를 유추합니다. 나중에 필요한 경우 데이터 모델 스키마는 들어오는 이벤트에 맞게 업데이트되며 이는 원래 스키마에 맞지 않을 수 있습니다.

행에서 동적 스키마를 업데이트하지 않으려면 `SELECT *` 쿼리는 피해야 합니다. 성능이 저하될 가능성이 있을 뿐만 아니라, 결과 생성에 소요되는 시간이 불확실해질 수도 있습니다. Power BI 대시보드에 표시될 정확한 필드를 선택해야 합니다. 또한 데이터 값은 선택한 데이터 형식을 준수해야 합니다.


이전/현재 | Int64 | 문자열 | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | 문자열 | 문자열 | Double
Double | Double | 문자열 | 문자열 | Double
문자열 | 문자열 | 문자열 | 문자열 |  | 문자열 | 
DateTime | 문자열 | 문자열 |  DateTime | 문자열


### <a name="renew-power-bi-authorization"></a>Power BI 권한 부여 갱신
Stream Analytics 작업을 만들거나 마지막으로 인증한 후 Power BI 계정 암호가 변경되면 Stream Analytics를 다시 인증해야 합니다. MFA(Multi-Factor Authentication)가 AAD(Azure Active Directory) 테넌트에 구성된 경우 2주마다 Power BI 권한 부여도 갱신해야 합니다. 이 문제의 증상은 작업 출력이 없으며 작업 로그에 "사용자 인증 오류"가 표시됩니다.

  ![Power BI 새로 고침 토큰 오류](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

이 문제를 해결하려면 실행 중인 작업을 중지하고 Power BI 출력으로 이동합니다.  **권한 부여 갱신** 링크를 선택하고, **마지막 중지 시간**부터 작업을 다시 시작하여 데이터 손실을 방지합니다.

  ![Power BI 갱신 권한 부여](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Azure Table Storage](../storage/common/storage-introduction.md)는 가용성이 높고 확장성이 큰 저장소를 제공하므로, 응용 프로그램이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. 테이블 저장소는 스키마에 대한 제약 조건이 적은 구조화된 데이터에 활용할 수 있는 Microsoft의 NoSQL 키/특성 저장소입니다. Azure 테이블 저장소는 지속적이고 효율적인 검색을 위해 데이터를 저장하는 데 사용할 수 있습니다.

다음 테이블은 테이블 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 테이블 저장소로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| Storage 계정 |출력을 보내는 저장소 계정의 이름 |
| Storage 계정 키 |저장소 계정과 연결된 선택키입니다. |
| 테이블 이름 |테이블의 이름입니다. 테이블이 존재하지 않는 경우 만들어집니다. |
| 파티션 키 |파티션 키를 포함하는 출력 열의 이름입니다. 파티션 키는 엔터티 기본 키의 첫 번째 부분을 형성하는 지정된 테이블 내에서 분할에 고유한 식별자입니다. 크기가 최대 1KB인 문자열 값입니다. |
| 행 키. |행 키를 포함하는 출력 열의 이름입니다. 행 키는 주어진 파티션 내 엔터티의 고유 식별자입니다. 엔터티의 기본 키에서 두 번째 부분을 형성합니다. 행 키는 크기가 최대 1KB인 문자열 값입니다. |
| Batch 크기 |배치 작업에 대한 레코드 수입니다. 기본값(100)은 대부분의 작업에 충분합니다. 이 설정을 수정하는 방법에 대한 자세한 내용은 [테이블 Batch 작업 사양](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)을 참조하세요. |
 
## <a name="service-bus-queues"></a>Service Bus 큐
[Service Bus 큐](https://msdn.microsoft.com/library/azure/hh367516.aspx) 는 하나 이상의 경쟁 소비자에게 FIFO(선입선출) 메시지 배달을 제공합니다. 일반적으로 메시지가 큐에 추가된 임시 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리됩니다.

다음 테이블은 큐 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 Service Bus 큐로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| Service Bus 네임스페이스 |Service Bus 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. |
| 큐 이름 |Service Bus 큐 이름 |
| 큐 정책 이름 |또한 큐를 만들 때 큐 구성 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 큐 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 선택키 |
| 이벤트 직렬화 형식 |출력 데이터에 대한 직렬화 형식입니다.  JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 |CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 |JSON 형식에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다. |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.

## <a name="service-bus-topics"></a>Service Bus 토픽
Service Bus 큐는 보낸 사람에서 받는 사람으로의 일대일 통신 방법을 제공하는 반면, [Service Bus 항목](https://msdn.microsoft.com/library/azure/hh367516.aspx) 은 일대다 형태의 통신을 제공합니다.

다음 테이블은 테이블 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 Service Bus 토픽으로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| Service Bus 네임스페이스 |Service Bus 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 Event Hub를 만들 때 Service Bus 네임스페이스도 만들었습니다. |
| 항목 이름 |토픽은 이벤트 허브 및 큐와 유사한 메시징 엔터티입니다. 다양한 장치 및 서비스에서 이벤트 스트림을 수집하도록 설계됩니다. 토픽을 만들 때 특정 이름도 지정됩니다. 토픽에 전송된 메시지는 구독이 생성되지 않으면 사용할 수 없으므로 토픽에 대해 구독이 하나 이상 있어야 합니다. |
| 토픽 정책 이름 |또한 토픽을 만들 때 토픽 구성 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 토픽 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 선택키 |
| 이벤트 직렬화 형식 |출력 데이터에 대한 직렬화 형식입니다.  JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 또는 JSON 형식을 사용하는 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호 |CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)는 세계적으로 분산된 다중 모델 데이터베이스 서비스로서, 제한없는 탄력적 크기 조정을 전 세계적으로 제공하고 스키마 제약 없는 데이터 모델에 대한 자동 인덱싱 및 다양한 쿼리, 낮은 대기 시간 보장과 업계 최고의 포괄적인 SLA를 제공합니다. Stream Analytics의 Cosmos DB 수집 옵션에 대해 알아 보려면 [Cosmos DB를 출력으로 사용하는 Stream Analytics](stream-analytics-documentdb-output.md) 문서를 참조하세요.

> [!Note]
> 현재 Azure Stream Analytics는 **SQL API**를 사용한 CosmosDB 연결만을 지원합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Azure Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다. 

다음 표에서는 Azure Cosmos DB 출력을 만드는 속성에 대해 설명합니다.
| 속성 이름 | description |
| --- | --- |
| 출력 별칭 | Stream Analytics 쿼리에서 이 출력을 참조할 별칭입니다. |
| sink | Cosmos DB |
| 가져오기 옵션 | "Select Cosmos DB from your subscription"(구독에서 Cosmos DB 선택) 또는 "Provide Cosmos DB settings manually"(수동으로 Cosmos DB 설정 제공) 중 하나를 선택합니다.
| 계정 ID | Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다. |
| 계정 키 | Cosmos DB 계정에 대한 공유 액세스 키입니다. |
| 데이터베이스 | Cosmos DB 데이터베이스 이름입니다. |
| 컬렉션 이름 패턴 | 사용할 컬렉션에 대한 컬렉션 이름 또는 패턴입니다. <br/>컬렉션 이름 형식은 선택적 {partition} 토큰을 사용하여 구성할 수 있으며 파티션은 0부터 시작합니다. 두 가지 예:  <br/>1. _MyCollection_ – "MyCollection"이라는 이름의 컬렉션이 있어야 합니다.  <br/>2. _MyCollection{partition}_ – 분할 열에 기반합니다. <br/>분할 열 컬렉션("MyCollection0", "MyCollection1", "MyCollection2" 등)은 반드시 존재해야 합니다. |
| Partition Key | 선택 사항입니다. 컬렉션 이름 패턴에 {parition} 토큰을 사용하는 경우에만 필요합니다.<br/> 파티션 키는 컬렉션에서 출력 분할을 위한 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다.<br/> 단일 컬렉션 출력의 경우 임의의 출력 열이 사용될 수 있습니다. 예를 들어 PartitionId입니다. |
| 문서 ID |선택 사항입니다. 삽입 또는 업데이트 작업이 기반으로 하는 기본 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다.  

## <a name="azure-functions"></a>Azure 기능
Azure Functions는 인프라를 명시적으로 프로비전 또는 관리하지 않고도 필요 시 코드를 실행할 수 있는 서버를 사용하지 않는 계산 서비스입니다. Azure 또는 타사 서비스에서 발생하는 이벤트에 의해 트리거되는 코드를 구현할 수 있습니다.  트리거에 응답하는 이러한 Azure Functions 기능 때문에 Azure Stream Analytics에 대한 출력이 자연스럽게 제공될 수 있습니다. 이 출력 어댑터를 사용하여 Stream Analytics를 Azure Functions에 연결하고, 다양한 이벤트에 대한 응답으로 스크립트 또는 코드 조각을 실행할 수 있습니다.

Azure Stream Analytics는 HTTP 트리거를 통해 Azure Functions를 호출합니다. 새 Azure Function 출력 어댑터는 다음의 구성 가능한 속성을 통해 사용할 수 있습니다.

| 속성 이름 | description |
| --- | --- |
| 함수 앱 |Azure Functions 앱의 이름 |
| 함수 |Azure Functions 앱의 함수 이름 |
| 키 |다른 구독에서 Azure 함수를 사용하려는 경우 함수에 액세스하기 위한 키를 제공하여 이렇게 할 수 있습니다. |
| 최대 일괄 처리 크기 |이 속성을 사용하여 Azure 함수에 전송될 각 출력 일괄 처리의 최대 크기를 설정할 수 있습니다. 기본적으로 이 값은 256KB입니다. |
| 최대 일괄 처리 수  |이름으로 알 수 있듯이 이 속성을 사용하면 Azure Functions로 전송되는 각 일괄 처리의 최대 이벤트 수를 지정할 수 있습니다. 기본 최대 일괄 처리 수 값은 100입니다. |

Azure Stream Analytics는 Azure 함수에서 413(http 요청 엔터티가 너무 큼) 예외를 수신하면 Azure Functions으로 전송하는 일괄 처리 크기를 줄입니다. Azure 함수 코드에서 이 예외를 사용하여 Azure Stream Analytics가 너무 큰 일괄 처리를 전송하지 않도록 합니다. 또한, 함수에 사용되는 최대 일괄 처리 수 및 크기 값이 Stream Analytics 포털에 입력한 값과 일치하는지 확인합니다. 

그뿐 아니라 상황에서는 또한 기간 안에 발생하는 이벤트가 없으면 출력이 생성되지 않습니다. 결과적으로 computeResult 함수는 호출되지 않습니다. 이 동작은 기본 제공 기간 이동 집계 함수와 일치합니다.

## <a name="partitioning"></a>분할

다음 표에서는 각 출력 유형에 대한 출력 기록기 수 및 파티션 지원이 요약되어 있습니다.

| 출력 형식 | 분할 지원 | 파티션 키  | 출력 기록기 수 | 
| --- | --- | --- | --- |
| Azure Data Lake Store | 예 | 경로 접두사 패턴에 {date} 및 {time} 토큰을 사용합니다. YYYY/MM/DD, DD/MM/YYYY, MM-DD-YYYY 등과 같은 날짜 형식을 선택합니다. HH는 시간 형식에 사용됩니다. | [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. | 
| Azure SQL Database | 아니오 | 없음 | 사용할 수 없습니다. | 
| Azure Blob 저장소 | 예 | 경로 패턴의 사용자 이벤트 필드에서 {date} 및 {time} 토큰을 사용합니다. YYYY/MM/DD, DD/MM/YYYY, MM-DD-YYYY 등과 같은 날짜 형식을 선택합니다. HH는 시간 형식에 사용됩니다. [미리 보기](https://aka.ms/ASAPreview)의 일부로 단일 사용자 지정 이벤트 특성 {fieldname}으로 Blob 출력을 분할할 수 있습니다. | [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. | 
| Azure Event Hub | 예 | 예 | 파티션 맞춤에 따라 달라집니다.</br> 출력 이벤트 허브 파티션 키가 업스트림(이전의) 쿼리 단계로 동일하게 맞춰질 때 작성기의 수는 출력 이벤트 허브 파티션의 수와 동일합니다. 각 작성기는 이벤트 허브의 [EventHubSender 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)를 사용하여 특정 파티션에 이벤트를 전송합니다. </br> 출력 이벤트 허브 파티션 키가 업스트림(이전의) 쿼리 단계로 맞춰지지 않는 경우 작성기의 수는 이전 단계의 파티션 수와 동일합니다. 각 작성기는 EventHubClient [SendBatchAsync 클래스](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)를 사용하여 모든 출력 파티션에 이벤트를 전송합니다. |
| Power BI | 아니오 | 없음 | 사용할 수 없습니다. | 
| Azure 테이블 저장소 | 예 | 모든 출력 열입니다.  | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. | 
| Azure Service Bus 항목 | 예 | 자동으로 선택됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.| 출력 항목의 파티션 수와 동일합니다.  |
| Azure Service Bus 큐 | 예 | 자동으로 선택됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.| 출력 큐의 파티션 수와 동일합니다. |
| Azure Cosmos DB | 예 | 컬렉션 이름 패턴에서 {partition} 토큰을 사용합니다. {partition} 값은 쿼리의 PARTITION BY 절에 기반합니다. | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure 기능 | 아니오 | 없음 | 사용할 수 없습니다. | 

## <a name="output-batch-size"></a>출력 일괄 처리 크기
Azure Stream Analytics는 변수 크기 일괄 처리를 사용하여 이벤트를 처리하고 출력에 기록합니다. 일반적으로 Stream Analytics 엔진은 한 번에 하나의 메시지를 기록하지 않고 효율성을 위해 일괄 처리를 사용합니다. 들어오고 나가는 이벤트 비율이 높을 때 더 큰 일괄 처리를 사용합니다. 송신 속도가 낮은 경우 대기 시간을 낮게 유지하기 위해 더 작은 일괄 처리를 사용합니다. 

다음 표는 출력 일괄 처리에 대한 일부 고려 사항을 설명합니다.

| 출력 형식 | 최대 메시지 크기 | 일괄 처리 크기 최적화 |
| :--- | :--- | :--- | 
| Azure Data Lake Store | [Data Lake Storage 용량 한도](../azure-subscription-service-limits.md#data-lake-store-limits) 참조 | 쓰기 작업당 최대 4MB |
| Azure SQL Database | 단일 대량 삽입당 최대 10,000행</br>단일 대량 삽입당 최소 100행 </br>참고 항목 [Azure SQL 한도](../sql-database/sql-database-resource-limits.md) |  모든 일괄 처리는 처음에 최대 일괄 처리 크기로 대량 삽입되고 SQL에서 다시 시도 가능한 오류를 기반으로 절반(최소 일괄 처리 크기까지)으로 일괄 처리를 분할할 수 있습니다. |
| Azure Blob 저장소 | [Azure Storage 용량 한도](../azure-subscription-service-limits.md#storage-limits) 참조 | 최대 Blob 블록 크기는 4MB입니다.</br>최대 Blob 블록 개수는 50000개입니다. |
| Azure Event Hub   | 메시지당 256KB </br>참고 항목 [Event Hubs 제한](../event-hubs/event-hubs-quotas.md) |    입력 출력 분할이 정렬되지 않는 경우 각 이벤트는 EventData에서 개별적으로 압축되고 최대 메시지 크기(Premium SKU에 대해 1MB)의 일괄 처리로 전송됩니다. </br></br>  입력-출력 분할이 정렬되는 경우 여러 이벤트는 최대 메시지 크기까지 단일 EventData로 압축되고 전송됩니다.    |
| Power BI | [Power BI Rest API 제한](https://msdn.microsoft.com/library/dn950053.aspx) 참조 |
| Azure 테이블 저장소 | [Azure Storage 용량 한도](../azure-subscription-service-limits.md#storage-limits) 참조 | 기본값은 단일 트랜잭션당 100개의 엔터티이며 필요에 따라 작은 값으로 구성할 수 있습니다. |
| Azure Service Bus 큐   | 메시지당 256KB</br> 참고 항목 [Service Bus 한도](../service-bus-messaging/service-bus-quotas.md) | 메시지당 단일 이벤트 |
| Azure Service Bus 항목 | 메시지당 256KB</br> 참고 항목 [Service Bus 한도](../service-bus-messaging/service-bus-quotas.md) | 메시지당 단일 이벤트 |
| Azure Cosmos DB   | [Azure Cosmos DB 제한](../azure-subscription-service-limits.md#azure-cosmos-db-limits) 참조 | 일괄 처리 크기 및 쓰기 빈도는 CosmosDB 응답에 따라 동적으로 조정됩니다. </br> Stream Analytics에서 미리 결정된 제한이 없습니다. |
| Azure 기능   | | 기본 일괄 처리 크기는 246KB입니다. </br> 일괄 처리당 기본 이벤트 수는 100입니다. </br> 일괄 처리 크기는 구성 가능하며 Stream Analytics [출력 옵션](#azure-functions)에서 증가 또는 감소될 수 있습니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
