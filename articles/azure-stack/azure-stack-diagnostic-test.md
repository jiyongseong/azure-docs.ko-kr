---
title: Azure 스택에서 유효성 검사 테스트를 실행 | Microsoft Docs
description: Azure 스택에서 진단 로그 파일을 수집 하는 방법
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: mabrigg
ms.openlocfilehash: c28216ced2a7cd2995c55a9faacb93cf27e60c65
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Azure 스택에 대 한 유효성 검사 테스트를 실행 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*
 
Azure 스택의 상태를 확인할 수 있습니다. 문제가 있는 경우에 Microsoft 고객 서비스 지원에 문의 합니다. 지원 요청 관리 노드에서 AzureStack 테스트를 실행할 수 있습니다. 유효성 검사 테스트 실패를 파악합니다. 지원 수 다음 자세한 로그를 분석, 오류가 발생 하는 영역에 집중 및 작동 하는 문제를 해결 합니다.

## <a name="run-test-azurestack"></a>AzureStack 테스트 실행

문제가 발생 하는 경우 Microsoft 고객 서비스 지원에 문의 한 후 실행 **테스트 AzureStack 실행**합니다.

1. 문제가 발생 합니다.
2. 연락처 Microsoft 고객 서비스 지원 합니다.
3. 실행 **테스트 AzureStack** 에서 권한 있는 끝점입니다.
    1. 권한 있는 끝점에 액세스 합니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure 스택의](azure-stack-privileged-endpoint.md)합니다. 
    2. ASDK에서 로그인 관리 호스트으로 **AzureStack\CloudAdmin**합니다.  
    통합된 된 시스템에서 권한 있는--끝점을 OEM 하드웨어 공급 업체에서 제공 하는 관리에 대 한 IP 주소를 사용 해야 합니다.
    3. 관리자 권한으로 PowerShell을 엽니다.
    4. 실행: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. 실행: `Test-AzureStack`
