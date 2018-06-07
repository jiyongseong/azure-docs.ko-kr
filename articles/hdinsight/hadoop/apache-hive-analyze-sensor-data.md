---
title: Hive 및 Hadoop을 사용하여 센서 데이터 분석 - Azure HDInsight | Microsoft Docs
description: HDInsight(Hadoop)에서 Hive 쿼리 콘솔을 사용하여 센서 데이터를 분석한 다음 Microsoft Excel에서 Power View를 사용하여 데이터를 시각화하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb3dc93d7cb741a8a3099abe13d00f40c9639705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>HDInsight의 Hadoop에서 Hive 쿼리 콘솔을 사용하여 센서 데이터 분석

HDInsight(Hadoop)에서 Hive 쿼리 콘솔을 사용하여 센서 데이터를 분석한 다음 Microsoft Excel에서 Power View를 사용하여 데이터를 시각화하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> 이 문서의 단계는 Windows 기반 HDInsight 클러스터에만 적용됩니다. HDInsight는 HDInsight 3.4 이하 버전의 경우 Windows에서만 사용 가능합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.


이 샘플에서는 Hive를 사용하여 기록 데이터를 처리하고 온방 및 냉방 시스템의 문제를 식별합니다. 특히 다음 작업을 실행하여 시스템이 설정된 온도를 안정적으로 유지할 수 없음을 식별합니다.

* HIVE 테이블을 만들어 CSV(쉼표로 구분된 값) 파일에 저장된 데이터 쿼리
* HIVE 쿼리를 만들어 데이터 분석
* Microsoft Excel로 HDInsight에 연결하여 분석된 데이터 검색
* Power View를 사용하여 데이터 시각화

![솔루션 아키텍처 다이어그램](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>필수 조건

* HDInsight(Hadoop) 클러스터: 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel은 [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US)를 통한 데이터 시각화에 사용됩니다.

* [Microsoft Hive ODBC 드라이버](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>샘플을 실행하려면

1. 웹 브라우저에서 다음 URL로 이동합니다. 

         https://<clustername>.azurehdinsight.net

    `<clustername>`을 HDInsight 클러스터 이름으로 바꿉니다.

    메시지가 표시되면 이 클러스터를 프로비전할 때 사용한 관리자 사용자 이름과 암호를 사용하여 인증합니다.

2. 웹 페이지가 열리면 **시작 갤러리** 탭을 클릭하고 **샘플 데이터가 있는 솔루션** 범주에서**센서 데이터 분석** 샘플을 클릭합니다.

    ![갤러리 이미지 시작하기](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. 웹 페이지에서 제공되는 지침에 따라 샘플을 완료합니다.
