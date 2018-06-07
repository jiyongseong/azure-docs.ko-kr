---
title: Azure 스택에 대 한 인증서 문제를 해결할 수 | Microsoft Docs
description: Azure 스택 준비 검사를 사용 하 여 검토 하 고 인증서 문제를 해결할 수 있습니다.
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
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Azure 스택 PKI 인증서에 대 한 일반적인 문제 해결
이 문서의 정보에는 이해 하 고 Azure 스택 PKI 인증서에 대 한 일반적인 문제를 해결할 수 있습니다. Azure 스택 준비 검사기 도구를 사용 하면 문제를 검색할 수 있습니다 [Azure 스택 PKI 인증서의 유효성을 검사](azure-stack-validate-pki-certs.md)합니다. 확인 인증서 Azure 스택 배포 및 Azure 스택 비밀 회전 PKI 요구 사항을 충족 하 고 결과 로그인 한 [report.json 파일](azure-stack-validation-report.md)합니다.  

## <a name="read-pfx"></a>PFX 읽기
**경고** -암호는 인증서의 개인 정보 보호 합니다.  
**업데이트 관리** -내보내기 PFX 파일에 대 한 선택적 설정이 사용 하는 것이 좋습니다 **인증서 개인 정보 사용**합니다.  

**오류** -PFX 파일이 잘못 되었습니다.  
**업데이트 관리** -다시의 단계를 사용 하 여 인증서를 내보내야 [배포를 위한 준비 Azure 스택 PKI 인증서](azure-stack-prepare-pki-certs.md)합니다.

## <a name="signature-algorithm"></a>서명 알고리즘
**오류** -서명 알고리즘은 s h a 1입니다.    
**업데이트 관리** -Azure 스택 인증서 서명 요청 CSR (인증서) 서명 SHA256 알고리즘으로 다시 생성 하는 요청 생성 서명의 단계를 사용 합니다. 다음 인증서를 다시 발급 해야 인증 기관에 CSR을 다시 전송 하십시오.

## <a name="private-key"></a>개인 키
**오류** -개인 키가 없습니다 또는 로컬 컴퓨터 특성을 포함 하지 않음.  
**업데이트 관리** -CSR을 생성 하는 컴퓨터에서 다시 준비 Azure 스택 PKI 인증서의 배포에 대 한 단계에 따라 인증서를 내보냅니다. 이러한 단계는 로컬 컴퓨터 인증서 저장소에서 내보내기 포함 됩니다.

## <a name="certificate-chain"></a>인증서 체인
**오류** -인증서 체인이 완전 하지 않습니다.  
**업데이트 관리** -인증서 전체 인증서 체인을 포함 해야 합니다.  단계를 사용 하 여 인증서를 다시 내보낼 [배포를 위한 준비 Azure 스택 PKI 인증서](azure-stack-prepare-pki-certs.md) 옵션을 선택 하 고 **가능 하면 인증 경로 있는 모든 인증서를 포함 합니다.**

## <a name="dns-names"></a>DNS 이름
**오류** -Azure 스택 서비스 끝점 이름 또는 유효한 와일드 카드 일치를 사용 하는 인증서의 DNSNameList 포함 되지 않습니다.  와일드 카드 일치 DNS 이름의 맨 왼쪽 네임 스페이스에 대해서만 유효 합니다. 예를 들어 _*. region.domain.com_ 대해서만 유효 *portal.region.domain.com*이 아니라 _*. table.region.domain.com_합니다.  
**업데이트 관리** -Azure 스택 끝점을 지원 하기 위해 Azure 스택 인증서 서명 요청 만들기 올바른 DNS 이름 갖는 CSR을 다시 생성 하의 단계를 사용 합니다. 인증 기관에 CSR을 다시 제출 하 고 다음의 단계에 따라 [배포를 위한 준비 Azure 스택 PKI 인증서](azure-stack-prepare-pki-certs.md) CSR을 생성 하는 컴퓨터에서 인증서를 내보냅니다.  

## <a name="key-usage"></a>키 사용
**오류** -키 사용 디지털 서명이 없거나 서버 인증과 클라이언트 인증 키 암호화, 키 사용 orEnhanced 없습니다.  
**업데이트 관리** -의 단계를 사용 하 여 [Azure 스택 인증서 서명 요청 만들기](azure-stack-get-pki-certs.md) 올바른 키 사용 특성을 가진 CSR을 다시 생성 해야 합니다.  인증 기관에 CSR을 다시 제출 하 고 인증서 템플릿을 요청의 키 사용을 덮어쓰지는 확인 합니다.

