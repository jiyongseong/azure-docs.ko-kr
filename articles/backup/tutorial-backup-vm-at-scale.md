---
title: "대규모로 Azure 가상 머신 백업 | Microsoft Docs"
description: "Azure에 여러 가상 머신을 동시에 백업"
services: backup
keywords: "가상 머신 백업; vm 백업; Azure vm 백업; 백업 및 재해 복구"
author: markgalioto
ms.author: markgal
ms.date: 2/14/2018
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: f1cfa72d0fb3c83ef6265649b740dec317f0e4b2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Azure Portal을 사용하여 여러 가상 머신을 백업

Azure에 데이터를 백업하면 Recovery Services 자격 증명 모음이라는 Azure 리소스에 해당 데이터가 저장됩니다. Recovery Services 자격 증명 모음 리소스는 대부분의 Azure 서비스의 설정 메뉴에서 사용할 수 있습니다. Recovery Services 자격 증명 모음이 Azure 서비스 대부분의 설정 메뉴에 통합되면 데이터의 백업이 매우 쉬워집니다. 그러나 회사의 각 데이터베이스 또는 가상 컴퓨터에서 개별적으로 작업하면 번거로울 수 있습니다. 한 부서 또는 한 위치에 있는 모든 가상 머신의 데이터를 백업하려면 어떻게 해야 할까요? 백업 정책을 만들어 원하는 가상 머신에 적용하면 여러 가상 머신을 쉽게 백업할 수 있습니다. 이 자습서에서는 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음 만들기
> * 백업 정책 정의
> * 여러 가상 머신을 보호하기 위해 백업 정책 적용
> * 보호된 가상 머신 대한 주문형 백업 작업 트리거

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음에는 백업 데이터, 그리고 보호된 가상 머신에 적용된 백업 정책이 포함되어 있습니다. 가상 머신 백업은 로컬 프로세스입니다. 한 위치의 가상 머신을 다른 위치의 Recovery Services 자격 증명 모음으로 백업할 수 없습니다. 따라서 백업할 가상 머신이 있는 각 Azure 위치마다 하나 이상의 Recovery Services 자격 증명 모음이 존재해야 합니다.

