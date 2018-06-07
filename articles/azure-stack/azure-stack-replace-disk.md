---
title: Azure 스택에서 실제 디스크를 교체 | Microsoft Docs
description: Azure 스택의 실제 디스크를 교체 하는 방법에 대 한 프로세스를 간략하게 설명 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: f168c005c729ae75a5369b80b3dc5eab03ee0243
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Azure 스택에서 실제 디스크 교체

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이 문서에서는 Azure 스택의 실제 디스크를 교체 하려면 일반적인 프로세스를 설명 합니다. 실제 디스크에 오류가 발생 하는 경우 가능한 한 빨리 바꿀 해야 있습니다.

통합된 시스템에 대 한 및 개발 키트를 갖는 배포는 핫 스왑 가능 디스크에 대해이 절차를 사용할 수 있습니다.

실제 디스크 교체 단계는 달라질 수 (oem) 하드웨어 공급 업체에 따라. 시스템에만 적용 되는 자세한 단계에 대 한 공급 업체의 필드 (FRU) 교체 장치 설명서를 참조 하십시오. 

## <a name="review-disk-alert-information"></a>디스크 경고 정보를 검토 합니다.
디스크 오류가 발생할 때 실제 디스크에 연결이 끊어졌습니다 알려주는 경고가 나타납니다. 

 ![실제 디스크에 표시 된 경고 연결 손실](media/azure-stack-replace-disk/DiskAlert.png)

경고를 열면 경고 설명 배율 단위 노드 및 대체 해야 하는 디스크에 대 한 정확한 실제 슬롯 위치를 포함 합니다. Azure 스택 추가 사용 하면 LED 표시기 기능을 사용 하 여 실패 한 디스크를 식별할 수 있습니다.

 ## <a name="replace-the-disk"></a>디스크를 교체

실제 디스크 교체에 대 한 OEM 하드웨어 공급 업체의 FRU 지침을 따릅니다.

> [!note]
> 한 번에 하나의 배율 단위 노드에 대 한 디스크를 대체 합니다. 다음 눈금 단위 노드로 이동 하기 전에 완료 하려면 가상 디스크 복구 작업에 대 한 대기

통합된 시스템에서 지원 되지 않는 디스크의 사용을 방지 하려면 시스템 공급 업체에서 지원 되지 않는 디스크를 차단 합니다. 지원 되지 않는 디스크를 사용 하려고 하면 새 경고가 지원 되지 않는 모델 또는 펌웨어 때문에 격리 된 디스크에 알려 줍니다.

디스크를 교체한 후 Azure 스택 자동으로 새 디스크를 검색 하이 가상 디스크 복구 프로세스를 시작 합니다.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>가상 디스크 복구의 상태 확인
 
 디스크를 교체한 후 가상 디스크 상태를 모니터링 하 고 권한 있는 끝점을 사용 하 여 작업 진행 상황을 복구 수 있습니다. 권한 있는 끝점에 대 한 네트워크 연결 되어 있는 모든 컴퓨터에서 다음이 단계를 수행 합니다.

1. Windows PowerShell 세션을 열고 권한 있는 끝점에 연결 합니다.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. 가상 디스크 상태를 보려면 다음 명령을 실행 합니다.
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Powershell 명령의 출력을 Get-virtualdisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. 현재 저장소 작업 상태를 보려면 다음 명령을 실행 합니다.
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Powershell 명령의 출력을 Get-storagejob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>가상 디스크 복구 문제 해결

가상 디스크를 복구 하는 경우 작업 작업을 다시 시작 하려면 다음 명령을 실행 걸린, 나타납니다.
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
