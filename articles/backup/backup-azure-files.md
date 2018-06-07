---
title: Azure Files을 Azure로 백업
description: 이 문서에서는 Azure 파일 공유를 백업 및 복원하는 방법과 관리 작업에 대해 설명합니다.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 3/23/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 440cee4309fad0a22d8964982f3aad2178397124
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-azure-file-shares"></a>Azure 파일 공유 백업
이 문서에서는 Azure Portal을 사용하여 [Azure 파일 공유](../storage/files/storage-files-introduction.md)를 백업 및 복원하는 방법을 설명합니다.

이 가이드에서는 다음 작업 방법을 배웁니다.
> [!div class="checklist"]
> * Azure 파일을 백업하도록 Recovery Services 자격 증명 모음 구성
> * 주문형 백업 작업을 실행하여 복원 지점 만들기
> * 복원 지점에서 파일 복원
> * Backup 작업 관리
> * Azure 파일에 대한 보호 중지
> * 백업 데이터 삭제

## <a name="prerequisites"></a>필수 조건
Azure 파일 공유를 백업하려면 파일 공유가 [지원되는 저장소 계정 유형](troubleshoot-azure-files.md#preview-boundaries) 중 하나에 있는지 확인합니다. 이를 확인한 후에는 파일 공유를 보호할 수 있습니다.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Azure 파일 공유 백업 미리 보기의 제한 사항
Azure 파일 공유를 위한 백업은 미리 보기에 있습니다. 미리 보기인 동안에는 다음과 같은 제한이 있습니다.
- 저장소 계정에서 [ZRS(영역 중복 저장소)](../storage/common/storage-redundancy-zrs.md) 또는 [RA-GRS(읽기 액세스 지역 중복 저장소)](../storage/common/storage-redundancy-grs.md) 복제를 사용하여 Azure 파일 공유를 보호할 수 없습니다.
- Virtual Networks를 사용하도록 설정된 저장소 계정에서 Azure 파일 공유를 보호할 수 없습니다.
- Azure Files 보호에 PowerShell 또는 CLI를 사용할 수 없습니다.
- 일별 최대 예약 백업의 수는 1개입니다.
- 일별 최대 주문형 백업의 수는 4개입니다.
- 저장소 계정에서 [리소스 잠금](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)을 사용하면 Recovery Services 자격 증명 모음에서 Backup이 실수로 삭제되는 것을 방지할 수 있습니다.
- Azure Backup으로 생성된 스냅숏은 삭제하지 마십시오. 스냅숏을 삭제하면 복구 지점이 손실되거나 복원이 실패할 수 있습니다. 

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure 파일 공유를 위한 백업 구성
모든 백업 데이터는 Recovery Services 자격 증명 모음에 저장됩니다. 이 자습서에서는 이미 Azure 파일 공유를 설정한 것으로 가정합니다. Azure 파일 공유를 백업하려면:

1. 파일 공유와 동일한 지역에 Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음이 이미 있는 경우 자격 증명 모음의 개요 페이지를 열고 **Backup**을 클릭합니다.

    ![Backup 목표로 Azure 파일 공유 선택](./media/backup-file-shares/overview-backup-page.png)

2. [Backup 목표] 메뉴의 **무엇을 백업하시겠습니까?**에서 Azure 파일 공유를 선택합니다.

    ![Backup 목표로 Azure 파일 공유 선택](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. **Backup**을 클릭하여 Recovery Services 자격 증명 모음에 대한 Azure 파일 공유를 구성합니다. 

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/set-backup-goal.png)

    자격 증명 모음이 Azure 파일 공유와 연결되면 Backup 메뉴가 열리고 저장소 계정을 선택하라는 메시지가 표시됩니다. 메뉴에는 개발자의 자격 증명 모음이 있는 지역에서 아직 Recovery Services 자격 증명 모음과 연결되지 않은 모든 지원되는 저장소 계정이 표시됩니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/list-of-storage-accounts.png)

4. 저장소 계정 목록에서 계정을 선택하고 **확인**을 클릭합니다. Azure는 저장소 계정에서 백업 가능한 파일 공유를 검색합니다. 최근에 파일 공유를 추가했는데 목록에 보이지 않으면 파일 공유가 표시될 때까지 잠시 기다립니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/discover-file-shares.png)

5. **파일 공유** 목록에서 백업하려는 파일 공유를 하나 이상 선택하고 **확인**을 클릭합니다.

