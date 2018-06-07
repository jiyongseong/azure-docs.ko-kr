---
title: PowerShell을 사용하여 Azure Data Lake Store 시작 | Microsoft Docs
description: Azure PowerShell을 사용하여 데이터 레이크 저장소 계정을 만들고 기본 작업을 수행합니다.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 57b7755bda499cd7113f84d9950b1b06175e11b7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake Store에 대한 자세한 내용은 [Data Lake Store 개요](data-lake-store-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure PowerShell 1.0 이상**. [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a name="authentication"></a>인증
이 문서는 Data Lake Store에 Azure 계정 자격 증명을 입력하라는 메시지가 표시되는 보다 간단한 인증 방법을 사용합니다. Data Lake Store 계정 및 파일 시스템에 대한 액세스 수준은 로그인한 사용자의 액세스 수준을 따릅니다. 하지만 Data Lake Store에 인증하는 다른 방법인 **최종 사용자 인증** 또는 **서비스간 인증**도 있습니다. 지침 및 인증 방법에 대한 자세한 내용은 [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md) 또는 [서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="create-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 만들기
1. 바탕 화면에서 새 Windows PowerShell 창을 엽니다. 다음 코드 조각을 입력하여 Azure 계정에 로그인하고 구독을 설정한 다음 Data Lake Store 공급자를 등록합니다. 로그인하라는 메시지가 표시되면 구독 관리자/소유자 중 하나로 로그인해야 합니다.

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Azure 데이터 레이크 저장소 계정은 Azure 리소스 그룹과 연결됩니다. Azure 리소스 그룹을 만드는 작업부터 시작합니다.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure 리소스 그룹 만들기](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure 리소스 그룹 만들기")
3. Azure 데이터 레이크 저장소 계정을 만듭니다. 지정하는 이름은 소문자와 숫자만 포함해야 합니다.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake Store 계정 만들기](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake Store 계정 만들기")
4. 계정이 성공적으로 만들어졌는지 확인합니다.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    cmdlet에 대한 출력은 **True**여야 합니다.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Azure 데이터 레이크 저장소에서 디렉터리 구조 만들기
Azure 데이터 레이크 저장소 계정에서 디렉터리를 만들어 데이터를 관리하고 저장할 수 있습니다.

1. 루트 디렉터리를 지정합니다.

        $myrootdir = "/"
2. 지정된 루트 아래 **mynewdirectory** 라는 새 디렉터리를 만듭니다.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 새 디렉터리가 성공적으로 만들어졌는지 확인합니다.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    다음 스크린샷에 표시된 것처럼 출력됩니다.

    ![디렉터리 확인](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "디렉터리 확인")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Azure 데이터 레이크 저장소에 데이터 업로드
루트 수준에서 Data Lake Store에 직접 데이터를 업로드하거나 계정 내에서 만든 디렉터리에 업로드할 수 있습니다. 이 섹션의 코드 조각은 이전 섹션에서 만든 디렉터리(**mynewdirectory**)에 일부 샘플 데이터를 업로드하는 방법을 보여 줍니다.

업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다. 파일을 다운로드하고 컴퓨터의 로컬 디렉터리(예: C:\sampledata\)에 저장합니다.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>데이터 레이크 저장소에서 데이터 이름 바꾸기, 다운로드 및 삭제
파일의 이름을 바꾸려면 다음 명령을 사용합니다.

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

파일을 다운로드하려면 다음 명령을 사용합니다.

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

파일을 삭제하려면 다음 명령을 사용합니다.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

메시지가 표시되면 **Y** 를 입력하여 항목을 삭제합니다. 삭제할 파일이 둘 이상 있는 경우 쉼표로 구분하여 모든 경로를 입력할 수 있습니다.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 삭제
다음 명령을 사용하여 데이터 레이크 저장소 계정을 삭제합니다.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

메시지가 표시되면 **Y** 를 입력하여 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Data Lake Store에서 PowerShell을 사용하기 위한 성능 조정 지침](data-lake-store-performance-tuning-powershell.md)
* [빅 데이터 요구 사항에 Azure Data Lake Store 사용](data-lake-store-data-scenarios.md) 
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 Data Lake Store 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
