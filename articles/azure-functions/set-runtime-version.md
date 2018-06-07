---
title: "Azure Functions 런타임 버전을 대상으로 지정하는 방법"
description: "Azure Functions는 여러 버전의 런타임을 지원합니다. Azure에서 호스팅된 함수 앱의 런타임 버전을 지정하는 방법을 알아봅니다."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

함수 앱은 특정 Azure Functions 런타임 버전에서 실행됩니다. [1.x와 2.x](functions-versions.md)의 두 가지 주요 버전이 있습니다. 이 문서에는 선택한 버전에서 실행할 함수 앱을 Azure에서 구성하는 방법을 설명합니다. 특정 버전에 대한 로컬 개발 환경을 구성하는 방법에 대한 자세한 내용은 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md)를 참조하세요.

>[!IMPORTANT]   
> Azure Functions 런타임 2.0은 미리 보기 상태이며 현재 Azure Functions의 일부 기능은 지원되지 않습니다. 자세한 내용은 [Azure Functions 런타임 버전 개요](functions-versions.md)를 참조하세요.

## <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트

Functions를 통해 함수 앱에서 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 사용하여 특정 버전의 런타임을 대상으로 지정할 수 있습니다. 함수 앱을 새 버전으로 변경하도록 명시적으로 선택할 때까지 지정된 주 버전에서 유지합니다.

주 버전(1.x의 경우 "~1" 또는 2.x의 경우 "beta")만 지정하면 새로운 런타임 부 버전을 사용할 수 있을 때 함수 앱이 해당 런타임으로 자동 업데이트됩니다. 새로운 부 버전에는 주요 변경 내용이 도입되지 않습니다. 부 버전(예: "1.0.11360")을 지정하면 사용자가 명시적으로 변경할 때까지 함수 앱이 해당 버전을 유지합니다. 

새 버전을 공개적으로 사용할 수 있으면 포털에서 메시지를 표시하여 해당 버전으로 업그레이드할 수 있는 기회를 제공합니다. 새 버전으로 변경한 후에 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 사용하여 이전 버전으로 다시 변경할 수 있습니다.

런타임 버전을 변경하면 함수 앱을 다시 시작하게 됩니다.

자동 업데이트를 사용하기 위해 `FUNCTIONS_EXTENSION_VERSION` 앱 설정에서 설정할 수 있는 값은 현재 1.x 런타임의 경우 "~ 1"이고 2.x의 경우 "beta"입니다.

## <a name="view-the-current-runtime-version"></a>현재 런타임 버전 보기

현재 함수 앱에서 사용하는 런타임 버전을 보려면 다음 절차를 따르세요. 

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **구성된 기능** 아래에서 **함수 앱 설정**을 선택합니다. 

    ![함수 앱 설정을 선택합니다.](./media/functions-versions/add-update-app-setting.png)

2. **함수 앱 설정** 탭에서 **런타임 버전**을 찾습니다. 특정 런타임 버전 및 요청된 주 버전을 적어둡니다. 다음 예제에는 FUNCTIONS\_EXTENSION\_VERSION 앱 설정이 `~1`로 설정되어 있습니다.
 
   ![함수 앱 설정을 선택합니다.](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>포털을 사용하여 대상 버전 지정

현재 주 버전이 아닌 버전을 대상 또는 버전 2.0으로 지정해야 하는 경우 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **구성된 기능** 아래에서 **응용 프로그램 설정**을 선택합니다.

    ![함수 앱 설정을 선택합니다.](./media/functions-versions/add-update-app-setting1a.png)

2. **응용 프로그램 설정** 탭에서 `FUNCTIONS_EXTENSION_VERSION` 설정을 찾고, 1.x 런타임의 유효한 버전 또는 버전 2.0의 `beta`로 값을 변경합니다. 주 버전의 물결표는 해당 주 버전의 최신 버전(예: "~ 1")을 사용한다는 의미입니다. 

    ![함수 앱 설정 지정](./media/functions-versions/add-update-app-setting2.png)

3. **저장**을 클릭하여 응용 프로그램 설정 업데이트를 저장합니다. 

## <a name="target-a-version-using-azure-cli"></a>Azure CLI를 사용하여 대상 버전 지정

 Azure CLI에서 `FUNCTIONS_EXTENSION_VERSION`을 설정할 수도 있습니다. Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 명령으로 함수 앱에서 응용 프로그램 설정을 업데이트합니다.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. `<version>`을 1.x 런타임의 유효한 버전 또는 버전 2.0의 `beta`로 바꿉니다. 

앞의 코드 샘플에서 **사용해 보세요.**를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az_login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 개발 환경에서 2.0 런타임을 대상으로 지정](functions-run-local.md)

> [!div class="nextstepaction"]
> [런타임 버전에 대한 릴리스 정보 참조](https://github.com/Azure/azure-webjobs-sdk-script/releases)