6. 파일 공유를 선택하면 Backup 메뉴가 **Backup 정책**으로 전환됩니다. 이 메뉴에서 기존 백업 정책을 선택하거나 새 백업 정책을 만든 후 **Backup 사용**을 클릭합니다. 

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/apply-backup-policy.png)

    백업 정책을 설정한 후에는 예정된 시간에 파일 공유의 스냅숏이 생성되고, 선택한 기간 동안 복구 지점이 유지됩니다.

## <a name="create-an-on-demand-backup"></a>주문형 백업 만들기
가끔 백업 정책에 예약된 시간을 벗어나서 백업 스냅숏 또는 복구 지점을 생성하려는 경우가 있습니다. 주문형 백업을 생성하는 일반적인 시간은 백업 정책을 구성한 직후입니다. 백업 정책의 일정에 따라 스냅숏이 만들어질 때까지 몇 시간 또는 며칠이 걸릴 수 있습니다. 백업 정책이 적용될 때까지 데이터를 보호하려면 주문형 백업을 시작합니다. 파일 공유에 대한 계획된 변경 작업을 수행하기 전에 주문형 백업을 만들어야 하는 경우가 종종 있습니다. 

### <a name="to-create-an-on-demand-backup"></a>주문형 백업을 만들려면

1. 파일 공유 복구 지점이 포함된 Recovery Services 자격 증명 모음을 열고 **Backup 항목**을 클릭합니다. Backup 항목 형식 목록이 표시됩니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/list-of-backup-items.png)

2. 목록에서 **Azure Storage(Azure Files)**를 선택합니다. Azure 파일 공유 목록이 나타납니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Azure 파일 공유 목록에서 원하는 파일 공유를 선택합니다. 선택한 파일 공유에 대한 Backup 항목 메뉴가 열립니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/backup-item-menu.png)

4. Backup 항목 메뉴에서 **지금 백업**을 클릭합니다. 이것은 주문형 백업 작업이기 때문에 복구 지점과 연결된 보존 정책이 없습니다. **지금 백업** 대화 상자가 열립니다. 복구 지점을 보존할 마지막 날을 지정합니다. 
  
   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Azure 파일 공유의 백업에서 복원
복원 지점에서 전체 파일 공유나 개별 파일 또는 폴더를 복원해야 하는 경우 이전 섹션에서 자세히 설명한 것처럼 Backup 항목으로 이동합니다. **공유 복원**을 선택하여 원하는 지점에서 전체 파일 공유를 복원합니다. 표시되는 복원 지점 목록에서, 현재 파일 공유를 덮어쓸 수 있거나 동일한 지역에 있는 대체 파일 공유로 복원할 수 있는 복원 지점을 선택합니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Azure 파일 공유의 백업에서 개별 파일 또는 폴더 복원
Azure Backup은 Azure Portal 내에서 복원 지점을 찾아보는 기능을 제공합니다. 원하는 파일 또는 폴더를 복원하려면 [Backup 항목] 페이지에서 [파일 복구]를 클릭하고 복원 지점 목록에서 선택합니다. [복구 대상]을 선택한 다음, **파일 선택**을 클릭하여 복원 지점을 찾아봅니다. 원하는 파일 또는 폴더를 선택하고 **복원**을 선택합니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Azure 파일 공유 백업 관리

