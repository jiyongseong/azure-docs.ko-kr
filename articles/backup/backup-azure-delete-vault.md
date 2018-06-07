---
title: " Azure에서 Recovery Services 자격 증명 모음 삭제 | Microsoft Docs "
description: "이 문서는 Recovery Services 자격 증명 모음을 삭제하는 방법을 설명합니다. 문서에는 자격 증명 모음을 삭제하려고 하나 할 수 없는 경우의 문제 해결 단계가 포함되어 있습니다."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 4f4a92159b01b197984130c15195419e1b166fd3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제
이 문서는 Azure Portal에서 Recovery Services 자격 증명 모음을 삭제하는 방법을 설명합니다. 백업 자격 증명 모음이 있는 경우 Recovery Services 자격 증명 모음으로 변환되었을 것입니다.   

Recovery Services 자격 증명 모음을 삭제하는 것은 1단계 프로세스입니다. - *자격 증명 모음에 어떤 리소스도 포함되어 있지 않은 경우* Recovery Services 자격 증명 모음을 삭제하려면 먼저 자격 증명 모음의 모든 리소스를 제거하거나 삭제해야 합니다. 리소스를 포함하는 자격 증명 모음을 삭제하려면 다음 이미지와 같이 오류가 표시됩니다.

![자격 증명 모음 삭제 오류](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

자격 증명 모음에서 리소스를 지우기 전에는 **다시 시도** 를 클릭해도 같은 오류가 표시됩니다. 이 오류 메시지에서 멈춰 있는 경우 **취소** 를 클릭하고 다음 단계를 사용하여 자격 증명 모음에 있는 리소스를 삭제합니다.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>VM을 보호하는 자격 증명 모음에서 항목 제거
Recovery Services 자격 증명 모음이 이미 열려 있는 경우 두 번째 단계를 건너뜁니다.

1. Azure Portal을 열고 대시보드에서 삭제할 자격 증명 모음을 엽니다.

   허브 메뉴에서 대시보드에 고정된 Recovery Services 자격 증명 모음이 없는 경우 **서비스 더 보기**를 클릭하고 리소스 목록에서 **Recovery Services**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services 자격 증명 모음**을 클릭합니다.

   ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Recovery Services 자격 증명 모음의 목록이 표시됩니다. 목록에서 삭제할 자격 증명 모음을 선택합니다.

   ![목록에서 자격 증명 모음 선택](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. 자격 증명 모음 보기에서 **필수** 창을 확인합니다. 자격 증명 모음을 삭제하려면 보호된 항목이 없어야 합니다. **백업 항목** 또는 **백업 관리 서버**에 0이 표시되지 않으면 해당 항목을 제거해야 합니다. 데이터가 포함된 자격 증명 모음은 삭제할 수 없습니다.

    ![보호된 항목에 대한 필수 창 확인](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    VM 및 파일/폴더는 Backup 항목으로 간주되고 필수 창의 **Backup 항목** 영역에 나열됩니다. DPM 서버는 필수 창의 **Backup 관리 서버** 영역에 나열됩니다. **복제된 항목** 은 Azure Site Recovery 서비스와 관계가 있습니다.
3. 자격 증명 모음에서 보호된 항목을 제거하기 시작하려면 자격 증명 모음에서 항목을 찾습니다. 자격 증명 모음 대시보드에서 **설정**을 클릭한 다음 **백업 항목**을 클릭하여 해당 메뉴를 엽니다.

    ![목록에서 자격 증명 모음 선택](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    **백업 항목** 메뉴는 Azure Virtual Machines 또는 파일-폴더 등 항목 형식에 따라 별도의 목록이 있습니다(이미지 참조). 표시된 기본 항목 형식 목록은 Azure Virtual Machines입니다. 자격 증명 모음에서 파일-폴더 항목의 목록을 보려면 드롭다운 메뉴에서 **파일-폴더** 를 선택합니다.
4. VM을 보호하는 자격 증명 모음에서 항목을 삭제하려면 먼저 항목의 백업 작업을 중지하고 복구 지점 데이터를 삭제해야 합니다. 자격 증명 모음의 각 항목에 대해 다음 단계를 따르세요.

    a. **백업 항목** 메뉴에서 해당 항목을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 작업 중지](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    백업 중지 메뉴가 열립니다.

    나. **백업 중지** 메뉴의 **옵션 선택** 메뉴에서 **백업 데이터 삭제** > 항목의 이름을 입력하고 > **백업 중지**를 클릭합니다.

    항목 이름을 입력하여 항목을 삭제할지 확인합니다. 항목을 확인하면 **Backup 중지** 단추가 활성화됩니다. 백업 항목의 이름을 입력할 대화 상자가 보이지 않으면 **Backup 데이터 보관** 옵션을 선택한 경우입니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    필요에 따라 데이터를 삭제하는 이유를 제공하고 메모를 추가할 수 있습니다. **Backup 중지**를 클릭하면 삭제 작업이 완료되어 자격 증명 모음을 삭제할 수 있습니다. 작업이 완료되었는지 확인하려면 Azure 메시지 ![delete backup data](./media/backup-azure-delete-vault/messages.png)를 입력합니다. <br/>
    작업이 완료되면 서비스에서는 백업 프로세스가 중지되고 백업 데이터가 삭제되었다는 메시지를 전송합니다.

    다. 목록에서 항목을 삭제한 후 **Backup 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 나머지 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

      목록에 항목이 없는 경우 Recovery Services 자격 증명 모음 메뉴에서 **필수** 창을 스크롤합니다. 어떤 **Backup 항목**, **Backup 관리 서버** 또는 **복제된 항목**도 나열되지 않아야 합니다. 자격 증명 모음에 항목이 아직 나타날 경우 3단계로 돌아가서 다른 항목 유형 목록을 선택합니다.  
5. 자격 증명 모음 도구 모음에 더 이상 항목이 없을 때 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-vault.png)
6. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>백업 프로세스를 중지했지만 데이터가 남아 있는 경우
백업 프로세스를 중지했지만 실수로 데이터가 *남아 있는* 경우 자격 증명 모음을 삭제하려면 먼저 백업 데이터를 삭제해야 합니다. 백업 데이터를 삭제하려면 다음을 수행합니다.

1. **백업 항목** 메뉴에서 항목을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **백업 데이터 삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **백업 데이터 삭제** 메뉴가 열립니다.
2. **백업 데이터 삭제** 메뉴에서 항목의 이름을 입력하고 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-retained-vault.png)

    데이터를 삭제한 후 4c단계로 돌아가서 프로세스를 계속합니다.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>DPM 서버를 보호하는 데 사용되는 자격 증명 모음 삭제
DPM 서버를 보호하는 데 사용되는 자격 증명 모음을 삭제하려면 먼저 만들어진 모든 복구 지점을 지운 다음 자격 증명 모음에서 서버 등록을 취소해야 합니다.

보호 그룹과 연결된 데이터를 삭제하려면 다음을 수행합니다.

1. DPM 관리자 콘솔에서 **보호**를 클릭하고, 보호 그룹을 선택하고, 보호 그룹 구성원을 선택하고, 도구 리본에서 **제거**를 클릭합니다.

  도구 리본의 **제거** 단추를 활성화하려면 보호 그룹 구성원을 선택합니다. 예제에서 구성원은 **dummyvm9**입니다. 보호 그룹에서 여러 구성원을 선택하려면 Ctrl 키를 누른 채로 구성원을 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    **보호 중지** 대화 상자가 열립니다.
2. **보호 중지** 대화 상자에서 **보호된 데이터 삭제**를 선택하고 **보호 중지**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    자격 증명 모음을 삭제하려면 보호된 데이터의 자격 증명 모음을 선택 취소하거나 삭제해야 합니다. 보호 그룹에 복구 지점 및 데이터가 많이 있는 경우 데이터를 삭제하는 데 몇 분 정도 걸릴 수 있습니다. 작업이 완료되면 **보호 중지** 대화 상자가 표시됩니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. 모든 보호 그룹의 모든 멤버에 대해 이 프로세스를 계속합니다.

    모든 보호된 데이터와 보호 그룹을 제거합니다.
4. 보호 그룹에서 모든 구성원을 삭제한 후 Azure Portal로 전환합니다. 자격 증명 모음 대시보드를 열고 **Backup 항목**, **Backup 관리 서버** 또는 **복제된 항목**이 없는지 확인합니다. 자격 증명 모음 도구 모음에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-vault.png)

    자격 증명 모음에 등록된 Backup 관리 서버가 있는 경우 자격 증명 모음에 데이터가 없더라도 자격 증명 모음을 삭제할 수 없습니다. 자격 증명 모음과 연결된 Backup 관리 서버를 삭제했지만 **필수** 창에 서버가 나열되는 경우 [자격 증명 모음에 등록된 Backup 관리 서버 찾기](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault)를 참조하세요.
5. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>프로덕션 서버를 보호하는 데 사용되는 자격 증명 모음 삭제
프로덕션 서버를 보호하는 데 사용되는 자격 증명 모음을 삭제하려면 먼저 자격 증명 모음에서 서버를 삭제하거나 등록을 취소해야 합니다.

자격 증명 모음과 연결된 프로덕션 서버를 삭제하려면

1. Azure Portal에서 자격 증명 모음 대시보드를 열고 **설정** > **백업 인프라** > **프로덕션 서버**를 입력합니다.

    ![프로덕션 서버 메뉴 열기](./media/backup-azure-delete-vault/delete-production-server.png)

    **프로덕션 서버** 메뉴가 열리고 자격 증명 모음에 모든 프로덕션 서버가 나열됩니다.

    ![프로덕션 서버 목록](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. **프로덕션 서버** 메뉴에서 서버를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

    ![프로덕션 서버 삭제 ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    **삭제** 메뉴가 열립니다.

    ![프로덕션 서버 삭제 ](./media/backup-azure-delete-vault/delete-blade.png)
3. **삭제** 메뉴에서 서버 이름을 확인하고 **삭제**를 클릭합니다. **삭제** 단추를 활성화하려면 서버의 이름을 올바르게 지정해야 합니다.

    자격 증명 모음이 삭제되면 자격 증명 모음이 삭제되었다는 메시지를 받습니다. 자격 증명 모음에서 모든 서버를 삭제한 후 자격 증명 모음 대시보드의 필수 창으로 다시 스크롤합니다.
4. 자격 증명 모음 대시보드에서 **Backup 항목**, **Backup 관리 서버** 또는 **복제된 항목**이 없는지 확인합니다. 자격 증명 모음 도구 모음에서 **삭제**를 클릭합니다.
5. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>자격 증명 모음에 등록된 Backup 관리 서버 찾기
자격 증명 모음에 여러 서버가 등록되어 있는 경우 기억하기 어려울 수 있습니다. 자격 증명 모음에 등록된 서버를 확인하고 삭제하려면

1. 자격 증명 모음 대시보드를 엽니다.
2. **필수** 창에서 **설정**을 클릭하여 해당 메뉴를 엽니다.

    ![설정 메뉴 열기](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. **설정** 메뉴에서 **백업 인프라**를 클릭합니다.
4. **백업 인프라** 메뉴에서 **백업 관리 서버**를 클릭합니다. 백업 관리 서버 메뉴가 열립니다.

    ![백업 관리 서버 목록](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. 목록에서 서버를 삭제하려면 서버 이름을 마우스 오른쪽 단추로 클릭한 다음 **삭제**를 클릭합니다.
    **삭제** 메뉴가 열립니다.
6. **삭제** 메뉴에서 서버의 이름을 입력합니다. 이름이 긴 경우 Backup 관리 서버 목록에서 복사하고 붙여넣을 수 있습니다. 그런 다음 **삭제**를 클릭합니다.  
