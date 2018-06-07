---
title: Azure Media Services API-CLI 2.0 액세스 | Microsoft Docs
description: Azure Media Services API에 액세스하려면 이 방법의 단계를 따르도록 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a4a7c59e93b860245d67695de90fbae2becac3e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="access-azure-media-services-api-with-cli-20"></a>CLI 2.0을 사용하여 Azure Media Services API에 액세스
 
Azure Media 서비스 API에 연결하려면 Azure AD 서비스 주체 인증을 사용해야 합니다. 응용 프로그램이 다음 매개 변수가 있는 Azure AD 토큰을 요청해야 합니다.

* Azure AD 테넌트 끝점
* Media Services 리소스 URI
* REST Media Services의 리소스 URI
* Azure AD 응용 프로그램 값: 클라이언트 ID 및 클라이언트 암호.

이 문서에서는 CLI 2.0을 사용하여 Azure AD 응용 프로그램 및 서비스 주체를 만들고 Azure Media Services 리소스에 액세스하는 데 필요한 값을 얻는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건 

[이 빠른 시작](create-account-cli-quickstart.md)에서 설명된 대로 새로운 Azure Media Services 계정을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

다음 단계와 같이[Azure Portal](http://portal.azure.com)에 로그인하고 **CloudShell**을 시작하여 CLI명령을 실행합니다.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [파일 스트리밍](stream-files-dotnet-quickstart.md)