**Backup 작업** 페이지에서 다음을 포함하여 파일 공유 백업에 대한 여러 가지 관리 작업을 수행할 수 있습니다.
- [작업 모니터링](backup-azure-files.md#monitor-jobs)
- [새 정책 만들기](backup-azure-files.md#create-a-new-policy)
- [파일 공유에 대한 보호 중지](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [파일 공유에 대한 보호 다시 시작](backup-azure-files.md#resume-protection-for-azure-file-share)
- [백업 데이터 삭제](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>작업 모니터링

**Backup 작업** 페이지에서 모든 작업의 진행률을 모니터링할 수 있습니다.

**Backup 작업** 페이지를 열려면:

- 모니터링할 Recovery Services 자격 증명 모음을 열고, Recovery Services 자격 증명 모음 메뉴에서 **작업**을 클릭하고, **Backup 작업**을 클릭합니다.
   ![모니터링할 작업 선택](./media/backup-file-shares/open-backup-jobs.png)

    Backup 작업 목록과 이러한 작업의 상태가 표시됩니다.
   ![모니터링할 작업 선택](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>새 정책 만들기

Recovery Services 자격 증명 모음의 **Backup 정책**에서 Azure 파일 공유를 백업하는 새 정책을 만들 수 있습니다. 파일 공유에 대한 Backup을 구성할 때 만든 모든 정책은 정책 유형이 Azure 파일 공유로 표시됩니다.

기존 Backup 정책을 보려면:

- 원하는 Recovery Services 자격 증명 모음을 열고, Recovery Services 자격 증명 모음 메뉴에서 **Backup 정책**을 클릭합니다. 모든 Backup 정책이 나열됩니다.

   ![모니터링할 작업 선택](./media/backup-file-shares/list-of-backup-policies.png)

새 Backup 정책을 만들려면:

1. Recovery Services 자격 증명 모음 메뉴에서 **Backup 정책**을 클릭합니다.
2. Backup 정책 목록에서 **추가**를 클릭합니다.

   ![모니터링할 작업 선택](./media/backup-file-shares/new-backup-policy.png)

3. **추가** 메뉴에서 **Azure 파일 공유**를 선택합니다. Azure 파일 공유에 대한 Backup 정책 메뉴가 열립니다. 정책 이름, 백업 빈도, 복구 지점에 대한 보존 범위를 입력합니다. 정책을 정의한 후 [확인]을 클릭합니다.

   ![모니터링할 작업 선택](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Azure 파일 공유 보호 중지

Azure 파일 공유 보호를 중지하기로 선택하면 복구 지점을 보존할 것인지 묻는 메시지가 표시됩니다. Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

- 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제 또는
- 미래의 모든 백업 작업을 중지하고 복구 지점을 유지

복구 지점을 유지할 경우 Azure Backup에서 만드는 기본 스냅숏이 보관되므로 비용이 발생할 수 있습니다. 그러나 복구 지점을 유지할 경우 나중에 원할 때 파일 공유를 복원할 수 있다는 장점이 있습니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 가격 책정 세부 정보를 참조하세요. 모든 복구 지점을 삭제하기로 선택하면 파일 공유를 복원할 수 없습니다.

Azure 파일 공유 보호를 중지하려면:

1. 파일 공유 복구 지점이 포함된 Recovery Services 자격 증명 모음을 열고 **Backup 항목**을 클릭합니다. Backup 항목 형식 목록이 표시됩니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/list-of-backup-items.png) 

2. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)**를 선택합니다. (Azure Storage(Azure Files))에 대한 Backup 항목 목록이 표시됩니다.

   ![추가 메뉴를 열려면 항목 클릭](./media/backup-file-shares/azure-file-share-backup-items.png) 

3. Backup 항목(Azure Storage(Azure Files)) 목록에서 중지하려는 백업 항목을 선택합니다.

4. Azure 파일 공유 항목에서 **더 보기** 메뉴를 클릭하고 **Backup 중지**를 선택합니다. 

   ![추가 메뉴를 열려면 항목 클릭](./media/backup-file-shares/stop-backup.png)

5. [Backup 중지] 메뉴에서 **보존** 또는 **Backup 데이터 삭제**를 선택하고 **Backup 중지**를 클릭합니다.

   ![추가 메뉴를 열려면 항목 클릭](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Azure 파일 공유 보호 다시 시작

파일 공유 보호가 중지되었을 때 [Backup 데이터 보관] 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. **Backup 데이터 삭제** 옵션을 선택한 경우 파일 공유 보호를 다시 시작할 수 없습니다.

파일 공유 보호를 다시 시작하려면 Backup 항목으로 이동하여 [Backup 다시 시작]을 클릭합니다. Backup 정책이 열리고 원하는 정책을 선택하여 백업을 다시 시작할 수 있습니다.

   ![모니터링할 작업 선택](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Backup 데이터 삭제 

백업 중지 작업이 진행되는 동안 또는 보호를 중지한 후 언제든지 파일 공유 백업을 삭제할 수 있습니다. 복구 지점을 삭제하기 전에 며칠 또는 몇 주 기다리는 것이 도움이 될 수도 있습니다. 백업 데이터를 삭제하는 경우 복구 지점을 복원하는 작업과 달리 특정 복구 지점을 삭제하도록 선택할 수 없습니다. 백업 데이터를 삭제하려는 경우 항목에 연결된 모든 복구 지점을 삭제합니다.

다음 절차에서는 가상 머신에 대한 Backup 작업이 중지된 것으로 가정합니다. Backup 작업이 중지된 경우 Backup 항목 대시보드에서 [백업 다시 시작] 및 [Backup 데이터 삭제] 옵션을 다시 시작할 수 있습니다. [Backup 데이터 삭제]를 클릭하고 파일 공유 이름을 입력하여 삭제를 확인합니다. 필요에 따라 삭제 이유 또는 주석을 입력합니다.

## <a name="see-also"></a>참고 항목
Azure 파일 공유에 대한 자세한 내용은 다음을 참조하세요.
- [Azure 파일 공유 백업에 대한 FAQ](backup-azure-files-faq.md)
- [Azure 파일 공유 백업 문제 해결](troubleshoot-azure-files.md)
