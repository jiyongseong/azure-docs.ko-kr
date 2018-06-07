---
title: DPM을 사용하여 Azure Portal에 워크로드 백업 | Microsoft Docs
description: Azure Backup 서비스를 사용하여 DPM 서버를 백업하는 방법 소개
services: backup
documentationcenter: ''
author: adigan
manager: nkolli
editor: ''
keywords: System Center Data Protection Manager, 데이터 보호 관리자, dpm 백업
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: 05917705264965afda873fb32a28b70e4da87aa3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>DPM을 통해 Azure에서 워크로드 백업 준비
> [!div class="op_single_selector"]
> * [Azure Backup 서버](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

이 문서에서는 Microsoft Azure Backup을 사용하여 System Center Data Protection Manager(DPM) 서버와 워크로드를 보호하는 방법을 소개합니다. 이 문서를 읽어 보면 다음을 이해하게 됩니다.

* Azure DPM 서버 백업 작동 방식
* 원활한 백업 경험을 위한 필수 조건
* 발생하는 일반적인 오류 및 오류를 처리하는 방법
* 지원되는 시나리오

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 이 문서에서는 리소스 관리자 모델을 사용하여 배포된 VM을 복원하기 위한 정보 및 절차를 제공합니다.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview)는 파일 및 응용 프로그램 데이터를 백업합니다. 지원되는 워크로드에 관한 자세한 내용은 [여기](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix)에서 찾을 수 있습니다. DPM에 백업된 데이터는 테이프나 디스크에 저장하거나 Microsoft Azure Backup을 사용하여 Azure에 백업할 수 있습니다. DPM 은 Azure Backup과 다음과 같이 상호작용합니다.

* **물리적 서버 또는 온-프레미스 가상 머신으로 배포하는 DPM** — DPM을 물리적 서버 또는 온-프레미스 Hyper-V 가상 머신으로 배포하는 경우, 디스크나 테이프 백업에 더해 데이터를 Recovery Services 자격 증명 모음에 백업할 수 있습니다.
* **Azure 가상 머신으로 배포하는 DPM** — System Center 2012 R2 업데이트 3부터 Azure 가상 머신에 DPM을 배포할 수 있습니다. DPM을 Azure 가상 머신으로 배포하는 경우, VM에 연결된 Azure 디스크에 데이터를 백업하거나 Recovery Services 자격 증명 모음에 백업하여 데이터 저장소를 오프로드할 수 있습니다.

## <a name="why-back-up-dpm-to-azure"></a>DPM을 Azure에 백업하는 이유?
DPM 서버를 Azure에 백업하는 경우 업무상 이점은 다음과 같습니다.

* 온-프레미스 DPM 배포의 경우, 테이프에 대한 장기 배포하는 대신 Azure를 사용할 수 있습니다.
* Azure의 VM에 DPM을 배포하려면 Azure 디스크에서 저장소를 오프로드합니다. 오래된 데이터를 Recovery Services 자격 증명 모음에 저장하면 새로운 데이터를 디스크에 저장하여 비즈니스를 강화할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
DPM 데이터를 백업하기 위해 다음과 같이 Azure Backup을 준비합니다.

1. **Recovery Services 자격 증명 모음 만들기** - Azure Portal에 자격 증명 모음을 만듭니다.
2. **자격 증명 모음 자격 증명 다운로드** - DPM 서버를 Recovery Services 자격 증명 모음에 등록하는 데 사용하는 자격 증명을 다운로드합니다.
3. **Azure Backup 에이전트 설치** - 각 DPM 서버에 에이전트를 설치합니다.
4. **서버 등록** - DPM 서버를 Recovery Services 자격 증명 모음에 등록합니다.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>주요 정의
DPM용 Azure에서 백업에 대한 몇 가지 주요 정의는 다음과 같습니다.

