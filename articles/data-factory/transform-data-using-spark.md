---
title: Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환 | Microsoft Docs
description: Spark 작업을 사용하여 Azure Data Factory 파이프라인에서 Spark 프로그램을 실행함으로써 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 52b6489d14c016ce2efdd06614102a40651b94c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-spark.md)
> * [버전 2 - 미리 보기](transform-data-using-spark.md)

Data Factory [파이프라인](concepts-pipelines-activities.md)에서 Spark 작업은 [사용자 고유](compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](transform-data.md) 문서에서 작성합니다. 주문형 Spark 연결 서비스를 사용하면 Data Factory에서는 적시에 데이터를 처리할 수 있도록 자동으로 Spark 클러스터를 만든 다음, 처리가 완료되면 클러스터를 삭제합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Spark 작업](v1/data-factory-spark.md)을 참조하세요.

> [!IMPORTANT]
> Spark 활동은 기본 저장소로 Azure Data Lake Store를 사용하는 HDInsight Spark 클러스터를 지원하지 않습니다.

## <a name="spark-activity-properties"></a>Spark 활동 속성
Spark 작업의 샘플 JSON 정의는 다음과 같습니다.    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다.

| 자산              | 설명                              | 필수 |
| --------------------- | ---------------------------------------- | -------- |
| 이름                  | 파이프라인의 작업 이름입니다.    | 예      |
| description           | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 아니오       |
| 형식                  | Spark 작업의 경우 작업 유형은 HDInsightSpark입니다. | 예      |
| linkedServiceName     | Spark 프로그램이 실행되는 HDInsight Spark 연결된 서비스의 이름입니다. 이 연결된 서비스에 대한 자세한 내용은 [연결된 Compute Services](compute-linked-services.md) 문서를 참조하세요. | 예      |
| SparkJobLinkedService | Spark 작업 파일, 종속성 및 로그를 보유하는 Azure Storage 연결된 서비스입니다.  이 속성에 대한 값을 지정하지 않으면 HDInsight 클러스터와 연결된 저장소가 사용됩니다. 이 속성의 값은 Azure Storage 연결된 서비스만 될 수 있습니다. | 아니오       |
| rootPath              | Spark 파일이 포함된 Azure Blob 컨테이너 및 폴더입니다. 파일 이름은 대/소문자를 구분합니다. 이 폴더의 구조에 대한 자세한 내용은 폴더 구조 섹션(다음 섹션)을 참조하세요. | 예      |
| entryFilePath         | Spark 코드/패키지의 루트 폴더에 대한 상대 경로입니다. | 예      |
| className             | 응용 프로그램의 Java/Spark main 클래스      | 아니오       |
| arguments             | Spark 프로그램에 대한 명령줄 인수 목록입니다. | 아니오       |
| proxyUser             | Spark 프로그램 실행을 가장하는 사용자 계정 | 아니오       |
| sparkConfig           | [Spark 구성 - 응용 프로그램 속성](https://spark.apache.org/docs/latest/configuration.html#available-properties) 항목에 나열된 Spark 구성 속성의 값을 지정합니다. | 아니오       |
| getDebugInfo          | sparkJobLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 Spark 로그 파일을 언제 복사할지 지정합니다. 허용되는 값: None, Always 또는 Failure. 기본값: None. | 아니오       |

## <a name="folder-structure"></a>폴더 구조
Spark 작업은 Pig/Hive 작업보다 확장성이 뛰어납니다. Spark 작업의 경우 jar 패키지(java CLASSPATH에 배치), python 파일(PYTHONPATH에 배치) 및 기타 파일과 같은 여러 종속성을 제공할 수 있습니다.

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 다음 폴더 구조를 만듭니다. 그런 다음 종속 파일을 **entryFilePath**로 표시되는 루트 폴더의 해당 하위 폴더에 업로드합니다. 예를 들어 python 파일을 루트 폴더의 pyFiles 하위 폴더에, jar 파일을 jars 하위 폴더에 업로드합니다. 런타임 시, Data Factory 서비스는 Azure Blob Storage에서 다음 폴더 구조를 필요로 합니다.     

| path                  | 설명                              | 필수 | type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(root)            | 저장소 연결된 서비스에서 Spark 작업의 루트 경로 | 예      | 폴더 |
| &lt;사용자 정의 &gt; | Spark 작업의 엔트리 파일을 가리키는 경로 | 예      | 파일   |
| ./jars                | 이 폴더 아래 모든 파일이 업로드되고 클러스터의 java classpath에 배치됨 | 아니오       | 폴더 |
| ./pyFiles             | 이 폴더 아래 모든 파일이 업로드되고 클러스터의 PYTHONPATH에 배치됨 | 아니오       | 폴더 |
| ./files               | 이 폴더 아래 모든 파일이 업로드되고 실행기 작업 디렉터리에 배치됨 | 아니오       | 폴더 |
| ./archives            | 이 폴더 아래 모든 파일이 압축 해제됨 | 아니오       | 폴더 |
| ./logs                | Spark 클러스터의 로그를 포함하는 폴더입니다. | 아니오       | 폴더 |

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 두 개의 Spark 작업 파일이 포함된 저장소에 대한 예는 다음과 같습니다.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch 실행 작업](transform-data-using-machine-learning.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
