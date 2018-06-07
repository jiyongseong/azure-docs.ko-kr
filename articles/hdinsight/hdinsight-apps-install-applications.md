---
title: Azure HDInsight에 타사 Hadoop 응용 프로그램 설치 | Microsoft Docs
description: Azure HDInsight에 타사 Hadoop 응용 프로그램을 설치하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 3ad112544a703a9b6ec37fa07cbd6df6976d5e26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight에 타사 Hadoop 응용 프로그램 설치

Azure HDInsight에 타사 Hadoop 응용 프로그램을 설치하는 방법에 대해 알아봅니다. 사용자 고유의 응용 프로그램을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

HDInsight 응용 프로그램은 HDInsight 클러스터에 사용자가 설치할 수 있는 응용 프로그램입니다. Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 응용 프로그램을 개발할 수 있습니다.  

다음 목록에서는 게시된 응용 프로그램을 보여 줍니다.

* **AtScale 인텔리전스 플랫폼**은 HDInsight 클러스터를 확장 OLAP 서버로 전환합니다. Microsoft Excel, PowerBI, Tableau 소프트웨어에서 QlikView까지 응용 프로그램을 통해 BI 도구를 사용하여 대화형으로 수십억 개의 데이터 행을 쿼리할 수 있습니다.
* **HDInsight용 Cask CDAP**은 데이터 응용 프로그램 및 Data Lake에 대한 프로덕션 시간을 80%까지 줄일 수 있는 첫 번째 빅 데이터용 통합 플랫폼을 제공합니다. 이 응용 프로그램은 표준 HBase 3.4 클러스터만을 지원합니다.
* **HDInsight의 DATAIKU DDS**를 사용하면 데이터 전문가가 원시 데이터를 영향력이 강한 비즈니스 예측으로 변환하는 매우 특정한 서비스를 프로토타입, 빌드 및 배포할 수 있습니다.
* **HDInsight용 H2O Artificial Intelligence(베타)** H2O Sparkling Water는 GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection 및 Autoencoders 등의 분산 알고리즘을 지원합니다.
* **Kyligence Analytics Platform** KAP(Kyligence Analytics Platform)는 Apache Kylin 및 Apache Hadoop 기반의 엔터프라이즈 지원 데이터 웨어하우스입니다. 대규모 데이터 집합에 대해 1초 미만의 쿼리 대기 시간을 제공하고, 비즈니스 사용자 및 분석가를 위해 데이터 분석을 간소화합니다. 
* **Paxata 셀프 서비스 데이터 준비**
* **KNIME Spark Executor용 Spark 작업 서버** KNIME Spark Executor용 Spark 작업 서버는 KNIME Analytics Platform을 HDInsight 클러스터에 연결하는 데 사용됩니다.
* **HDnsight용 Streamsets Data Collector**는 모든 기능을 갖춘 IDE(통합 개발 환경)로서 이를 통해 사용자 지정 코드를 작성할 필요 없이 스트림을 맞추고 데이터를 일괄 처리하는 수집 파이프라인 간에 디자인, 테스트, 배포 및 관리하고 다양한 스트림 내 변환을 수행할 수 있습니다. 
* **[Trifacta](http://www.trifacta.com/)** 는 획기적인 사용자 경험과 워크플로, 아키텍처를 제공하기 위해 머신 러닝을 활용해 데이터 엔지니어 및 분석가가 현대의 다양한 데이터를 효율적으로 탐색하고 준비할 수 있게 합니다.
* **WANdisco Fusion HDI 앱**은 위치에 상관없이 변경되는 데이터에 일관되게 계속 연결할 수 있도록 합니다. 이렇게 하면 중지 및 중단 없이 언제 어디서나 데이터에 액세스할 수 있습니다.

이 문서에서 제공하는 지침은 Azure Portal을 사용합니다. 또한 포털에서 Azure Resource Manager 템플릿을 내보내거나 공급 업체에서 Resource Manager 템플릿의 복사본을 가져오고 Azure PowerShell 및 Azure CLI를 사용하여 템플릿을 배포할 수 있습니다.  [Resource Manager 템플릿을 사용하여 HDInsight의 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
기존 HDInsight 클러스터에 HDInsight 응용 프로그램을 설치하려면 HDInsight 클러스터가 있어야 합니다. HDInsight 클러스터를 만들려면 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요. HDInsight 클러스터를 만들 경우 HDInsight 응용 프로그램도 설치할 수 있습니다.

## <a name="install-applications-to-existing-clusters"></a>기존 클러스터에 응용 프로그램 설치
다음 절차에서는 기존 HDInsight 클러스터에 HDInsight 응용 프로그램을 설치하는 방법을 보여 줍니다.

**HDInsight 응용 프로그램 설치**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터** 를 클릭합니다.
3. HDInsight 클러스터를 클릭합니다.  HDInsight 클러스터가 없는 경우 만듭니다.  see [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.
4. **구성** 범주에서 **응용 프로그램**을 클릭합니다. 설치된 응용 프로그램 목록이 표시됩니다. 응용 프로그램을 찾을 수 없다면 이는 이 버전의 HDInsight 클러스터에 대한 응용 프로그램이 없다는 의미입니다.
   
    ![HDInsight 응용 프로그램 포털 메뉴](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 메뉴에서 **추가**를 클릭합니다. 기존 HDInsight 응용 프로그램 목록이 표시됩니다.
   
    ![HDInsight 응용 프로그램 사용 가능한 응용 프로그램](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. 사용할 수 있는 응용 프로그램 중 하나를 클릭한 다음, 지시에 따라 약관을 수락합니다.

포털 알림에서 설치 상태를 확인할 수 있습니다(포털 맨 위에 있는 종 모양 아이콘 클릭). 응용 프로그램이 설치되면 설치된 앱 목록에 표시됩니다.

## <a name="install-applications-during-cluster-creation"></a>클러스터 생성 중에 응용 프로그램 설치
클러스터를 만들 때 HDInsight 응용 프로그램을 설치하는 옵션이 있습니다. 클러스터가 만들어지고 실행 상태가 되면 프로세스 중에 HDInsight 응용 프로그램이 설치됩니다. Azure Portal을 사용하여 클러스터를 만드는 동안 응용 프로그램을 설치하려면 기본 --빨리 만들기-- 옵션 대신 --사용자 지정-- 옵션을 사용합니다.

## <a name="list-installed-hdinsight-apps-and-properties"></a>설치된 HDInsight 앱 및 속성 나열
포털에서는 클러스터에 설치된 HDInsight 응용 프로그램의 목록과 설치된 응용 프로그램 각각의 속성을 보여 줍니다.

**HDInsight 응용 프로그램 나열 및 속성 표시**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터** 를 클릭합니다. 
3. HDInsight 클러스터를 클릭합니다.
4. **설정**에서 **구성** 범주에 있는 **응용 프로그램**을 클릭합니다. 설치된 앱이 오른쪽에 나열됩니다. 
   
    ![HDInsight 응용 프로그램 설치된 앱](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 속성을 표시하려면 설치된 응용 프로그램 중 하나를 클릭합니다. 속성 목록:
   
   * 앱 이름: 응용 프로그램 이름입니다.
   * 상태: 응용 프로그램 상태입니다. 
   * 웹 페이지: 에지 노드에 배포한 웹 응용 프로그램의 URL입니다. 자격 증명은 클러스터에 대해 구성한 HTTP 사용자 자격 증명과 동일합니다.
   * HTTP 끝점: 자격 증명은 클러스터에 대해 구성한 HTTP 사용자 자격 증명과 동일합니다. 
   * SSH 끝점: SSH를 사용하여 에지 노드에 연결할 수 있습니다. SSH 자격 증명은 클러스터에 대해 구성한 SSH 사용자 자격 증명과 동일합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
6. 응용 프로그램을 삭제하려면 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **삭제**를 클릭합니다.

## <a name="connect-to-the-edge-node"></a>에지 노드에 연결
HTTP 및 SSH를 사용하여 에지 노드에 연결할 수 있습니다. 끝점 정보는 [포털](#list-installed-hdinsight-apps-and-properties)에서 찾을 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

HTTP 끝점 자격 증명은 HDInsight 클러스터에 대해 구성한 HTTP 사용자 자격 증명입니다. SSH 끝점 자격 증명은 HDInsight 클러스터에 대해 구성한 SSH 자격 증명입니다.

## <a name="troubleshoot"></a>문제 해결
[설치 문제 해결](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
* [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
* [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
* [HDInsight에서 비어 있는 에지 노드 사용](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에 액세스, HDInsight 응용 프로그램 테스트 및 HDInsight 응용 프로그램 호스팅하는 데 비어 있는 에지 노드를 사용하는 방법을 알아봅니다.

