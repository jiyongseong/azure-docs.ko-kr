---
title: 인프라 백업 서비스에 대 한 유용한 Azure 스택 | Microsoft Docs
description: 배포 하 고 치명적인 오류가 발생할 경우 데이터 손실 위험을 완화 하려면 데이터 센터에서 Azure 스택을 관리 하는 경우 일련의 모범 사례를 따르면 됩니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: ec30832e6863ad92eff8f5c2e613adc503c73af5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>인프라 백업 서비스에 대 한 유용한 정보

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

배포 하 고 Azure 스택 심각한 오류가 발생 하면 데이터 손실 위험을 완화 하려면 데이터 센터에서 관리 하는 경우 모범 사례를 따르면 됩니다.

작업 흐름에 변경 사항이 있을 경우 규정 준수에 설치 되었는지 확인 하려면 일정 한 간격으로 모범 사례를 검토 해야 합니다. 이들 모범 사례를 구현 하는 동안 문제가 발생 해야 하면, 도움말에 대 한 Microsoft 지원에 문의 합니다.

## <a name="configuration-best-practices"></a>구성 모범 사례

### <a name="deployment"></a>배포

각 Azure 스택 클라우드에 배포 된 후 인프라 Backup을 설정 합니다. 도구로 AzureStack-연산자 관리 API 끝점에 액세스할 수 있는 모든 클라이언트/서버에서 백업을 예약할 수 있습니다.

### <a name="networking"></a>네트워킹

경로 대 한 범용 명명 규칙 (UNC) 문자열 정규화 된 도메인 이름 (FQDN)를 사용 해야 합니다. IP 주소는 경우에 이름 확인은 가능 하지 않습니다. UNC 문자열 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다.

### <a name="encryption"></a>암호화

암호화 키 백업 데이터를 내보낼 수 외부 저장소에 암호화에 사용 됩니다. AzureStack 도구를 사용 하 여 키를 생성할 수 있습니다. 

![AzureStack 도구](media\azure-stack-backup\azure-stack-backup-encryption1.png)

키 (예를 들어 공용 Azure 키 자격 증명 모음 secret)입니다. 안전한 위치에 저장 되어야 합니다. 이 키는 Azure 스택의 재배포 하는 동안 사용 되어야 합니다. 

![키를 안전한 위치에 저장 합니다.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>운영 모범 사례

### <a name="backups"></a>Backup

 - 인프라 백업 컨트롤러는 필요에 따라 트리거되지 해야 합니다. 하루 두 번 이상 백업 하도록 권장이 합니다.
 - 백업 작업에는 시스템 관리 환경을 또는 사용자 응용 프로그램을 가동 중지 시간 없이 이므로 실행 되는 동안 실행 합니다. 20-40 분 적절 한 부하에서 관리 하는 솔루션에 대 한 백업 작업을 기대 합니다.
 - OEM 안내 하 고, 사용 하 여 수동으로 백업 네트워크 스위치와 하드웨어 수명 주기 호스트 (HLH) 저장할지 여기서 인프라 백업 컨트롤러 저장소 제어 평면 백업 데이터는 동일한 백업 공유에 있습니다. 스위치와 HLH 구성 영역 폴더에 저장 하는 것이 좋습니다. 여러 Azure 스택 인스턴스를 동일한 지역에 있는 경우에 배율 단위에 속하는 각 구성에 대 한 식별자를 사용 하는 것이 좋습니다.

### <a name="folder-names"></a>폴더 이름

 - 인프라는 MASBACKUP 폴더를 자동으로 만듭니다. Microsoft 관리 공유입니다. MASBACKUP와 동일한 수준에서 공유를 만들 수 있습니다. 폴더를 만들거나 Azure 스택 만들지 않는 MASBACKUP 안에 데이터 저장소는 권장 되지 않습니다. 
 -  사용자 FQDN 및 폴더 이름에 지역에서 다른 클라우드 백업 데이터를 구분 하 합니다. Azure 스택 배포 및 끝점의 정규화 된 도메인 이름 (FQDN)은 영역 매개 변수 및 외부 도메인 이름 매개 변수 조합 합니다. 자세한 내용은 참조 [스택 Azure 데이터 센터 통합 DNS](azure-stack-integrate-dns.md)합니다.

예를 들어 백업 공유 AzSBackups fileserver01.contoso.com에서 호스트 됩니다. 해당 파일 공유에서 외부 도메인 이름 및 영역 이름을 사용 하는 하위 폴더를 사용 하 여 Azure 스택 배포 마다 폴더 있을 수 있습니다. 

FQDN: contoso.com  
지역: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup 폴더는 Azure 스택 백업 데이터를 저장 하는 위치입니다. 사용자 고유의 데이터를 저장할이 폴더를 사용 하지 마십시오. OEM은 데이터를 저장 하기 백업 하거나이 폴더를 사용 해야 합니다. 

Oem 영역 폴더에서 해당 구성 요소에 대 한 백업 데이터를 저장 하는 것이 좋습니다. 각 네트워크 스위치, 하드웨어 수명 주기 호스트 (HLH) 등에 해당 하위 폴더에 저장 될 수 있습니다. 예: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>모니터링

다음 경고는 시스템에서 지원 됩니다.

| 경고                                                   | 설명                                                                                     | 재구성                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 파일 공유 용량 부족 하기 때문에 백업 하지 못했습니다. | 파일 공유 용량을 벗어나 백업 컨트롤러 위치에 백업 파일을 내보낼 수 없습니다. | 더 많은 저장 공간을 추가 하 고 백업을 다시 시도 하십시오. 공간을 확보 하십시오 (가장 오래 된에서 먼저 시작) 하는 기존 백업을 삭제 합니다.                    |
| 연결 문제로 인해 백업 하지 못했습니다.             | 네트워크 공유 Azure 스택 및 파일 간의 문제가 발생 합니다.                          | 네트워크 문제를 해결 하 고 백업을 다시 시도 하십시오.                                                                                            |
| 경로에 오류 때문에 백업 하지 못했습니다.                | 파일 공유 경로 확인할 수 없습니다.                                                          | 공유를 액세스할 수 있도록 다른 컴퓨터에서 공유를 매핑하십시오. 더 이상 유효한 경우 경로 업데이트 해야 할 수 없습니다.       |
| 인증 문제로 인해 백업 하지 못했습니다.               | 인증에 영향을 주는 네트워크 문제 또는 자격 증명에 문제가 있을 수 있습니다.    | 공유를 액세스할 수 있도록 다른 컴퓨터에서 공유를 매핑하십시오. 더 이상 유효한 경우 자격 증명을 업데이트 해야 할 수 없습니다. |
| 일반 오류 때문에 백업 하지 못했습니다.                    | 실패 한 요청은 한 일시적인 문제일 수 있습니다. 백업 다시 시도 합니다.                    | 지원 서비스에 문의                                                                                                                               |

## <a name="next-steps"></a>다음 단계

 - 검토에 대 한 참조 자료는 [인프라 백업 서비스](azure-stack-backup-reference.md)합니다.  
 - 사용 하도록 설정 된 [인프라 백업 서비스](azure-stack-backup-enable-backup-console.md)합니다.