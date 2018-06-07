---
title: 스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정 - Azure | Microsoft Docs
description: '스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터에 사용자 지정 구성 요소를 추가합니다. 스크립트 동작은 클러스터 구성을 사용자 지정하거나 다른 서비스 및 유틸리티(예: Hue, Solr 또는 R)를 추가하는 데 사용할 수 있는 Bash 스크립트입니다.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 856a94b0cf64a20fbe9267b76422c47d88d21f43
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정

HDInsight는 클러스터를 사용자 지정하는 사용자 지정 스크립트를 호출하는 **스크립트 작업**이라는 구성 방법을 제공합니다. 이러한 스크립트는 추가 구성 요소를 설치하고 구성 설정을 변경하는 데 사용합니다. 스크립트 작업은 클러스터를 만드는 중이거나 만든 후에 사용할 수 있습니다.

> [!IMPORTANT]
> 이미 실행 중인 클러스터에 스크립트 작업을 사용하는 기능은 Linux 기반 HDInsight 클러스터에만 제공됩니다.
>
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

스크립트 작업을 Azure Marketplace에 HDInsight 응용 프로그램으로 게시할 수도 있습니다. HDInsight 응용 프로그램에 대한 자세한 내용은 [Azure Marketplace에 HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md)를 참조하세요.

## <a name="permissions"></a>권한

도메인 가입 HDInsight 클러스터를 사용하는 경우 클러스터에서 스크립트 동작을 사용할 때는 다음 두 가지 Ambari 권한이 필요합니다.

* **AMBARI.RUN\_CUSTOM\_COMMAND**: Ambari 관리자 역할은 기본적으로 이 권한을 가집니다.
* **CLUSTER.RUN\_CUSTOM\_COMMAND**: HDInsight 클러스터 관리자와 Ambari 관리자는 기본적으로 이 권한을 가집니다.

도메인 가입 HDInsight에서 권한으로 작업하는 방법에 대한 자세한 내용은 [도메인 가입 HDInsight 클러스터 관리](./domain-joined/apache-domain-joined-manage.md)를 참조하세요.

## <a name="access-control"></a>Access Control

Azure 구독의 관리자/소유자가 아닌 경우 적어도 HDInsight 클러스터가 포함된 리소스 그룹에 대한 **참여자** 액세스 권한이 있어야 합니다.

또한 HDInsight 클러스터를 만드는 경우 Azure 구독에 대한 **참가자** 이상의 액세스 권한이 있는 사용자는 HDInsight에 대한 공급자를 미리 등록해 두어야 합니다. 공급자 등록은 구독에 대해 참가자 액세스가 있는 사용자가 구독에서 처음으로 리소스를 만들 때 이루어집니다. [REST를 사용하여 공급자를 등록](https://msdn.microsoft.com/library/azure/dn790548.aspx)하면 리소스를 만들지 않고도 수행될 수 있습니다.

액세스 관리 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 포털에서 액세스 관리 시작](../role-based-access-control/overview.md)
* [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)

## <a name="understanding-script-actions"></a>스크립트 동작 이해

스크립트 작업은 HDInsight 클러스터의 노드에서 실행되는 Bash 스크립트입니다. 다음은 스크립트 작업의 특징 및 기능입니다.

