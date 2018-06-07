---
title: "Azure Cloud Shell에서 Databricks CLI 사용 | Microsoft Docs"
description: "Azure Cloud Shell에서 Databricks CLI를 사용하는 방법을 알아봅니다."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 8e4213813ff23586ac683556d4a3c0c587edea58
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Azure Cloud Shell에서 Databricks CLI 사용

Azure Cloud Shell에서 Databricks CLI를 사용하여 Databricks에서 작업을 수행하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* Azure Databricks 작업 영역 및 클러스터. 지침은 [Azure Databricks 시작](quickstart-create-databricks-workspace-portal.md)을 참조하세요. 

* Databricks에서 개인용 액세스 토큰을 설정합니다. 지침은 [토큰 관리](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)를 참조하세요.

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
 
2. 오른쪽 위 모서리에서 **Cloud Shell** 아이콘을 클릭합니다.

   ![Cloud Shell 시작](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Excel에서 ODBC 시작")

3. Cloud Shell 환경에 대해 **Bash**를 선택했는지 확인합니다. 다음 스크린샷과 같이 드롭다운 옵션에서 선택할 수 있습니다.

   ![Cloud Shell 시작](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Excel에서 ODBC 시작") 

4. Databtricks CLI를 설치할 수 있는 가상 환경을 만듭니다. 아래 코드 조각에서는 `databrickscli`라는 가상 환경을 만듭니다.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. 만든 가상 환경으로 전환합니다.

       source databrickscli/bin/activate

6. Databricks CLI를 설치합니다.

       pip install databricks-cli

7. 만든 액세스 토큰(필수 조건의 일부로 나열됨)을 사용하여 Databricks에 인증을 설정합니다. 다음 명령을 사용합니다.

       databricks configure --token

    다음과 같은 메시지가 표시됩니다.

    * Databricks 호스트를 입력하라는 메시지가 표시됩니다. `https://eastus2.azuredatabricks.net` 형식의 값을 입력합니다. 여기서 **eastus2**는 Azure Databricks 작업 영역을 만든 Azure 영역입니다.

    * 사용자 이름을 입력하라는 메시지가 표시됩니다. **토큰**을 입력합니다.

    * 마지막으로, 암호를 입력하라는 메시지가 표시됩니다. 앞에서 만든 토큰을 입력합니다.

이러한 단계가 완료되면 Azure Cloud Shell에서 Databricks CLI를 사용할 수 있습니다.

## <a name="use-databricks-cli"></a>Databricks CLI 사용

이제 Databricks CLI 사용을 시작할 수 있습니다. 예를 들어 다음 명령을 실행하여 작업 영역에 있는 모든 Databricks 클러스터를 나열합니다.

    databricks clusters list

또한 다음 명령을 사용하여 DBFS(Databricks 파일 시스템)에 액세스할 수도 있습니다.

    databricks fs ls


명령에 대한 전체 참조는 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* Azure CLI에 대한 자세한 내용은 [Azure CLI 개요](../cloud-shell/overview.md)를 참조하세요.
* Azure CLI에 대한 명령 목록을 보려면 [Azure CLI 참조](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)를 참조하세요.
* Databricks CLI에 대한 명령 목록을 보려면 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요.


