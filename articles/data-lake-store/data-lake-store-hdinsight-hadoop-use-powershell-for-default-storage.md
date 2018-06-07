---
title: PowerShell을 사용하여 Data Lake Store를 기본 저장소로 사용하여 HDInsight 클러스터 만들기 | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure Data Lake Store로 HDInsight Hadoop 클러스터 만들기 및 사용
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 2a9f2e9c2634173cf2feb9ded2e87e49f9c8d1e4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>PowerShell을 사용하여 Data Lake Store를 기본 저장소로 사용한 HDInsight 클러스터 만들기

> [!div class="op_single_selector"]
> * [Azure Portal 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용(기본 저장소의 경우)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 사용(추가 저장소의 경우)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Azure PowerShell을 사용하여 Azure Data Lake Store를 기본 저장소로 사용하여 HDInsight 클러스터를 구성하는 방법에 대해 알아봅니다. Data Lake Store를 추가 저장소로 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침은 [Data Lake Store를 추가 저장소로 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-powershell.md)를 참조하세요.

Data Lake Store에서 HDInsight를 사용하는 몇 가지 중요한 고려 사항은 다음과 같습니다.

* 기본 저장소인 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.5 및 3.6에서 사용할 수 있습니다.

* 기본 저장소인 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight Premium 클러스터에서 *사용할 수 없습니다*.

PowerShell을 사용하여 Data Lake Store를 사용하도록 HDInsight를 구성하려면 다음 5개 섹션의 지침을 따릅니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 요구 사항을 충족하는지 확인합니다.

* **Azure 구독**: [Azure 무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/)로 이동하세요.
* **Azure PowerShell 1.0 이상**: [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.
* **Windows SDK(소프트웨어 개발 키트)**: Windows SDK를 설치하려면 [Windows 10용 도구 다운로드](https://dev.windows.com/downloads)로 이동합니다. 보안 인증서를 만드는 데 SDK가 사용됩니다.
* **Azure Active Directory 서비스 주체**: 이 자습서에서는 Azure AD(Azure Active Directory)에서 서비스 주체를 만드는 방법을 설명합니다. 그러나 서비스 주체를 만들려면 Azure AD 관리자여야 합니다. 관리자인 경우 이 필수 요소를 건너뛰고 자습서를 진행할 수 있습니다.

    >[!NOTE]
    >Azure AD 관리자인 경우에만 서비스 주체를 만들 수 있습니다. Azure AD 관리자가 서비스 주체를 만들어야 Data Lake Store와 HDInsight 클러스터를 만들 수 있습니다. [인증서를 사용하여 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority)에 설명된 대로 인증서를 사용하여 서비스 주체를 만들어야 합니다.
    >

## <a name="create-a-data-lake-store-account"></a>Data Lake 저장소 계정 만들기

Data Lake Store 계정을 만들려면 다음을 수행합니다.

1. 바탕 화면에서 PowerShell 창을 열고 다음 코드 조각을 입력합니다. 로그인하라는 메시지가 표시되면 구독 관리자 또는 소유자 중 하나로 로그인합니다. 

        # Sign in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Data Lake Store 리소스 공급자를 등록하고 `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`과 유사한 오류가 발생하는 경우 구독을 Data Lake Store에 대한 허용 목록에 추가할 수 없습니다. Data Lake Store 공개 미리 보기에 Azure 구독을 사용하려면 [Azure Portal을 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)의 지침에 따르세요.
    >

2. Data Lake Store 계정은 Azure 리소스 그룹과 연결됩니다. 리소스 그룹을 만들기 시작합니다.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    다음과 유사한 출력이 표시됩니다.

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Data Lake Store 계정을 만듭니다. 지정하는 계정 이름은 소문자와 숫자만 포함해야 합니다.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    다음과 유사한 출력이 표시됩니다.

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

4. Data Lake Store를 기본 저장소로 사용하면 클러스터를 만드는 동안 클러스터 관련 파일을 복사하는 루트 경로를 지정해야 합니다. 코드 조각에서 **/clusters/hdiadlcluster**라는 루트 경로를 만들려면 다음과 같은 cmdlet을 사용합니다.

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>데이터 레이크 저장소에 대한 역할 기반 액세스를 위한 인증 설정
모든 Azure 구독은 Azure AD 엔터티와 연결됩니다. Azure Portal 또는 Azure Resource Manager API를 사용하여 구독 리소스에 액세스하는 사용자와 서비스는 먼저 Azure AD에 인증해야 합니다. Azure 리소스에 대한 적절한 역할을 할당하여 Azure 구독과 서비스에 액세스 권한을 부여합니다. 서비스의 경우 서비스 주체는 Azure AD에서 서비스를 식별합니다.

이 섹션에서는 HDInsight와 같은 응용 프로그램 서비스에 권한을 부여하고 Azure 리소스(앞에서 만든 Data Lake Store 계정)에 액세스하는 방법을 설명합니다. 이렇게 하려면 응용 프로그램에 서비스 주체를 만들고 PowerShell을 통해 역할을 할당합니다.

Azure Data Lake에 대한 Active Directory 인증을 설정하려면 다음 두 개의 섹션에 있는 태스크를 수행해야 합니다.

### <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기
이 섹션의 단계를 진행하기 전에 [Windows SDK](https://dev.windows.com/en-us/downloads) 가 설치되어 있는지 확인합니다. 또한 인증서를 만든 *C:\mycertdir*과 같은 디렉터리를 만들었어야 합니다.

1. PowerShell 창에서 Windows SDK를 설치한 위치로 이동(일반적으로 *C:\Program Files (x86)\Windows Kits\10\bin\x86*)하고 [MakeCert][makecert] 유틸리티를 사용하여 자체 서명된 인증서와 개인 키를 만듭니다. 다음 명령을 사용합니다.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    개인 키 암호를 입력하라는 메시지가 표시됩니다. 명령을 성공적으로 실행한 후에 지정한 인증서 디렉터리에서 **CertFile.cer** 및 **mykey.pvk**를 확인해야 합니다.
2. [Pvk2Pfx][pvk2pfx] 유틸리티를 사용하여 MakeCert가 생성한 .pvk 및 .cer 파일을 .pfx 파일로 변환합니다. 다음 명령 실행:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    메시지가 표시되면 이전에 지정한 개인 키 암호를 입력합니다. **-po** 매개 변수에 대해 지정한 값은 .pfx 파일에 연관된 암호입니다. 또한 명령을 성공적으로 완료한 후에 지정한 인증서 디렉터리에서 **CertFile.pfx**를 확인해야 합니다.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Azure AD 및 서비스 주체 만들기
이 섹션에서는 Azure AD 응용 프로그램에 대한 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 인증서를 제공하여 서비스 주체로 인증합니다. Azure AD에서 응용 프로그램을 만들려면 다음과 같은 명령을 실행합니다.

1. PowerShell 콘솔 창에 다음 cmdlet을 붙여 넣습니다. **-DisplayName** 속성에 대해 지정한 값이 고유한지 확인합니다. **-HomePage** 및 **-IdentiferUris**의 값은 자리 표시자이며 확인되지 않습니다.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. 응용 프로그램 ID를 사용하여 서비스 주체를 만듭니다.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. 이전에 지정한 루트 경로에 있는 Data Lake Store 루트 및 모든 폴더에 서비스 주체 액세스 권한을 부여합니다. 다음 cmdlet을 사용합니다.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Data Lake Store를 기본 저장소로 사용하여 HDInsight 클러스터 만들기

이 섹션에서는 Data Lake Store를 기본 저장소로 사용하여 HDInsight 클러스터를 만듭니다. 이 릴리스의 경우 HDInsight 클러스터와 Data Lake Store는 동일한 위치에 있어야 합니다.

1. 구독 테넌트 ID를 검색하고 나중에 사용하기 위해 저장합니다.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. 다음 cmdlet을 사용하여 HDInsight 클러스터를 만듭니다.

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    cmdlet이 성공적으로 완료된 후에 클러스터 세부 정보를 나열하는 출력이 표시됩니다.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>HDInsight 클러스터에서 테스트 작업을 실행하여 Data Lake Store 사용
HDInsight 클러스터를 구성한 후에 테스트 작업을 실행하여 Data Lake Store에 액세스할 수 있는지 확인할 수 있습니다. 이렇게 하려면 *<cluster root>/example/data/sample.log*에서 Data Lake Store에서 사용할 수 있는 샘플 데이터를 사용하여 테이블을 만드는 샘플 Hive 작업을 실행합니다.

이 섹션에서는 사용자가 만든 HDInsight Linux 클러스터에 대한 SSH(보안 셸) 연결을 확인하고 샘플 Hive 쿼리를 실행합니다.

* Windows 클라이언트를 사용하여 클러스터로 SSH 연결을 설정하는 경우 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.
* Linux 클라이언트를 사용하여 클러스터로 SSH 연결을 설정하는 경우 [Linux에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. 연결한 후에 다음 명령을 사용하여 Hive CLI(명령줄 인터페이스)를 시작합니다.

        hive
2. CLI를 사용하여 다음 문을 입력합니다. 그러면 Data Lake Store에서 샘플 데이터를 사용하여 **vehicles**라는 새 테이블을 만듭니다.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    SSH 콘솔에서 쿼리 결과가 나타나야 합니다.

    >[!NOTE]
    >앞의 CREATE TABLE 명령에서 샘플 데이터에 대한 경로는 `adl:///example/data/`이고 여기서 `adl:///`는 클러스터 루트입니다. 이 자습서에서 지정한 클러스터 루트에 따르면 명령은 `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`입니다. 짧은 대체 루트를 사용하거나 클러스터 루트에 전체 경로를 제공할 수 있습니다.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>HDFS 명령을 사용하여 Data Lake Store에 액세스
Data Lake Store를 사용하도록 HDInsight 클러스터를 구성한 후에 HDFS(Hadoop 분산 파일 시스템) 셸 명령을 사용하여 저장소에 액세스할 수 있습니다.

이 섹션에서는 사용자가 만든 HDInsight Linux 클러스터에 대한 SSH 연결을 확인하고 HDFS 명령을 실행합니다.

* Windows 클라이언트를 사용하여 클러스터로 SSH 연결을 설정하는 경우 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.
* Linux 클라이언트를 사용하여 클러스터로 SSH 연결을 설정하는 경우 [Linux에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

연결을 완료한 후에 다음 HDFS 파일 시스템 명령을 사용하여 Data Lake Store의 파일을 나열합니다.

    hdfs dfs -ls adl:///

`hdfs dfs -put` 명령을 사용하여 일부 파일을 Data Lake Store에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.

## <a name="see-also"></a>참고 항목
* [Azure HDInsight 클러스터에 Data Lake Store 사용](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure Portal: HDInsight 클러스터를 만들어 Data Lake Store 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
