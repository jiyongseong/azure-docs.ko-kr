---
title: Azure 스택에 대 한 유효성 검사 보고서 | Microsoft Docs
description: Azure 스택 준비 검사 보고서를 사용 하 여 유효성 검사 결과 검토 합니다.
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
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-validation-report"></a>Azure 스택 유효성 검사 보고서
Azure 스택 준비 검사 도구를 지 원하는 배포 및 Azure 스택 환경 서비스를 제공 하는 유효성 검사를 실행 합니다. 이 도구는.json 보고서 파일에 유효성 검사 결과 씁니다. 보고서 표시 세부 및 요약 된 데이터의 Azure 스택의 배포 필수 구성 요소 상태에 비밀 회전에 기존 Azure 스택 배포에 대 한 합니다.  

 ## <a name="where-to-find-the-report"></a>보고서를 찾을 수 있는 위치
이 도구를 실행 하는 경우 결과를 기록 **AzsReadinessCheckerReport.json**합니다. 이 도구는 또한 라는 로그 만듭니다 **AzsReadinessChecker.log**합니다. 이러한 파일의 위치는 PowerShell에서 유효성 검사 결과를 표시합니다.

![유효성 검사 실행](./media/azure-stack-validation-report/validation.png)

두 파일 모두 동일한 컴퓨터에서 실행 시점 이후의 유효성 검사의 결과 유지 합니다.  예를 들어, 인증서의 유효성을 검사 하 고, Azure id 유효성 검사를 다시 실행 하 고, 등록 유효성을 검사 하는 세 번째 하는 다음에 도구를 실행할 수 있습니다. 모든 세 가지 유효성 검사의 결과 결과.json 보고서에서 사용할 수 있습니다.  

기본적으로 두 파일에 기록 됩니다 *C:\Users\<사용자 이름 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*합니다.  
- 사용 하 여는 **-OutputPath** ***&lt;경로&gt;*** 다른 보고서 위치를 지정 하려면 실행된 명령줄의 끝에 매개 변수입니다.   
- 사용 하 여 **-CleanReport** 끝에서 정보를 지우려면 run 명령에 매개 변수 *AzsReadinessCheckerReport.json*합니다. 이전에 대 한 도구를 실행합니다.

## <a name="view-the-report"></a>보고서 보기
PowerShell에서 보고서를 보려면 보고서에 대 한 경로 대 한 값으로 제공 **-ReportPath**합니다. 이 명령은 보고서의 내용을 표시 하 고도 아직 결과 갖지 않는 유효성 검사를 식별 합니다.

예를 들어 보고서가 있는 위치에 열려 있는 PowerShell 프롬프트에서 보고서를 보려면 다음을 실행 합니다. 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

출력에는 다음 이미지와 유사합니다.

![보고서 보기](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>요약 보고서를 보려면
보고서의 요약 보기를 추가할 수 있습니다는 **-요약** PowerShell 명령줄의 끝으로 전환 합니다. 예:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

요약은 유효성 검사 결과 갖지 않는 보여주며, 통과 하거나 완료 하는 유효성 검사 실패를 나타냅니다. 출력에는 다음 이미지와 유사합니다.

![보고서-요약](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>필터링 된 보고서 보기
단일 유형의 유효성 검사에 필터링 된 보고서를 보려면 사용 하 여는 **-ReportSections** 매개 변수를 보려는 유효성 검사 형식에 해당 하는 다음 값 중 하나를 지정 합니다.
- 인증서
- AzureRegistration
- AzureIdentity
- 교육   
- 모두  

예를 들어 보고서를 보려면 인증서에 대 한 요약만을 사용 하 여 다음 PowerShell 명령줄: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>참고 항목
[시작 AzsReadinessChecker cmdlet 참조](azure-stack-azsreadiness-cmdlet.md)
