---
title: Azure Data Factory를 사용하여 HDFS에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 클라우드 또는 온-프레미스 HDFS 원본에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: be60e79d9e5a57cd93a4aa57efdbb3e4c034d064
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010900"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Azure Data Factory를 사용하여 HDFS에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-hdfs-connector.md)
> * [버전 2 - 미리 보기](connector-hdfs.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 HDFS에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 HDFS 커넥터](v1/data-factory-hdfs-connector.md)를 참조하세요.


## <a name="supported-capabilities"></a>지원되는 기능

HDFS에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히 이 HDFS 커넥터는 다음을 지원합니다.

- **Windows**(Kerberos) 또는 **Anonymous** 인증을 사용하여 파일을 복사합니다.
- **webhdfs** 프로토콜 또는 **기본 제공 DistCp** 지원을 사용하여 파일을 복사합니다.
- 파일을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일을 붙여넣거나 생성합니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 HDFS 간 데이터를 복사하려면 자체 호스팅 Integration Runtime을 설정해야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임](concepts-integration-runtime.md) 문서를 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 HDFS에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

HDFS 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 다음으로 설정해야 함: **Hdfs** | 예 |
| URL |HDFS에 대한 URL |예 |
| authenticationType | 가능한 값은 **Anonymous** 또는 **Windows**입니다. <br><br> HDFS 커넥터에 **Kerberos 인증**을 사용하려면 [이 섹션](#use-kerberos-authentication-for-hdfs-connector)을 참조하여 온-프레미스 환경을 적절히 설정합니다. |예 |
| userName |Windows 인증에 대한 사용자 이름. Kerberos 인증의 경우 `<username>@<domain>.com`을 지정합니다. |예(Windows 인증에 대한) |
| 암호 |Windows 인증에 대한 암호. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예(Windows 인증에 대한) |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니오 |

**예제: 익명 인증 사용**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Windows 인증 사용**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 HDFS 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

HDFS에서 데이터를 복사하려면 데이터 집합의 형식 속성을 **FileShare**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 형식 속성을 **FileShare**로 설정해야 합니다. |예 |
| folderPath | 파일의 경로입니다. 와일드카드 필터는 지원되지 않습니다. 예: 폴더/하위 폴더/ |예 |
| fileName |  지정된 "folderPath" 아래의 파일에 대한 **이름 또는 와일드 카드 필터**입니다. 이 속성의 값을 지정하지 않으면 데이터 집합은 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 허용되는 와일드카드는 `*`(여러 문자) 및 `?`(단일 문자)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다. |아니오 |
| format | 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 구문 분석하려는 경우 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 파일 형식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [Json 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>지원되는 수준은 **최적** 및 **가장 빠름**입니다. |아니오 |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>지정된 이름의 단일 파일을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName**을 지정합니다.<br>폴더 아래에서 파일의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName**을 지정합니다.

**예제:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 HDFS 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="hdfs-as-source"></a>원본으로 HDFS

HDFS에서 데이터를 복사하려면 복사 작업의 원본 형식을 **HdfsSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성을 **HdfsSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive가 true로 설정되고 싱크가 파일 기반 저장소인 경우 싱크에서 빈 폴더/하위 폴더가 복사/생성되지 않습니다.<br/>허용되는 값은 **true**(기본값), **false**입니다. | 아니오 |
| distcpSettings | HDFS DistCp를 사용하는 경우 속성 그룹입니다. | 아니오 |
| resourceManagerEndpoint | Yarn ResourceManager 끝점 | 예(DistCp를 사용하는 경우) |
| tempScriptPath | 임시 DistCp 명령 스크립트를 저장하는 데 사용되는 폴더 경로입니다. 스크립트 파일이 Data Factory에 의해 생성되고 복사 작업을 완료한 후에 제거됩니다. | 예(DistCp를 사용하는 경우) |
| distcpOptions | DistCp 명령에 제공된 추가 옵션입니다. | 아니오 |

**예제: UNLOAD를 사용하여 복사 작업에서 HDFS 원본**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

다음 섹션에서 DistCp를 사용하여 HDFS에서 효율적으로 데이터를 복사하는 방법에 대해 알아봅니다.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp를 사용하여 HDFS에서 데이터 복사

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html)는 Hadoop 클러스터에 분산된 복사를 수행하는 Hadoop 네이티브 명령줄 도구입니다. Distcp 명령을 실행하는 경우 먼저 복사될 모든 파일을 나열하고, Hadoop 클러스터에 여러 맵 작업을 만들며 각 맵 작업은 원본에서 싱크로 이진 복사를 수행합니다.

복사 작업은 DistCp를 사용하여 파일을 있는 그대로 Azure Blob([준비된 복사](copy-activity-performance.md) 또는 Azure Data Lake Store 포함)으로 복사를 지원하며 이 경우 자체 호스팅 Integration Runtime에서 실행하는 대신 클러스터의 기능을 완벽하게 활용할 수 있습니다. 클러스터가 매우 강력한 경우에 특히 더 나은 복사 처리량을 제공합니다. Azure Data Factory의 구성에 따라 복사 작업은 자동으로 distcp 명령을 생성하고, Hadoop 클러스터에 제출하고, 복사 상태를 모니터링합니다.

### <a name="prerequsites"></a>필수 구성 요소

DistCp를 사용하여 HDFS에서 Azure Blob(준비된 복사 포함) 또는 Azure Data Lake Store로 파일을 있는 그대로 복사하려면 Hadoop 클러스터가 아래 요구 사항을 충족하는지 확인합니다.

1. MapReduce 및 Yarn 서비스가 활성화됩니다.
2. Yarn 버전은 2.5 이상입니다.
3. HDFS 서버는 대상 데이터 저장소 - Azure Blob 또는 Azure Data Lake Store와 통합됩니다.

    - Azure Blob 파일 시스템은 Hadoop 2.7부터 기본적으로 지원됩니다. Hadoop env config에서 jar 경로를 지정하기만 하면 됩니다.
    - Azure Data Lake Store 파일 시스템은 Hadoop 3.0.0-alpha1부터 패키지됩니다. Hadoop 클러스터가 해당 버전보다 낮은 경우 [여기](https://hadoop.apache.org/releases.html)에서 ADLS 관련 jar 패키지(azure-datalake-store.jar)를 클러스터로 수동으로 가져오고, Hadoop env config에서 jar 경로를 지정해야 합니다.

4. HDFS에서 임시 폴더를 준비합니다. 이 임시 폴더는 KB 수준 공간을 차지할 수 있도록 DistCp 셸 스크립트를 저장하는 데 사용됩니다.
5. HDFS 연결된 서비스에 제공된 사용자 계정이 a) Yarn에서 응용 프로그램을 제출하고, b) 위의 임시 폴더 아래에 하위 폴더, 읽기/쓰기 파일을 만드는 권한을 갖도록 합니다.

