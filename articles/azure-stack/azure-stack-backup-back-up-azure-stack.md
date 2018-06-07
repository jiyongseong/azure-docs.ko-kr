---
title: Azure 스택 백업을 | Microsoft Docs
description: Azure 스택 위치에 백업을 사용 하 여 요청 시 백업을 수행 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>Azure 스택 백업

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 위치에 백업을 사용 하 여 요청 시 백업을 수행 합니다. 인프라 백업 서비스를 사용 하도록 설정 해야 하는 경우 참조 [관리 포털에서 Azure 스택에 대 한 백업 사용](azure-stack-backup-enable-backup-console.md)합니다.

> [!Note]  
>  PowerShell 환경을 구성 하는 방법에 지침은 [Azure 스택 위한 PowerShell 설치 ](azure-stack-powershell-install.md)합니다.

## <a name="start-azure-stack-backup"></a>Azure 스택 백업 시작

연산자 관리 환경에서 관리자 권한 프롬프트를 통한 Windows PowerShell을 열고 다음 명령을 실행 합니다.

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>관리 포털에서 완료 된 백업 확인

1. 스택 Azure 관리 포털을 열고 [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)합니다.
2. 선택 **더 많은 서비스** > **인프라 백업**합니다. 선택 **구성** 에 **인프라 백업** 블레이드입니다.
3. 찾을 **이름** 및 **완료 날짜** 에서 백업의 **사용 가능한 백업을** 목록입니다.
4. 확인 된 **상태** 은 **Succeeded**합니다.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>다음 단계

- 데이터 손실 이벤트에서 복구에 대 한 워크플로 대해 자세히 알아보기 참조 [치명적인 데이터 손실 로부터 복구할](azure-stack-backup-recover-data.md)합니다.
