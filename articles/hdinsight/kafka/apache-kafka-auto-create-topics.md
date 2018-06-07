---
title: Apache Kafka-Azure HDInsight에서 자동 토픽 생성 사용 | Microsoft Docs
description: 자동으로 토픽을 만들도록 HDInsight에서 Apache Kafka를 구성하는 방법에 대해 알아봅니다. Ambari를 통해 auto.create.topics.enable을 true로 설정하거나 또는 PowerShell 또는 Resource Manager 템플릿을 통해 클러스터를 만드는 동안에 Kafka를 구성할 수 있습니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>자동으로 토픽을 작성하기 위해 HDInsight에서 Apache Kafka를 구성하는 방법

기본적으로 HDInsight의 Kafka는 자동 토픽 생성을 허용하지 않습니다. Ambari를 사용하여 기존 클러스터에 자동 토픽 작성을 사용할 수 있습니다. Azure Resource Manager 템플릿을 사용하여 새로운 Kafka 클러스터를 만들 때 자동 토픽 만들기도 사용할 수 있습니다.

## <a name="ambari-web-ui"></a>Ambari 웹 UI

Ambari 웹 UI를 통해 기존 클러스터에서 자동 토픽 생성을 사용할 수 있게 하려면 다음 단계를 사용합니다.

1. [Azure 포털](https://portal.azure.com)에서 Kafka 클러스터를 선택합니다.

2. __클러스터 개요__에서 __클러스터 대시보드__를 선택합니다. 

    ![클러스터 대시 보드로 선택한 포털 이미지](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. 그런 다음 __HDInsight 클러스터 대시보드__를 선택합니다. 메시지가 표시되면 클러스터에 대한 로그인(관리자) 자격 증명을 사용하여 인증을 받습니다.

    ![HDInsight 클러스터 대시보드 항목 이미지](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. 페이지 왼쪽 목록에서 Kafka 서비스를 선택합니다.

    ![서비스 목록](./media/apache-kafka-auto-create-topics/service-list.png)

4. 페이지 중간의 Config를 선택합니다.

    ![서비스 구성 탭](./media/apache-kafka-auto-create-topics/service-config.png)

5. 필터 필드에 `auto.create` 값을 입력합니다. 

    ![필터 필드의 이미지](./media/apache-kafka-auto-create-topics/filter.png)

    이렇게 하면 속성 목록이 필터링되고 `auto.create.topics.enable` 설정이 표시됩니다.

6. `auto.create.topics.enable` 값을 `true`로 변경하고 저장을 선택합니다. 메모를 추가하고 저장을 다시 선택합니다.

    ![auto.create.topics.enable 항목의 이미지](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Kafka 서비스를 선택하고 __다시 시작__을 선택한 후 __영향을 받는 모든 서비스 다시 시작__을 선택합니다. 메시지가 나타나면 __모두 다시 시작 확인__을 선택합니다.

    ![다시 시작 선택 이미지](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> 또한 Ambari REST API를 통해 Ambari 값을 설정할 수 있습니다. 일반적으로 이렇게 하는 것은 현재 구성을 검색하고 수정하기 위해 REST 호출을 여러 번 해야 하기 때문에 더 어렵습니다. 자세한 내용은 [Ambari REST API를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari-rest-api.md) 문서를 참조하세요.

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

Azure Resource Manager 템플릿을 사용하여 Kafka 클러스터를 만들 때 `kafka-broker`에 추가하여 직접 `auto.create.topics.enable`을 설정할 수 있습니다. 다음 JSON 코드 조각은 이 값을 `true`로 설정하는 방법을 보여줍니다.

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight에서 Kafka에 대한 자동 화제 생성을 활성화하는 방법을 배웠습니다. Kafka 작업에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)