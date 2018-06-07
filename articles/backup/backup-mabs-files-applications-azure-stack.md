---
title: Azure Stack 파일 및 응용 프로그램 백업 | Microsoft Docs
description: Azure Backup을 사용하여 Azure Stack 환경에 Azure Stack 파일과 응용 프로그램을 백업하고 복구합니다.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196715"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Azure Stack의 파일 및 응용 프로그램 백업
Azure Backup을 사용하여 Azure Stack의 파일 및 응용 프로그램을 보호(또는 백업)합니다. 파일 및 응용 프로그램을 백업하려면 Microsoft Azure Backup Server를 Azure Stack에서 실행되는 가상 머신으로 설치합니다. 동일한 가상 네트워크의 Azure Stack 서버에서 실행 중인 응용 프로그램을 보호할 수 있습니다. Azure Backup Server를 설치하면 Azure 디스크를 추가하여 단기 백업 데이터에 사용할 수 있는 로컬 저장소를 증가시킵니다. Azure Backup Server는 장기 보존을 위해 Azure Storage를 사용합니다.

> [!NOTE]
> Azure Backup Server 및 System Center DPM(Data Protection Manager)은 유사하지만 DPM은 Azure Stack에서 사용하도록 지원되지 않습니다.
>


## <a name="azure-backup-server-protection-matrix"></a>Azure Backup Server 보호 매트릭스
Azure Backup Server는 다음과 같은 Azure Stack 가상 머신 워크로드를 보호합니다.

| 보호된 데이터 원본 | 보호 및 복구 |
| --------------------- | ----------------------- |
| Windows Server 상하반기 채널 - Datacenter/Enterprise/Standard | 볼륨, 파일, 폴더 |
| Windows Server 2016 - Datacenter/Enterprise/Standard | 볼륨, 파일, 폴더 |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | 볼륨, 파일, 폴더 |
| Windows Server 2012 - Datacenter/Entprise/Standard | 볼륨, 파일, 폴더 |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | 볼륨, 파일, 폴더 |
| SQL Server 2016 | 데이터베이스 |
| SQL Server 2014 | 데이터베이스 |
| SQL Server 2012 SP1 | 데이터베이스 |
| SharePoint 2013 | 팜, 데이터베이스, 프런트 엔드, 웹 서버 |
| SharePoint 2010 | 팜, 데이터베이스, 프런트 엔드, 웹 서버 |


## <a name="install-azure-backup-server"></a>Azure Backup Server 설치
Azure Stack 가상 머신에 Azure Backup Server를 설치하려면 [Azure Backup Server를 사용하여 워크로드 백업 준비](backup-azure-microsoft-azure-backup.md) 아티클을 참조하세요. Azure Backup Server를 설치하고 구성하기 전에 다음을 고려해야 합니다.

