---
title: HDInsight에서 PowerShell과 Hadoop Hive 사용 - Azure | Microsoft Docs
description: PowerShell을 사용하여 HDInsight의 Hadoop에서 Hive 쿼리 실행
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d72d5223373043648aeb3d783477781dbce35f6d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="run-hive-queries-using-powershell"></a>PowerShell을 사용하여 Hive 쿼리 실행
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

이 문서는 Azure 리소스 그룹 모드에서 Azure PowerShell을 사용하여 HDInsight 클러스터의 Hadoop에서 Hive 쿼리를 실행하는 예제를 제공합니다.

> [!NOTE]
> 이 문서에는 예제에 사용된 HiveQL 문이 수행하는 작업에 대해 자세한 설명을 제공하지 않습니다. 이 예제에서 사용된 HiveQL에 대한 자세한 내용은 [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터 버전 3.4 이상의 Linux 기반 Hadoop

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* Azure PowerShell이 설치된 클라이언트

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>HIVE 쿼리 실행

Azure PowerShell은 HDInsight에서 Hive 쿼리를 원격으로 실행할 수 있는 *cmdlet* 을 제공합니다. 내부적으로 cmdlet은 HDInsight 클러스터에서 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)에 대한 REST를 호출합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 Hive 쿼리를 실행할 때 사용됩니다.

* `Connect-AzureRmAccount`: Azure 구독에 대해 Azure PowerShell을 인증합니다.
* `New-AzureRmHDInsightHiveJobDefinition`: 지정한 HiveQL 문을 사용하여 *작업 정의*를 만듭니다.
* `Start-AzureRmHDInsightJob`: HDInsight에 작업 정의를 보내고 작업을 시작합니다. *작업* 개체가 반환됩니다.
* `Wait-AzureRmHDInsightJob`: 작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료되거나 대기 시간이 초과될 때까지 기다립니다.
* `Get-AzureRmHDInsightJobOutput`: 작업의 출력을 검색하는 데 사용했습니다.
* `Invoke-AzureRmHDInsightHiveJob`: HiveQL 문을 실행하는 데 사용했습니다. 이 cmdlet은 쿼리 완료를 차단한 후 결과를 반환합니다.
* `Use-AzureRmHDInsightCluster`: `Invoke-AzureRmHDInsightHiveJob` 명령에 사용할 현재 클러스터를 설정합니다.

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다.

1. 편집기를 사용하여 다음 코드를 `hivejob.ps1`로 저장합니다.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. 새 **Azure PowerShell** 명령 프롬프트를 엽니다. `hivejob.ps1` 파일의 디렉터리 위치를 변경한 다음, 명령을 사용하여 스크립트를 실행합니다.

        .\hivejob.ps1

    스크립트를 실행할 때 클러스터에 대한 클러스터 이름 및 HTTPS/관리자 계정 자격 증명을 입력하라는 메시지가 표시됩니다. Azure 구독에 로그인하라는 메시지도 표시될 수 있습니다.

3. 작업이 완료되면 다음 텍스트와 유사한 정보가 반환됩니다.

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 앞서 설명한 것처럼 `Invoke-Hive`는 쿼리를 실행하고 응답을 기다리는 데 사용할 수 있습니다. 다음 스크립트를 사용하여 Invoke-Hive 작동 방식을 확인합니다.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    출력은 다음 텍스트와 비슷합니다.

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > 더 긴 HiveQL 쿼리에는 Azure PowerShell **Here-Strings** cmdlet 또는 HiveQL 스크립트 파일을 사용할 수 있습니다. 다음 코드 조각은 `Invoke-Hive` cmdlet을 사용하여 HiveQL 스크립트 파일을 실행하는 방법을 보여 줍니다. HiveQL 스크립트 파일은 wasb://에 업로드해야 합니다.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > **Here-Strings**에 대한 자세한 내용은 <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings 사용</a>을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

작업이 완료될 때 정보가 반환되지 않으면 오류 로그를 봅니다. 이 작업에 대한 오류 정보를 보려면 `hivejob.ps1` 파일의 끝에 다음 내용을 추가하고 파일을 저장한 다음, 다시 실행합니다.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

이 cmdlet은 작업 프로세스 중 STDERR에 작성된 정보를 반환합니다.

## <a name="summary"></a>요약

여기에서 볼 수 있듯이 Azure PowerShell은 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

## <a name="next-steps"></a>다음 단계

HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
