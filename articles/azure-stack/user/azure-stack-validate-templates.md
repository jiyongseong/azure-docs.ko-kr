---
title: 서식 파일 유효성 검사 도구를 사용 하 여 Azure 스택에 대 한 서식 파일을 확인 | Microsoft Docs
description: Azure 스택 배포에 대 한 템플릿 확인
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 1800db8cd25aa37bffb76adf00901e11c75d49fe
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604083"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>서식 파일 유효성 검사 도구와 함께 Azure 스택에 대 한 템플릿을 확인 하십시오.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

템플릿 유효성 검사 도구를 사용 하 여 확인할 Azure 리소스 관리자 [템플릿](azure-stack-arm-templates.md) Azure 스택을 배포할 때 사용할 준비가 되었습니다. 서식 파일 유효성 검사 도구는 스택 Azure tools의 일부로 사용할 수 있습니다. 에 설명 된 단계를 사용 하 여 Azure 스택 도구 다운로드는 [GitHub에서 도구를 다운로드](azure-stack-powershell-download.md) 문서.

## <a name="overview"></a>개요

서식 파일의 유효성을 검사할 클라우드 기능을 구축 해야 첫 번째 파일을 다음 유효성 검사 도구를 실행 합니다. Azure 스택 도구에서 다음 PowerShell 모듈을 사용 하는 경우:

- 에 **TemplateValidator** 폴더:<br>         AzureRM.CloudCapabilities.psm1은 서비스 및 Azure 스택 클라우드에서 버전을 나타내는 클라우드 기능 JSON 파일을 만듭니다.
- 에 **CloudCapabilities** 폴더:<br>
AzureRM.TemplateValidator.psm1은 Azure 스택에서 배포에 대 한 서식 파일을 테스트 하려면 클라우드 기능 JSON 파일을 사용 합니다.

## <a name="build-the-cloud-capabilities-file"></a>클라우드 기능 파일 빌드

서식 파일 유효성 검사기를 사용 하려면 먼저 JSON 파일을 작성 AzureRM.CloudCapabilities PowerShell 모듈을 실행 합니다.

>[!NOTE]
>통합된 시스템을 업데이트 하거나 새 서비스 또는 가상 확장을 추가할 경우이 모듈을 다시 실행 해야 합니다.

1. Azure 스택에 연결이 있는지 확인 합니다. Azure 스택 개발 키트 호스트에서 다음이 단계를 수행할 수 있으며 하거나 사용할 수 있습니다는 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 워크스테이션에서 연결할 수 있습니다.
2. AzureRM.CloudCapabilities PowerShell 모듈을 가져옵니다.

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. 서비스 버전을 검색 하 고 클라우드 기능 JSON 파일을 만들도록 하려면 Get CloudCapabilities cmdlet을 사용 합니다. 지정 하지 않으면 **-OutputPath**, æ ä AzureCloudCapabilities.Json 현재 디렉터리에 만들어집니다. 사용자의 실제 위치를 사용 합니다.

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>서식 파일의 유효성을 검사합니다

템플릿 AzureRM.TemplateValidator PowerShell 모듈을 사용 하 여 유효성을 검사 하려면 다음이 단계를 사용 합니다. 사용자 고유의 템플릿을 사용 하거나 유효성을 검사 수는 [Azure 스택 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다.

1. AzureRM.TemplateValidator.psm1 PowerShell 모듈을 가져옵니다.

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. 서식 파일 유효성 검사기를 실행 합니다.

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

서식 파일 유효성 검사 경고 또는 오류가 PowerShell 콘솔 및 원본 디렉터리에 HTML 파일에 기록 됩니다. 다음 화면 캡처에서는 유효성 검사 보고서의 예를 보여 줍니다.

![서식 파일 유효성 검사 보고서](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>매개 변수

서식 파일 유효성 검사기는 다음 매개 변수를 지원합니다.

| 매개 변수 | 설명 | 필수 |
| ----- | -----| ----- |
| TemplatePath | Azure 리소스 관리자 템플릿이를 재귀적으로 경로 지정 | 예 | 
| TemplatePattern | 일치 하는 템플릿 파일의 이름을 지정 합니다. | 아니요 |
| CapabilitiesPath | 클라우드 기능 JSON 파일의 경로를 지정합니다. | 예 | 
| IncludeComputeCapabilities | VM 크기 및 VM 확장 같은 IaaS 리소스의 평가 포함합니다. | 아니요 |
| IncludeStorageCapabilities | SKU 형식 처럼 저장소 리소스의 평가 포함합니다. | 아니요 |
| 보고서 | 생성된 된 HTML 보고서의 이름을 지정합니다. | 아니요 |
| 자세한 정보 표시 | 콘솔에 오류 및 경고를 기록합니다. | 아니요|

### <a name="examples"></a>예

이 예에서는 모든 유효성을 검사는 [Azure 스택 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) 로컬 저장소에 다운로드 합니다. 또한이 예제에서는 가상 컴퓨터 크기와 Azure 스택 개발 키트 기능에 대해 확장 유효성을 검사 합니다.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>다음 단계

- [Azure 스택에 템플릿을 배포합니다](azure-stack-arm-templates.md)
- [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)