1. **자격 증명 모음** — 자격 증명 모음은 Azure Backup 서비스의 식별된 자격 증명 모음에 백업 데이터를 보내도록 컴퓨터를 인증하는 데 필요합니다. 자격 증명 모음에서 다운로드할 수 있고 48시간 동안 유효합니다.
2. **암호** - 암호는 클라우드에 대한 백업을 암호화하는 데 사용됩니다. 이 파일은 복구 작업 중에 필요하므로 안전한 위치에 저장하십시오.
3. **보안 PIN** - 자격 증명 모음의 [보안 설정](https://docs.microsoft.com/azure/backup/backup-azure-security-feature)을 사용하는 경우 보안 PIN은 중요한 백업 작업을 수행하는 데 필요합니다. Multi-Factor Authentication은 다른 보안 계층을 추가합니다. 
4. **복구 폴더** - 클라우드의 백업이 클라우드 복구 중에 일시적으로 다운로드되는 구입니다. 해당 크기는 병렬로 복구하려는 백업 항목의 크기와 동일해야 합니다.


### <a name="1-create-a-recovery-services-vault"></a>1. 복구 서비스 자격 증명 모음 만들기
복구 서비스 자격 증명 모음을 만들려면:

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 허브 메뉴에서 **찾아보기**를 클릭하고 리소스 목록에서 **Recovery Services**를 입력합니다. 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **Recovery Services 자격 증명 모음**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    Recovery Services 자격 증명 모음의 목록이 표시됩니다.
3. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    Recovery Services 자격 증명 모음 메뉴가 열리고 **이름**, **구독**, **리소스 그룹** 및 **위치**를 입력하라는 메시지가 표시됩니다.

    ![Recovery Services 자격 증명 모음 만들기 5단계](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 대해 고유해야 합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
5. **구독** 을 클릭하여 사용 가능한 구독 목록을 볼 수 있습니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 조직 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
6. **리소스 그룹**을 클릭하여 사용 가능한 리소스 그룹 목록을 표시하거나 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.
7. **위치** 를 클릭하여 자격 증명 모음에 대한 지리적 지역을 선택합니다.
8. **만들기**를 클릭합니다. Recovery Services 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다.
   사용자 자격 증명 모음을 만들면 포털에서 열립니다.

### <a name="set-storage-replication"></a>저장소 복제 설정
저장소 복제 옵션을 사용하면 지역 중복 저장소와 로컬 중복 저장소 중에서 선택할 수 있습니다. 기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다. 기본 백업인 경우 지역 중복 저장소 옵션이 설정된 상태로 둡니다. 오래 지속되지 않는 저렴한 옵션을 원하는 경우에는 로컬 중복 저장소를 선택합니다. [지역 중복](../storage/common/storage-redundancy-grs.md) 및 [로컬 중복](../storage/common/storage-redundancy-lrs.md) 저장소 옵션에 대한 자세한 내용은 [Azure Storage 복제 개요](../storage/common/storage-redundancy.md)를 참조하세요.

저장소 복제 설정을 편집하려면

1. 자격 증명 모음 대시보드 및 설정 메뉴를 열 자격 증명 모음을 선택합니다. **설정** 메뉴가 열리지 않을 경우 자격 증명 모음 대시보드에서 **모든 설정**을 클릭합니다.
2. **설정** 메뉴에서 **Backup 인프라** > **Backup 구성**을 클릭하여 **Backup 구성** 메뉴를 엽니다. **백업 구성** 메뉴에서 자격 증명 모음에 대한 저장소 복제 옵션을 선택합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    자격 증명 모음에 대한 저장소 옵션을 선택하면 자격 증명 모음이 있는 VM에 연결할 준비가 됩니다. 연결을 시작하려면 Azure 가상 머신을 검색하고 등록해야 합니다.

### <a name="2-download-vault-credentials"></a>2. 저장소 자격 증명 다운로드
자격 증명 모음 자격 증명 파일은 포털에서 각 백업 자격 증명 모음에 대해 생성하는 인증서입니다. 그런 다음 포털은 Access Control Service(ACS)에 공개 키를 업로드합니다. 컴퓨터 등록 워크플로에서 입력으로 제공된 워크플로의 일부로 사용자에게 인증서의 개인 키가 제공됩니다. 이는 Azure Backup 서비스의 식별된 자격 증명 모음에 백업 데이터를 보내도록 컴퓨터를 인증합니다.

자격 증명 모음 자격 증명은 등록 워크플로 중에만 사용됩니다. 자격 증명 모음 자격 증명 파일이 손상되지 않도록 하는 것은 사용자의 책임입니다. 악의적인 사용자의 손에 들어갈 경우 자격 증명 모음 자격 증명 파일을 사용하여 동일한 자격 증명 모음에 다른 컴퓨터를 등록할 수 있습니다. 그러나 백업 데이터는 고객이 소유한 암호를 사용 하여 암호화되므로 기존 백업 데이터는 손상되지 않습니다. 이 문제를 완화하려면 보관 자격 증명을 48시간에 만료되도록 설정합니다. 복구 서비스의 보관 자격 증명은 여러 번 다운로드할 수 있지만 등록 워크플로 중에는 최신 보관 자격 증명 파일만 적용할 수 있습니다.

자격 증명 모음 자격 증명 파일은 Azure 포털에서 보안 채널을 통해 다운로드됩니다. Azure Backup 서비스는 인증서의 개인 키를 인식하지 않으며 개인 키는 포털 또는 서비스에 보관되지 않습니다. 로컬 컴퓨터에 자격 증명 모음 자격 증명 파일을 다운로드하려면 다음 단계를 따르십시오.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. DPM 컴퓨터를 등록할 Recovery Services 자격 증명 모음을 엽니다.
3. 설정 메뉴가 기본적으로 열립니다. 닫혀있으면, 자격 증명 모음 대시보드의 **설정**을 클릭하고 설정 메뉴를 엽니다. 설정 메뉴에서 **속성**을 클릭합니다.

    ![자격 증명 모음 메뉴 열기](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. [속성] 페이지에서 **Backup 자격 증명** 아래쪽에 있는 **다운로드**를 클릭합니다. 포털에서 다운로드할 수 있는 자격 증명 모음 자격 증명 파일을 생성합니다.

    ![다운로드](./media/backup-azure-dpm-introduction/vault-credentials.png)

포털에서는 저장소 이름과 현재 날짜를 조합하여 저자격 증명 모음 자격 증명을 생성합니다. **저장** 을 클릭하여 자격 증명 모음 자격 증명을 로컬 계정의 다운로드 폴더로 다운로드하거나, 저장 메뉴에서 다른 이름으로 저장을 선택하여 자격 증명 모음 자격 증명을 저장할 위치를 지정합니다. 파일이 생성되기 까지 최대 1분이 소요됩니다.

### <a name="note"></a>참고
* 보관 자격 증명 파일이 컴퓨터에서 액세스할 수 있는 위치에 저장되었는지 확인하세요. 파일 공유/SMB에 저장된 경우 액세스 권한을 확인합니다.
* 자격 증명 모음 자격 증명 파일은 등록 워크플로 중에만 사용됩니다.
* 자격 증명 모음 자격 증명 파일은 48시간 후에 만료되고, 만료된 후에는 포털에서 다운로드할 수 있습니다.

### <a name="3-install-backup-agent"></a>3. Backup 에이전트 설치
Azure Backup 자격 증명 모음을 만든 후에는 각 Windows 컴퓨터(Windows Server, Windows 클라이언트, 시스템 센터, Data Protection Manager 서버 또는 Azure Backup 서버 컴퓨터)에 Azure에 데이터 및 응용 프로그램을 백업할 수 있게 해주는 에이전트가 설치되어야 합니다.

1. DPM 컴퓨터를 등록할 Recovery Services 자격 증명 모음을 엽니다.
2. 설정 메뉴가 기본적으로 열립니다. 닫혀있으면, **설정**을 클릭하고 설정 메뉴를 엽니다. 설정 메뉴에서 **속성**을 클릭합니다.

    ![자격 증명 모음 메뉴 열기](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. [설정] 페이지에서 **Azure Backup Agent** 아래쪽에 있는 **다운로드**를 클릭합니다.

    ![다운로드](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   에이전트가 다운로드되면 MARSAgentInstaller.exe를 실행하여 Azure Backup 에이전트를 설치하기 시작합니다. 설치 폴더 및 에이전트에 필요한 스크래치 폴더를 선택합니다. 지정된 캐시 위치에 백업 데이터의 5% 이상이 되는 여유 공간이 있어야 합니다.
4. 프록시 서버를 사용하여 인터넷에 연결하는 경우 **프록시 구성** 화면에서 프록시 서버 세부 정보를 입력합니다. 인증된 프록시를 사용하는 경우 이 화면에 사용자 이름 및 암호 세부 정보를 입력합니다.
5. Azure Backup 에이전트가 .NET Framework 4.5 및 Windows PowerShell(아직 설치되지 않은 경우)을 설치하여 설치를 완료합니다.
6. 에이전트가 설치되면 창을 **닫습니다** .

   ![닫습니다](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. 자격 증명 모음에 **DPM 서버를 등록**하려면 **관리** 탭에서 **온라인**을 클릭합니다. 그런 다음 **등록**을 선택합니다. 설치 프로그램 등록 마법사가 열립니다.
8. 프록시 서버를 사용하여 인터넷에 연결하는 경우 **프록시 구성** 화면에서 프록시 서버 세부 정보를 입력합니다. 인증된 프록시를 사용하는 경우 이 화면에 사용자 이름 및 암호 세부 정보를 입력합니다.

    ![프록시 구성](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. 자격 증명 모음 자격 증명 화면에서 이전에 다운로드한 자격 증명 모음 자격 증명 파일로 이동하여 선택합니다.

    ![자격 증명 모음 자격 증명](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    자격 증명 모음 자격 증명 파일은 48시간 동안만 유효합니다(그 이후에는 포털에서 다운로드). 이 화면에서 오류가 발생할 경우(예: "제공한 자격 증명 모음 자격 증명 파일이 만료되었습니다") Azure 포털에 로그인하고 자격 증명 모음 자격 증명 파일을 다시 다운로드합니다.

    설치 응용 프로그램에서 액세스할 수 있는 위치에 있는 자격 증명 모음 자격 증명 파일인지 확인합니다. 액세스 관련 오류가 발생할 경우 이 컴퓨터의 임시 위치에 자격 증명 모음 자격 증명 파일을 복사하고 작업을 다시 시도합니다.

    잘못된 자격 증명 모음 자격 증명 오류(예: "잘못된 자격 증명 모음 자격 증명을 제공했습니다.")가 발생한 경우 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. 포털에서 새 자격 증명 모음 자격 증명 파일을 다운로드한 후에 작업을 다시 시도합니다. 이 오류는 일반적으로 사용자가 Azure 포털에서 **자격 증명 모음 자격 증명 다운로드** 옵션을 빠르게 연속적으로 클릭할 경우에 표시됩니다. 이 경우 두 번째 자격 증명 모음 자격 증명 파일만 유효합니다.
10. 근무 시간 및 휴무 시간 동안 네트워크 대역폭의 사용량을 제어하려면 **제한 설정** 화면에서 대역폭 사용 제한을 설정하고 근무 시간 및 휴무 시간을 정의하면 됩니다.

    ![제한 설정](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. **복구 폴더 설정** 화면에서 Azure에서 다운로드한 파일을 일시적으로 스테이징할 폴더를 찾아봅니다.

    ![복구 폴더 설정](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. **암호화 설정** 화면에서 전달 구를 생성하거나 제공합니다(최소 16자). 암호를 안전한 위치에 저장해야 합니다.

    ![암호화](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > 암호를 분실하거나 잊어버린 경우 Microsoft는 백업 데이터 복구를 도와드릴 수 없습니다. 암호화 암호는 최종 사용자가 소유하며 Microsoft는 최종 사용자가 사용하는 암호를 전혀 볼 수 없습니다. 이 파일은 복구 작업에 필요하므로 안전한 위치에 저장하십시오.
    >
    >
13. **등록** 단추를 클릭하면 컴퓨터가 자격 증명 모음에 성공적으로 등록되고 이제 Microsoft Azure에 백업을 시작할 준비가 완료된 것입니다.
14. Data Protection Manager를 사용하는 경우 **구성** 옵션을 클릭하고 **관리** 탭 아래쪽에 있는 **온라인**을 선택하여 등록 워크플로 중에 지정된 설정을 수정할 수 있습니다.

## <a name="requirements-and-limitations"></a>요구 사항(및 제한 사항)
* DPM은 물리적 서버 또는 System Center 2012 SP1 또는 System Center 2012 R2에 설치된 Hyper-V 가상 머신으로 실행할 수 있습니다. 최소 System Center 2012 R2 DPM 2012 R2 업데이트 롤업 3에서 실행되는 Azure 가상 컴퓨터 또는 최소 System Center 2012 R2 업데이트 롤업 5에서 실행되는 VMWare의 Windows 가상 컴퓨터로도 실행할 수 있습니다.
* DPM을 System Center 2012 SP1에서 실행 중인 경우,  System Center Data Protection Manager SP1용 업데이트 롤업 2를 설치해야 합니다. Azure Backup 에이전트를 설치하기 전에 필수입니다.
* DPM 서버는 Windows PowerShell 및 .Net Framework 4.5가 설치되어 있어야 합니다.
* DPM은 대부분의 워크로드를 Azure Backup에 백업할 수 있습니다. 지원되는 전체 목록은 아래의 Azure Backup 지원 항목을 참조하십시오.
* Azure Backup에 저장된 데이터는 “테이프에 복사” 옵션으로 복구할 수 없습니다.
* Azure Backup 기능을 사용할 수 있는 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. [Azure Backup 가격 정책](https://azure.microsoft.com/pricing/details/backup/)을 읽어보십시오.
* Azure Backup을 사용하려면 백업하고자 하는 서버에 Azure Backup 에이전트를 설치해야 합니다. 각 서버에서는 백업 중인 데이터 크기의 5% 이상을 로컬 저장소로 사용할 수 있어야 합니다. 예를 들어 100GB 데이터를 백업하는 경우 스크래치 위치에 최소 5GB의 여유 공간이 필요합니다.
* 데이터는 Azure 자격 증명 모음 저장소에 저장됩니다. Azure Backup 자격 증명 모음에 백업할 수 있는 데이터의 양에는 제한이 없지만 데이터 원본(예를 들면 가상 머신 또는 데이터베이스)의 크기는 54400GB를 초과해서는 안 됩니다.

다음 파일 형식은 Azure에 대한 백업을 지원합니다.

* 암호화(전체 백업에만 해당)
* 압축(증분 백업 지원)
* 스파스(증분 백업 지원)
* 압축 및 스파스(스파스로 처리됨)

다음은 지원하지 않습니다.

* 대/소문자 구분 파일 시스템의 서버는 지원하지 않습니다.
* 하드 링크(건너뜀)
* 재분석 지점(건너뜀)
* 암호화 및 압축(건너뜀)
* 암호화 및 스파스(건너뜀)
* 압축된 스트림
* 스파스 스트림

> [!NOTE]
> System Center 2012 DPM SP1부터는 DPM으로 보호되는 워크로드를 Microsoft Azure Backup을 사용하여 Azure에 백업할 수 있습니다.
>
>
