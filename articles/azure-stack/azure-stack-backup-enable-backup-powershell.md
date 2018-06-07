---
title: Azure 스택 powershell에 대 한 백업을 사용 하도록 설정 | Microsoft Docs
description: 오류가 발생 하는 경우 Azure 스택 복원할 수 있도록 Windows PowerShell과 함께 인프라 백업 서비스를 설정 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Azure 스택 powershell에 대 한 백업을 사용 하도록 설정

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Enable Windows PowerShell과 함께 인프라 백업 서비스는 지금 백업을 정기적으로 수행 합니다.
 - 내부 id 서비스 및 루트 인증서
 - 사용자의 계획, 제안, 구독
 - Keyvault 암호
 - 사용자 RBAC 역할 및 정책

백업을 사용 하도록 설정 하 고, 백업, 시작, 연산자 관리 끝점을 통해 백업 정보를 가져올을 PowerShell cmdlet을 액세스할 수 있습니다.

## <a name="prepare-powershell-environment"></a>PowerShell 환경 준비

PowerShell 환경을 구성 하는 방법에 지침은 [Azure 스택 위한 PowerShell 설치 ](azure-stack-powershell-install.md)합니다.

## <a name="generate-a-new-encryption-key"></a>새 암호화 키 생성

설치 하 고 Azure 스택에 대해 구성 된 PowerShell 및 Azure 스택 도구.
 - 참조 [스택에서 Azure PowerShell을 시작 하 고 실행](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart)합니다.
 - 참조 [GitHub에서 도구를 다운로드 하는 Azure 스택](azure-stack-powershell-download.md)

관리자 권한 프롬프트를 통한 Windows PowerShell을 열고 다음 명령을 실행 합니다.
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
동일한 PowerShell 세션에서 다음 명령을 실행 합니다.

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> 키를 생성 하는 AzureStack 도구를 사용 해야 합니다.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Backup을 사용 하려면 백업 공유, 자격 증명 및 암호화 키를 제공 합니다.

동일한 PowerShell 세션에서 사용자 환경에 대 한 변수를 추가 하 여 다음 PowerShell 스크립트를 편집 합니다. 인프라 백업 서비스에 백업 공유, 자격 증명 및 암호화 키를 제공 하는 업데이트 된 스크립트를 실행 합니다.

| 변수        | 설명   |
|---              |---                                        |
| $username       | 형식에서 **Username** 파일 읽기와 쓰기에 대 한 액세스 권한이 있는 공유 드라이브 위치에 대 한 도메인 및 사용자를 사용 하 여 합니다. 예: `Contoso\backupshareuser` |
| $password       | 형식에서 **암호** 사용자에 대 한 합니다. |
| $sharepath      | 경로를 입력는 **백업 저장소 위치**합니다. 에 대 한 별도 장치에서 호스팅되는 파일 공유의 경로를 범용 명명 규칙 (UNC) 문자열을 사용 해야 합니다. UNC 문자열 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다. 백업 데이터의 가용성을 보장 하려면 장치의 별도 위치에 있어야 합니다. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>백업 설정 확인

동일한 PowerShell 세션에서 다음 명령을 실행 합니다.

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

결과 JSON 다음과 같이 표시 됩니다.

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>다음 단계

 - 백업, 참조를 실행 하는 방법을 배울 [Azure 스택 백업을](azure-stack-backup-back-up-azure-stack.md )합니다.  
 - 백업 실행 되었는지 확인 하는 방법을 배울, 참조 [관리 포털에서 완료 된 확인 백업](azure-stack-backup-back-up-azure-stack.md )합니다.
