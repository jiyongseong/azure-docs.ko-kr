---
title: Visual Studio에서 Key Vault 연결된 서비스 시작(ASP.NET Core 프로젝트) | Microsoft Docs
description: 이 자습서를 통해 ASP.NET 또는 ASP.NET Core 웹 응용 프로그램에 Key Vault 지원을 추가하는 방법을 배울 수 있습니다.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 64d1a404eac955f47308f01edd56b3d008e250a0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Visual Studio에서 Key Vault 연결된 서비스 시작(ASP.NET Core 프로젝트)

> [!div class="op_single_selector"]
> - [시작](vs-key-vault-aspnet-core-get-started.md)
> - [변경된 내용](vs-key-vault-aspnet-core-what-happened.md)

이 문서에서는 Visual Studio의 **연결된 서비스 추가** 명령을 통해 ASP.NET Core 프로젝트에 Key Vault를 추가한 후의 추가 지침을 제공합니다. 프로젝트에 아직 서비스를 추가하지 않은 경우 [Visual Studio 연결된 서비스를 사용하여 웹 응용 프로그램에 Key Vault 추가](vs-key-vault-add-connected-service.md)에 설명된 지침에 따라 언제든지 그렇게 할 수 있습니다.

연결된 서비스를 추가할 때 프로젝트에서 변경되는 내용은 [내 ASP.NET Core 프로젝트에서 변경된 내용](vs-key-vault-aspnet-core-what-happened.md)을 참조하세요.

## <a name="after-you-connect"></a>연결 후

1. Azure의 Key Vault에 비밀을 추가합니다. 포털에서 올바른 위치로 이동하려면 이 Key Vault에 저장된 비밀 관리 링크를 클릭합니다. 페이지 또는 프로젝트를 닫은 후 **보안**에서 **모든 서비스**를 선택하여 [Azure Portal](https://portal.azure.com)에서 링크로 이동하고, **Key Vault**를 선택한 다음, 방금 만든 Key Vault를 선택할 수 있습니다.

   ![포털로 이동](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 만든 키 자격 증명 모음의 Key Vault 섹션에서 **비밀**을 선택한 다음, **생성/가져오기**를 선택합니다.

   ![비밀 생성/가져오기](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. “MySecret”과 같은 암호를 입력하고 테스트로 임의의 문자열 값을 입력한 다음, **만들기** 단추를 선택합니다.

   ![비밀 만들기](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (선택 사항) 다른 비밀을 입력합니다. 단, 이번에는 **비밀--MySecret**이라는 이름을 지정하여 범주에 포함시킵니다. 이 구문은 **MySecret**이란 비밀을 포함하는 **비밀** 범주를 지정합니다.
1. Visual Studio 프로젝트에서는 이제 코드에 다음 식을 사용하여 이러한 비밀을 참조할 수 있습니다.
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

축하합니다. 이제 Key Vault를 사용하여 안전하게 저장된 비밀에 액세스할 수 있도록 웹앱을 활성화했습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Key Vault와 관련된 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **TYPE THE RESOURCE GROUP NAME:** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

# <a name="next-steps"></a>다음 단계

[Key Vault 개발자 가이드](key-vault-developers-guide.md)에서 Key Vault로 개발하는 방법에 대해 자세히 알아봅니다.