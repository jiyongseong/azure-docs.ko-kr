---
title: Azure Functions를 사용하여 HDInsight의 Kafka로 데이터 보내기 | Microsoft Docs
description: Azure 함수를 사용하여 HDInsight의 Kafka에 데이터를 쓰는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: c657cda7dce0611cb357a0a2063a154f2f6f25f2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Azure 함수 앱에서 HDInsight의 Kafka사용

HDInsight의 Kafka에 데이터를 보내는 Azure 함수 앱을 만드는 방법을 알아봅니다.

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/)는 서버를 사용하지 않는 계산 서비스입니다. 인프라를 명시적으로 프로비전 또는 관리하지 않고도 필요 시 코드를 실행할 수 있습니다.

[Apache Kafka](https://kafka.apache.org)는 실시간 스트리밍 데이터 파이프라인과 응용 프로그램을 만드는 데 사용할 수 있는 오픈 소스 분산형 스트리밍 플랫폼입니다. 또한 Kafka는 명명된 데이터 스트림을 게시하고 구독할 수 있는 메시지 대기열과 비슷한 메시지 브로커 기능을 제공합니다. HDInsight의 Kafka는 Microsoft Azure 클라우드에서 관리되고 확장성이 뛰어난 고가용성 서비스를 제공합니다.

HDInsight의 Kafka 공용 인터넷에 API를 제공하지 않습니다. Kafka의 데이터를 게시하거나 사용하려면 Azure Virtual Network를 사용하여 Kafka 클러스터에 연결해야 합니다. Azure Functions는 Virtual Network 게이트웨이를 통해 HDInsight의 Kafka로 데이터를 푸시하는 공용 엔드포인트를 만드는 편리한 방법을 제공합니다.

> [!NOTE]
> 이 문서에서는 Azure Functions가 HDInsight의 Kafka와 통신하도록 설정하는 데 필요한 단계에 중점을 두고 있습니다. 예제 자체는 구성이 작동하는 것을 보여주는 기본적인 Kafka 생산자입니다.

## <a name="prerequisites"></a>필수 조건

* Azure 함수 앱. 자세한 내용은 [첫 번째 함수 만들기](../../azure-functions/functions-create-first-azure-function.md) 설명서를 참조하세요.

* Azure Virtual Network. Azure Functions를 사용하려면 가상 네트워크에서 다음 IP 범위 중 하나를 사용해야 합니다.

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    자세한 내용은 [Azure Virtual Network에 앱 통합](../../app-service/web-sites-integrate-with-vnet.md) 문서를 참조하세요.

* HDInsight 클러스터의 Kafka. HDInsight 클러스터에 Kafka 만들기에 대한 내용은 [Kafka 클러스터 만들기](apache-kafka-get-started.md) 문서를 참조하세요.

    > [!IMPORTANT]
    > 클러스터 구성 중에 __고급 설정__ 단계를 사용하여 HDInsight에 사용하는 Azure Virtual Network 및 서브넷을 선택해야 합니다. 이전에 단계에서 만든 가상 네트워크 및 서브넷을 선택합니다.

    Kafka 및 가상 네트워크에 대한 자세한 내용은 [가상 네트워크를 통해 Kafka에 연결](apache-kafka-connect-vpn-gateway.md) 문서를 참조하세요.

## <a name="architecture"></a>아키텍처

HDInsight의 Kafka는 Azure Virtual Network에 포함되어 있습니다. Azure Functions는 지점 및 사이트 간 게이트웨이를 사용하여 가상 네트워크와 통신할 수 있습니다. 다음 이미지는 이 네트워크 토폴로지의 다이어그램입니다.

![HDInsight에 연결하는 Azure Functions의 아키텍처](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Kafka 구성

이 섹션의 정보는 Kafka 클러스터가 Azure 함수의 데이터를 수락하도록 준비합니다. 다음 구성 작업을 설명합니다.

* IP 보급
* Kafka broker IP 주소 수집
* Kafka 토픽 만들기

### <a name="configure-kafka-for-ip-advertising"></a>IP 보급을 위해 Kafka 구성

기본적으로 Zookeeper는 Kafka 브로커의 도메인 이름을 클라이언트에 반환합니다. 이 구성은 클라이언트(Azure Functions)가 가상 네트워크의 이름을 확인할 수 없으므로 DNS 서버 없이는 작동하지 않습니다. 이 구성의 경우, 다음 단계에 따라 도메인 이름 대신 IP 주소를 보급하도록 Kafka를 구성합니다.

1. 웹 브라우저를 사용하여 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. __CLUSTERNAME__을 HDInsight 클러스터의 Kafka 이름으로 바꿉니다.

    메시지가 표시되면, 클러스터의 HTTPS 사용자 이름 및 암호를 입력합니다. 클러스터에 대한 Ambari Web UI가 표시됩니다.

2. Kafka에 대한 정보를 보려면 왼쪽 목록에서 __Kafka__를 선택합니다.

    ![Kafka가 강조 표시된 서비스 목록](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Kafka 구성을 보려면 위쪽 가운데에서 __Configs__를 선택합니다.

    ![Kafka에 대한 링크 구성](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. __kafka-env__ 구성을 찾으려면 오른쪽 위에 있는 __필터__ 필드에 `kafka-env`를 입력합니다.

    ![kafka-env의 Kafka 구성](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. IP 주소를 보급하도록 Kafka를 구성하려면 __kafka-env-template__ 맨 아래에 다음 텍스트를 추가합니다.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka에서 수신 대기하는 인터페이스를 구성하려면 오른쪽 위의 __필터__ 필드에 `listeners`를 입력합니다.

7. 모든 네트워크 인터페이스에서 수신 대기하도록 Kafka를 구성하려면 __수신기__ 필드의 값을 `PLAINTEXT://0.0.0.0:9092`로 변경합니다.

8. 구성 변경 내용을 저장하려면 __저장__ 단추를 사용합니다. 변경 내용을 설명하는 텍스트 메시지를 입력합니다. 변경 내용이 저장되면 __확인__을 선택합니다.

    ![구성 저장 단추](./media/apache-kafka-azure-functions/save-button.png)

9. Kafka를 다시 시작할 때 오류를 방지하려면 __서비스 작업__ 단추를 사용하여 __유지 관리 모드 켜기__를 선택합니다. 확인을 선택하여 이 작업을 완료합니다.

    ![유지 관리 모드 켜기가 강조 표시된 서비스 작업](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Kafka를 다시 시작하려면 __다시 시작__ 단추를 사용하고 __영향 받은 모든 항목 다시 시작__을 선택합니다. 다시 시작을 확인하고 작업이 완료되면 __확인__ 단추를 사용합니다.

    ![영향 받은 모든 항목 다시 시작이 강조 표시된 다시 시작 단추](./media/apache-kafka-azure-functions/restart-button.png)

11. 유지 관리 모드를 사용하지 않도록 설정하려면 __서비스 작업__ 단추를 사용하고 __유지 관리 모드 끄기__를 선택합니다. **확인**을 선택하여 이 작업을 완료합니다.

### <a name="get-the-kafka-broker-ip-address"></a>Kafka broker IP 주소 가져오기

다음 방법 중 하나를 사용하여 Kafka 클러스터에서 정규화된 도메인 이름(FQDN) 및 노드 IP 주소를 검색합니다.

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

이 명령에서는 `<resourcegroupname>`을 가상 네트워크가 포함된 Azure 리소스 그룹의 이름으로 가정합니다.

반환된 이름 목록에서 작업자 노드의 IP 주소를 선택합니다. 노드의 내부 정규화된 도메인 이름은 `wn`으로 시작됩니다. 이 IP 주소는 함수가 Kafka와 통신하는 데 사용됩니다.

### <a name="create-a-kafka-topic"></a>Kafka 토픽 만들기

Kafka는 __토픽__에 데이터를 저장합니다. Azure 함수에서 Kafka로 데이터를 보내기 전에 함수를 만들어야 합니다.

토픽을 만들려면 [Kafka 클러스터 만들기](apache-kafka-get-started.md) 문서의 단계를 참조하세요.

## <a name="create-a-function-that-sends-to-kafka"></a>Kafka에 보내는 함수 만들기

> [!NOTE]
> 이 섹션의 단계에서는 [kafka-node](https://www.npmjs.com/package/kafka-node) 패키지를 사용하여 Kafka에 데이터를 게시하는 JavaScript 함수를 만듭니다.

1. __WebHook + API__ 함수를 만들고 __JavaScript__를 언어로 선택합니다. 새 함수 만들기에 대한 자세한 내용은 [첫 번째 함수 만들기](../../azure-functions/functions-create-first-azure-function.md#create-function) 문서를 참조하세요.

2. 다음 코드를 함수의 본문으로 사용합니다.

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    `'test'`를 HDInsight 클러스터에 만든 Kafka 토픽의 이름으로 바꿉니다.

    `10.1.0.7`을 이전에 검색한 IP 주소로 바꿉니다. `:9092` 값은 유지합니다. 9092는 Kafka가 수신 대기하는 포트입니다.

    __Save__ 단추를 사용하여 변경 내용을 저장합니다.

    ![저장 단추가 있는 편집기](./media/apache-kafka-azure-functions/function-editor.png)

3. 함수 편집기의 오른쪽에서 __파일 보기__를 선택합니다. __+ 추가__를 선택하고 `package.json`이라는 새 파일을 추가합니다. 이 파일은 `kafka-node` 패키지의 종속성을 지정하는 데 사용됩니다.

    ![파일 추가](./media/apache-kafka-azure-functions/add-files.png)

4. 새 파일을 편집하려면 __파일 보기__ 목록에서 `package.json`을 선택합니다. 다음 텍스트를 파일의 내용으로 사용합니다.

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    __Save__ 단추를 사용하여 변경 내용을 저장합니다.

5. `kafka-node` 패키지를 설치하려면 [Azure Functions JavaScript 개발자 가이드](../../azure-functions/functions-reference-node.md#node-version-and-package-management)의 _노드 버전 및 패키지 관리_ 섹션을 사용합니다.

    > [!NOTE]
    > kafka-node 패키지가 설치되면서 몇 가지 오류가 표시될 수 있습니다. 이러한 오류는 무시해도 됩니다.

## <a name="run-the-function"></a>함수 실행

함수 편집기의 오른쪽에서 __테스트__를 선택합니다. 테스트의 기본 설정은 그대로 두고 __실행__을 선택합니다. 테스트가 실행되면서 `name` 매개 변수를 함수에 전달합니다. 이 매개 변수는 함수가 Kafka에 삽입하는 `Azure`의 값을 포함합니다.

![테스트 대화 상자 스크린샷](./media/apache-kafka-azure-functions/function-test-dialog.png)

테스트가 실행되는 동안 함수가 기록한 정보를 보려면 페이지 맨 아래에서 __로그__를 선택합니다. 테스트를 다시 실행하여 로그 정보를 생성합니다.

![함수 로그 출력의 예](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>데이터가 Kafka에 있는지 확인

데이터가 Kafka 토픽에 도착했는지 확인하려면 [Kafka 클러스터 만들기](apache-kafka-get-started.md#produce-and-consume-records) 문서의 _레코드 생성 및 소비_ 섹션에 있는 정보를 사용합니다. `kafka-console-consumer`는 토픽의 데이터를 읽고 토픽에 저장된 메시지 목록을 표시합니다.

## <a name="next-steps"></a>다음 단계

다음 링크를 사용하여 HDInsight에서 Apache Kafka를 사용하는 방법에 대해 알아봅니다.

* [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)

* [MirrorMaker를 사용하여 HDInsight에 Kafka 복제본 만들기](apache-kafka-mirroring.md)

* [HDInsight의 Kafka에서 Apache Storm 사용](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight의 Kafka에서 Apache Spark 사용](../hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network를 통해 Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
