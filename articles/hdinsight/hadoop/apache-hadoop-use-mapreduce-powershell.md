---
title: Hadoop과 MapReduce 및 PowerShell 사용 - Azure HDInsight | Microsoft Docs
description: PowerShell을 사용하여 HDInsight에서 Hadoop으로 MapReduce 작업을 원격으로 실행하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.openlocfilehash: 7416d064f89515feb04523ca6d4ea73f37c14e38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>PowerShell을 사용하여 HDInsight에서 Hadoop과 MapReduce 작업 실행

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서는 Azure PowerShell을 사용하여 HDInsight 클러스터의 Hadoop에서 MapReduce 작업을 실행하는 예제를 제공합니다.

## <a id="prereq"></a>필수 조건

* **Azure HDInsight(HDInsight의 Hadoop) 클러스터**

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* **Azure PowerShell이 포함된 워크스테이션**.

## <a id="powershell"></a>MapReduce 작업 실행

Azure PowerShell은 HDInsight에서 MapReduce 작업을 원격으로 실행할 수 있는 *cmdlet* 을 제공합니다. PowerShell은 내부적으로 HDInsight 클러스터에서 실행되는 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (이전의 Templeton)에 REST를 호출합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 MapReduce 작업을 실행할 때 사용됩니다.

* **Connect-AzureRmAccount**: Azure 구독에 대해 Azure PowerShell을 인증합니다.

* **New-AzureRmHDInsightMapReduceJobDefinition**: 지정한 MapReduce 정보를 사용하여 새 *작업 정의*를 만듭니다.

* **Start-AzureRmHDInsightJob**: HDInsight에 작업 정의를 보내고 작업을 시작합니다. *작업* 개체가 반환됩니다.

* **Wait-AzureRmHDInsightJob**: 작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료되거나 대기 시간이 초과될 때까지 기다립니다.

* **Get-AzureRmHDInsightJobOutput**: 작업 출력을 검색하는 데 사용됩니다.

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다.

1. 편집기를 사용하여 다음 코드를 **mapreducejob.ps1**로 저장합니다.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. 새 **Azure PowerShell** 명령 프롬프트를 엽니다. **mapreducejob.ps1** 파일의 디렉터리 위치를 변경한 다음 명령을 사용하여 스크립트를 실행합니다.

        .\mapreducejob.ps1

    스크립트를 실행하는 경우 HDInsight 클러스터의 이름 및 클러스터 로그인을 묻는 메시지가 나타납니다. Azure 구독에서 인증을 받으라는 메시지도 표시될 수 있습니다.

3. 작업이 완료되면 다음 텍스트와 유사한 출력이 나타납니다.

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    이 출력은 작업이 성공적으로 완료되었음을 나타냅니다.

    > [!NOTE]
    > **ExitCode** 가 0이 아닌 값이면 [문제 해결](#troubleshooting)을 참조하세요.

    이 예제에서도 다운로드한 파일을 스크립트를 실행한 디렉터리의 **output.txt** 파일에 저장합니다.

### <a name="view-output"></a>출력 보기

작업에서 생성한 단어 및 단어 개수를 보려면 텍스트 편집기에서 **output.txt** 파일을 엽니다.

> [!NOTE]
> MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

## <a id="troubleshooting"></a>문제 해결

작업이 완료될 때 정보가 반환되지 않으면 작업에 대한 오류를 봅니다. 이 작업에 대한 오류 정보를 보려면 **mapreducejob.ps1** 파일의 끝에 다음 명령을 추가하고 파일을 저장한 다음 다시 실행합니다.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

이 cmdlet은 작업이 실행될 때 STDERR에 작성된 정보를 반환합니다.

## <a id="summary"></a>요약

여기에서 볼 수 있듯이 Azure PowerShell은 HDInsight 클러스터에서 MapReduce 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

## <a id="nextsteps"></a>다음 단계

HDInsight의 MapReduce 작업에 대한 일반적인 정보:

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
