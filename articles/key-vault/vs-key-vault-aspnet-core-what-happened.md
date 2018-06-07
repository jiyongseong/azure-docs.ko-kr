---
title: Azure Key Vault에 연결 시 ASP.NET Core 프로젝트의 변경 사항| Microsoft Docs
description: Visual Studio 연결된 서비스를 사용하여 Key Vault에 연결하면 ASP.NET Core 프로젝트에서 무엇이 변경되는지 설명합니다.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>ASP.NET Core 프로젝트에서 무엇이 변경되나요(Visual Studio Key Vault 연결된 서비스)?

> [!div class="op_single_selector"]
> - [시작](vs-key-vault-aspnet-core-get-started.md)
> - [변경된 내용](vs-key-vault-aspnet-core-what-happened.md)

이 문서에서는 [Visual Studio를 사용하여 Key Vault 연결된 서비스](vs-key-vault-add-connected-service.md)를 추가하면 ASP.NET 프로젝트에서 정확히 무엇이 변경되는지 알아봅니다.

연결된 서비스를 사용한 작업에 대한 자세한 내용은 [시작](vs-key-vault-aspnet-core-get-started.md)을 참조하세요.

## <a name="added-references"></a>추가된 참조

프로젝트 파일*.NET 참조 및 NuGet 패키지 참조에 영향을 줍니다.

| type | 참고 자료 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>추가된 파일

- 연결된 서비스 공급자, 버전 및 설명서 링크에 대한 정보를 기록하는 ConnectedService.json이 추가됩니다.

## <a name="project-file-changes"></a>프로젝트 파일 변경 내용

- 연결된 서비스 ItemGroup 및 ConnectedServices.json 파일이 추가됩니다.

## <a name="launchsettingsjson-changes"></a>launchsettings.json changes

- IIS Express 프로필 및 웹 프로젝트 이름과 일치하는 프로필에 다음 환경 변수 항목이 추가됩니다.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Azure 변경 내용

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

