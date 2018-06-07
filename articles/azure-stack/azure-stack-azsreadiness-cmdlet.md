---
title: 시작 AzsReadinessChecker cmdlet 참조 | Microsoft Docs
description: Azure 스택 준비 검사 모듈에 대 한 PowerShell cmdlet 도움말입니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>시작 AzsReadinessChecker cmdlet 참조

모듈: Microsoft.AzureStack.ReadinessChecker

이 모듈에는 단일 cmdlet을 포함합니다.  이 cmdlet는 Azure 스택에 대 한 하나 이상의 배포 전 또는 미리 서비스 기능을 수행합니다.

## <a name="syntax"></a>구문
```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```


```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```





 ## <a name="description"></a>설명
**시작 AzsReadinessChecker** cmdlet 인증서, Azure 계정, Azure 구독 및 Azure Active 디렉터리의 유효성을 검사 합니다. Azure 스택을 배포 하기 전에 또는 Azure 스택 비밀 회전와 같은 작업을 처리 하기 전에 유효성 검사를 실행 합니다. 또한 cmdlet 인프라 인증서 및 필요에 따라 PaaS 인증서에 대 한 인증서 서명 요청을 생성할 데 사용할 수 있습니다.  마지막으로, cmdlet 다시 일반적인 패키징 문제를 해결할 수 있는 PFX 인증서 패키지입니다.

## <a name="examples"></a>예
**인증서 서명 요청을 생성 하는 예:**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

이 예제에서는 시작 AzsReadinessChecker 여러 요청 CSR (인증서 서명)의 "east" 지역 이름과 프로그램 ADFS Azure 스택 배포에 적합 한 인증서에 대 한와 "azurestack.contoso.com"의 외부 FQDN을 생성

**예제: 인증서를 확인 합니다.**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

이 예에서 PFX 암호를 묻는 메시지가 표시 되는지 안전 하 게 하 고 시작 AzsReadinessChecker "east"과 "azurestack.contoso.com"의 외부 FQDN의 지역 이름과 AAD 배포에 적합 한 인증서에 대 한 상대 폴더 "인증서"를 확인 합니다. 

**예: 배포 데이터 (배포 및 지원) 사용 하 여 인증서 유효성 검사**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
이 배포 및 지원 예제는 PFX 암호를 묻는 메시지가 표시 되는지 안전 하 게 하 고 시작 AzsReadinessChecker 확인 id, 지역 및 외부 FQDN에서 읽은 배포에 적합 한 인증서에 대 한 상대 폴더 "인증서"는 배포에 대해 생성 된 배포 데이터 JSON 파일입니다. 

**예: PaaS 인증서를 확인 합니다.**
```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

이 예제에서는 경로 및 각 PaaS 인증서에 암호 해시 테이블 구성 됩니다. 인증서를 생략할 수 있습니다. 시작 AzsReadinessChecker 각 PFX 경로 존재 확인 '동쪽' 영역을 사용 하 여 및 외부 FQDN 'azurestack.contoso.com' 유효성을 검사 합니다.

**예: PaaS 데이터가 있는 인증서 배포의 유효성을 검사합니다**
```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

이 예제에서는 경로 및 각 PaaS 인증서에 암호 해시 테이블 구성 됩니다. 인증서를 생략할 수 있습니다. 시작 AzsReadinessChecker 각 PFX 경로가 있는지 그리고 영역을 사용 하 여 유효성을 검사 하 고 배포에 대해 생성 된 배포 데이터 JSON 파일에서 읽은 외부 FQDN을 확인 합니다. 

**예: Azure id 유효성 검사**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

이 예제에서는 서비스 관리자 계정 자격 증명에 대 한 안전 하 고 시작 AzsReadinessChecker는 AAD 테 넌 트 디렉터리 이름으로 "azurestack.contoso.com"의 배포를 위한 Azure 계정 및 Azure Active Directory 올바른지 확인


**예: 배포 데이터 (배포 지원)와 함께 Azure id를 확인 합니다.**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

