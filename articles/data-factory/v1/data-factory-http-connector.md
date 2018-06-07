---
title: HTTP 소스에서 데이터 이동 - Azure | Microsoft Docs
description: Azure Data Factory를 사용하여 온-프레미스 또는 클라우드 HTTP 소스에서 데이터를 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9820ed9b4c0abbb79c6f92e62f294fb7fbd4c87e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Azure Data Factory를 사용하여 HTTP 소스에서 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](data-factory-http-connector.md)
> * [버전 2 - 미리 보기](../connector-http.md)

> [!NOTE]
> 이 문서는 GA(일반 공급) 상태인 Data Factory 버전 1에 적용됩니다. 미리 보기 상태인 Data Factory 버전 2 서비스를 사용 중인 경우 [V2의 HTTP 커넥터](../connector-http.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스/클라우드 HTTP 끝점의 데이터를 지원되는 싱크 데이터 저장소로 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소를 원본/싱크로 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 Data Factory는 HTTP 소스에서 다른 데이터 저장소로 데이터 이동이 아닌 다른 데이터 저장소에서 HTTP 대상으로 데이터 이동만을 지원합니다.

## <a name="supported-scenarios-and-authentication-types"></a>지원되는 시나리오 및 인증 형식
이 HTTP 커넥터에서 HTTP **GET** 또는 **POST** 메서드를 사용하여 **클라우드 및 온-프레미스 HTTP/s 끝점**에서 데이터를 검색할 수 있습니다. 다음 인증 형식이 지원됩니다. **Anonymous**, **Basic**, **Digest**, **Windows** 및 **ClientCertificate**. 이 커넥터와 [웹 테이블 커넥터](data-factory-web-table-connector.md) 간이 차이는 후자는 웹 HTML 페이지에서 테이블 콘텐츠를 추출하는 데 사용됩니다.

온-프레미스 HTTP 끝점에서 데이터를 복사할 때 온-프레미스 환경/Azure VM에서 데이터 관리 게이트웨이를 설치해야 합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 HTTP 원본의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

- 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

- 또한 **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API**를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. HTTP 소스에서 Azure Blob Storage로 데이터를 복사하는 JSON 샘플은 이 문서의 [JSON 예제](#json-examples) 섹션을 참조하세요.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 표는 HTTP 연결 서비스에 특정한 JSON 요소에 대한 설명을 제공합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 | type 속성을 `Http`로 설정해야 합니다. | 예 |
| URL | 웹 서버에 대한 기본 URL입니다. | 예 |
| authenticationType | 인증 유형을 지정합니다. 허용되는 값: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> 각 인증 형식에 대한 더 많은 속성 및 JSON 샘플은 표 아래 섹션을 참조하세요. | 예 |
| enableServerCertificateValidation | 소스가 HTTPS 웹 서버인 경우 서버 SSL 인증서 유효성 검사를 사용할지 지정합니다. | 아니요. 기본값은 True입니다. |
| gatewayName | 온-프레미스 HTTP 소스에 연결하기 위한 데이터 관리 게이트웨이의 이름입니다. | 온-프레미스 HTTP 소스에서 데이터를 복사하는 경우에는 필수입니다. |
| encryptedCredential | HTTP 끝점 액세스를 위한 암호화된 자격 증명입니다. 복사 마법사 또는 ClickOnce 팝업 대화 상자에서 인증 정보를 구성할 때 자동 생성됩니다. | 번호 온-프레미스 HTTP 서버에서 데이터를 복사하는 경우에만 적용됩니다. |

온-프레미스 HTTP 커넥터 데이터 원본의 자격 증명을 설정하는 방법에 대한 자세한 내용은 [데이터 관리 게이트웨이를 사용하여 온-프레미스 원본과 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

### <a name="using-basic-digest-or-windows-authentication"></a>Basic, Digest 또는 Windows 인증 사용

`authenticationType`을 `Basic`, `Digest` 또는 `Windows`로 설정하고, 위에서 소개한 HTTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 사용자 이름 | HTTP 끝점 액세스를 위한 사용자 이름입니다. | 예 |
| 암호 | 사용자(사용자 이름) 암호. | 예 |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>예: Basic, Digest 또는 Windows 인증

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate 인증 사용

기본 인증을 사용하려면 `authenticationType`을 `ClientCertificate`로 설정하고, 위에서 소개한 HTTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| embeddedCertData | PFX(개인 정보 교환) 파일의 이진 데이터에 대해 Base64로 인코딩된 콘텐츠 | `embeddedCertData` 또는 `certThumbprint`를 지정합니다. |
| certThumbprint | 게이트웨이 컴퓨터의 인증서 저장소에 설치된 인증서의 지문입니다. 온-프레미스 HTTP 소스에서 데이터를 복사하는 경우에만 적용됩니다. | `embeddedCertData` 또는 `certThumbprint`를 지정합니다. |
| 암호 | 인증서와 연결된 암호입니다. | 아니오 |

인증에 `certThumbprint`를 사용하고 인증서가 로컬 컴퓨터의 개인 저장소에 설치된 경우 게이트웨이 서비스에 읽기 권한을 부여해야 합니다.

1. MMC(Microsoft Management Console)를 시작합니다. **로컬 컴퓨터**를 대상으로 하는 **인증서** 스냅인을 추가합니다.
2. **인증서**, **개인**을 확장하고 **인증서**를 클릭합니다.
3. 개인 저장소에서 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**->**개인 키 관리...**를 선택합니다.
3. **보안** 탭에서 인증서에 대한 읽기 권한으로 데이터 관리 게이트웨이 호스트 서비스를 실행 중인 사용자 계정을 추가합니다.  

#### <a name="example-using-client-certificate"></a>예제: 클라이언트 인증서 사용
이 연결된 서비스는 데이터 팩터리를 온-프레미스 HTTP 웹 서버에 연결합니다. 데이터 관리 게이트웨이가 설치된 컴퓨터에 설치된 클라이언트 인증서를 사용합니다.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>예제: 파일로 클라이언트 인증서 사용
이 연결된 서비스는 데이터 팩터리를 온-프레미스 HTTP 웹 서버에 연결합니다. 데이터 관리 게이트웨이가 설치된 컴퓨터에서 클라이언트 인증서 파일을 사용합니다.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성
데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **Http** 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 형식을 지정했습니다. `Http`로 설정해야 합니다. | 예 |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. <br><br> 동적 URL을 생성하려면 [데이터 팩터리 함수 및 시스템 변수](data-factory-functions-variables.md)를 사용할 수 있습니다(예: "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"). | 아니요 |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **GET** 또는 **POST**입니다. | 번호 기본값은 `GET`입니다. |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 아니오 |
| requestBody | HTTP 요청의 본문입니다. | 아니요 |
| format | 데이터를 구문 분석하지 않고 **HTTP 끝점에서 데이터를 그대로 검색**하려면 이 서식 설정을 건너뜁니다. <br><br> 복사 중에 HTTP 응답 내용을 구문 분석하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 서식 유형이 지원됩니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. |아니오 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다. 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |아니오 |

### <a name="example-using-the-get-default-method"></a>예제: GET(기본) 메서드 사용

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>예제: POST 메서드 사용

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

현재 복사 작업의 원본이 **HttpSource** 형식인 경우 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(TimeSpan)입니다. 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. | 번호 기본값: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>지원되는 파일 및 압축 형식
자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md) 문서를 참조하세요.

## <a name="json-examples"></a>JSON 예
다음 예제에서는 [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. HTTP 원본에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 **여기**에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>예제: HTTP 원본에서 Azure Blob Storage로 데이터 복사
이 샘플에 대한 Data Factory 솔루션은 다음 Data Factory 엔터티를 포함합니다.

1. [HTTP](#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [Http](#dataset-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5. [HttpSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 HTTP 소스의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

### <a name="http-linked-service"></a>HTTP 연결된 서비스
이 예제에서는 익명 인증으로 HTTP 연결된 서비스를 사용합니다. 사용할 수 있는 다른 유형의 인증은 [HTTP 연결 서비스](#linked-service-properties) 섹션을 참조하세요.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP 데이터 집합
**external**을 **true**로 설정하면 데이터 집합이 Data Factory의 외부에 있고 Data Factory의 활동으로 생성되지 않는다고 Data Factory 서비스에 전달됩니다.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 집합

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회)

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>복사 작업을 포함하는 파이프라인

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **HttpSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

HttpSource에서 지원하는 속성 목록은 [HttpSource](#copy-activity-properties)를 참조하세요.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> 원본 데이터 집합의 열을 싱크 데이터 집합의 열로 매핑하려면 [Azure Data Factory의 데이터 집합 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
