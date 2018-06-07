---
title: Ambari REST API를 사용하여 Hadoop 모니터링 및 관리 - Azure HDInsight | Microsoft Docs
description: Ambari를 사용하여 Azure HDInsight에서 Hadoop 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다. 이 문서에서는 HDInsight 클러스터에 포함된 Ambari REST API를 사용하는 방법을 배웁니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 55017b139ce89fa74a8105da05792024ecee86b2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Ambari REST API를 사용하여 HDInsight 클러스터 관리

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Ambari REST API를 사용하여 Azure HDInsight에서 Hadoop 클러스터를 관리하고 모니터링하는 방법에 대해 알아봅니다.

Apache Ambari는 손쉬운 웹 UI 및 REST API 사용을 제공하여 Hadoop 클러스터의 관리 및 모니터링을 간소화합니다. Ambari는 Linux 운영 체제를 사용하는 HDInsight 클러스터에 포함됩니다. Ambari를 사용하여 클러스터를 모니터링하고 구성을 변경할 수 있습니다.

## <a id="whatis"></a>Ambari 정의

[Apache Ambari](http://ambari.apache.org)는 Hadoop 클러스터를 관리 및 모니터링하는 데 사용되는 웹 UI를 제공합니다. 개발자는 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 사용하여 자신의 응용 프로그램에 이러한 기능을 통합할 수 있습니다.

Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다.

## <a name="how-to-use-the-ambari-rest-api"></a>Ambari REST API 사용 방법

> [!IMPORTANT]
> 이 문서의 정보 및 예제에는 Linux 운영 체제를 사용하는 HDInsight 클러스터가 필요합니다. 자세한 내용은 [HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

이 문서의 예제는 Bourne 셸(bash) 및 PowerShell 둘 다로 제공됩니다. bash 예제는 GNU bash 버전 4.3.11로 테스트되었으나 다른 Unix 셸에서도 작동됩니다. PowerShell 예제는 PowerShell 5.0으로 테스트되었으나 PowerShell 3.0 이상에서 작동해야 합니다.

__Bourne 셸__(Bash)을 사용하는 경우에는 다음이 설치되어 있어야 합니다.

* [cURL](http://curl.haxx.se/): cURL은 명령줄에서 REST API와 함께 작동하도록 사용할 수 있는 유틸리티입니다. 이 문서에서 Ambari REST API와 통신하는데 사용됩니다.

Bash 또는 PowerShell을 사용할 경우 [jq](https://stedolan.github.io/jq/)도 설치되어 있어야 합니다. Jq는 JSON 문서를 사용하기 위한 유틸리티입니다. 이 유틸리티는 **모든** Bash 예제와 PowerShell 예제 중 **하나**에서 사용됩니다.

### <a name="base-uri-for-ambari-rest-api"></a>Ambari REST API의 기본 URI

HDInsight의 Ambari REST API에 대한 기본 URI는 https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME입니다. 여기서 **CLUSTERNAME**은 클러스터의 이름입니다.

> [!IMPORTANT]
> URI의 FQDN(정규화된 도메인 이름) 부분에 있는 클러스터 이름(CLUSTERNAME.azurehdinsight.net)은 대/소문자를 구분하지 않지만 URI의 다른 항목은 대/소문자를 구분합니다. 예를 들어 클러스터 이름이 `MyCluster`인 경우 올바른 URI는 다음과 같습니다.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> 이름의 두 번째 항목에 대/소문자가 잘못되었기 때문에 다음 URI는 오류를 반환합니다.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>인증

HTTPS를 요구하는 HDInsight에서 Ambari로 연결 클러스터 만들기 중 입력한 관리자 계정 이름(기본값은 **admin**) 및 암호를 사용합니다.

## <a name="examples-authentication-and-parsing-json"></a>예제: 인증 및 JSON 구문 분석

다음 예제에서는 기본 Ambari REST API에 대해 GET 요청을 수행하는 방법을 보여 줍니다.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> 이 문서의 Bash 예제는 다음과 같이 가정합니다.
>
> * 클러스터의 로그인 이름 기본값은 `admin`입니다.
> * `$CLUSTERNAME`에는 클러스터의 이름이 포함됩니다. `set CLUSTERNAME='clustername'`을 사용하여 이 값을 설정할 수 있습니다.
> * 메시지가 표시되면 클러스터 로그인(관리자)에 대한 암호를 입력합니다.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> 이 문서의 PowerShell 예제는 다음과 같이 가정합니다.
>
> * `$creds`는 클러스터에 대한 관리자 로그인 및 암호를 포함하는 자격 증명 개체입니다. `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"`을 사용하고 자격 증명을 제공하라는 메시지가 표시되면 제공하여 이 값을 설정할 수 있습니다.
> * `$clusterName`은 클러스터의 이름을 포함하는 문자열입니다. `$clusterName="clustername"`을 사용하여 이 값을 설정할 수 있습니다.

두 예제 모두 다음과 비슷한 정보로 시작되는 JSON 문서를 반환합니다.

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>JSON 데이터 구문 분석

다음 예제에서는 `jq`를 사용하여 JSON 응답 문서를 구문 분석하고 결과 중에서 `health_report` 문서만 표시합니다.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 이상에서는 JSON 문서를 PowerShell에서 보다 쉽게 사용할 수 있는 개체로 변환하는 `ConvertFrom-Json` cmdlet을 제공합니다. 다음 예제에서는 `ConvertFrom-Json`을 사용하여 결과 중에서 `health_report` 정보만 표시합니다.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> 이 문서의 예제 대부분은 `ConvertFrom-Json`을 사용하여 응답 문서의 요소를 표시하고 [Ambari 구성 업데이트](#example-update-ambari-configuration) 예제는 jq를 사용합니다. Jq는 JSON 응답 문서에서 새 템플릿을 생성하기 위해 이 예제에서 사용됩니다.

REST API의 모든 참조 문서를 보려면 [Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>예: 클러스터 노드의 FQDN 가져오기

HDInsight에서 작업할 때 클러스터 노드의 정규화된 도메인 이름(FQDN)에 대해 알아야 할 수도 있습니다. 다음 예제를 사용하여 클러스터의 다양한 노드에 대한 FQDN을 쉽게 검색할 수 있습니다.

* **모든 노드**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **헤드 노드**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **작업자 노드**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Zookeeper 노드**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>예제: 클러스터 노드의 내부 IP 주소 가져오기

> [!IMPORTANT]
> 이 섹션의 예제에서 반환된 IP 주소는 인터넷을 통해 직접 액세스할 수 없습니다. HDInsight 클러스터를 포함하는 Azure Virtual Network 내에서만 액세스할 수 있습니다.
>
> HDInsight 및 가상 네트워크 작업에 대한 자세한 내용은 [사용자 지정 Azure Virtual Network를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

IP 주소를 찾으려면 클러스터 노드의 내부 FQDN(정규화된 도메인 이름)을 알아야 합니다. FQDN을 알고 있으므로 호스트의 IP 주소를 얻을 수 있습니다. 다음 예제에서는 먼저 Ambari를 쿼리하여 모든 호스트 노드의 FQDN을 알아낸 다음 다시 Ambari를 쿼리하여 각 호스트의 IP 주소를 알아냅니다.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> 앞의 예제에서는 암호를 묻는 메시지를 표시합니다. 이 예제에서는 `curl` 명령을 여러 번 실행하므로 프롬프트가 여러 번 표시되지 않도록 하기 위해 `$PASSWORD` 암호가 제공됩니다.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>예제: 기본 저장소 가져오기

HDInsight 클러스터를 만드는 경우 Azure Storage 계정 또는 Data Lake Store를 클러스터에 대한 기본 저장소로 사용해야 합니다. 클러스터를 만든 후 Ambari를 사용하여 이 정보를 검색할 수 있습니다. 예를 들어 HDInsight 외부 컨테이너에 데이터를 읽고 쓰려는 경우가 여기에 해당합니다.

다음 예제에서는 클러스터에서 기본 저장소 구성을 검색합니다.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> 이러한 예제는 서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환하며 이 정보를 포함합니다. 클러스터를 만든 후에 수정된 값을 검색하는 경우 구성 버전을 나열하고 최신 버전을 검색해야 합니다.

반환 값은 다음 예제 중 하나와 유사합니다.

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - 이 값은 클러스터에서 기본 저장소에 Azure Storage 계정을 사용하고 있음을 나타냅니다. `ACCOUNTNAME` 값은 저장소 계정의 이름입니다. `CONTAINER` 부분은 저장소 계정에서 blob 컨테이너의 이름입니다. 이 컨테이너는 클러스터에 대한 HDFS 호환 저장소의 루트입니다.

* `adl://home` - 이 값은 클러스터가 기본 저장소에 Azure Data Lake Store를 사용하고 있음을 나타냅니다.

    Data Lake Store 계정 이름을 찾으려면 다음 예제를 사용합니다.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    반환 값은 `ACCOUNTNAME.azuredatalakestore.net`과 비슷합니다. 여기서 `ACCOUNTNAME`은 Data Lake Store 계정의 이름입니다.

    Data Lake Store 내에서 클러스터에 대한 저장소를 포함하는 디렉터리를 찾으려면 다음 예제를 사용합니다.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    반환 값은 `/clusters/CLUSTERNAME/`과 비슷합니다. 이 값은 Data Lake Store 계정 내의 경로입니다. 이 경로는 클러스터에 대한 HDFS 호환 파일 시스템의 루트입니다. 

> [!NOTE]
> [Azure PowerShell](/powershell/azure/overview)에서 제공하는 `Get-AzureRmHDInsightCluster` cmdlet 또한 클러스터에 대한 저장소 정보를 반환합니다.


## <a name="example-get-configuration"></a>예제: 구성 가져오기

1. 클러스터에 사용할 수 있는 구성을 가져옵니다.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    이 예제는 클러스터에 설치된 구성 요소에 대한 현재 구성이 포함된 JSON 문서(*tag* 값으로 식별됨)를 반환합니다. 다음 예제는 Spark 클러스터 형식에서 반환된 데이터에서 발췌한 것입니다.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. 관심 있는 구성 요소에 대한 구성을 가져옵니다. 다음 예제에서는 이전 요청에서 반환된 태그 값으로 `INITIAL`을 바꿉니다.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    이 예제는 `core-site` 구성 요소에 대한 현재 구성을 포함하는 JSON 문서를 반환합니다.

## <a name="example-update-configuration"></a>예제: 구성 업데이트

1. Ambari에서 "필요한 구성"으로 저장하는 현재 구성을 가져옵니다.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    이 예제는 클러스터에 설치된 구성 요소에 대한 현재 구성이 포함된 JSON 문서(*tag* 값으로 식별됨)를 반환합니다. 다음 예제는 Spark 클러스터 형식에서 반환된 데이터에서 발췌한 것입니다.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    이 목록에서 구성 요소의 이름(예: **spark\_thrift\_sparkconf** 및 **tag** 값을 복사해야 합니다.

2. 다음 명령을 사용하여 구성 요소 및 태그의 구성을 검색합니다.
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > **spark-thrift-sparkconf** 및 **INITIAL**을 검색할 구성 요소 및 태그로 바꿉니다.
   
    Jq는 HDInsight에서 검색한 데이터를 새 구성 템플릿으로 반환하는 데 사용됩니다. 특히, 이러한 예제에서는 다음 작업을 수행합니다.
   
    * 문자열 "version" 및 날짜를 포함하는 고유 값을 만듭니다. 이 값은 `newtag`에 저장됩니다.

    * 필요한 새 구성의 루트 문서를 만듭니다.

    * `.items[]` 배열의 내용을 가져와서 **desired_config** 요소에 추가합니다.

    * `href`, `version`, and `Config` 요소는 새 구성을 제출하는 데 필요하지 않으므로 삭제합니다.

    * 값이 `version#################`인 `tag` 요소를 추가합니다. 숫자 부분은 현재 날짜를 기반으로 합니다. 각 구성에 고유한 태그가 있어야 합니다.
     
    마지막으로 데이터가 `newconfig.json` 문서에 저장됩니다. 문서 구조는 다음 예제와 유사하게 표시되어야 합니다.
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. `newconfig.json` 문서를 열고 `properties` 개체의 값을 수정/추가합니다. 다음 예제는 `"spark.yarn.am.memory"` 값을 `"1g"`에서 `"3g"`로 변경합니다. 또한 값이 `"256m"`인 `"spark.kryoserializer.buffer.max"`를 추가합니다.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    수정을 완료했으면 파일을 저장합니다.

4. 다음 명령을 사용하여 업데이트된 구성을 Ambari에 제출합니다.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    이러한 명령은 **newconfig.json** 파일의 내용을 새 구성으로 클러스터에 제출합니다. 이 요청은 JSON 문서를 반환합니다. 이 문서의 **versionTag** 요소는 제출한 버전과 일치해야 하며, **configs** 개체에는 요청한 구성 변경 내용이 포함됩니다.

### <a name="example-restart-a-service-component"></a>예: 서비스 구성 요소 다시 시작

이제 새 구성을 적용하려면 먼저 Spark 서비스를 다시 시작해야 한다는 메시지가 Ambari 웹 UI에 표시됩니다. 다음 단계를 사용하여 서비스를 다시 시작합니다.

1. 다음을 사용하여 Spark 서비스에 대한 유지 관리 모드를 사용하도록 설정합니다.

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    이러한 명령은 서버에 JSON 문서를 보내 유지 관리 모드를 켭니다. 이제 다음 요청을 사용하여 서비스가 유지 관리 모드인지 확인할 수 있습니다.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    반환 값은 `ON`입니다.

2. 그런 후 다음을 사용하여 서비스를 해제합니다.

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    응답은 다음 예제와 유사합니다.
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > 이 URI에서 반환된 `href` 값은 클러스터 노드의 내부 IP 주소를 사용합니다. 클러스터 외부에서 이를 사용하려면 '10.0.0.18:8080' 부분을 클러스터의 FQDN으로 바꿉니다. 
    
    다음 명령은 요청의 상태를 검색합니다.

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    응답 `COMPLETED`는 요청이 완료되었음을 나타냅니다.

3. 이전 요청이 완료되면 다음을 사용하여 서비스를 시작합니다.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    이제 서비스는 새 구성을 사용합니다.

4. 마지막으로, 다음을 사용하여 유지 관리 모드를 해제합니다.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>다음 단계

REST API의 모든 참조 문서를 보려면 [Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.

