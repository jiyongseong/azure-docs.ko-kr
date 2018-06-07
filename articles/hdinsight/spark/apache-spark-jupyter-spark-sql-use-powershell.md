---
title: '빠른 시작: Azure PowerShell을 사용하여 HDInsight에서 Spark 클러스터 만들기'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만들고 간단한 Spark SQL 쿼리를 실행하는 방법을 보여 줍니다.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.custom: mvc
ms.openlocfilehash: 321f84e0d56a2bda57e1fbfa2cc562b65c6e1d30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-powershell"></a>빠른 시작: PowerShell을 사용하여 HDInsight에서 Spark 클러스터 만들기
Azure HDInsight에서 Apache Spark 클러스터를 만드는 방법과 Hive 테이블에 대해 Spark SQL 쿼리를 실행하는 방법을 알아봅니다. Apache Spark를 통해 메모리 내 처리 기능을 사용하여 데이터 분석 및 클러스터 컴퓨팅을 신속하게 처리합니다. HDInsight의 Spark에 대한 자세한 내용은 [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)를 참조하세요.

이 빠른 시작에서는 Azure PowerShell을 사용하여 HDInsight Spark 클러스터를 만듭니다. 클러스터는 클러스터 저장소로 Azure Storage Blob을 사용합니다.

> [!IMPORTANT]
> HDInsight 클러스터에 대한 결제는 사용 여부에 관계없이 분당으로 비례 배분됩니다. 사용한 후에 클러스터를 삭제해야 합니다. 자세한 내용은 이 문서의 [리소스 정리](#clean-up-resources) 섹션을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터 만들기

다음과 같은 Azure 개체와 리소스 만들기를 포함하는 HDInsight 클러스터를 만듭니다.

- Azure 리소스 그룹. Azure 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다. 
- Azure Storage 계정 또는 Azure Data Lake Store.  각 HDInsight 클러스터에는 종속 데이터 저장소가 필요합니다. 이 빠른 시작에서는 저장소 계정을 만듭니다.
- 다른 클러스터 유형의 HDInsight 클러스터.  이 빠른 시작에서는 Spark 2.2 클러스터를 만듭니다.

PowerShell 스크립트를 사용하여 리소스를 만듭니다.  스크립트를 실행하는 경우 다음 값을 입력하라는 메시지가 표시됩니다.

|매개 변수|값|
|------|------|
|Azure 리소스 그룹 이름 | 리소스 그룹에 사용할 고유한 이름을 입력합니다.|
|위치| 예를 들면 ‘미국 중부’와 같은 Azure 영역을 지정합니다. |
|기본 저장소 계정 이름 | 저장소 계정에 고유한 이름을 제공합니다. |
|클러스터 이름 | HDInsight Spark 클러스터에 사용할 고유한 이름을 입력합니다.|
|클러스터 로그인 자격 증명 | 빠른 시작의 뒷부분에 나오는 클러스터 대시보드에 연결하려면 이 계정을 사용합니다.|
|SSH 사용자 자격 증명 | SSH 클라이언트를 HDInsight 클러스터와 함께 원격 명령줄 세션을 만드는 데 사용할 수 있습니다.|



1. 다음 코드 블록이 [Azure Cloud Shell](../../cloud-shell/overview.md)을 열도록 오른쪽 위 모서리에서 **시도**를 클릭하고, 지침을 수행하여 Azure에 연결합니다.
2. 다음 PowerShell 스크립트를 Cloud Shell에 복사하여 붙여넣습니다. 

    ```azurepowershell-interactive
    ### Create a Spark 2.2 cluster in Azure HDInsight
        
    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"
    
    # Create an Azure storae account and container
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_LRS `
        -Location $location
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    
    # Create a Spark 2.2 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"
    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"
    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"
    
    # Default cluster size (# of worker nodes), version, type, and OS
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"
    $clusterOS = "Linux"
    
    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName
    
    # Create a blob container. This holds the default data store for the cluster.
    New-AzureStorageContainer `
        -Name $clusterName -Context $defaultStorageContext 
    
    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.2")
    
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials 
    
    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    ```
클러스터를 만드는 데 약 20분이 걸립니다. 다음 세션을 계속하려면 먼저 클러스터를 만들어야 합니다.

HDInsight 클러스터를 만드는 데 문제가 발생하는 경우 이를 수행하기 위한 적절한 사용 권한이 없을 수 있습니다. 자세한 내용은 [액세스 제어 요구 사항](../hdinsight-administer-use-portal-linux.md#create-clusters)을 참조하세요.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook 만들기

Jupyter Notebook은 다양한 프로그래밍 언어를 지원하는 대화형 노트북 환경입니다. 노트북을 사용하면 데이터와 상호 작용하고, 코드를 markdown 텍스트와 결합하고, 간단한 시각화를 수행할 수 있습니다. 

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. **HDInsight 클러스터**를 선택한 다음, 만든 클러스터를 선택합니다.

    ![Azure Portal에서 HDInsight 클러스터 열기](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. 포털에서 **클러스터 대시보드**를 선택한 다음, **Jupyter Notebook**을 선택합니다. 메시지가 표시되면 클러스터에 대한 클러스터 로그인 자격 증명을 입력합니다.

   ![Jupyter Notebook을 열어서 대화형 Spark SQL 쿼리 실행](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook을 열어서 대화형 Spark SQL 쿼리 실행")

4. **새로 만들기** > **PySpark**를 선택하여 노트북을 만듭니다. 

   ![Jupyter Notebook을 만들어서 대화형 Spark SQL 쿼리 실행](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook을 만들어서 대화형 Spark SQL 쿼리 실행")

   새 노트북이 만들어지고 Untitled(Untitled.pynb) 이름으로 열립니다.


## <a name="run-spark-sql-statements"></a>Spark SQL 문 실행

SQL(구조적 쿼리 언어)은 데이터 쿼리 및 정의에 가장 일반적이며 널리 사용되는 언어입니다. Spark SQL은 익숙한 SQL 구문을 사용하여 구조화된 데이터를 처리하기 위한 Apache Spark에 대한 확장으로 작동합니다.

1. 커널이 준비되었는지 확인합니다. 노트북의 커널 이름 옆에 속이 빈 원이 보이면 커널이 준비된 것입니다. 속이 찬 원은 커널이 사용 중이라는 뜻입니다.

    ![HDInsight Spark의 Hive 쿼리](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "HDInsight Spark의 Hive 쿼리")

    노트북을 처음으로 시작하면 커널이 백그라운드에서 몇 가지 작업을 수행합니다. 커널이 준비될 때까지 기다립니다. 
2. 빈 셀에 다음 코드를 붙여 넣은 다음 **SHIFT + ENTER**를 눌러 코드를 실행합니다. 이 명령은 클러스터의 Hive 테이블을 나열합니다.

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Jupyter Notebook을 HDInsight Spark 클러스터와 함께 사용하는 경우 Spark SQL을 사용하여 Hive 쿼리를 실행하는 데 사용할 수 있는 미리 설정된 `sqlContext`를 얻게 됩니다. `%%sql`은 Jupyter Notebook에 미리 설정된 `sqlContext`를 사용하여 Hive 쿼리를 실행하도록 지시합니다. 쿼리는 기본적으로 모든 HDInsight 클러스터와 함께 제공되는 Hive 테이블(**hivesampletable**)에서 상위 10개의 행을 검색합니다. 결과를 얻는데 약 30초가 걸립니다. 출력은 다음과 같이 표시됩니다. 

    ![HDInsight Spark의 Hive 쿼리](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark의 Hive 쿼리")

    Jupyter에서 쿼리를 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다.
    
2. 또 다른 쿼리를 실행하여 `hivesampletable`의 데이터를 봅니다.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    쿼리 출력이 표시되도록 화면이 새로 고쳐집니다.

    ![HDInsight Spark의 Hive 쿼리 출력](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark의 Hive 쿼리 출력")

2. 노트북의 **파일** 메뉴에서 **닫기 및 중지**를 선택합니다. 노트북을 종료하면 클러스터 리소스가 릴리스됩니다.

## <a name="clean-up-resources"></a>리소스 정리
HDInsight는 Azure Storage 또는 Azure Data Lake Store에 데이터를 저장하므로 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. [다음 단계](#next-steps)에 나열된 자습서를 즉시 수행하려는 경우 클러스터를 유지할 수 있습니다.

Azure Portal로 다시 전환하고, **삭제**를 선택합니다.

![HDInsight 클러스터 삭제](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

또한 리소스 그룹 이름을 선택하여 리소스 그룹 페이지를 연 다음, **리소스 그룹 삭제**를 선택할 수도 있습니다. 리소스 그룹을 삭제하여 HDInsight Spark 클러스터와 기본 저장소 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계 

이 빠른 시작에서는 HDInsight Spark 클러스터를 만들고 기본 Spark SQL 쿼리를 실행하는 방법을 알아보았습니다. 다음 자습서를 진행하여 샘플 데이터에서 대화형 쿼리를 실행하는 데 HDInsight Spark 클러스터를 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
>[Spark에서 대화형 쿼리 실행](./apache-spark-load-data-run-query.md)
