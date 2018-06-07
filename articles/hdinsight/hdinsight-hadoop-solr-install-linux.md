---
title: 스크립트 작업을 사용하여 Linux 기반 HDInsight에 Solr 설치 - Azure | Microsoft Docs
description: 스크립트 작업을 사용하여 Linux 기반 HDInsight Hadoop 클러스터에 Solr를 설치하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 77d3964dd54d63db58c63b567ebbe7e529473999
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201563"
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop 클러스터에서 Solr 설치 및 사용

스크립트 동작을 사용하여 Azure HDInsight에 Solr을 설치하는 방법에 대해 알아봅니다. Solr은 강력한 검색 플랫폼으로서 Hadoop에서 관리하는 데이터에 대한 엔터프라이즈 수준의 검색 기능을 제공합니다.

> [!IMPORTANT]
    > 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

> [!IMPORTANT]
> 이 문서에 사용된 샘플 스크립트는 특정 구성의 Solr 4.9를 설치합니다. 다른 컬렉션, 분할, 스키마, 복제 등으로 Solr 클러스터를 구성하려는 경우 이 스크립트와 Solr 바이너리를 수정해야 합니다.

## <a name="whatis"></a>Solr이란

[Apache Solr](http://lucene.apache.org/solr/features.html)은 데이터에 대한 강력한 전체 텍스트 검색을 가능하게 해주는 엔터프라이즈 검색 플랫폼입니다. Hadoop는 막대한 양의 데이터를 저장 및 관리할 수 있도록 해주고 Apache Solr은 이 데이터를 신속하게 검색할 수 있는 검색 기능을 제공합니다.

> [!WARNING]
> HDInsight 클러스터에 제공되는 구성 요소는 Microsoft에 완벽히 지원됩니다.
>
> Solr와 같은 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원은 사용자 지정 구성 요소의 문제를 해결하지 못할 수 있습니다. 도움을 받기 위해 오픈 소스 커뮤니티에 참여해야 할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/)).

## <a name="what-the-script-does"></a>스크립트가 수행하는 작업

이 스크립트는 HDInsight 클러스터에서 다음을 변경합니다.