4. 테스트 실패를 보고 하는 경우 실행: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` 테스트 AzureStack에서 로그를 수집 하는 cmdlet입니다. 진단 로그에 대 한 자세한 내용은 참조 [Azure 스택 진단 도구](azure-stack-diagnostics.md)합니다.
5. 보내기는 **SeedRing** 로그를 Microsoft 고객 서비스 지원 합니다. Microsoft 고객 서비스 지원 작동 문제를 해결 해야 합니다.

## <a name="reference-for-test-azurestack"></a>AzureStack 테스트에 대 한 참조

이 섹션에서는 테스트 AzureStack cmdlet 및 유효성 검사 보고서의 요약에 대 한 개요입니다.

### <a name="test-azurestack"></a>Test-AzureStack

Azure 스택의 상태를 확인합니다. Azure 스택 하드웨어 및 소프트웨어의 상태를 보고 하는 cmdlet입니다. 지원 담당자 Azure 스택 지원 사례를 해결 하는 시간을 줄이기 위해이 보고서를 사용할 수 있습니다.

> [!Note]  
> 단일 디스크 또는 단일 실제 호스트 노드 오류 실패와 같은 테스트 AzureStack 클라우드 작동 중단은 생성 되지 않고 오류를 검색할 수 있습니다.

#### <a name="syntax"></a>구문

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>매개 변수

| 매개 변수               | 값           | 필수 | 기본값 |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | 아니요       | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | 아니요       | FALSE   |
| AdminCredential         | PSCredential    | 아니요       | 해당 없음      |
<!-- | StorageConnectionString | 문자열          | 아니요       | 해당 없음      | 1802에서 지원 되지 않습니다-->
| 나열                    | SwitchParameter | 아니요       | FALSE   |
| 무시                  | 문자열          | 아니요       | 해당 없음      |
| 포함                 | 문자열          | 아니요       | 해당 없음      |

테스트 AzureStack cmdlet은 공통 매개 변수: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable, 및 outvariable을 지원 합니다. 자세한 내용은 참조 [에 대 한 공통 매개 변수](http://go.microsoft.com/fwlink/?LinkID=113216)합니다. 

### <a name="examples-of-test-azurestack"></a>테스트 AzureStack의 예

다음 예제로 로그인 가정 **CloudAdmin** (PEP) 권한이 가진된 끝점에 액세스 하 고 있습니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure 스택의](azure-stack-privileged-endpoint.md)합니다. 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>클라우드 시나리오 없이 테스트 AzureStack를 대화형으로 실행

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>테스트 AzureStack 클라우드 시나리오를 사용 하 여 실행

Azure 스택에 대해 클라우드 시나리오를 실행 하려면 테스트 AzureStack를 사용할 수 있습니다. 이 시나리오에는 다음이 포함됩니다.

 - 리소스 그룹 만들기
 - 계획 만들기
 - 제안 만들기
 - 저장소 계정 만들기
 - 가상 컴퓨터 만들기
 - 테스트 시나리오에서 만든 저장소 계정을 사용 하 여 blob 작업을 수행 합니다.
 - 테스트 시나리오에서 만든 저장소 계정을 사용 하 여 큐 작업을 수행 합니다.
 - 테스트 시나리오에서 만든 저장소 계정을 사용 하 여 테이블 작업을 수행 합니다.

클라우드 시나리오에는 클라우드 관리자 자격 증명이 필요 합니다. 
> [!Note]  
> 페더레이션 서비스 ADFS (Active Directory) 자격 증명을 사용 하 여 클라우드 시나리오를 실행할 수 없습니다. **테스트 AzureStack** 만 cmdlet에는 PEP를 통해 액세스할 수 있습니다. 그러나는 PEP ADFS 자격 증명을 지원 하지 않습니다.

클라우드 관리자 사용자 이름이 UPN 형식으로 입력 serviceadmin@contoso.onmicrosoft.com (AAD). 메시지가 표시 되 면 클라우드 관리자 계정에 암호를 입력 합니다.

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>클라우드 시나리오 없이 AzureStack 테스트 실행

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>사용 가능한 테스트 시나리오를 나열 합니다.

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>지정된 된 테스트 실행

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

특정 테스트를 제외 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>유효성 검사 테스트

다음 표에서 테스트 AzureStack 실행 하는 유효성 검사 테스트를 요약 합니다.

| 이름                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure 스택 클라우드 인프라 요약 호스팅                                                                                  |
| Azure 스택 저장소 서비스 요약                                                                                              |
| Azure 스택 인프라 역할 인스턴스 요약                                                                                  |
| Azure 스택 클라우드 인프라 사용률 호스팅                                                                              |
| Azure 스택 인프라 용량                                                                                               |
| Azure 스택 포털 및 API 요약                                                                                                |
| Azure 스택 Azure 리소스 관리자 인증서 요약                                                                                               |
| 인프라 관리 컨트롤러, 네트워크 컨트롤러, 저장소 서비스 및 인프라 역할 권한 있는 끝점          |
| 인프라 관리 컨트롤러, 네트워크 컨트롤러, 저장소 서비스 및 권한 있는 역할 인스턴스 인프라 끝점 |
| Azure 스택 인프라 역할 요약                                                                                           |
| Azure 스택 클라우드 서비스 패브릭 서비스                                                                                         |
| Azure 스택 인프라 역할 인스턴스 성능                                                                              |
| Azure 스택 클라우드 호스트 성능 요약                                                                                        |
| Azure 스택 서비스 리소스 소비 요약                                                                                  |
| Azure 스택 배율 단위 중요 한 이벤트 (마지막 8 시간)                                                                             |
| Azure 스택 저장소 서비스의 실제 디스크 요약                                                                               |

## <a name="next-steps"></a>다음 단계

 - Azure 스택 진단 도구 및 문제 로깅에 대 한 자세한 참조 [ Azure 스택 진단 도구](azure-stack-diagnostics.md)합니다.
 - 문제 해결에 대 한 자세한 참조 [Microsoft Azure 스택 문제 해결](azure-stack-troubleshooting.md)