---
title: Log Analytics를 사용하여 Azure HDInsight 클러스터 모니터링 | Microsoft Docs
description: Azure Log Analytics를 사용하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: e11c9672e2e96f3370c69b33f57a007f7d63ccf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics를 사용하여 HDInsight 클러스터 모니터링

Azure Log Analytics를 사용하여 HDInsight에서 Hadoop 클러스터 작업을 모니터링하는 방법을 알아봅니다.

[Log Analytics](../log-analytics/log-analytics-overview.md)는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성 및 성능을 유지하는 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다. 

## <a name="prerequisites"></a>필수 조건

* **Azure 구독** - 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/free)를 참조하세요.

* **Azure HDInsight 클러스터**. Log Analytics는 현재 다음 HDInsight 클러스터 유형에서 사용할 수 있습니다.

    * Hadoop은
    * HBase
    * 대화형 쿼리
    * Kafka
    * Spark
    * Storm

    HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

* **Log Analytics 작업 영역** - 이 작업 영역은 자체의 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Log Analytics 환경으로 생각할 수 있습니다. Azure HDInsight 클러스터와 연결할 수 있는 작업 영역 하나가 이미 만들어져 있어야 합니다. 지침은 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)를 참조하세요.

## <a name="enable-log-analytics-by-using-the-portal"></a>포털을 사용하여 Log Analytics 사용

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. Azure Portal에서 HDInsight 클러스터를 엽니다.
2. 왼쪽 창에서 **모니터링**을 클릭합니다.
3. 오른쪽 창에서 **사용**을 클릭하고, 기존 Log Analytics 작업 영역을 선택하고 나서, **저장**을 클릭합니다.

    ![HDInsight 클러스터에 대한 모니터링 사용](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight 클러스터에 대한 모니터링 사용")

    설정을 저장하는 데 몇 분 정도 걸립니다.  완료되면 맨 위에서 **OMS 대시보드 열기** 단추를 볼 수 있습니다. 

    ![Operations Management Suite 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "OMS 대시보드 열기")

5. **OMS 대시보드 열기**를 클릭합니다.
6. 메시지가 표시되면 Azure 자격 증명을 입력합니다.

    ![Operations Management Suite 포털](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite 포털")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 Log Analytics 사용

Azure PowerShell을 사용하여 Log Analytics를 사용할 수 있습니다. cmdlet는 다음과 같습니다.

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

[Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)를 참조하세요.

사용하지 않으려면 cmdlet는 다음과 같습니다. 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

[Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)를 참조하세요.


## <a name="next-steps"></a>다음 단계
* [Log Analytics에 HDInsight 클러스터 관리 솔루션 추가](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