### <a name="configurations"></a>구성

다음은 DistCp를 사용하여 HDFS에서 Azure Blob으로 데이터를 복사하기 위한 복사 작업 구성의 예입니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS 커넥터에 Kerberos 인증 사용

HDFS 커넥터에 Kerberos 인증을 사용하도록 온-프레미스 환경을 설정하는 옵션은 두 가지가 있습니다. 이 중 더 잘 맞는 옵션을 선택할 수 있습니다.
* 옵션 1: [Kerberos 영역에 자체 호스팅 Integration Runtime 컴퓨터 가입](#kerberos-join-realm)
* 옵션 2: [Windows 도메인과 Kerberos 영역 사이에 상호 트러스트를 사용하도록 설정](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>옵션 1: Kerberos 영역에 자체 호스팅 Integration Runtime 컴퓨터 가입

#### <a name="requirements"></a>요구 사항

* 자체 호스팅 Integration Runtime 컴퓨터가 Kerberos 영역에 가입해야 하고 Windows 도메인에는 가입할 수 없습니다.

#### <a name="how-to-configure"></a>구성 방법

**자체 호스팅 Integration Runtime 컴퓨터에서:**

1.  **Ksetup** 유틸리티를 실행하여 Kerberos KDC 서버 및 영역을 구성합니다.

    Kerberos 영역은 Windows 도메인과 다르기 때문에 컴퓨터를 작업 그룹의 구성원으로 구성해야 합니다. 그러려면 다음과 같이 Kerberos 영역을 설정하고 KDC 서버를 추가합니다. *REALM.COM*은 고유한 각 영역으로 필요에 맞게 바꿉니다.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    이러한 2개 명령을 실행한 후 컴퓨터를 **다시 시작**합니다.

2.  **Ksetup** 명령을 사용하여 구성을 확인합니다. 출력은 다음과 같아야 합니다.

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory에서:**

* HDFS 데이터 원본에 연결하는 Kerberos 주체 이름 및 암호와 **Windows 인증**을 함께 사용하여 HDFS 커넥터를 구성합니다. 구성 세부 정보에서 [HDFS 연결된 서비스 속성](#linked-service-properties)을 확인합니다.

### <a name="kerberos-mutual-trust"></a>옵션 2: Windows 도메인과 Kerberos 영역 사이에 상호 트러스트를 사용하도록 설정

#### <a name="requirements"></a>요구 사항

*   자체 호스팅 Integration Runtime 컴퓨터는 Windows 도메인에 가입해야 합니다.
*   도메인 컨트롤러의 설정을 업데이트하는 권한이 있어야 합니다.

#### <a name="how-to-configure"></a>구성 방법

> [!NOTE]
> 다음 자습서에서 REALM.COM 및 AD.COM을 고유한 각 영역과 도메인 컨트롤러로 필요에 맞게 바꿉니다.

**KDC 서버에서:**

1.  다음 구성 템플릿을 참조하여 **krb5.conf** 파일에서 KDC가 Windows 도메인을 신뢰하도록 KDC 구성을 편집합니다. 기본적으로 이 구성은 **/etc/krb5.conf**에 있습니다.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **다시 시작** 구성 후에 KDC 서비스입니다.

2.  다음 명령을 사용하여 KDC 서버의 **krbtgt/REALM.COM@AD.COM**이라는 주체를 준비합니다.

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  **hadoop.security.auth_to_local** HDFS 서비스 구성 파일에서 `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`를 추가합니다.

**도메인 컨트롤러에서:**

1.  다음 **Ksetup** 명령을 실행하여 영역 항목을 추가합니다.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Windows 도메인에서 Kerberos 영역으로의 트러스트를 설정합니다. [password]는 **krbtgt/REALM.COM@AD.COM** 주체의 암호입니다.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Kerberos에서 사용되는 암호화 알고리즘을 선택합니다.

    1. 서버 관리자 > 그룹 정책 관리 > 도메인 > 그룹 정책 개체 > 기본 또는 활성 도메인 정책으로 이동하고 편집을 클릭합니다.

    2. **그룹 정책 관리 편집기** 팝업 창에서 컴퓨터 구성 > 정책 > Windows 설정 > 보안 설정 > 로컬 정책 > 보안 옵션으로 이동하고 **네트워크 보안: Kerberos에 허용된 암호화 유형 구성**을 구성합니다.

    3. KDC에 연결할 때 사용할 암호화 알고리즘을 선택합니다. 일반적으로 간단히 모든 옵션을 선택할 수 있습니다.

        ![Kerberos의 암호화 유형 구성](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. **Ksetup** 명령을 사용하여 특정 영역에서 사용할 암호화 알고리즘을 지정합니다.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Windows 도메인에서 Kerberos 주체를 사용하려면 도메인 계정 및 Kerberos 주체 간의 매핑을 만듭니다.

    1. 관리 도구 > **Active Directory 사용자 및 컴퓨터**를 시작합니다.

    2. **보기** > **고급 기능**을 클릭하여 고급 기능을 구성합니다.

    3. 매핑을 만들려는 계정을 찾고 마우스 오른쪽 단추를 클릭하여 **이름 매핑**을 본 후 **Kerberos 이름** 탭을 클릭합니다.

    4. 영역의 주체를 추가합니다.

        ![보안 ID 매핑](media/connector-hdfs/map-security-identity.png)

**자체 호스팅 Integration Runtime 컴퓨터에서:**

* 다음 **Ksetup** 명령을 실행하여 영역 항목을 추가합니다.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory에서:**

* HDFS 데이터 원본에 연결하는 도메인 계정 또는 Kerberos 주체와 **Windows 인증**을 함께 사용하여 HDFS 커넥터를 구성합니다. 구성 세부 정보에서 [HDFS 연결된 서비스 속성](#linked-service-properties)을 확인합니다.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.