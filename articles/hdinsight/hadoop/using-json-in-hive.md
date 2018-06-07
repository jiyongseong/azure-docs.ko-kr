---
title: Azure HDInsight에서 Apache Hive를 사용하여 JSON 문서 분석 및 처리 | Microsoft Docs
description: Azure HDInsight에서 Apache Hive를 사용하여 JSON 문서를 사용하고 분석하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 19d8e74e6f3ca8e7a0b0a64bdd782ea21b233c17
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Hive를 사용하여 JSON 문서 처리 및 분석

Azure HDInsight에서 Apache Hive를 사용하여 JSON(JavaScript Object Notation) 파일을 처리하고 분석하는 방법에 대해 알아봅니다. 이 자습서에서는 다음 JSON 문서를 사용합니다.

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

이 파일은 **wasb://processjson@hditutorialdata.blob.core.windows.net/** 에 있습니다. HDInsight에서 Azure Blob 저장소를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop을 사용하여 HDFS 호환 Azure Blob 저장소 사용](../hdinsight-hadoop-use-blob-storage.md)을 참조하세요. 클러스터의 기본 컨테이너에 파일을 복사할 수 있습니다.

이 자습서에서는 Hive 콘솔을 사용합니다. Hive 콘솔을 여는 방법에 대한 지침은 [원격 데스크톱을 사용하여 HDInsight에서 Hadoop과 Hive 사용](apache-hadoop-use-hive-remote-desktop.md)을 참조하세요.

## <a name="flatten-json-documents"></a>JSON 문서 평면화
다음 섹션에 나열된 메서드에서는 JSON 문서가 단일 행으로 구성되어야 합니다. 따라서 JSON 문서를 문자열로 평면화해야 합니다. JSON 문서가 이미 평면화되어 있으면 이 단계를 건너뛰고 다음 섹션인 JSON 데이터 분석으로 바로 이동할 수 있습니다. JSON 문서를 평면화하려면 다음 스크립트를 실행합니다.

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

원시 JSON 파일은 **wasb://processjson@hditutorialdata.blob.core.windows.net/** 에 있습니다. **StudentsRaw** Hive 테이블은 평면화되지 않은 원시 JSON 문서를 가리킵니다.

**StudentsOneLine** Hive 테이블은 HDInsight 기본 파일 시스템에서 데이터를 **/json/students/** 경로에 저장합니다.

**INSERT** 문은 **StudentOneLine** 테이블을 평면화된 JSON 데이터로 채웁니다.

**SELECT** 문은 한 행만 반환합니다.

**SELECT** 문의 출력은 다음과 같습니다.

![JSON 문서 평면화][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Hive에서 JSON 문서 분석
Hive는 JSON 문서에서 쿼리를 실행할 수 있는 서로 다른 세 가지 메커니즘을 제공하거나 직접 작성할 수 있습니다.

* get_json_object UDF(사용자 정의 함수) 사용
* json_tuple UDF 사용
* 사용자 지정 Serializer/Deserializer(SerDe) 사용
* Python 또는 다른 언어를 사용하여 사용자 고유의 UDF 작성 Hive를 사용하여 사용자 고유의 Python 코드를 실행하는 방법에 대한 자세한 내용은 [Apache Hive와 Pig에서 Python UDF 사용][hdinsight-python]을 참조하세요.

### <a name="use-the-getjsonobject-udf"></a>get_json_object UDF 사용
Hive는 런타임에 JSON 쿼리를 수행할 수 있는 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)라는 기본 제공 UDF를 제공합니다. 이 메서드에는 평면화된 JSON 문서와 구문 분석해야 하는 JSON 필드가 있는 두 개의 인수, 즉 테이블 이름 및 메서드 이름이 사용됩니다. 이 UDF의 작동 방식에 대한 예를 살펴보겠습니다.

다음 쿼리는 각 학생의 이름과 성을 반환합니다.

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

콘솔 창에서 이 쿼리를 실행하는 경우 출력은 다음과 같습니다.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get-json_object UDF에는 다음과 같은 제한 사항이 있습니다.

* 쿼리의 각 필드는 쿼리를 다시 구문 분석하는 데 필요하므로 성능에 영향을 줍니다.
* **GET\_JSON_OBJECT()** 는 배열의 문자열 표현을 반환합니다. 이 배열을 Hive 배열로 변환하려면 정규식을 사용하여 대괄호("[" 및 "]")를 바꾼 다음 분할된 호출을 통해 배열을 가져와야 합니다.

따라서 Hive Wiki에서는 json_tuple을 사용하도록 권장합니다.  

### <a name="use-the-jsontuple-udf"></a>json_tupl UDF 사용
Hive에서 제공하는 다른 UDF는 [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)보다 성능이 뛰어난 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)입니다. 이 메서드는 키 집합 및 JSON 문자열을 사용하며, 하나의 함수를 통해 값의 튜플을 반환합니다. 다음 쿼리는 JSON 문서에서 학생 ID와 등급을 반환합니다.

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Hive 콘솔에 표시되는 이 스크립트의 출력은 다음과 같습니다.

![json_tuple UDF][image-hdi-hivejson-jsontuple]

json_tuple UDF는 Hive에서 [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 구문을 사용합니다. 이 구문을 사용하면 json\_tuple에서 원래 테이블의 각 행에 UDT 함수를 적용하여 가상 테이블을 만들 수 있습니다. **LATERAL VIEW**를 반복적으로 사용하기 때문에 복잡한 JSON을 다루기가 너무 어려워집니다. 또한 **JSON_TUPLE**은 중첩된 JSON을 처리할 수 없습니다.

### <a name="use-a-custom-serde"></a>사용자 지정 SerDe 사용
SerDe는 중첩된 JSON 문서를 구문 분석하기 위한 최상의 선택입니다. 이를 통해 JSON 스키마를 정의한 다음 이 스키마를 사용하여 문서를 구문 분석할 수 있습니다. 자세한 내용은 [Microsoft Azure HDInsight에서 사용자 지정 JSON SerDe를 사용하는 방법](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)을 참조하세요.

## <a name="summary"></a>요약
결론적으로 선택하는 하이브의 JSON 연산자 유형은 시나리오에 따라 달라집니다. 간단한 JSON 문서가 있고 조회할 필드가 하나뿐인 경우 get_json_object Hive UDF를 사용하도록 선택할 수 있습니다. 조회가 키가 두 개 이상인 경우에는 json_tuple을 사용할 수 있습니다. 중첩된 문서가 있는 경우에는 JSON SerDe를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

관련 문서는 다음을 참조하세요.

* [샘플 Apache log4j 파일 분석을 위해 HDInsight에서 Hadoop과 함께 Hive 및 HiveQL 사용](../hdinsight-use-hive.md)
* [HDInsight의 Hive를 사용하여 비행 지연 데이터 분석](../hdinsight-analyze-flight-delay-data.md)
* [HDInsight에서 Hive를 사용하여 Twitter 데이터 분석](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

