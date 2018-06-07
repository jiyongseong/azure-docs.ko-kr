---
title: Azure Virtual Network에서 Hive를 사용하여 데이터 변환 | Microsoft Docs
description: 이 자습서에서는 Azure Data Factory에서 Hive 작업을 사용하여 데이터를 변환하는 단계별 지침을 제공합니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2018
ms.author: douglasl
ms.openlocfilehash: e9ec338e04c456a9a91d51a7a4741e4760f1b3c9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory에서 Hive 작업을 사용하여 Azure Virtual Network에서 데이터 변환
이 자습서에서는 Azure Portal을 사용하여 Azure VNet(Virtual Network)에 있는 HDInsight 클러스터에서 Hive 활동을 통해 데이터를 변환하는 Data Factory 파이프라인을 만듭니다. 이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * 자체 호스팅 Integration Runtime 만들기
> * Azure Storage 및 Azure HDInsight 연결된 서비스 만들기
> * Hive 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 트리거
> * 파이프라인 실행을 모니터링합니다. 
> * 출력 확인

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
- **Azure Storage 계정**. Hive 스크립트를 만들어 Azure 저장소에 업로드합니다. Hive 스크립트의 출력은 이 저장소 계정에 저장됩니다. 이 샘플에서 HDInsight 클러스터는 이 Azure Storage 계정을 기본 저장소로 사용합니다. 
- **Azure Virtual Network** - 아직 없는 경우 [이 지침](../virtual-network/quick-create-portal.md)에 따라 Azure Virtual Network를 만듭니다. 이 샘플에서 HDInsight는 Azure Virtual Network에 있습니다. 다음은 Azure Virtual Network의 샘플 구성입니다. 

    ![가상 네트워크 만들기](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **HDInsight 클러스터** - [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight/hdinsight-extend-hadoop-virtual-network.md) 문서에 따라 HDInsight 클러스터를 만들고 이전 단계에서 만든 가상 네트워크에 조인합니다. 다음은 가상 네트워크에 속한 HDInsight의 샘플 구성입니다. 

    ![가상 네트워크의 HDInsight](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.
- **가상 머신**. Azure VM(가상 머신)을 만들고 HDInsight 클러스터가 포함된 동일한 가상 네트워크에 조인합니다. 자세한 내용은 [가상 머신을 만드는 방법](../virtual-network/quick-create-portal.md#create-virtual-machines)을 참조하세요. 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Blob Storage 계정에 Hive 스크립트 업로드

1. 다음 내용이 포함된 **hivescript.hql**이라는 Hive SQL 파일을 만듭니다.

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Azure Blob Storage에 아직 없는 경우 **adftutorial**이라는 컨테이너를 만듭니다.
3. **hivescripts**라는 폴더를 만듭니다.
4. **hivescript.hql** 파일을 **hivescripts** 하위 폴더로 업로드합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.    
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialHiveFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameMyAzureSsisDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)** 를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 데이터 팩터리 만들기를 지원하는 위치만 목록에 표시됩니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. **작성 및 모니터링**을 클릭하여 별도의 탭에서 Data Factory UI(사용자 인터페이스)를 시작합니다.
11. **시작** 페이지에서 다음 이미지와 같이 왼쪽 패널의 **편집** 탭으로 전환합니다. 

   ![편집 탭](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기
Hadoop 클러스터는 가상 네트워크 내에 있으므로 동일한 가상 네트워크에 자체 호스팅 IR(통합 런타임)을 설치해야 합니다. 이 섹션에서는 새 VM을 만들고, 동일한 가상 네트워크에 조인하고, 자체 호스팅 IR을 설치합니다. 자체 호스팅 IR을 통해 Data Factory 서비스에서 처리 요청을 가상 네트워크 내의 HDInsight와 같은 계산 서비스에 디스패치할 수 있습니다. 또한 가상 네트워크 내의 데이터 저장소에서 Azure로 데이터를 이동할 수 있습니다. 데이터 저장소 또는 계산이 온-프레미스 환경에 있는 경우에도 자체 호스팅 IR을 사용합니다. 

1. Azure Data Factory UI의 창 아래쪽에서 **연결**을 클릭하고, **Integration Runtime** 탭으로 전환한 다음, 도구 모음에서 **+ 새로 만들기** 단추를 클릭합니다. 

   ![새 Integration Runtime 메뉴](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. **Integration Runtime 설정** 창에서 **Perform data movement and dispatch activities to external computes**(데이터 이동을 수행하고 활동을 외부 계산으로 디스패치합니다) 옵션을 선택하고, **다음**을 클릭합니다. 

   ![[데이터 이동을 수행하고 활동을 외부 계산으로 디스패치합니다] 옵션 선택](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. **사설망**을 선택하고 **다음**을 클릭합니다.
    
   ![사설망 선택](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. **이름**에 대해 **MySelfHostedIR**을 입력하고 **다음**을 클릭합니다. 

   ![통합 런타임 이름 지정](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. 복사 단추를 클릭하여 통합 런타임에 대한 **인증 키**를 복사하고 저장합니다. 창을 계속 열어 둡니다. 이 키를 사용하여 가상 머신에 설치한 IR을 등록합니다. 

   ![인증 키 복사](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>가상 머신에 IR 설치

1. Azure VM에서 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 다운로드합니다. 이전 단계에서 얻은 **인증 키**를 사용하여 자체 호스팅 통합 런타임을 수동으로 등록합니다. 

    ![통합 런타임 등록](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 메시지가 표시됩니다. 
   
    ![성공적으로 등록되었습니다.](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. **구성 관리자 시작**을 클릭합니다. 노드가 클라우드 서비스에 연결되면 다음 페이지가 표시됩니다. 
   
    ![노드가 연결됨](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Azure Data Factory UI의 자체 호스팅 IR

1. **Azure Data Factory UI**에서 자체 호스팅 VM 이름의 이름과 해당 상태를 확인해야 합니다.

   ![기존 자체 호스팅 노드](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. **마침**을 클릭하여 **Integration Runtime 설정** 창을 닫습니다. 자체 호스팅 IR이 통합 런타임 목록에 표시됩니다.

   ![목록에 표시된 자체 호스팅 IR](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>연결된 서비스 만들기

이 섹션에서는 두 개의 연결된 서비스를 작성하고 배포합니다.
- Azure Storage 계정을 데이터 팩터리에 연결하는 **Azure Storage 연결된 서비스**. 이 저장소는 HDInsight 클러스터에서 사용하는 기본 저장소입니다. 이 경우 이 Azure Storage 계정을 사용하여 Hive 스크립트와 스크립트 출력을 저장합니다.
- **HDInsight 연결된 서비스**. Azure Data Factory에서 Hive 스크립트를 이 HDInsight 클러스터에 제출하여 실행합니다.

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

1. **연결된 서비스** 탭으로 전환하고 **새로 만들기**를 클릭합니다.

   ![새 연결된 서비스 단추](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. **새 연결된 서비스** 창에서 **Azure Blob Storage**를 선택하고 **계속**을 클릭합니다. 

   ![Azure Blob Storage 선택](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다.
    2. **통합 런타임을 통해 연결**에 대해 **MySelfHostedIR**을 선택합니다.
    3. **저장소 계정 이름**에 대해 Azure Storage 계정을 선택합니다. 
    4. 저장소 계정에 대한 연결을 테스트하려면 **연결 테스트**를 클릭합니다.
    5. **저장**을 클릭합니다.
   
        ![Azure Blob Storage 계정 지정](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>HDInsight 연결된 서비스 만들기

1. **새로 만들기** 단추를 다시 클릭하여 또 하나의 연결된 서비스를 만듭니다. 
    
   ![새 연결된 서비스 단추](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. **계산** 탭으로 전환하고, **Azure HDInsight**를 선택하고, **계속**을 클릭합니다.

    ![Azure HDInsight 선택](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **AzureHDInsightLinkedService**를 입력합니다.
    2. **Bring your own HDInsight**(사용자 고유 HDInsight 가져오기)를 선택합니다. 
    3. **Hdi 클러스터**에 대해 HDInsight 클러스터를 선택합니다. 
    4. HDInsight 클러스터에 대한 **사용자 이름**을 입력합니다.
    5. 사용자에 대한 **암호**를 입력합니다. 
    
        ![Azure HDInsight 설정](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

이 문서에서는 인터넷을 통해 클러스터에 액세스할 수 있다고 가정합니다. 예를 들어 `https://clustername.azurehdinsight.net`에 있는 클러스터에 연결할 수 있습니다. 이 주소는 NSG(네트워크 보안 그룹) 또는 UDR(사용자 정의 경로)을 사용하여 인터넷 액세스를 제한한 경우 사용할 수 없는 공용 게이트웨이를 사용합니다. Data Factory에서 Azure Virtual Network의 HDInsight 클러스터에 작업을 제출하려면, URL을 HDInsight에서 사용하는 게이트웨이의 사설 IP 주소로 확인할 수 있도록 Azure Virtual Network를 구성해야 합니다.

1. Azure Portal에서 HDInsight가 있는 Virtual Network를 엽니다. 이름이 `nic-gateway-0`으로 시작하는 네트워크 인터페이스를 엽니다. 사설 IP 주소를 적어 둡니다. 예를 들어 10.6.0.15입니다. 
2. Azure Virtual Network에 DNS 서버가 있는 경우 `https://<clustername>.azurehdinsight.net` HDInsight 클러스터 URL을 `10.6.0.15`로 확인할 수 있도록 DNS 레코드를 업데이트합니다. Azure Virtual Network에 DNS 서버가 없으면, 자체 호스팅 통합 런타임 노드로 등록된 모든 VM의 호스트 파일(C:\Windows\System32\drivers\etc)을 편집하여 다음과 비슷한 항목을 추가함으로써 이 문제를 일시적으로 해결할 수 있습니다. 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다. 
이 단계에서는 Hive 작업이 있는 새 파이프라인을 만듭니다. 이 작업은 Hive 스크립트를 실행하여 샘플 테이블의 데이터를 반환하고 사용자가 정의한 경로에 저장합니다.

다음 사항에 유의하세요.

- **scriptPath**는 MyStorageLinkedService에 사용한 Azure Storage 계정의 Hive 스크립트 경로를 가리킵니다. 경로는 대/소문자를 구분합니다.
- **output**은 Hive 스크립트에서 사용되는 인수입니다. `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` 형식을 사용하여 Azure Storage의 기존 폴더를 가리킵니다. 경로는 대/소문자를 구분합니다. 

1. Data Factory UI의 왼쪽 창에서 **+(더하기)** 를 클릭하고, **파이프라인**을 클릭합니다. 

    ![새 파이프라인 메뉴](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. **활동** 도구 상자에서 **HDInsight**를 펼치고, **Hive** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

    ![Hive 활동 끌어서 놓기](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. 속성 창에서 **HDI 클러스터** 탭으로 전환하고, **HDInsight 연결된 서비스**에 대해 **AzureHDInsightLinkedService**를 선택합니다.

    ![HDInsight 연결된 서비스 선택](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. **스크립트** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **스크립트 연결된 서비스**에 대해 **AzureStorageLinkedService**를 선택합니다. 
    2. **파일 경로**에서 **저장소 찾아보기**를 클릭합니다. 
 
        ![저장소 찾아보기](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. **파일 또는 폴더 선택** 창에서 **adftutorial** 컨테이너의 **hivescripts** 폴더로 이동하고, **hivescript.hql**을 선택하고, **마침**을 클릭합니다.  
        
        ![파일 또는 폴더 선택](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. **파일 경로**에서 **adftutorial/hivescripts/hivescript.hql**이 표시되는지 확인합니다.

        ![스크립트 설정](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. **스크립트 탭**에서 **고급** 섹션을 펼칩니다. 
    6. **매개 변수**에 대해 **스크립트에서 자동 채우기**를 클릭합니다. 
    7. **출력** 매개 변수에 대한 값을 `wasb://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` 형식으로 입력합니다. 예: `wasb://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`
 
        ![스크립트 인수](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Data Factory에 아티팩트를 게시하려면 **게시**를 클릭합니다.

    ![게시](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거

1. 먼저 도구 모음에서 **유효성 검사** 단추를 클릭하여 파이프라인에 대한 유효성을 검사합니다. **오른쪽 화살표(>>)** 를 클릭하여 **파이프라인 유효성 검사 출력** 창을 닫습니다. 

    ![파이프라인 유효성 검사](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. 파이프라인 실행을 트리거하려면 도구 모음에서 [트리거], [지금 트리거]를 차례로 클릭합니다. 

    ![지금 트리거](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. 왼쪽의 **모니터** 탭으로 전환합니다. **파이프라인 실행** 목록에 파이프라인 실행이 표시됩니다. 

    ![파이프라인 실행 모니터링](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. 목록을 새로 고치려면 **새로 고침**을 클릭합니다.
4. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기**를 클릭합니다. 다른 작업 링크는 파이프라인을 중지/다시 실행하기 위한 링크입니다. 

    ![활동 실행 보기](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. **HDInsightHive** 유형의 파이프라인에는 하나의 활동만 있으므로 하나의 활동 실행만 표시됩니다. 이전 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다.

    ![작업 실행](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. **adftutorial** 컨테이너의 **outputfolder**에 있는 출력 파일이 표시되는지 확인합니다. 

    ![출력 파일](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * 자체 호스팅 Integration Runtime 만들기
> * Azure Storage 및 Azure HDInsight 연결된 서비스 만들기
> * Hive 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 트리거
> * 파이프라인 실행을 모니터링합니다. 
> * 출력 확인

Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[Data Factory 제어 흐름 분기 및 연결](tutorial-control-flow-portal.md)