## <a name="key-size"></a>키 크기
**오류** -키 크기가 2048 보다 작습니다.    
**업데이트 관리** -의 단계를 사용 하 여 [Azure 스택 인증서 서명 요청 만들기](azure-stack-get-pki-certs.md) 에 올바른 키 길이 (2048)를 가진 CSR을 다시 생성 한 다음 인증 기관에 CSR을 다시 전송 하십시오.

## <a name="chain-order"></a>체인 순서
**오류** -인증서 체인의 순서가 잘못 되었습니다.  
**업데이트 관리** -다시의 단계를 사용 하 여 인증서를 내보내야 [배포를 위한 준비 Azure 스택 PKI 인증서](azure-stack-prepare-pki-certs.md) 옵션을 선택 하 고 **가능 하면 인증 경로 있는 모든 인증서를 포함 합니다.** 내보내기에 대 한 리프 인증서만 선택 되어 있는지 확인 합니다. 

## <a name="other-certificates"></a>기타 인증서
**오류** -리프 인증서 또는 인증서 체인의 일부가 되는 인증서를 PFX 패키지에 포함 되어 있습니다.  
**업데이트 관리** -다시의 단계를 사용 하 여 인증서를 내보내야 [배포를 위한 준비 Azure 스택 PKI 인증서](azure-stack-prepare-pki-certs.md), 옵션을 선택 하 고 **가능 하면 인증 경로 있는 모든 인증서를 포함 합니다.** 내보내기에 대 한 리프 인증서만 선택 되어 있는지 확인 합니다.

## <a name="fix-common-packaging-issues"></a>일반적인 패키징 문제 해결
AzsReadinessChecker 가져오고를 비롯 한 일반적인 패키징 문제를 해결 하려면 PFX 파일을 내보낼 수 있습니다.: 
 - *개인 키* 로컬 컴퓨터 특성이 없습니다.
 - *인증서 체인* 완전 하지 않거나 잘못 되었습니다. (로컬 컴퓨터 인증서 체인의 경우 해서는 PFX 패키지 되지 않습니다 됩니다.) 
 - *다른 인증서*합니다.
그러나 새 CSR을 생성 하 고 인증서를 다시 실행 해야 할 경우에 AzsReadinessChecker 도울 수 없습니다. 

### <a name="prerequisites"></a>필수 조건
다음과 같은 전제 조건을 도구가 실행 되는 컴퓨터에서 직접에서 해야 합니다. 
 - Windows 10 또는 Windows Server 2016 인터넷에 연결 합니다.
 - PowerShell 5.1 이상입니다. 사용 중인 버전을 확인 하려면 다음 PowerShell cmd를 실행 한 다음 검토는 *주요* 버전 및 *부* 버전입니다.

   > `$PSVersionTable.PSVersion`
 - 구성 [Azure 스택에 대 한 PowerShell](azure-stack-powershell-install.md)합니다. 
 - 최신 버전의 다운로드 [Microsoft Azure 스택 준비 검사](https://aka.ms/AzsReadinessChecker) 도구입니다.

### <a name="import-and-export-an-existing-pfx-file"></a>기존 PFX 파일 가져오기 및 내보내기
1. 를 선행 조건을 충족 하는 컴퓨터에서 관리자 PowerShell 프롬프트를 열고는 AzsReadinessChecker를 설치 하려면 다음 명령을 실행 하  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. PowerShell 프롬프트에서 PFX 암호를 설정 하려면 다음을 실행 합니다. 대체 *PFXpassword* 실제 암호를 사용 합니다. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. PowerShell 프롬프트에서 새 PFX 파일로 내보내려면 다음과 같이 실행 합니다.
   - 에 대 한 *-PfxPath*를 사용 하는 PFX 파일의 경로를 지정 합니다.  다음 예제에서 경로 *.\certificates\ssl.pfx*합니다.
   - 에 대 한 *-ExportPFXPath*, 내보내기에 대 한 PFX 파일의 이름과 위치를 지정 합니다.  다음 예제에서 경로 *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. 도구를 완료 한 후 성공에 대 한 출력을 검토할: ![결과](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>다음 단계
[Azure 스택 보안에 대 한 자세한 정보](azure-stack-rotate-secrets.md)