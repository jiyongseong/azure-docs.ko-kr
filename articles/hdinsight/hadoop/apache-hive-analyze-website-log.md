---
title: Hadoop에서 웹 사이트 로그 분석에 Hive 사용 - Azure HDInsight | Microsoft Docs
description: HDInsight와 함께 Hive를 사용하여 웹 사이트 로그를 분석하는 방법에 대해 알아봅니다. 로그 파일을 HDInsight 테이블에 대한 입력으로 사용하고 HiveQL을 사용해 데이터를 쿼리합니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 53a0560d3bc5a52069d5829b9c3bd353e0c37ef3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Windows 기반 HDInsight와 함께 Hive를 사용하여 웹 사이트의 로그 분석
HDInsight와 함께 HiveQL을 사용하여 웹 사이트의 로그를 분석하는 방법에 대해 알아봅니다. 웹 사이트 로그 분석을 통해 비슷한 활동을 기준으로 대상을 구분하고, 인구 통계별로 사이트 방문자를 분류하고, 방문자가 보는 콘텐츠와 이전에 방문했던 웹 사이트 등을 확인할 수 있습니다.

> [!IMPORTANT]
> 이 문서의 단계는 Windows 기반 HDInsight 클러스터에만 적용됩니다. HDInsight는 HDInsight 3.4 이하 버전의 경우 Windows에서만 사용 가능합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

이 샘플에서는 HDInsight 클러스터를 사용하여 웹 사이트 로그 파일을 분석해 외부 웹 사이트로부터의 방문 빈도를 파악하고, 사용자에게 발생하는 웹 사이트 오류의 요약을 확인합니다. 다음 방법에 대해 알아봅니다.

* 웹 사이트 로그 파일이 포함된 Azure Blob Storage에 연결
* HIVE 테이블을 만들어 이러한 로그 쿼리
* HIVE 쿼리를 만들어 데이터 분석
* Microsoft Excel에서 ODBC(Open Database Connectivity)를 통해 HDInsight에 연결하여 분석된 데이터 검색

![HDI.Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>필수 조건
* Azure HDInsight에서 Hadoop 클러스터를 프로비전해야 합니다. 관련 지침은 [HDInsight 클러스터 프로비전](../hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.
* Microsoft Excel 2013 또는 Excel 2010을 설치해야 합니다.
* Hive에서 Excel로 데이터를 가져오려면 [Microsoft Hive ODBC 드라이버](http://www.microsoft.com/download/details.aspx?id=40886) 가 필요합니다.

## <a name="to-run-the-sample"></a>샘플을 실행하려면
1. [Azure Portal](https://portal.azure.com/)의 시작 보드(클러스터를 여기에 고정한 경우)에서 샘플을 실행할 클러스터 타일을 클릭합니다.
2. 클러스터 블레이드의 **빠른 링크** 아래에서 **클러스터 대시보드**를 클릭한 다음 **클러스터 대시보드** 블레이드에서 **HDInsight 클러스터 대시보드**를 클릭합니다. 다음 URL을 사용하여 대시보드를 직접 열 수도 있습니다.

         https://<clustername>.azurehdinsight.net

    메시지가 표시되면 클러스터를 프로비전할 때 사용한 관리자 사용자 이름과 암호를 사용하여 인증합니다.
3. 웹 페이지가 열리면 **갤러리 시작** 탭을 클릭하고 **샘플 데이터가 있는 솔루션** 범주에서 **웹 사이트 데이터 분석** 샘플을 클릭합니다.
4. 웹 페이지에서 제공되는 지침에 따라 샘플을 완료합니다.

## <a name="next-steps"></a>다음 단계
[HDInsight에서 Hive를 사용하여 센서 데이터 분석](apache-hive-analyze-sensor-data.md)샘플을 사용해 봅니다.

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
