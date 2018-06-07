---
title: 시작 및 중지 Azure 스택 개발 키트 (ASDK) | Microsoft Docs
description: 시작 하 여 Azure 스택 개발 키트 (ASDK)를 종료 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>시작 및 중지 Azure 스택 개발 키트 (ASDK)
단순히 ASDK 호스트 컴퓨터를 다시 시작 하는 권장 되지 않습니다. 대신, 제대로 종료 하 고 ASDK 서비스를 다시 시작이 문서의 절차 따라야 합니다. 

## <a name="stop-azure-stack"></a>Azure 스택 중지 
제대로 Azure 스택 서비스 및 ASDK 호스트 컴퓨터를 종료 하려면 다음 PowerShell 명령을 사용 합니다.

1. AzureStack\CloudAdmin로 ASDK 호스트 컴퓨터에 로그인 합니다.
2. (하지 PowerShell ISE) 관리자 권한으로 PowerShell을 엽니다.
3. 권한 있는 끝점 (PEP) 세션을 설정 하려면 다음 명령을 실행 합니다. 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 다음 PEP 세션을 사용 하 여 **중지 AzureStack** Azure 스택 서비스를 중지 하 고 ASDK 호스트 컴퓨터를 종료 하는 cmdlet:

   ```powershell
   Stop-AzureStack
   ```
5. 모든 Azure 스택 서비스를 성공적으로 종료 전에 ASDK 호스트 컴퓨터가 종료 되도록 PowerShell 출력을 검토 합니다. 종료 프로세스는 몇 분 정도 걸립니다.

## <a name="start-azure-stack"></a>Azure 스택 시작 
ASDK 서비스는 호스트 컴퓨터를 시작할 때 자동으로 시작 해야 합니다. 그러나 ASDK 인프라 서비스 시작 시간 ASDK 호스트 컴퓨터 하드웨어 구성의 성능에 따라 달라 집니다. 일부 경우에 성공적으로 다시 시작 하려면 모든 서비스에 몇 시간이 걸릴 수 있습니다.

ASDK 종료 된 방식에 관계 없이 있는지 모든 Azure 스택 서비스가 시작 되 고 완벽 하 게 작동 호스트 컴퓨터의 전원이 켜져 후 확인 하려면 다음 단계를 사용 해야 합니다. 

1. ASDK 호스트 컴퓨터의 전원을 켭니다. 
2. AzureStack\CloudAdmin로 ASDK 호스트 컴퓨터에 로그인 합니다.
3. (하지 PowerShell ISE) 관리자 권한으로 PowerShell을 엽니다.
4. 권한 있는 끝점 (PEP) 세션을 설정 하려면 다음 명령을 실행 합니다.

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. 다음으로, PEP 세션에서 Azure 스택 서비스의 시작 상태를 확인 하려면 다음 명령을 실행 합니다.

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Azure 스택 서비스가 다시 시작 되었는지 있는지 확인 하려면 출력을 검토 합니다.

올바르게 종료 하 고 Azure 스택 서비스를 다시 시작 하는 권장된 절차에 대 한 자세한 참조 [Start 및 stop Azure 스택](.\.\azure-stack-start-and-stop.md)합니다. 

## <a name="troubleshoot-startup-and-shutdown"></a>시작 및 종료 문제 해결 
Azure 스택 서비스 ASDK 호스트 컴퓨터의 전원 후 2 시간 이내에 성공적으로 시작 하지 않는 경우 이러한 단계를 수행 합니다.

1. AzureStack\CloudAdmin로 ASDK 호스트 컴퓨터에 로그인 합니다.
2. (하지 PowerShell ISE) 관리자 권한으로 PowerShell을 엽니다.
3. 권한 있는 끝점 (PEP) 세션을 설정 하려면 다음 명령을 실행 합니다.

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 다음으로, PEP 세션에서 Azure 스택 서비스의 시작 상태를 확인 하려면 다음 명령을 실행 합니다.

   ```powershell
   Test-AzureStack
   ```
5. 출력을 검토 하 고 오류를 해결 합니다. 자세한 내용은 참조 [Azure 스택의 유효성 검사 테스트를 수행](.\.\azure-stack-diagnostic-test.md)합니다.
6. 실행 하 여 PEP 세션 내에서 Azure 스택 서비스를 다시 시작은 **시작 AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

실행 하는 경우 **시작 AzureStack** 결과 오류를 방문는 [Azure 스택 지원 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) 문제 해결 지원 ASDK 얻으려고 합니다. 

## <a name="next-steps"></a>다음 단계 
Azure 스택 진단 도구에 대 한 자세한 정보 및 로깅 문제점, 참조 [Azure 스택 진단 도구](.\.\azure-stack-diagnostics.md)합니다.
