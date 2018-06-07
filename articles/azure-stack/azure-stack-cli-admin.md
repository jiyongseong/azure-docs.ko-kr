---
title: "Azure 스택 사용자에 대 한 Azure CLI를 사용 하도록 설정 | Microsoft Docs"
description: "크로스 플랫폼 명령줄 인터페이스 (CLI)를 사용 하 여 관리 하 고 스택에서 Azure 리소스를 배포 하는 방법을 알아봅니다"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: e2483bda5a0c6a6b270759946f146c37c5dad5b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure 스택 사용자에 대 한 Azure CLI를 사용 하도록 설정

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

모든 Azure 스택 연산자 관련 작업을 Azure CLI를 사용 하 여 수행할 수 없습니다. 하지만 사용자가 CLI 통해 리소스를 관리 하려면 Azure 스택 연산자를 제공 해야 해당를 다음으로.

* **Azure 스택 CA 루트 인증서** 은 사용자가 Azure 스택 개발 키트 외부 워크스테이션에서 CLI를 사용 하는 경우 필요 합니다.  

* **가상 컴퓨터 별칭 끝점** "UbuntuLTS" 또는 "Win2012Datacenter," Vm을 배포 하는 경우 단일 매개 변수로 이미지 게시자, 제품, SKU 및 버전을 참조 하는 같은 별칭을 제공 합니다.  

다음 섹션에서는 이러한 값을 가져오는 방법에 설명 합니다.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure 스택 CA 루트 인증서 내보내기

Azure 스택 CA 루트 인증서가 개발 키트 환경 내에서 실행 되는 테 넌 트 가상 컴퓨터와 개발 키트에서 사용할 수 있습니다. PEM 형식으로 Azure 스택 루트 인증서를 내보내려면 개발 키트 또는 테 넌 트 가상 컴퓨터에 로그인 하 고 다음 스크립트를 실행 합니다.

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>가상 컴퓨터 별칭 끝점을 설정 합니다.

Azure 스택 운영자 별칭 가상 컴퓨터 파일을 호스팅하는 공개적으로 액세스할 수 있는 끝점을 설정 해야 합니다. 가상 컴퓨터 별칭 파일이 이미지에 대 한 일반 이름을 제공 하는 JSON 파일입니다. Azure CLI 매개 변수는 VM을 배포 하는 경우에 이후에 해당 이름이 지정 됩니다.  

별칭 파일에 항목을 추가 하기 전에 확인 하면 [Azure 마켓플레이스의 이미지 다운로드](azure-stack-download-azure-marketplace-item.md), 수도 있고 [사용자 지정 이미지를 게시](azure-stack-add-vm-image.md)합니다. 사용자 지정 이미지를 게시 하는 경우 확인 게시 중에 지정 된 게시자, 제품, SKU 및 버전 정보를 기록 합니다. 마켓플레이스의 이미지 경우 사용 하 여 정보를 볼 수 있습니다는 ```Get-AzureVMImage``` cmdlet.  
   
A [샘플 별칭 파일](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) 많은 일반적인 이미지 별칭을 사용할 수 있습니다. 시작 지점으로 사용할 수 있습니다. CLI 클라이언트가 연결할 수 있는 공간에이 파일을 호스트 합니다. 한 가지 방법은 blob 저장소 계정에 파일을 호스트 URL을 사용자와 공유할:

1. 다운로드는 [샘플 파일](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) GitHub에서 합니다.
2. Azure 스택에서 새 저장소 계정을 만듭니다. 완료 되 면 새 blob 컨테이너를 만듭니다. "공개 합니다."로 액세스 정책 설정  
3. 새 컨테이너에는 JSON 파일을 업로드 합니다. 완료 되 면 blob 이름을 클릭 하 고 다음 blob 속성에서 URL을 선택 하 여 blob의 URL을 볼 수 있습니다.


## <a name="next-steps"></a>다음 단계

[Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)

[PowerShell로 연결](azure-stack-connect-powershell.md)

[사용자 권한 관리](azure-stack-manage-permissions.md)

