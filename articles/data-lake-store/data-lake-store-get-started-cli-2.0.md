---
title: "Azure 명령줄 2.0 인터페이스를 사용하여 Azure Data Lake Store 시작 | Microsoft Docs"
description: "Azure 플랫폼 간 명령줄 2.0을 사용하여 Data Lake Store 계정을 만들고 기본 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: b93b9a801b3e5e1fd869cda7f3d67554e2d03195
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Azure Data Lake Store 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Azure CLI 2.0을 사용하여 Azure Data Lake Store 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake Store에 대한 자세한 내용은 [Data Lake Store 개요](data-lake-store-overview.md)를 참조하세요.

Azure CLI 2.0은 Azure 리소스를 관리하기 위한 Azure의 새로운 명령줄 환경입니다. macOS, Linux 및 Windows에서 사용할 수 있습니다. 자세한 내용은 [Azure CLI 2.0 개요](https://docs.microsoft.com/cli/azure)를 참조하세요. 또한 명령 및 구문 전체 목록은 [Azure Data Lake Store CLI 2.0 참조](https://docs.microsoft.com/cli/azure/dls)에서도 확인할 수 있습니다.


## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure CLI 2.0** - 지침은 [Install Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="authentication"></a>인증

이 문서는 최종 사용자로 로그인하는 Data Lake Store에 보다 간단한 인증 방식을 사용합니다. Data Lake Store 계정 및 파일 시스템에 대한 액세스 수준은 로그인한 사용자의 액세스 수준을 따릅니다. 하지만 Data Lake Store에 인증하는 다른 방법인 **최종 사용자 인증** 또는 **서비스간 인증**도 있습니다. 지침 및 인증 방법에 대한 자세한 내용은 [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md) 또는 [서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.


## <a name="log-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

1. Azure 구독에 로그인합니다.

    ```azurecli
    az login
    ```

    다음 단계에 사용할 코드를 가져옵니다. 웹 브라우저를 사용하여 https://aka.ms/devicelogin 페이지를 열고 인증 코드를 입력합니다. 자격 증명을 사용하여 로그인하라는 메시지가 표시됩니다.

2. 로그인하면 해당 창에 계정과 연결된 모든 Azure 구독이 나열됩니다. 특정 구독을 사용하려면 다음 명령을 사용합니다.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 만들기

1. 새 리소스 그룹을 만듭니다. 다음 명령에서 사용하려는 매개 변수 값을 제공합니다. 위치 이름이 공백을 포함하는 경우 이중 따옴표로 묶습니다. 예를 들어 "East US 2"입니다. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. 데이터 레이크 저장소 계정을 만듭니다.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Data Lake 저장소 계정에서 폴더 만들기

Azure 데이터 레이크 저장소 계정에서 폴더를 만들어 데이터를 관리하고 저장할 수 있습니다. 다음 명령을 사용하여 Data Lake Store의 루트에 있는 **mynewfolder**라는 폴더를 만듭니다.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> `--folder` 매개 변수는 명령에서 폴더를 만드는지 확인합니다. 이 매개 변수가 없으면 명령에서 Data Lake Store 계정의 루트에 mynewfolder라는 빈 파일을 만듭니다.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Data Lake Store 계정에 데이터 업로드

루트 수준에서 Data Lake Store에 직접 데이터를 업로드하거나 계정 내에서 만든 폴더에 업로드할 수 있습니다. 아래 코드 조각은 이전 섹션에서 만든 폴더(**mynewfolder**)에 일부 샘플 데이터를 업로드하는 방법을 보여 줍니다.

업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다. 파일을 다운로드하고 컴퓨터의 로컬 디렉터리(예: C:\sampledata\)에 저장합니다.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> 대상에 대해 파일 이름을 포함한 전체 경로를 지정해야 합니다.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Data Lake Store 계정의 파일 나열

다음 명령을 사용하여 데이터 레이크 저장소 계정의 파일을 나열합니다.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

이 명령의 출력은 다음과 유사합니다.

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Data Lake Store 계정에서 데이터 이름 바꾸기, 다운로드 및 삭제 

* **파일의 이름을 바꾸려면**다음 명령을 사용합니다.
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **파일을 다운로드하려면**다음 명령을 사용합니다. 이미 지정한 대상 경로가 있는지 확인합니다.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > 이 명령은 대상 폴더를 만듭니다(없는 경우).
    > 
    >

* **파일을 삭제하려면**다음 명령을 사용합니다.
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    **mynewfolder** 폴더와 **vehicle1_09142014_copy.csv** 파일을 한 명령으로 함께 삭제하려면 --recurse 매개 변수를 사용합니다.

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Data Lake Store 계정에 대한 ACL 및 사용 권한 작업

이 섹션에서는 Azure CLI 2.0을 사용하여 ACL 및 사용 권한을 관리하는 방법에 대해 알아봅니다. Azure Data Lake Store에서 ACL을 구현하는 방법에 대한 자세한 내용은 [Data Lake Store에서 액세스 제어 개요](data-lake-store-access-control.md)를 참조하세요.

* **파일/폴더의 소유자를 업데이트하려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **파일/폴더의 사용 권한을 업데이트하려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **지정된 경로에 대한 ACL을 가져오려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    다음과 유사하게 출력되어야 합니다.

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **ACL에 대한 항목을 설정하려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **ACL에 대한 항목을 제거하려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **전체 기본 ACL을 제거하려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **전체 비기본 ACL을 제거하려면** 다음 명령을 사용하세요.

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Data Lake 저장소 계정 삭제
다음 명령을 사용하여 데이터 레이크 저장소 계정을 삭제합니다.

```azurecli
az dls account delete --account mydatalakestore
```

메시지가 표시되면 **Y** 를 입력하여 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계
* [빅 데이터 요구 사항에 Azure Data Lake Store 사용](data-lake-store-data-scenarios.md) 
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 Data Lake Store 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