이 예제에서는 서비스 관리자 계정 자격 증명에 대 한 안전 하 고 시작 AzsReadinessChecker Azure 계정 및 Azure Active Directory는 확인 AAD 배포에 적합 한 azure 클라우드 및 TenantName 배포 데이터에서 읽은 여기서 배포에 대해 생성 된 JSON 파일입니다.


**예: Azure 등록 유효성 검사**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

이 예제에서는 안전 하 게 구독 소유자 자격 증명에 대 한 메시지가 나타나면 및 시작 AzsReadinessChecker 다음 지정 된 계정에 대해 유효성 검사를 수행 하 고 구독 하는지 확인 하려면 Azure 스택 등록에 사용할 수 있습니다. 


**예: 배포 데이터 (배포 팀)와 함께 Azure 등록 유효성 검사**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

이 예제에서는 구독 소유자 자격 증명을 안전 하 게에 대 한 메시지가 및 시작 AzsReadinessChecker 다음 지정 된 계정에 대해 유효성 검사를 수행 하 고 추가 세부 정보는 여기서 Azure 스택 등록에 구독을 확인 하기를 사용할 수 있습니다. 배포에 대해 생성 된 배포 데이터 JSON 파일에서 읽습니다.

**예: 가져오기/내보내기 PFX 패키지**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

이 예제에서는 PFX 암호를 묻는 메시지가 표시 되는지 안전 하 게 합니다. Ssl.pfx 파일 로컬 컴퓨터 인증서 저장소로와 동일한 암호를 사용 하 여 내보낸 다시 가져오고 ssl_new.pfx로 저장 합니다.  이 절차는 개인 키에는 로컬 컴퓨터 특성 집합 없는, 인증서 체인은 끊어지고, 관련이 없는 인증서는 PFX에 있는지 또는 인증서 체인은 잘못 된 순서로 인증서 유효성 검사 플래그가 지정 되 면 사용 됩니다.


**예: 보기 유효성 검사 보고서 (배포 지원)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

이 예제에서는 배포 또는 지원 팀 고객 (Contoso)에서 준비 보고서를 받을 Contoso 수행 된 유효성 검사 실행의 상태를 볼 시작 AzsReadinessChecker 사용 합니다.

**예: 유효성 검사 보고서 인증서 유효성 검사 전용 (배포 및 지원)에 대 한 요약 보기**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

이 예제에서는 배포 또는 지원 팀 준비 보고서 Contoso 고객 으로부터 받는 Contoso 수행 인증서 유효성 검사 실행에 대 한 요약 된 상태를 볼 시작 AzsReadinessChecker 사용 합니다.



## <a name="required-parameters"></a>필수 매개 변수
> -RegionName

Azure 스택 배포의 영역 이름을 지정합니다.
|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |없음          |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자 허용: |False         |

> -FQDN    

또한 별칭이 ExternalFQDN 및 ExternalDomainName Azure 스택 배포의 외부 FQDN을 지정합니다.
|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |ExternalFQDN, ExternalDomainName |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자 허용: |False         |

 

> -IdentitySystem    

Azure Active Directory 및 Active Directory Federated Services에 대 한 Azure 스택 배포 Id 시스템 올바른 값, AAD 또는 ADFS를 각각 지정합니다.
|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |없음          |
|유효한 값:                |' AAD', 'ADFS'  |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자 허용: |False         |

> -PfxPassword    

PFX 인증서 파일을 연관 된 암호를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |SecureString |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -PaaSCertificates

경로 PaaS 인증서 암호를 포함 하는 해시 테이블을 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |해시 테이블 |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -DeploymentDataJSONPath

Azure 스택 배포 데이터 JSON 구성 파일을 지정합니다. 이 파일은 배포를 위해 생성 됩니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -PfxPath

이 도구에서 인증서 유효성 검사에 표시 된 대로 가져오기/내보내기를 해결 하는 루틴을 필요로 하는 문제가 있는 인증서의 경로를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -ExportPFXPath  

