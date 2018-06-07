---
title: HDInsight에서 Pig와 함께 DataFu 사용 - Azure | Microsoft Docs
description: DataFu는 Hadoop과 함께 사용하기 위한 라이브러리의 컬렉션입니다. HDInsight 클러스터에서 Pig와 함께 DataFu를 사용하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 30243d0b7db41fbe19c60d6c11d56fb7e801797b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>HDInsight에서 pig와 함께 DataFu 사용

HDInsight과 함께 DataFu를 사용하는 방법에 대해 알아봅니다. DataFu는 Hadoop에서 Pig와 함께 사용하기 위한 오픈 소스 라이브러리의 컬렉션입니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독.

* Azure HDInsight 클러스터(Linux 또는 Windows 기반)

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Linux 기반 HDInsight에 DataFu 설치

> [!IMPORTANT]
> DataFu는 Linux 기반 클러스터 버전 3.3 이상 및 Windows 기반 클러스터에 설치됩니다. 3.3 이전 버전의 Linux 기반 클러스터에 설치되지 않습니다.
>
> Windows 기반 또는 Linux 기반 클러스터 버전 3.3 이상 클러스터를 사용하는 경우 이 섹션을 건너뛸 수 있습니다.

Maven 리포지토리에서 DataFu를 다운로드하고 설치할 수 있습니다. 다음 단계를 따라 DataFu를 HDInsight 클러스터에 추가합니다.

1. SSH를 사용하여 Linux 기반 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 wget 유틸리티를 사용하여 DataFu jar 파일을 다운로드하거나 브라우저에 링크를 복사하여 붙여 넣어 다운로드를 시작합니다.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. 그런 다음 HDInsight 클러스터의 기본 저장소에 파일을 업로드합니다. 기본 저장소에 파일을 배치하면 클러스터의 모든 노드에 사용할 수 있습니다.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > 이전 명령은 이 디렉터리가 클러스터 저장소에 이미 존재하기 때문에 jar 파일을 `/example/jars`에 저장합니다. HDInsight 클러스터 저장소에 원하는 모든 위치를 사용할 수 있습니다.

## <a name="use-datafu-with-pig"></a>Pig와 함께 DataFu 사용

이 섹션의 단계에서는 HDInsight에서 Pig 사용에 익숙하다고 가정합니다. HDInsight와 함께 Pig 사용에 대한 자세한 내용은 [Pig와 함께 HDInsight 사용](hdinsight-use-pig.md)을 참조하세요.

> [!IMPORTANT]
> 이전 섹션의 단계에 따라 DataFu를 수동으로 설치한 경우 사용하기 전에 등록해야 합니다.
>
> * 클러스터에서 Azure Storage를 사용하는 경우 `wasb://` 경로를 사용합니다. 예: `register wasb:///example/jars/datafu-1.2.0.jar`
>
> * 클러스터에서 Azure Data Lake Store를 사용하는 경우 `adl://` 경로를 사용합니다. 예: `register adl://home/example/jars/datafu-1.2.0.jar`

종종 DataFu 함수에 대한 별칭을 정의합니다. 다음 예제에서는 `SHA`의 별칭을 정의합니다.

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

그러면 이 별칭을 Pig Latin 스크립트에서 사용하여 입력 데이터에 대한 해시를 생성할 수 있습니다. 예를 들어 다음 코드는 입력 데이터의 위치를 해시 값으로 대체합니다.

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

다음 출력이 생성됩니다.

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>다음 단계

DataFu 또는 Pig에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Apache DataFu Pig 가이드](http://datafu.incubator.apache.org/docs/datafu/guide.html)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