1. 왼쪽 메뉴에서 **모든 서비스**를 선택하고 서비스 목록에서 *Recovery Services*를 입력합니다. 입력하면 리소스 목록이 필터링됩니다. 목록에 Recovery Services 자격 증명 모음이 표시되면 이를 선택하여 Recovery Services 자격 증명 모음 메뉴를 엽니다.

    ![Recovery Services 자격 증명 모음 메뉴 열기](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 클릭하여 Recovery Services 자격 증명 모음 메뉴를 엽니다.

    ![자격 증명 모음 메뉴 열기](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. Recovery Services 자격 증명 모음 메뉴에서 

    - **이름**에 *myRecoveryServicesVault*를 입력합니다.
    - 현재 구독 ID가 **구독**에 표시됩니다. 추가 구독이 있는 경우 새 자격 증명 모음에 대해 다른 구독을 선택할 수 있습니다.
    - **리소스 그룹**에서 **기존 항목 사용**을 선택하고 *myResourceGroup*을 선택합니다. *myResourceGroup*이 없는 경우에는 **새로 만들기**를 선택한 후 *myResourceGroup*을 입력합니다.
    - **위치** 드롭다운 메뉴에서 *유럽 서부*를 선택합니다.
    - **만들기**를 클릭하면 Recovery Services 자격 증명 모음이 생성됩니다.

Recovery Services 자격 증명 모음은 보호 중인 가상 머신과 동일한 위치에 있어야 합니다. 가상 머신이 여러 지역에 있는 경우 각 지역에 Recovery Services 자격 증명 모음을 만듭니다. 이 자습서에서는 *유럽 서부*에 Recovery Services 자격 증명 모음을 만듭니다. 이 지역에 *myVM*(퀵 스타트로 만든 가상 머신)을 생성했기 때문입니다.

Recovery Services 자격 증명 모음을 만드는 데 몇 분 정도 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다.

Recovery Services 자격 증명 모음을 만들면 기본적으로 자격 증명 모음에 지역 중복 저장소도 만들어집니다. 데이터 복원력을 제공하기 위해 지역 중복 저장소는 두 Azure 영역 간에 여러 번 데이터를 복제합니다.

## <a name="set-backup-policy-to-protect-vms"></a>VM을 보호하기 위한 백업 정책 설정

Recovery Services 자격 증명 모음을 만든 후 다음 단계는 데이터 형식에 맞게 자격 증명 모음을 구성하고 백업 정책을 설정하는 것입니다. 백업 정책은 복구 지점이 발생하는 빈도 및 시기에 대한 일정입니다. 정책에는 복구 지점의 보존 범위(데이터 보존 기간)도 포함됩니다. 이 자습서에서는 호텔, 경기장, 레스토랑 및 할인점이 있는 스포츠 복합 시설을 운영하는 비즈니스에서 가상 머신의 데이터를 보호한다고 가정해 보겠습니다. 다음 단계에서는 재무 데이터에 대한 백업 정책을 만듭니다.

1. Recovery Services 자격 증명 모음 목록에서 **myRecoveryServicesVault**를 선택하여 해당 대시보드를 엽니다.

   ![시나리오 메뉴 열기](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. 자격 증명 모음 대시보드 메뉴에서 **백업**을 클릭하여 백업 메뉴를 엽니다.

3. 백업 목표 메뉴의 **Where is your workload running**(워크로드가 실행되는 위치) 드롭다운 메뉴에서 *Azure*를 선택합니다. **백업할 항목** 드롭다운 메뉴에서 *가상 컴퓨터*를 선택한 다음 **백업**을 클릭합니다.

    이제 Recovery Services 자격 증명 모음이 가상 머신과 상호 작용할 준비가 되었습니다. Recovery Services 자격 증명 모음에는 매일 복원 지점을 만들고 30일 동안 복원 지점을 유지하는 기본 정책이 있습니다.

    ![시나리오 메뉴 열기](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. 새 정책을 만들려면 백업 정책 메뉴의 **백업 정책 선택** 드롭다운 메뉴에서 *새로 만들기*를 선택합니다.

    ![워크로드 선택](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. **백업 정책** 메뉴에서 **정책 이름**에 *재무*를 입력합니다. 백업 정책에 대해 다음과 같은 변경 사항을 입력합니다. 
    - **백업 빈도**에서 *중부 표준시*의 표준 시간대를 설정합니다. 스포츠 복합 시설이 텍사스에 있으므로 소유자는 현지 시간을 설정하고자 합니다. 백업 빈도는 매일 오전 3시 30분 설정을 그대로 둡니다.
    - **일일 백업 지점 보존**에서 기간은 90일로 설정합니다.
    - **주간 백업 지점 보존**에서 *월요일*을 복원 지점으로 사용하고 52주 동안 유지합니다.
    - **월간 백업 지점 보존**에서 해당 월 첫 번째 일요일의 복원 지점을 사용하고 36개월 동안 유지합니다.
    - **연간 백업 지점 보존** 옵션은 선택 취소합니다. 재무 책임자는 데이터를 36개월 넘게 보관하려고 하지 않습니다.
    - **확인**을 클릭하여 백업 정책을 만듭니다.

    ![워크로드 선택](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    백업 정책을 만든 후 정책을 가상 머신과 연결합니다.

6. **가상 머신 선택** 대화 상자에서 *myVM*을 선택하고 **확인**을 클릭하여 가상 머신에 백업 정책을 배포합니다. 

    아직 백업 정책과 연결되지 않고 같은 위치에 있는 모든 가상 머신이 표시됩니다. *myVMH1* 및 *myVMR1*이 선택되어 *재무* 정책에 연결됩니다.

    ![워크로드 선택](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    배포가 완료되면 배포가 성공적으로 완료되었다는 알림을 받게 됩니다.

## <a name="initial-backup"></a>초기 백업

Recovery Services 자격 증명 모음에 대한 백업을 사용하도록 설정했지만, 아직 초기 백업은 생성되지 않았습니다. 초기 백업을 트리거하는 것은 재해 복구 모범 사례로, 데이터를 보호할 수 있습니다. 

주문형 백업 작업을 실행하려면

1. 자격 증명 모음 대시보드에서 **백업 항목** 아래의 **3**을 클릭하여 백업 항목 메뉴를 엽니다.

    ![설정 아이콘](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    **백업 항목** 메뉴가 열립니다.

2. **백업 항목** 메뉴에서 **Azure Virtual Machine**을 클릭하여 자격 증명 모음과 연결된 가상 머신의 목록을 엽니다.

    ![설정 아이콘](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    **백업 항목** 목록이 열립니다.

    ![백업 작업 트리거됨](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. **백업 항목** 목록에서 줄임표 **...**를 클릭하여 상황에 맞는 메뉴를 엽니다.

4. 상황에 맞는 메뉴에서 **지금 백업**을 선택합니다.

    ![상황에 맞는 메뉴](./media/tutorial-backup-vm-at-scale/context-menu.png)

    지금 백업 메뉴가 열립니다.

5. 지금 백업 메뉴에서 복구 지점을 유지할 마지막 날짜를 입력하고 **백업**을 클릭합니다.

    ![지금 백업 복구 지점을 유지할 마지막 날을 설정합니다.](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    배포 알림을 통해 백업 작업이 트리거되고 백업 작업 페이지에서 작업의 진행률을 모니터링할 수 있다는 것을 알립니다. 가상 컴퓨터의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

    초기 백업 작업이 완료되면 백업 작업 메뉴에서 상태를 확인할 수 있습니다. 주문형 백업 작업이 *myVM*에 대한 초기 복원 지점을 만들었습니다. 다른 가상 머신을 백업하려는 경우 각 가상 머신에 대해 이러한 단계를 반복합니다. 

    ![백업 작업 타일](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 계속 작업하려는 경우 이 자습서에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계에 따라 이 자습서에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.

1. **myRecoveryServicesVault** 대시보드의 **백업 항목** 아래에서 **3**을 클릭하여 백업 항목 메뉴를 엽니다.

    ![설정 아이콘](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. **백업 항목** 메뉴에서 **Azure Virtual Machine**을 클릭하여 자격 증명 모음과 연결된 가상 머신의 목록을 엽니다.

    ![설정 아이콘](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    **백업 항목** 목록이 열립니다.

3. **백업 항목** 메뉴에서 줄임표를 클릭하여 상황에 맞는 메뉴를 엽니다.

    ![설정 아이콘](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. 상황에 맞는 메뉴에서 **백업 중지**를 선택하여 백업 중지 메뉴를 엽니다. 

    ![설정 아이콘](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. **백업 중지** 메뉴에서 상단의 드롭다운 메뉴를 선택하고 **백업 데이터 삭제**를 선택합니다.

6. **백업 항목의 이름 입력** 대화 상자에서 *myVM*을 입력합니다.
 
7. 백업 항목이 확인되면(확인 표시가 나타남) **백업 중지** 단추를 사용할 수 있습니다. **백업 중지**를 클릭하여 정책을 중지하고 복원 지점을 삭제합니다. 

    ![백업 중지를 클릭하여 자격 증명 모음 삭제](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)에서도 확인할 수 있습니다.

8. **myRecoveryServicesVault** 메뉴에서 **삭제**를 클릭합니다.

    ![백업 중지를 클릭하여 자격 증명 모음 삭제](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    자격 증명 모음이 삭제되면 Recovery Services 자격 증명 모음 목록으로 돌아갑니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal을 사용하여 다음을 수행했습니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음 만들기
> * 자격 증명 모음을 설정하여 가상 머신 보호
> * 사용자 지정 백업 및 보존 정책 만들기
> * 정책을 할당하여 여러 가상 머신 보호
> * 가상 머신에 대한 주문형 백업 트리거

디스크에서 Azure 가상 머신을 복원하려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
> [CLI를 사용하여 VM 복원](./tutorial-restore-disk.md)