가져오기/내보내기 루틴에서 결과 PFX 파일에 대 한 대상 경로 지정합니다.  
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -제목

인증서 요청 생성에 대 한 대상의 정렬된 된 사전을 지정합니다.
|  |  |
|----------------------------|---------|
|형식:                       |OrderedDictionary   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -RequestType

인증서 요청의 SAN 유형을 지정합니다. 유효한 값 MultipleCSR, SingleCSR입니다.
- *MultipleCSR* 각 서비스에 대해 하나씩 여러 인증서 요청을 생성 합니다.
- *SingleCSR* 모든 서비스에 대 한 인증서 요청을 생성 합니다.   

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|유효한 값:                |' MultipleCSR', 'SingleCSR' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -OutputRequestPath

대상 경로 지정 인증서 요청 파일을 디렉터리가 이미 존재 해야 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -AADServiceAdministrator

Azure 스택 배포에 사용할 Azure Active Directory 서비스 관리자를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |PSCredential   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -AADDirectoryTenantName

Azure 스택 배포에 사용할 Azure Active Directory 이름을 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -AzureEnvironment

Azure 스택 배포 및 등록에 사용할 계정, 디렉터리 및 구독에 포함 된 Azure 서비스의 인스턴스를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|유효한 값:                |' Azure 클라우드 ', 'AzureChinaCloud', 'AzureGermanCloud' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -RegistrationAccount

Azure 스택 등록에 사용할 등록 계정을 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -RegistrationSubscriptionID

Azure 스택 등록에 사용할 등록 구독 ID를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |Guid     |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |

> -ReportPath

현재 디렉터리 및 기본 보고서 이름으로 기본 설정를 준비 보고서에 대 한 경로 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |모두      |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |



## <a name="optional-parameters"></a>선택적 매개 변수
> -CertificatePath     

경로는 인증서만 필요한 인증서 폴더를 사용할 수 있는지를 지정 합니다.

Azure Active Directory id 시스템으로 Azure 스택 배포 하기 위해 필요한 폴더는:

ACSBlob, ACSQueue, ACSTable, 관리 포털, ARM Admin ARM Public, KeyVault, KeyVaultInternal, 공용 포털

Active Directory Federation Services id 시스템을 사용 하는 배포는 Azure 스택에 대 한 폴더를 필요 합니다.

ACSBlob, ACSQueue, ACSTable, ADFS, 관리 포털, ARM 관리, ARM 공개, 그래프, KeyVault, KeyVaultInternal, 공용 포털


|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |. \Certificates |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |


> -IncludePaaS  

PaaS 서비스/호스트 이름이 인증서 요청에 추가 해야 하는지 지정 합니다.


|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자 허용: |False             |


> -ReportSections        

세부 정보 생략 요약, 보고서 표시 여부를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |모두      |
|유효한 값:                |'Certificate', 'AzureRegistration', 'AzureIdentity', '작업', 'All' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자 허용: |False    |


> -요약 

세부 정보 생략 요약, 보고서 표시 여부를 지정 합니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자 허용: |False             |


> -CleanReport  

이전 실행 및 유효성 검사 기록을 제거 하 고 새 보고서를 유효성 검사를 씁니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |cf                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자 허용: |False             |


> -OutputPath    

준비 JSON 보고서와 자세한 로그 파일 저장에 대 한 사용자 지정 경로 지정 합니다.  경로 아직 없는 경우이 도구는 디렉터리를 만드는 시도 합니다.
|  |  |
|----------------------------|------------------|
|형식:                       |문자열            |
|위치:                   |named             |
|기본값:              |$ENV: TEMP\AzsReadinessChecker  |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자 허용: |False             |


> -확인  

이 cmdlet을 실행 하기 전에 확인 메시지가 나타납니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |cf                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자 허용: |False             |


> -WhatIf  

Cmdlet을 실행 하면 어떻게 되는지를 보여 줍니다. Cmdlet이 실행 되지 않습니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |wi                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자 허용: |False             |

 
