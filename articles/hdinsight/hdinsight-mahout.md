---
title: PowerShell에서 Mahout HDInsight를 사용하여 추천 생성 - Azure | Microsoft Docs
description: Apache Mahout Machine Learning 라이브러리를 사용하여 클라이언트에서 실행 중인 PowerShell에서 HDInsight(Hadoop)로 영화 추천을 생성하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 49a092ee23b79c483aa7bbd8b3d5150e909b6884
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>HDInsight(PowerShell)의 Hadoop 및 Apache Mahout을 사용하여 영화 추천 생성

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight에서 [Apache Mahout](http://mahout.apache.org) 기계 학습 라이브러리를 사용하여 영화 추천을 생성하는 방법에 대해 알아봅니다. 이 문서의 예제는 Azure PowerShell을 사용하여 Mahout 작업을 실행합니다.

## <a name="prerequisites"></a>필수 조건

* Linux 기반 HDInsight 클러스터입니다. HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 사용 시작][getstarted]을 참조하세요.

    > [!IMPORTANT]
    > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Azure PowerShell을 사용하여 추천 생성

> [!WARNING]
> 이 섹션의 작업은 Azure PowerShell을 사용하여 실행합니다. Mahout에 제공되는 대부분의 클래스는 현재 Azure PowerShell에서 작동하지 않습니다. Azure PowerShell에서 작동하지 않는 클래스의 목록은 [문제 해결](#troubleshooting) 섹션을 참조하세요.
>
> SSH를 사용하여 HDInsight에 연결하고 클러스터에서 Mahout 예제를 직접 실행하는 예제의 경우 [Mahout 및 HDInsight (SSH)를 사용하여 영화 추천 생성](hadoop/apache-hadoop-mahout-linux-mac.md)을 참조하세요.

Mahout에서 제공하는 기능 중 하나가 추천 엔진입니다. 이 엔진은 `userID`, `itemId` 및 `prefValue`(항목에 대한 사용자 선호도) 형식의 데이터를 허용합니다. Mahout은 데이터를 사용하여 특정 품목에 대한 선호도를 가진 사용자를 결정합니다. 이 결과는 추천에 사용할 수 있습니다.

다음 예제는 추천 프로세스가 작동하는 방식을 간단히 요약한 것입니다.

* **동시 발생**: Joe, Alice 및 Bob은 모두 *스타워즈*, *제국의 역습* 및 *제다이의 귀환*을 좋아합니다. Mahout은 이러한 영화 중 하나를 좋아하면서 다른 두 개도 좋아하는 사용자를 확인합니다.

* **동시 발생**: Bob 및 Alice는 *보이지 않는 위협*, *클론의 습격* 및 *시스의 복수*도 좋아합니다. Mahout은 이전의 3개 영화를 좋아하는 사용자가 이러한 영화도 좋아하는지를 판단합니다.

* **유사성 추천**: Joe가 첫 3개 영화를 좋아하므로, Mahout은 유사한 선호도를 가진 다른 사람이 좋아하지만 Joe는 본(좋아하거나 평가한) 적이 없는 영화를 검색합니다. 이 경우 Mahout은 *보이지 않는 위협*, *클론의 습격* 및 *시스의 복수*를 추천합니다.

### <a name="understanding-the-data"></a>데이터 이해

[GroupLens Research][movielens]에서 Mahout과 호환되는 형식으로 영화에 대한 평가 데이터를 제공합니다. 이 데이터는 클러스터의 기본 저장소인 `/HdiSamples/HdiSamples/MahoutMovieData`에서 사용할 수 있습니다.

`moviedb.txt`(영화 정보) 및 `user-ratings.txt`의 두 가지 파일이 있습니다. `user-ratings.txt` 파일은 분석 중 사용됩니다. `moviedb.txt` 파일은 분석 결과를 표시할 때 사용자에게 친숙한 텍스트를 제공하는 데 사용됩니다.

user-ratings.txt에 포함된 데이터의 구조는 `userID`, `movieID`, `userRating` 및 `timestamp`이며, 각 사용자의 영화 등급 평가를 보여줍니다. 다음은 데이터의 예제입니다.

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>작업 실행

다음 Windows PowerShell 스크립트를 사용하여 영화 데이터로 Mahout 추천 엔진을 사용하는 작업을 실행합니다.

> [!NOTE]
> 이 파일은 HDInsight 클러스터에 연결하고 작업을 실행하는 데 사용하는 정보를 안내합니다. 작업을 완료하고 output.txt 파일을 다운로드하는 데 몇 분이 걸립니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout 작업은 작업을 처리하는 동안 생성된 임시 데이터를 제거하지 않습니다. 이 `--tempDir` 매개 변수는 쉽게 삭제할 수 있도록 임시 파일을 특정 디렉터리로 분리하기 위해 예제 작업에서 지정되었습니다.

Mahout 작업은 STDOUT로 출력을 반환하지 않습니다. 대신 지정된 출력 디렉터리에 **part-r-00000**으로 저장합니다. 이 스크립트는 이 파일을 워크스테이션의 현재 디렉터리에서 **output.txt** 에 다운로드합니다.

다음 텍스트는 이 파일의 내용에 대한 예제입니다.

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

첫 번째 열은 `userID`입니다. '[' 및 ']'에 포함된 값은 `movieId`:`recommendationScore`입니다.

또한 스크립트는 출력의 가독성을 개선하도록 서식 지정하는 데 필요한 `moviedb.txt` 및 `user-ratings.txt` 파일을 다운로드합니다.

### <a name="view-the-output"></a>출력 보기

생성된 출력을 응용 프로그램에서 사용할 수 있긴 하지만 사용자에게 친숙한 형식은 아닙니다. 서버의 `moviedb.txt`를 사용하여 영화 이름으로 `movieId`를 분석할 수 있습니다. 다음 PowerShell 스크립트를 사용하여 영화 이름과 함께 추천 항목을 표시합니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

다음 명령을 사용하여 사용자에게 친숙한 형식으로 추천 정보를 표시합니다. 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

다음 텍스트와 유사하게 출력됩니다.

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>문제 해결

### <a name="cannot-overwrite-files"></a>파일을 덮어쓸 수 없음

Mahout 작업은 처리 중 생성된 임시 파일을 정리하지 않습니다. 또한 이 작업은 기존 출력 파일을 덮어쓰지 않습니다.

Mahout 작업을 실행할 때 오류를 방지하려면 실행 사이에 임시 및 출력 파일을 삭제하세요. 이 문서의 이전 스크립트에서 생성된 파일을 제거하려면 다음 PowerShell 스크립트를 사용합니다.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Azure PowerShell에서 작동하지 않는 클래스

Windows PowerShell에서 사용하는 경우 다음 클래스를 사용하는 Mahout 작업은 다양한 오류 메시지를 반환합니다.

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

이러한 클래스를 사용하는 작업을 실행하려면 SSH를 사용하여 HDInsight 클러스터에 연결하고 명령줄에서 작업을 실행하세요. SSH를 사용하여 Mahout 작업을 실행하는 예제는 [Mahout 및 HDInsight(SSH)를 사용하여 영화 추천 생성](hadoop/apache-hadoop-mahout-linux-mac.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Mahout을 사용하는 방법을 배웠으므로 HDInsight에서 데이터로 작업하는 다른 방법을 검색합니다.

* [HDInsight에서 Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
