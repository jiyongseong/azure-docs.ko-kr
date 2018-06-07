---
title: Azure 스택 인프라 Backup 서비스에 대 한 백업 및 데이터 복구 | Microsoft Docs
description: 백업 하 고 구성 및 인프라 백업 서비스를 사용 하 여 서비스 데이터를 복원할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 8c8037fe3936485082299250e603b2f3ea3859b9
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Azure 스택 인프라 Backup 서비스에 대 한 백업 및 데이터 복구

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

백업 하 고 구성 및 인프라 백업 서비스를 사용 하 여 서비스 데이터를 복원할 수 있습니다. Azure 스택 설치 하는 각 서비스의 인스턴스를 포함합니다. Id, 보안 및 Azure 리소스 관리자 데이터를 복원 하려면 Azure 스택 클라우드에 다시 배포에 대 한 서비스에서 만든 백업을 사용할 수 있습니다.

프로덕션 환경에 클라우드 배포를 준비 되 면 백업을 사용할 수 있습니다. 테스트를 수행 하려는 경우 백업 및 오랜 시간에 대 한 유효성 검사를 사용 하지 마십시오.

백업 서비스에 사용 하도록 설정 했는지 확인 [요구](#verify-requirements-for-the-infrastructure-backup-service)합니다.

> [!Note]  
> 사용자 데이터 및 응용 프로그램 인프라 백업 서비스를 포함 하지 않습니다. 백업에 대 한 지침은 다음 문서를 참조 하 고 복원 [응용 프로그램 서비스](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), 및 [MySQL](https://aka.ms/azure-stack-mysql) 리소스 공급자와 연결 된 사용자 데이터...

## <a name="the-infrastructure-backup-service"></a>인프라 백업 서비스

서비스는 다음과 같은 기능이 포함 되어 있습니다.

| 기능                                            | 설명                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 백업 인프라 서비스                     | 스택에서 Azure 인프라 서비스의 하위 집합에서 백업을 조정 합니다. 재해가 경우 재배포의 일부로 데이터를 복원할 수 있습니다. |
| 압축 및 내보낸된 백업 데이터의 암호화 | 백업 데이터 압축 및 외부 저장 위치는 관리자가 제공한로 내보낸 전에 시스템에서 암호화 합니다.                |
| 백업 작업 모니터링                              | 백업 작업이 실패 하 고 재구성 단계 시스템에 알립니다.                                                                                                |
| 백업 관리 경험                       | 백업 RP 백업을 사용할 수 있도록 지원합니다.                                                                                                                         |
| 클라우드 복구                                     | 없을 경우 치명적인 데이터 손실, 백업은 배포의 일부로 핵심 Azure 스택 정보를 복원 하려면 사용할 수 있습니다.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>인프라 백업 서비스에 대 한 요구 사항 확인

- **저장소 위치**  
  필요한 파일 공유 7 개의 백업을 포함 될 수 있는 Azure 스택에서 액세스할 수 있는 합니다. 각 백업에는 약 10GB입니다. 공유에 저장할 140 GB의 백업 할 수 있어야 합니다. Azure 스택 인프라 백업 서비스에 대 한 저장소 위치를 선택 하는 방법에 대 한 자세한 내용은 참조 [백업 컨트롤러 요구 사항](azure-stack-backup-reference.md#backup-controller-requirements)합니다.
- **자격 증명**  
  도메인 사용자 계정 및 자격 증명 필요, 예를 들어 Azure 스택 관리자 자격 증명을 사용할 수 있습니다.
- **암호화 키**  
  이 키를 사용 하 여 백업 파일이 암호화 됩니다. 이 키는 안전한 위치에 저장 했는지 확인 합니다. 를 처음으로이 키를 설정 하거나 나중에 키를 회전 후이 인터페이스에서이 키를 볼 수 없습니다. 사전 공유 키를 생성 하려면 명령에 대 한 스크립트에 따라 [PowerShell과 함께 Azure 스택에 대 한 백업 사용](http://azure-stack-backup-enable-backup-powershell.md)합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 방법 [관리 포털에서 Azure 스택에 대 한 백업 사용](azure-stack-backup-enable-backup-console.md)합니다.
- 자세한 방법 [PowerShell과 함께 Azure 스택에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.
- 자세한 내용은 방법 [Azure 스택 백업을](azure-stack-backup-back-up-azure-stack.md )
- 자세한 내용은 방법 [치명적인 데이터 손실 로부터 복구](azure-stack-backup-recover-data.md)