* HDInsight 클러스터에서 액세스할 수 있는 URI에 저장되어야 합니다. 가능한 저장소 위치는 다음과 같습니다.

    * HDInsight 클러스터에서 액세스할 수 있는 **Azure Data Lake Store** 계정 - HDInsight에서 Azure Data Lake Store를 사용하는 방법에 대한 자세한 내용은 [Azure Data Lake Store에서 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

        Data Lake Store에 저장된 스크립트를 사용하는 경우 URI 형식은 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`입니다.

        > [!NOTE]
        > HDInsight에서 Data Lake Store에 액세스하는 데 사용하는 서비스 주체에는 스크립트에 대한 읽기 권한이 있어야 합니다.

    * HDInsight 클러스터에 대한 기본 또는 추가 저장소 계정인 **Azure Storage 계정**의 Blob - HDInsight는 클러스터를 만드는 동안 이러한 두 유형의 저장소 계정 모두에 대해 액세스 권한을 부여받습니다.

    * Azure Blob, GitHub, OneDrive, Dropbox 등과 같은 공용 파일 공유 서비스

        URI 예제는 [예제 스크립트 작업 스크립트](#example-script-action-scripts) 섹션을 참조하세요.

        > [!WARNING]
        > HDInsight는 __범용__ Azure Storage 계정만 지원합니다. 현재 __Blob 저장소__ 계정 유형은 지원하지 않습니다.

* 헤드 노드 또는 작업자 노드와 같은 **특정 노드 유형에서만 실행**되도록 제한할 수 있습니다.

* **지속형** 또는 **임시** 스크립트일 수 있습니다.

    **지속형** 스크립트는 크기 조정 작업을 통해 클러스터에 추가되는 새 작업자 노드를 사용자 지정하는 데 사용됩니다. 지속형 스크립트는 크기 조정 작업이 발생하면 헤드 노드 등의 다른 노드 유형에도 변경 사항을 적용할 수 있습니다.

  > [!IMPORTANT]
  > 지속형 스크립트 작업은 고유한 이름이 있어야 합니다.

    **임시** 스크립트는 지속되지 않습니다. 스크립트를 실행한 후 클러스터에 추가된 작업자 노드에 적용되지 않습니다. 이후에 임시 스크립트를 지속형 스크립트로 승격하거나 지속형 스크립트를 임시 스크립트로 강등할 수 있습니다.

  > [!IMPORTANT]
  > 클러스터를 만들 때 사용되는 스크립트 작업은 자동으로 보존됩니다.
  >
  > 사용자가 지속형 스크립트로 지정하더라도 실패하는 스크립트는 보존되지 않습니다.

* 실행 중에 스크립트에서 사용하는 **매개 변수**를 수락할 수 있습니다.

* 클러스터 노드에서 **루트 수준 권한**으로 실행합니다.

* **Azure Portal**, **Azure PowerShell**, **Azure CLI v1.0** 또는 **HDInsight .NET SDK**를 통해 사용할 수 있습니다.

클러스터는 실행된 모든 스크립트 기록을 보관합니다. 이 기록은 승격 또는 강등 작업에 스크립트의 ID를 찾아야 할 경우에 유용합니다.

> [!IMPORTANT]
> 스크립트 작업을 통해 변경된 내용을 자동으로 취소하는 방법은 없습니다. 변경 사항을 수동으로 되돌리거나 되돌린 스크립트를 제공하세요.

### <a name="script-action-in-the-cluster-creation-process"></a>클러스터 만들기 프로세스의 스크립트 동작

클러스터를 만들 때 사용되는 스크립트 동작은 기존 클러스터에서 실행되는 스크립트 동작과 약간 다릅니다.

* 이 스크립트는 **자동으로 보존**됩니다.

* 스크립트가 **실패**하면 클러스터 만들기 프로세스가 실패할 수 있습니다.

다음 다이어그램에서는 만들기 프로세스 중 스크립트 동작을 실행할 때를 보여줍니다.

![HDInsight 클러스터 사용자 지정 및 클러스터 만드는 동안의 단계][img-hdi-cluster-states]

HDInsight를 구성하는 동안 스크립트가 실행됩니다. 스크립트는 클러스터에 지정된 모든 노드에서 병렬로 실행되고, 노드에 대한 루트 권한으로 실행됩니다.

> [!NOTE]
> Hadoop 관련 서비스를 포함하여 서비스 중지 및 시작 같은 작업을 수행할 수 있습니다. 서비스를 중지하는 경우 스크립트가 완료되기 전에 Ambari 서비스 및 기타 Hadoop 관련 서비스가 실행 중인지 확인해야 합니다. 클러스터가 생성되는 동안 클러스터의 상태를 확인하려면 이러한 서비스가 필요합니다.


클러스터를 만드는 동안 한 번에 여러 스크립트 작업을 사용할 수 있습니다. 이러한 스크립트는 지정된 순서로 호출됩니다.

> [!IMPORTANT]
> 스크립트 작업은 60분 이내에 완료하지 않으면 시간이 초과됩니다. 클러스터 프로비전 중에 스크립트가 다른 설정 및 구성 프로세스와 동시에 실행됩니다. CPU 시간 또는 네트워크 대역폭 등의 리소스에 대한 경합으로 인해 스크립트 실행이 개발 환경에서보다 더 오래 걸릴 수 있습니다.
>
> 스크립트 실행 시간을 최소화하려면 다운로드 및 소스에서의 응용 프로그램 컴파일 등과 같은 작업은 실행하지 않습니다. Azure Storage에서 응용 프로그램을 미리 컴파일하고 이진을 저장합니다.


### <a name="script-action-on-a-running-cluster"></a>실행 중인 클러스터의 스크립트 작업

이미 실행 중인 클러스터에서 실행되는 스크립트가 실패해도 클러스터가 실패 상태로 자동 변경되지 않습니다. 스크립트가 완료되면 클러스터가 "실행 중" 상태로 돌아갈 것입니다.

> [!IMPORTANT]
> 클러스터에 ‘실행 중’ 상태인 경우라도 실패한 스크립트가 중단된 것일 수도 있습니다. 예를 들어, 스크립트는 클러스터에서 필요한 파일을 삭제할 수 있습니다.
>
> 스크립트 동작은 루트 권한으로 실행됩니다. 클러스터에 스크립트 작업을 적용하기 전에 스크립트가 어떤 일을 하는지 정확하게 이해해야 합니다.

클러스터에 스크립트를 적용하면 클러스터 상태가 **실행 중**에서 **수락됨**으로 바뀌었다가 다시 **HDInsight 구성**으로 바뀝니다. 그리고 스크립트가 성공하면 다시 **실행 중**으로 바뀝니다. 스크립트 상태는 스크립트 작업 내역에 기록되며, 이 정보를 사용하여 스크립트가 성공했는지 아니면 실패했는지 확인할 수 있습니다. 예를 들어 `Get-AzureRmHDInsightScriptActionHistory` PowerShell cmdlet을 사용하여 스크립트 상태를 볼 수 있습니다. 이 명령은 다음 텍스트와 비슷한 정보를 반환합니다.

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> 클러스터를 만든 후 클러스터 사용자(관리자) 암호를 변경한 경우 이 클러스터를 실행하는 스크립트 작업에 오류가 발생할 수 있습니다. 작업자 노드를 대상으로 하는 지속적인 스크립트 작업이 있는 경우 클러스터 크기 조정 시 이러한 스크립트가 실패할 수 있습니다.

## <a name="example-script-action-scripts"></a>예제 스크립트 동작 스크립트

스크립트 동작 스크립트는 다음 유틸리티를 통해 사용할 수 있습니다.

* Azure portal
* Azure PowerShell
* Azure CLI v1.0
* HDInsight .NET SDK

HDInsight는 HDInsight 클러스터에서 다음 구성 요소를 설치하는 스크립트를 제공합니다.

| Name | 스크립트 |
| --- | --- |
| **Azure Storage 계정 추가** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh [HDInsight 클러스터에 추가 저장소 추가](hdinsight-hadoop-add-storage.md)를 참조하세요. |
| **Hue 설치** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)을 참조하세요. |
| **Presto 설치** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh [HDInsight 클러스터에 Presto 설치 및 사용](hdinsight-hadoop-install-presto.md)을 참조하세요. |
| **Solr 설치** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh [HDInsight 클러스터에서 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)을 참조하세요. |
| **Giraph 설치** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)을 참조하세요. |
| **Hive 라이브러리 사전 로드** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh [HDInsight 클러스터에서 Hive 라이브러리 추가](hdinsight-hadoop-add-hive-libraries.md)를 참조하세요. |
| **Mono 설치 또는 업데이트** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash [HDInsight에서 Mono 설치 또는 업데이트](hdinsight-hadoop-install-mono.md)를 참조하세요. |

## <a name="use-a-script-action-during-cluster-creation"></a>클러스터를 만드는 동안 스크립트 동작 사용

이 섹션에는 HDInsight 클러스터를 만들 때 스크립트 작업을 사용할 수 있는 다양한 방법에 대한 예제를 제공합니다.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>클러스터를 만드는 동안 Azure Portal에서 스크립트 동작 사용

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)에서 설명한 대로 클러스터를 만들기 시작합니다. __클러스터 요약__ 섹션에 도달하면 중지합니다.

2. __클러스터 요약__ 섹션에서 __고급 설정__에 대한 __편집__ 링크를 선택합니다.

    ![고급 설정 링크](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. __고급 설정__ 섹션에서 __스크립트 작업__을 선택합니다. __스크립트 작업__ 섹션에서 __+새로운 항목 제출__을 선택합니다.

    ![새 스크립트 작업 제출](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. __스크립트 선택__ 항목을 사용하여 미리 만든 스크립트를 선택합니다. 사용자 지정 스크립트를 사용하려면 __사용자 지정__을 선택한 다음 스크립트에 대한 __이름__ 및 __Bash 스크립트 URI__를 입력합니다.

    ![엄선된 스크립트 양식에서 스크립트 추가](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    다음 표에서는 양식의 요소에 대해 설명합니다.

    | 자산 | 값 |
    | --- | --- |
    | 스크립트 선택 | 사용자 소유 스크립트를 사용하려면 __사용자 지정__을 선택합니다. 그렇지 않은 경우 제공된 스크립트 중 하나를 선택합니다. |
    | Name |스크립트 작업의 이름을 지정합니다. |
    | Bash 스크립트 URI |스크립트의 URI를 지정합니다. |
    | Head/Worker/Zookeeper |스크립트가 실행되는 노드(**헤드**, **작업자** 또는 **ZooKeeper**)를 지정합니다. |
    | 매개 변수 |스크립트에 필요한 경우 매개 변수를 지정합니다. |

    __이 스크립트 작업을 유지__ 항목을 사용하여 크기 조정 작업 시 스크립트가 적용되도록 합니다.

5. __만들기__를 선택하여 스크립트를 저장합니다. 그런 다음 __+ Submit new(새로 제출)__ 를 사용하여 다른 스크립트를 추가할 수 있습니다.

    ![여러 스크립트 작업](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    스크립트 추가가 완료되면 __선택__ 단추, __다음__ 단추를 차례로 사용하여 __클러스터 요약__ 섹션으로 돌아갑니다.

3. 클러스터를 만들려면 __클러스터 요약__ 선택 영역에서 __만들기__를 선택합니다.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 스크립트 동작 사용

스크립트 동작을 Azure Resource Manager 템플릿과 함께 사용할 수 있습니다. 예제는 [https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)에서 확인할 수 있습니다.

이 예제에서는 스크립트 동작이 다음 코드를 사용하여 추가됩니다.

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

템플릿 배포 방법에 대한 내용은 다음 문서를 참조하세요.

* [템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [템플릿과 Azure CLI로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>클러스터를 만드는 동안 Azure PowerShell에서 스크립트 동작 사용

이 섹션에서는 스크립트를 호출하여 클러스터를 사용자 지정하는 [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/overview)을 참조하세요.

다음 스크립트는 PowerShell을 사용하는 클러스터를 만들 때 스크립트 작업을 적용하는 방법에 대해 보여줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>클러스터를 만드는 동안 HDInsight .NET SDK에서 스크립트 동작 사용

HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 클라이언트 라이브러리를 제공합니다. 코드 샘플은 [.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)를 참조하세요.

## <a name="apply-a-script-action-to-a-running-cluster"></a>실행 중인 클러스터에 스크립트 동작 적용

이 섹션에서는 실행 중인 클러스터에 스크립트 작업을 적용하는 방법을 알아봅니다.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Azure Portal에서 실행 중인 클러스터에 스크립트 동작 적용

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터를 선택합니다.

2. HDInsight 클러스터 개요에서 **스크립트 작업** 타일을 선택합니다.

    ![스크립트 작업 타일](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > **모든 설정**을 선택한 다음 설정 섹션에서 **스크립트 작업**을 선택할 수도 있습니다.

3. 스크립트 동작 섹션 맨 위에서 **새로운 항목 제출**을 선택합니다.

    ![실행 중인 클러스터에 스크립트 적용](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. __스크립트 선택__ 항목을 사용하여 미리 만든 스크립트를 선택합니다. 사용자 지정 스크립트를 사용하려면 __사용자 지정__을 선택한 다음 스크립트에 대한 __이름__ 및 __Bash 스크립트 URI__를 입력합니다.

    ![엄선된 스크립트 양식에서 스크립트 추가](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    다음 표에서는 양식의 요소에 대해 설명합니다.

    | 자산 | 값 |
    | --- | --- |
    | 스크립트 선택 | 사용자 소유 스크립트를 사용하려면 __사용자 지정__을 선택합니다. 그렇지 않은 경우 제공된 스크립트를 선택합니다. |
    | Name |스크립트 작업의 이름을 지정합니다. |
    | Bash 스크립트 URI |스크립트의 URI를 지정합니다. |
    | Head/Worker/Zookeeper |스크립트가 실행되는 노드(**헤드**, **작업자** 또는 **ZooKeeper**)를 지정합니다. |
    | 매개 변수 |스크립트에 필요한 경우 매개 변수를 지정합니다. |

    __이 스크립트 작업을 유지__ 항목을 사용하여 크기 조정 작업 시 스크립트가 적용되도록 합니다.

5. 마지막으로 **만들기** 단추를 사용하여 클러스터에 스크립트를 적용합니다.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Azure PowerShell에서 실행 중인 클러스터에 스크립트 동작 적용

계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/overview)을 참조하세요.

다음 예제에서는 실행 중인 클러스터에 스크립트 작업을 적용하는 방법을 보여줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

작업이 완료되면 다음 텍스트와 유사한 정보가 제공됩니다.

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Azure CLI에서 실행 중인 클러스터에 스크립트 동작 적용

계속하기 전에 Azure CLI를 설치 및 구성했는지 확인하세요. 자세한 내용은 [Azure CLI 1.0 설치](../cli-install-nodejs.md)를 참조하세요.

> [!IMPORTANT]
> HDInsight에는 Azure CLI 1.0이 필요합니다. 현재 Azure CLI 2.0에서는 HDInsight 작업을 위한 명령을 제공하지 않습니다.

1. Azure Resource Manager 모드로 전환하려면 명령줄에 다음 명령을 사용합니다.

    ```bash
    azure config mode arm
    ```

2. 다음 정보를 사용하여 Azure 구독에 인증합니다.

    ```bash
    azure login
    ```

3. 다음 명령을 사용하여 실행 중인 클러스터에 스크립트 작업을 적용합니다.

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    이 명령에 대한 매개 변수를 생략한 경우 해당 매개 변수를 묻는 메시지가 나타납니다. `-u`와 함께 지정한 스크립트에서 매개 변수를 허용하는 경우 `-p` 매개 변수를 지정할 수 있습니다.

    유효한 노드 형식은 `headnode`, `workernode` 및 `zookeeper`입니다. 스크립트를 여러 노드 형식에 적용해야 하는 경우 세미콜론(';')으로 구분하여 형식을 지정합니다. 예: `-n headnode;workernode`

    스크립트를 보존하려면 `--persistOnSuccess`를 추가합니다. 나중에 `azure hdinsight script-action persisted set`을(를) 사용하여 스크립트를 지속할 수도 있습니다.

    작업이 완료되면 다음 텍스트와 유사한 출력이 나타납니다.

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>REST API를 사용하여 실행 중인 클러스터에 스크립트 동작 적용

[실행 중인 클러스터의 스크립트 동작](https://msdn.microsoft.com/library/azure/mt668441.aspx)을 참조하세요.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>HDInsight .NET SDK에서 실행 중인 클러스터에 스크립트 동작 적용

.NET SDK를 사용하여 클러스터에 스크립트를 적용하는 예는 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)에서 확인할 수 있습니다.

## <a name="view-history-promote-and-demote-script-actions"></a>기록 보기, 스크립트 동업 승격 및 강등

### <a name="using-the-azure-portal"></a>Azure Portal 사용

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터를 선택합니다.

2. HDInsight 클러스터 개요에서 **스크립트 작업** 타일을 선택합니다.

    ![스크립트 작업 타일](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > **모든 설정**을 선택한 다음 설정 섹션에서 **스크립트 작업**을 선택할 수도 있습니다.

4. 이 클러스터에 대한 스크립트 기록이 스크립트 동작 섹션에 표시됩니다. 이 정보에는 지속된 스크립트 목록이 포함되어 있습니다. 아래 스크린샷을 보시면 Solr 스크립트가 이 클러스터에서 실행되었지만 스크린샷에는 지속된 스크립트가 보이지 않습니다.

    ![스크립트 동작 섹션](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. 기록에서 스크립트를 선택하면 이 스크립트의 속성 섹션이 표시됩니다. 화면 맨 위에서 스크립트를 다시 실행하거나 승격할 수 있습니다.

    ![스크립트 작업 속성](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. 또한 스크립트 동작 섹션에서 항목 오른쪽에 있는 **...** 를 사용하여 작업을 수행할 수도 있습니다.

    ![스크립트 작업 ... 사용량](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

| 사용하는 명령 | 수행하는 동작 |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |지속형 스크립트 작업에 대한 정보 검색 |
| Get-AzureRmHDInsightScriptActionHistory |클러스터에 적용된 스크립트 작업의 기록 또는 특정 스크립트에 대한 세부 정보 검색 |
| Set-AzureRmHDInsightPersistedScriptAction |임시 스크립트 작업을 지속형 스크립트 작업으로 승격 |
| Remove-AzureRmHDInsightPersistedScriptAction |지속형 스크립트 작업을 임시 작업으로 강등 |

> [!IMPORTANT]
> `Remove-AzureRmHDInsightPersistedScriptAction`을 사용해도 스크립트에서 수행한 작업이 실행 취소되지 않습니다. 이 cmdlet만 지속된 플래그를 제거합니다.

다음 예제 스크립트는 cmdlet을 사용하여 스크립트를 승격한 후 다시 강등하는 방법을 보여줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Azure CLI 사용

| 사용하는 명령 | 수행하는 동작 |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |지속형 스크립트 작업의 목록을 검색합니다. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |특정 지속형 스크립트 작업에 대한 정보를 검색합니다. |
| `azure hdinsight script-action history list <clustername>` |클러스터에 적용된 스크립트 작업의 기록을 검색합니다. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |특정 스크립트 작업에 대한 정보 검색 |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |임시 스크립트 작업을 지속형 스크립트 작업으로 승격 |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |지속형 스크립트 작업을 임시 작업으로 강등 |

> [!IMPORTANT]
> `azure hdinsight script-action persisted delete`을 사용해도 스크립트에서 수행한 작업이 실행 취소되지 않습니다. 이 cmdlet만 지속된 플래그를 제거합니다.

### <a name="using-the-hdinsight-net-sdk"></a>HDInsight .NET SDK 사용

.NET SDK를 사용하여 클러스터에서 스크립트 기록을 검색하거나 스크립트를 승격 또는 강등하는 예제는 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)에서 확인할 수 있습니다.

> [!NOTE]
> 이 예제에서는 .NET SDK를 사용하여 HDInsight 응용 프로그램을 설치하는 방법도 보여 줍니다.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Hadoop으로 형성된 오픈 소스 기술의 에코시스템을 사용합니다. Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다. 자세한 내용은 [Azure Support FAQ 웹 사이트](https://azure.microsoft.com/support/faq/)의 **지원 범위** 섹션을 참조하세요. HDInsight 서비스는 기본 제공 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

* **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어, YARN ResourceManager, Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)에 있습니다.
* **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

> [!WARNING]
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도와줍니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원을 통해 문제를 해결할 수 있습니다. 또는 해당 기술에 대한 전문 지식이 있는 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요청할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/)).

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 구성 요소가 클러스터에 사용 및 설치된 방법과 상관없이, 동일한 수준의 지원이 적용됩니다. 다음 목록에는 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용할 수 있는 가장 일반적인 방법이 나와 있습니다.

1. 작업 제출 - 사용자 지정 구성 요소를 실행하거나 사용하는 Hadoop 또는 기타 유형의 작업을 클러스터에 제출할 수 있습니다.

2. 클러스터 사용자 지정 - 클러스터를 만들 때 클러스터 노드에 설치되는 사용자 지정 구성 요소 및 추가 설정을 지정할 수 있습니다.

3. 샘플 - 인기 있는 사용자 지정 구성 요소의 경우, Microsoft와 다른 사람들이 이러한 구성 요소를 HDInsight 클러스터에서 어떻게 사용할 수 있는지에 대한 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.

## <a name="troubleshooting"></a>문제 해결

Ambari 웹 UI를 사용하여 스크립트 작업에서 기록한 정보를 볼 수 있습니다. 또한 클러스터를 만드는 중에 스크립트가 실패할 경우 해당 클러스터와 연결된 기본 저장소 계정에서 로그를 사용할 수도 있습니다. 이 섹션에서는 두 옵션을 모두 사용하여 로그를 검색하는 방법에 대한 정보를 제공합니다.

### <a name="using-the-ambari-web-ui"></a>Ambari 웹 UI 사용

1. 브라우저에서 https://CLUSTERNAME.azurehdinsight.net로 이동합니다. CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.

    메시지가 표시되면 클러스터의 관리자 계정 이름(관리자) 및 암호를 입력합니다. 웹 폼에서 관리자 자격 증명을 다시 입력해야 합니다.

2. 페이지 위쪽의 모음에서 **작업** 항목을 선택합니다. Ambari 통해 클러스터에서 수행된 현재 및 이전 작업의 목록이 표시됩니다.

    ![선택한 작업으로 Ambari 웹 UI 모음](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. **작업** 열에 **run\_customscriptaction**이 있는 항목을 찾습니다. 이러한 항목을 스크립트 동작을 실행할 때 생성됩니다.

    ![작업의 스크린샷](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    STDOUT 및 STDERR 출력을 보려면 이 run\customscriptaction 항목을 선택하고 링크를 통해 드릴다운합니다. 이 출력은 스크립트가 실행될 때 생성되며 유용한 정보를 포함할 수 있습니다.

### <a name="access-logs-from-the-default-storage-account"></a>기본 저장소 계정에서 로그 액세스

스크립트 오류로 인해 클러스터를 만들 수 없는 경우 로그는 클러스터 저장소 계정에 유지됩니다.

* 저장소 로그는 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`에서 제공합니다.

    ![작업의 스크린샷](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    이 디렉터리에서는 로그가 헤드 노드, 작업자 노드, Zookeeper 노드에 대해 별도로 구성됩니다. 일부 사례:

    * **헤드 노드** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **작업자 노드** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper 노드** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 해당 호스트의 모든 stdout 및 stderr은 저장소 계정에 업로드됩니다. 각 스크립트 동작마다 하나의 **output-\*.txt** 및 **errors-\*.txt**가 있습니다. output-*.txt 파일은 호스트에서 실행되는 스크립트의 URI 정보를 포함합니다. 다음 텍스트는 이 정보의 예제입니다.

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 같은 이름으로 반복해서 스크립트 작업 클러스터를 만들 수 있습니다. 이 경우에는 날짜 폴더 이름을 기준으로 관련 로그를 구분할 수 있습니다. 예를 들어, 서로 다른 날짜에 만든 클러스터(mycluster)의 폴더 구조는 다음 로그 항목과 유사하게 나타납니다.

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 같은 날 같은 이름으로 스크립트 작업 클러스터를 만든 경우 고유의 접두사를 사용하여 관련 로그 파일을 식별할 수 있습니다.

* 밤 12시(자정)에 클러스터를 만든 경우 로그 파일이 이틀에 걸쳐 있을 수 있습니다. 이 경우 동일한 클러스터에 대해 서로 다른 두 개의 날짜 폴더가 표시됩니다.

* 기본 컨테이너에 로그 파일을 업로드하는 작업은 특히 대형 클러스터의 경우 최대 5분이 소요됩니다. 따라서 로그에 액세스하려면 스크립트 작업이 실패할 경우 클러스터를 즉시 삭제해서는 안 됩니다.

### <a name="ambari-watchdog"></a>Ambari 감시

> [!WARNING]
> Linux 기반 HDInsight 클러스터에서 Ambari Watchdog(hdinsightwatchdog)의 암호를 변경하지 마세요. 이 계정의 암호를 변경하면 HDInsight 클러스터에서 새 스크립트 동작을 실행할 수 없게 됩니다.

### <a name="cant-import-name-blobservice"></a>이름 BlobService를 가져올 수 없음

__증상__: 스크립트 작업이 실패했습니다. Ambari에서 작업을 볼 때 다음 오류와 유사한 텍스트가 표시됩니다.

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__원인__: 이 오류는 HDInsight 클러스터에 포함된 Python Azure Storage 클러스터를 업그레이드할 경우에 발생합니다. HDInsight는 Azure Storage 클라이언트 0.20.0을 예상합니다.

__해결 방법__: 이 오류를 해결하려면 `ssh`를 사용하여 각 클러스터 노드에 수동으로 연결하고 다음 명령을 사용하여 올바른 스토리지 클라이언트 버전을 다시 설치합니다.

```bash
sudo pip install azure-storage==0.20.0
```

SSH를 사용하여 클러스터에 연결하는 방법에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>클러스터를 만드는 동안 기록에 스크립트가 표시되지 않음

2016년 3월 15일 전에 클러스터를 만든 경우에는 스크립트 동작 기록에 항목이 나타나지 않을 수 있습니다. 클러스터의 크기를 조정하면 스크립트가 스크립트 동작 기록에 표시됩니다.

두 가지 예외 사항이 있습니다.

* 클러스터가 2015년 9월 1일 전에 생성되었습니다. 스크립트 동작이 이 날에 도입되었습니다. 따라서 이 날짜 이전에 생성된 클러스터는 클러스터 생성에 스크립트 동작이 사용되지 않았습니다.

* 클러스터를 만들 때 여러 스크립트 동작을 사용하고 여러 스크립트에 같은 이름을 사용했거나 여러 스크립트의 이름과 URI는 같지만 매개 변수는 서로 다르게 했습니다. 이 경우에는 다음 오류가 표시됩니다.

    기존 스크립트에 충돌하는 스크립트 이름이 있으므로 이 클러스터에서 새 스크립트 작업을 실행할 수 없습니다. 클러스터 만들기에 제공되는 스크립트 이름이 모두 고유해야 합니다. 크기 조정에 기존 스크립트가 실행됩니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight용 스크립트 동작 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)
* [HDInsight 클러스터에 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)
* [HDInsight 클러스터에 추가 저장소 추가](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "클러스터를 만드는 동안의 단계"
