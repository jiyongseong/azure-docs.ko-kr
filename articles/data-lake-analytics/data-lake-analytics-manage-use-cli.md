---
title: Azure 명령줄 인터페이스를 사용하여 Azure Data Lake Analytics 관리 | Microsoft Docs
description: Azure CLI를 사용하여 데이터 레이크 분석 계정, 데이터 원본, 작업, 사용자를 관리하는 방법을 알아봅니다.
services: data-lake-analytics
documentationcenter: ''
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: 9504e1fcbb0128f538cb6e959cf7586f24827836
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Azure 명령줄 인터페이스(CLI)를 사용하여 Azure 데이터 레이크 분석 관리

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure CLI를 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.


**필수 구성 요소**

이 자습서를 시작하기 전에 다음 리소스가 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* Azure CLI. [Azure CLI 설치 및 구성](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)을 참조하세요.

   * 이 데모를 완료하려면 **시험판** [Azure CLI 도구](https://github.com/MicrosoftBigData/AzureDataLake/releases) 를 다운로드하여 설치합니다.

* `az login` 명령을 사용하여 인증하고, 사용하려는 구독을 선택합니다. 회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](/cli/azure/authenticate-azure-cli)을 참조하세요.

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   이제 Data Lake Analytics 및 Data Lake Store 명령에 액세스할 수 있습니다. 다음 명령을 사용하여 Data Lake Store 및 Data Lake Analytics 명령을 나열합니다.

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>계정 관리

데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. Azure HDInsight와 달리 작업을 실행하지 않는 경우 분석 계정에 대해 비용을 지불하지 않습니다. 작업이 실행되는 시간에 대해서만 비용을 지불합니다.  자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.  

### <a name="create-accounts"></a>계정 만들기

다음 명령을 실행하여 Data Lake 계정을 만듭니다. 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>계정 업데이트

다음 명령은 기존 데이터 레이크 분석 계정의 속성을 업데이트합니다.

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>계정 나열

특정 리소스 그룹 내의 데이터 레이크 분석 계정 나열

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>계정에 대한 세부 정보 가져오기

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>계정 삭제

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>데이터 원본 관리

Data Lake Analytics는 현재 다음 두 데이터 원본을 지원합니다.

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

분석 계정을 만들 때 Azure 데이터 레이크 Storage 계정이 기본 Storage 계정이 되도록 지정해야 합니다. 기본 Data Lake 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. 분석 계정을 만든 후 데이터 레이크 Storage 계정 및/또는 Azure Storage 계정을 더 추가할 수 있습니다. 

### <a name="find-the-default-data-lake-store-account"></a>기본 데이터 레이크 저장소 계정 찾기

`az dla account show` 명령을 실행하여 사용되는 기본 Data Lake Store 계정을 볼 수 있습니다. 기본 계정 이름은 defaultDataLakeStoreAccount 속성에 나열됩니다.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>다른 Blob Storage 계정 추가

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Blob 저장소 짧은 이름만 지원됩니다. FQDN(예: “myblob.blob.core.windows.net”)을 사용하지 마십시오.
> 

### <a name="add-additional-data-lake-store-accounts"></a>데이터 레이크 저장소 계정 추가

다음 명령은 지정된 Data Lake Analytics 계정을 추가 Data Lake Store 계정으로 업데이트합니다.

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>기존 데이터 원본 업데이트

기존 Blob 저장소 계정 키를 업데이트하려면:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>데이터 원본 나열:

Data Lake Store 계정 나열:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Blob Storage 계정 나열:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![데이터 레이크 분석은 데이터 원본을 나열합니다.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>데이터 원본 삭제:
데이터 레이크 저장소 계정을 삭제하려면:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Blob 저장소 계정을 삭제하려면:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>작업 관리
작업을 만들려면 데이터 레이크 분석 계정이 있어야 합니다.  자세한 내용은 [데이터 레이크 분석 계정 관리](#manage-accounts)를 참조하세요.

### <a name="list-jobs"></a>작업 나열

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![데이터 레이크 분석은 데이터 원본을 나열합니다.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>작업 세부 정보 가져오기

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>작업 제출

> [!NOTE]
> 작업의 기본 우선 순위는 1000이고 작업에 대한 기본 병렬 처리 수준은 1입니다.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>작업 취소
list 명령을 사용하여 Job ID를 찾은 후 cancel을 사용하여 작업을 취소합니다.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>파이프라인 및 되풀이

**파이프라인 및 되풀이에 대한 정보 가져오기**

`az dla job pipeline` 명령을 사용하여 이전에 제출한 작업의 파이프라인 정보를 확인합니다.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

`az dla job recurrence` 명령을 사용하여 이전에 제출한 작업의 되풀이 정보를 확인합니다.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```


## <a name="use-azure-resource-manager-groups"></a>Azure 리소스 관리자 그룹 사용
응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. Azure Resource Manager를 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 조정된 단일 작업으로 응용 프로그램에 대한 모든 리소스를 배포, 업데이트, 모니터링 또는 삭제할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 

데이터 레이크 분석 서비스는 다음 구성 요소를 포함할 수 있습니다.

* Azure 데이터 레이크 분석 계정
* 필수 기본 Azure 데이터 레이크 Storage 계정
* 추가 Azure 데이터 레이크 Storage 계정
* 추가 Azure Storage 계정

이러한 모든 구성을 쉽게 관리할 수 있도록 하나의 Resource Manager 그룹 아래 만들 수 있습니다.

![Azure 데이터 레이크 분석 계정 및 저장소](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

데이터 레이크 분석 계정 및 종속 저장소 계정은 동일한 Azure 데이터 센터에 있어야 합니다.
그러나 Resource Manager 그룹은 다른 데이터 센터에 있을 수 있습니다.  

## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure 포털을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

