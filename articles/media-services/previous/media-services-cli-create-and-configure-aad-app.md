---
title: CLI 2.0을 사용하여 Azure AD 앱 만들기 및 Azure Media Services API 액세스 구성 | Microsoft Docs
description: 이 항목에서는 CLI 2.0을 사용하여 Azure AD 앱을 만들고 Azure Media Services API에 액세스하도록 구성하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 7685be97213f0b298499d474c0a6a772ca608fb2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>CLI 2.0을 사용하여 AAD 앱 만들기 및 Azure Media Services API 액세스 구성

이 항목에서는 CLI 2.0을 사용하여 Azure Media Services 리소스에 액세스하기 위한 Azure AD(Azure Active Directory) 응용 프로그램 및 서비스 주체를 만드는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건

- Azure 계정. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell 사용

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 포털의 위쪽 탐색 창에서 Cloud Shell을 시작합니다.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

자세한 내용은 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조하세요.

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>CLI 2.0으로 Azure AD 앱 만들기 및 미디어 계정에 대한 액세스 구성
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

예: 

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

이 예제에서 **scope**는 미디어 서비스 계정에 대한 전체 리소스 경로입니다. 하지만 **scope**는 모든 수준에 있을 수 있습니다.

예를 들어 다음 수준 중 하나일 수 있습니다.
 
* **구독** 수준.
* **리소스 그룹** 수준.
* **리소스** 수준(예: 미디어 계정).

자세한 내용은 [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

또한 [Azure 명령줄 인터페이스를 사용하여 역할 기반 Access Control 관리](../../role-based-access-control/role-assignments-cli.md)도 참조하세요. 

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-portal-upload-files.md) 시작
