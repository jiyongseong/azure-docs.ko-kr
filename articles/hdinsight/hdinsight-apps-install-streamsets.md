---
title: 게시된 응용 프로그램 설치 - StreamSets 데이터 수집기 - Azure HDInsight | Microsoft Docs
description: StreamSets 데이터 수집기 타사 Hadoop 응용 프로그램을 설치하고 사용합니다.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e433de82576f8b943988881ed0b6673c0dccd77e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>게시된 응용 프로그램 설치 - StreamSets 데이터 수집기

이 문서에서는 Azure HDInsight에 게시된 [HDInsight용 StreamSets 데이터 수집기](https://streamsets.com/) Hadoop 응용 프로그램을 설치하고 실행하는 방법에 대해 설명합니다. HDInsight 응용 프로그램 플랫폼 개요 및 사용 가능한 ISV(Independent Software Vendor) 게시된 응용 프로그램 목록은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요. 사용자 고유의 응용 프로그램을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

## <a name="about-streamsets-data-collector"></a>StreamSets 데이터 수집기 정보

StreamSets 데이터 수집기는 Azure HDInsight 응용 프로그램을 기반으로 하여 배포됩니다. StreamSets 데이터 수집기는 모든 기능을 갖춘 IDE(통합 개발 환경)로서, any-to-any 수집 파이프라인을 설계, 테스트, 배포 및 관리할 수 있습니다. 이러한 파이프라인은 스트림 및 일괄 처리 데이터를 메시할 수 있으며, 사용자 지정 코드를 전혀 작성하지 않고도 다양한 내부 스트림 변환을 포함합니다.

StreamSets 데이터 수집기를 사용하면 다양한 빅 데이터 구성 요소(예: HDFS, Kafka, Solr, Hive, HBASE 및 Kudu)를 사용하여 데이터 흐름을 작성할 수 있습니다. StreamSets 데이터 수집기가 에지 서버 또는 Hadoop 클러스터에서 실행되면 데이터 비정상 및 데이터 흐름 작업 모두를 실시간으로 모니터링할 수 있습니다. 이 모니터링에는 임계값 기반 경고, 변칙 탐지 및 오류 레코드 자동 재구성이 포함됩니다.

StreamSets 데이터 수집기는 파이프라인의 각 단계를 논리적으로 격리하도록 설계되었으므로, 코딩을 하지 않고 가동 중지 시간을 최소화하면서 새 프로세서 및 커넥터를 배치하여 새 비즈니스 요구 사항이 충족될 수 있습니다.

### <a name="streamsets-resource-links"></a>StreamSets 리소스 링크

* [설명서](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [블로그](https://streamsets.com/blog/)
* [자습서](https://github.com/streamsets/tutorials)
* [개발자 지원 포럼](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slack 공개 StreamSets 채널](https://streamsetters.slack.com/)
* [소스 코드](https://github.com/streamsets)

## <a name="prerequisites"></a>필수 조건

새 HDInsight 클러스터 또는 기존 클러스터에 이 앱을 설치하려면 다음 구성이 필요합니다.

* 클러스터 계층: 표준 또는 프리미엄
* 클러스터 버전: 3.5 이상

## <a name="install-the-streamsets-data-collector-published-application"></a>게시된 StreamSets 데이터 수집기 응용 프로그램 설치

이 버전 및 사용 가능한 다른 ISV 응용 프로그램을 설치하는 단계별 지침은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요.

## <a name="launch-streamsets-data-collector"></a>StreamSets 데이터 수집기 시작

1. 설치가 완료되면 **설정** 창으로 이동한 다음, **일반** 범주 아래에서 **응용 프로그램**을 선택하여 Azure Portal의 클러스터에서 StreamSets를 시작할 수 있습니다. [설치된 앱] 창에 설치된 응용 프로그램이 나열됩니다.

    ![설치된 StreamSets 앱](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. StreamSets 데이터 수집기가 선택되면 웹 페이지에 대한 링크와 SSH 엔드포인트 경로가 표시됩니다. WEBPAGE 링크를 선택합니다.

3. [로그인] 대화 상자에서 `admin` 및 `admin` 자격 증명을 사용하여 로그인합니다.

4. [시작] 페이지에서 **새 파이프라인 만들기**를 클릭합니다.

    ![새 파이프라인 만들기](./media/hdinsight-apps-install-streamsets/get-started.png)

5. [새 파이프라인] 창에서 파이프라인 이름("Hello World")을 입력하고, 필요에 따라 설명을 입력하고, **저장**을 선택합니다.

6. 데이터 수집기 콘솔이 표시됩니다. [속성] 패널에 파이프라인 속성이 표시됩니다.
 
    ![데이터 수집기 콘솔](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. 이제 [StreamSets 자습서](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html)를 수행할 준비가 되었습니다. 이 자습서에서는 첫 번째 파이프라인을 만드는 단계별 지침을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [StreamSets 데이터 수집기 설명서](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq)
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight에 게시되지 않은 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
* [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 동작을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
* [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md): 빈 에지 노드를 사용하여 HDInsight 클러스터에 액세스하고 HDInsight 응용 프로그램을 테스트 및 호스팅하는 방법을 알아봅니다.