* `/usr/hdp/current/solr`에 Solr 4.9 설치
* Solr 서비스를 실행하는 데 사용되는 사용자 **solrusr**을 만듭니다.
* **solruser**을 `/usr/hdp/current/solr`의 소유자로 설정합니다.
* Solr을 자동으로 시작하는 [Upstart](http://upstart.ubuntu.com/) 구성을 추가합니다.

## <a name="install"></a>스크립트 동작을 사용하여 Solr 설치

HDInsight 클러스터에서 Solr을 설치하는 샘플 스크립트는 다음 위치에서 사용할 수 있습니다.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Solr이 설치된 클러스터를 만들려면 [HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md) 문서의 단계를 사용합니다. 생성 프로세스 중 다음 단계를 사용하여 Solr을 설치합니다.

1. __클러스터 요약__ 섹션에서 __고급 설정__을 선택한 다음 __스크립트 동작__을 선택합니다. 양식에 다음 정보를 입력합니다.

   * **이름**: 스크립트 동작의 이름을 입력합니다.
   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: 이 옵션 선택
   * **WORKER**: 이 옵션을 선택합니다.
   * **ZOOKEEPER**: Zookeeper 노드에 설치하려면 이 옵션을 선택합니다.
   * **PARAMETERS**: 이 필드는 공백으로 둡니다.

2. **스크립트 동작** 섹션의 맨 아래에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **다음** 단추를 사용하여 __클러스터 요약__으로 돌아갑니다.

3. __클러스터 요약__ 페이지에서 __생성__을 선택하여 클러스터를 만듭니다.

## <a name="usesolr"></a>HDInsight에서 Solr을 사용하는 방법

> [!IMPORTANT]
> 이 섹션의 단계는 기본적 Solr 기능에 대해 설명합니다. Solr에 대한 자세한 내용은 [Apache Solr 사이트](http://lucene.apache.org/solr/)를 참조하십시오.

### <a name="index-data"></a>데이터 인덱싱

다음 단계를 사용하여 Solr에 예제 데이터를 추가한 다음 쿼리를 실행할 수 있습니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

    > [!NOTE]
    > `sshuser`를 클러스터의 SSH 사용자로 바꿉니다. `clustername`을 클러스터의 이름으로 바꿉니다.

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

     > [!IMPORTANT]
     > 이 문서의 뒷부분에 나오는 단계에서 SSH 터널을 사용하여 Solr 웹 UI에 연결합니다. 이러한 단계를 사용하려면 SSH 터널을 설정하고 브라우저를 구성하여 사용해야 합니다.
     >
     > 자세한 내용은 [HDInsight와 SSH 터널 사용](hdinsight-linux-ambari-ssh-tunnel.md) 문서를 참조하세요.

2. 다음 명령을 사용하여 Solr 인덱스 샘플 데이터를 포함합니다.

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    콘솔에 다음과 같은 출력이 반환됩니다.

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    `post.jar` 유틸리티는 인덱스에 **solr.xml** 및 **monitor.xml** 문서를 추가합니다.
  
3. 다음 명령을 사용하여 Solr REST API에 쿼리를 실행합니다.

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    이 명령은 **\*:\***(쿼리 문자열에서 \*%3A\*로 인코딩)와 일치하는 모든 문서에 대해 **collection1**을 검색합니다. 다음 JSON 문서는 응답의 예제입니다.

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Solr 대시보드 사용

Solr 대시보드는  웹 브라우저를 통해 Solr로 작업할 수 있는 웹 UI입니다. Solr 대시보드는 HDInsight 클러스터에서 인터넷에 직접 드러나지 않지만 SSH 터널을 사용하여 액세스할 수 있습니다. SSH 터널 사용에 대한 자세한 내용은 [HDInsight와 SSH 터널 사용](hdinsight-linux-ambari-ssh-tunnel.md) 문서를 참조하세요.

SSH 터널을 설정하면 다음 단계를 수행하여 Solr 대시보드를 사용합니다.

1. 기본 헤드 노드의 호스트 이름을 결정합니다.

   1. SSH를 사용하여 클러스터 헤드 노드에 연결합니다. 예: `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

       SSH를 사용하는 방법에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

   2. 다음 명령을 사용하여 정규화된 호스트 이름을 가져옵니다.

        ```bash
        hostname -f
        ```

        이 명령은 다음 호스트 이름과 유사한 값을 반환합니다.

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        반환된 값은 나중에 사용하므로 저장해 둡니다.

2. 브라우저에서 **http://HOSTNAME:8983/solr/#/** 에 연결하며, 여기서 **HOSTNAME**은 이전 단계에서 결정한 이름입니다.

    요청은 SSH 터널을 통해 클러스터의 Solr 웹 UI로 라우팅됩니다. 다음 이미지와 유사한 페이지가 나타납니다.

    ![Solr 대시보드의 이미지](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. 왼쪽 창에서 **코어 선택기** 드롭다운을 사용하여 **collection1**을 선택합니다. 여러 항목이 **collection1** 아래에 나타나야 합니다.

4. **collection1** 아래의 항목에서 **쿼리**를 선택합니다. 검색 페이지를 채우려면 다음 값을 사용합니다.

   * **q** 텍스트 상자에서 **\*:**\*을 입력합니다. 이 쿼리는 Solr에 인덱싱되는 모든 문서를 반환합니다. 문서 내에서 특정 문자열을 검색하려는 경우 여기에 해당 문자열을 입력할 수 있습니다.
   * **wt** 텍스트 상자에서 출력 형식을 선택합니다. 기본값은 **json**입니다.

     마지막으로 검색 페이지의 아래쪽에서 **쿼리 실행** 단추를 선택합니다.

     ![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     출력은 앞에서 인덱스에 추가한 두 문서를 반환합니다. 다음 JSON 문서와 유사하게 출력됩니다.

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Solr 시작 및 중지

다음 명령을 사용하여 Solr을 수동으로 중지하거나 시작합니다.

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>인덱싱된 데이터 Backup

Solr 데이터를 클러스터의 기본 저장소로 백업하려면 다음 단계를 사용합니다.

1. SSH를 사용하여 클러스터에 연결하고 다음 명령을 사용하여 헤드 노드에 호스트 이름을 가져옵니다.

    ```bash
    hostname -f
    ```

2. 다음 인덱싱된 데이터의 스냅샷을 만들려면 다음 명령을 사용합니다. **HOSTNAME**은 이전 명령에서 반환한 이름으로 바꿉니다.

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    응답은 다음 XML과 유사합니다.

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. 디렉터리를 `/usr/hdp/current/solr/example/solr`로 변경합니다. 여기에 각 컬렉션에 대한 하위 디텍터리가 있습니다. 각 컬렉션 디렉터리에는 컬렉션의 스냅샷이 포함된 `data` 디렉터리가 있습니다.

4. 스냅숏 폴더의 압축 보관 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    `snapshot.20150806185338855` 값을 컬렉션의 스냅샷 이름으로 바꿉니다.

    이 명령을 실행하면 **snapshot.20150806185338855** 디렉터리의 내용이 포함된 **snapshot.20150806185338855.tgz** 보관 파일이 생성됩니다.

5. 그러면 다음 명령을 사용하여 클러스터의 기본 저장소에 아카이브를 저장할 수 있습니다.

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Solr 백업 및 복원 작업에 대한 자세한 내용은 [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.

* [HDInsight 클러스터에서 Hue를 설치](hdinsight-hadoop-hue-linux.md)합니다. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 Hue를 설치합니다. Hue는 Hadoop 클러스터와 상호 작용하는 데 사용되는 웹 응용 프로그램 집합입니다.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