### <a name="determining-size-of-virtual-machine"></a>가상 머신의 크기 결정
Azure Stack 가상 머신에서 Azure Backup Server를 실행하려면 A2 이상의 크기를 사용합니다. 가상 머신 크기를 선택하는 데 도움이 필요한 경우 [Azure Stack VM 크기 계산기](https://www.microsoft.com/download/details.aspx?id=56832)를 다운로드합니다.

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack 가상 머신의 가상 네트워크
Azure Stack 워크로드에서 사용되는 모든 가상 머신은 동일한 Azure 가상 네트워크 및 Azure 구독에 속해야 합니다.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>로컬 디스크 및 Azure에 백업 데이터 저장
Azure Backup Server는 작업 복구를 위해 가상 머신에 연결된 Azure 디스크에 백업 데이터를 저장합니다. 디스크 및 저장소 공간이 가상 머신에 연결되면 Azure Backup Server에서는 저장소를 관리합니다. 백업 데이터 저장소의 크기는 [Azure Stack 가상 머신](../azure-stack/user/azure-stack-storage-overview.md) 각각에 연결된 디스크의 크기와 수에 따라 달라집니다. 각 Azure Stack VM 크기에는 가상 머신에 연결할 수 있는 디스크의 최대 수가 포함됩니다. 예를 들어 A2는 4개의 디스크입니다. A3은 8개의 디스크입니다. A4는 16개의 디스크입니다. 또한 디스크 크기와 수는 총 백업 저장소 풀을 결정합니다.

> [!IMPORTANT]
> 5일 넘게 Azure Backup Server에 연결된 디스크에서 작업 복구(백업) 데이터를 유지**하지** 않아야 합니다.
>

Azure에서 백업 데이터를 저장하면 Azure Stack에서 백업 인프라를 줄일 수 있습니다. 데이터가 5일이 넘으면 Azure에 저장되어야 합니다.

Azure에서 백업 데이터를 저장하려면 Recovery Services 자격 증명 모음을 만들거나 사용합니다. Azure Backup Server 워크로드를 백업하도록 준비할 때 [Recovery Services 자격 증명 모음을 구성](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)합니다. 구성되면 백업 작업이 실행될 때마다 자격 증명 모음에서 복구 지점이 생성됩니다. 각 Recovery Services 자격 증명 모음은 최대 9999개의 복구 지점을 유지합니다. 만든 복구 지점의 수 및 유지되는 기간에 따라 여러 해 동안 백업 데이터를 유지할 수 있습니다. 예를 들어 매월 복구 지점을 만들고 5년 동안 유지할 수 있습니다.
 
### <a name="using-sql-server"></a>SQL Server 사용하기
Azure Backup Server 데이터베이스에 원격 SQL Server를 사용하려는 경우 SQL Server를 실행하는 Azure Stack VM만을 선택합니다.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM 성능
다른 가상 머신과 공유하는 경우 저장소 계정 크기 및 IOPS 제한으로 인해 Azure Backup Server 가상 머신 성능이 달라질 수 있습니다. 이러한 이유로 Azure Backup Server 가상 머신에 대한 별도 저장소 계정을 사용해야 합니다. Azure Backup Server에서 실행되는 Azure Backup 에이전트에는 다음을 위한 임시 저장소가 필요합니다.
    - 고유한 용도(캐시 위치)
    - 클라우드(로컬 준비 영역)에서 복원된 데이터
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure Backup 임시 디스크 저장소 구성
각 Azure Stack 가상 머신에는 `D:\` 볼륨으로 사용자에게 제공되는 임시 디스크 저장소가 제공됩니다. Azure Backup에 필요한 로컬 준비 영역은 `D:\`에 위치하도록 구성할 수 있습니다. 캐시 위치는 `C:\`에 배치할 수 있습니다. 이러한 방식으로 Azure Backup Server 가상 머신에 연결된 데이터 디스크에서 저장소가 떨어지지 않아도 됩니다.

### <a name="scaling-deployment"></a>배포 크기 조정
배포의 크기를 조정하려는 경우 다음 옵션을 사용할 수 있습니다.
  - 강화 - A 시리즈부터 D 시리즈까지 Azure Backup Server 가상 머신의 크기를 늘리고, [Azure Stack 가상 머신 지침당](../azure-stack/user/azure-stack-manage-vm-disks.md) 로컬 저장소를 늘립니다.
  - 데이터 오프로드 - Azure Backup Server에 이전 데이터를 전송하고, Azure Backup Server에 연결된 저장소에 최신 데이터만 유지합니다.
  - 규모 확장 - Azure Backup Server를 더 추가하여 워크로드를 보호합니다.

## <a name="see-also"></a>참고 항목
Azure Backup Server를 사용하여 다른 워크로드를 보호하는 방법에 대한 내용은 다음 아티클 중 하나를 참조하세요.
- [SharePoint 팜 백업](backup-azure-backup-sharepoint-mabs.md)
- [SQL Server 백업](backup-azure-sql-mabs.md)
