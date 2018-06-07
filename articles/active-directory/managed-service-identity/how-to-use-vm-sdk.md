---
title: Azure SDK에서 Azure VM 관리 서비스 ID를 사용하는 방법
description: Azure VM MSI에서 Azure SDK를 사용하는 코드 샘플입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 389b9967003c681b974f4580264701d22c103b7e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Azure SDK에서 Azure VM MSI(관리 서비스 ID)를 사용하는 방법 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
이 문서에서는 MSI에 해당하는 Azure SDK 지원을 사용하는 방법을 보여주는 SDK 샘플 목록을 제공합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 이 문서의 모든 샘플 코드/스크립트에서는 MSI 사용 Virtual Machine에서 클라이언트를 실행한다고 가정합니다. Azure Portal에서 VM "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 MSI 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 

## <a name="sdk-code-samples"></a>SDK 코드 샘플

| SDK)             | 코드 샘플 |
| --------------- | ----------- |
| .NET            | [관리 서비스 ID를 사용하여 Windows VM에서 Azure Resource Manager 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [관리 서비스 ID를 사용하여 Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [관리 서비스 ID를 사용하여 리소스 관리](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| 파이썬          | [MSI를 사용하여 VM 내부에서 쉽게 인증](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [MSI가 활성화된 VM에서 리소스 관리](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>관련 콘텐츠

- 라이브러리 다운로드, 문서 등을 비롯한 Azure SDK 리소스의 전체 목록은 [Azure SDK](https://azure.microsoft.com/downloads/)를 참조하세요.
- Azure VM에서 MSI를 사용하도록 설정하려면 [Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vm.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.








