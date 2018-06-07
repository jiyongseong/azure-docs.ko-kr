---
title: "Azure Backup으로 VM에 파일 복원 | Microsoft Docs"
description: "Backup 및 Recovery Services를 사용하여 Azure VM에서 파일 수준 복원을 수행하는 방법을 알아봅니다."
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 77084c5663f9e12347c243c4e78160657d7443b2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Azure에서 가상 머신에 파일 복원
Azure Backup은 지역 중복 복구 자격 증명 모음에 저장되는 복구 지점을 만듭니다. 복구 지점에서 복원하는 경우 전체 VM 또는 개별 파일을 복원할 수 있습니다. 이 문서에서는 개별 파일을 복원하는 방법에 대해 자세히 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복구 지점 나열 및 선택
> * VM에 복구 지점 연결
> * 복구 지점에서 파일 복원

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.18 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 


## <a name="prerequisites"></a>필수 조건
이 자습서에서는 Azure Backup으로 보호된 Linux VM이 필요합니다. 실수로 인한 파일 삭제 및 복구 프로세스를 시뮬레이션하려면 웹 서버에서 페이지를 삭제합니다. 웹 서버를 실행하고 Azure Backup으로 보호된 Linux VM이 필요한 경우 [CLI를 사용하여 Azure에서 가상 컴퓨터 백업](quick-backup-vm-cli.md)을 참조하세요.


## <a name="backup-overview"></a>Backup 개요
Azure에서 백업을 시작하면 VM에 대한 백업 확장에서 특정 시점 스냅숏을 만듭니다. 첫 번째 백업이 요청될 때 백업 확장이 VM에 설치됩니다. 또한 백업이 수행될 때 VM이 실행되고 있지 않으면 Azure Backup에서 기본 저장소의 스냅숏을 만들 수도 있습니다.

기본적으로 Azure Backup은 파일 시스템 일치 백업을 만듭니다. Azure Backup에서 스냅숏을 만들면 데이터가 Recovery Services 자격 증명 모음으로 전송됩니다. 효율성을 극대화하기 위해 Azure Backup은 이전 백업 이후에 변경된 데이터 블록만 식별하여 전송합니다.

데이터 전송이 완료되면 스냅숏이 제거되고 복구 지점이 생성됩니다.


## <a name="delete-a-file-from-a-vm"></a>VM에서 파일 삭제
실수로 파일을 삭제하거나 파일을 변경한 경우 복구 지점에서 개별 파일을 복원할 수 있습니다. 이 프로세스를 사용하면 복구 지점에 백업된 파일을 찾아 필요한 파일만 복원할 수 있습니다. 이 예제에서는 웹 서버에서 파일을 삭제하여 파일 수준 복구 프로세스를 보여 줍니다.

1. VM에 연결하려면 [az vm show](/cli/azure/vm?view=azure-cli-latest#az_vm_show)를 사용하여 VM의 IP 주소를 얻습니다.

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. 현재 웹 사이트가 작동하는지 확인하려면 웹 브라우저를 열고 VM의 공용 IP 주소로 이동합니다. 웹 브라우저 창을 열어 둡니다.

    ![기본 NGINX 웹 페이지](./media/tutorial-restore-files/nginx-working.png)

3. SSH를 사용하여 VM에 연결합니다. *publicIpAddress*를 이전 명령에서 얻은 공용 IP 주소로 바꿉니다.

    ```bash
    ssh publicIpAddress
    ```

4. 다음과 같이 웹 서버에서 */var/www/html/index.nginx-debian.html* 기본 페이지를 삭제합니다.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. 웹 브라우저에서 웹 페이지를 새로 고칩니다. 다음 예제와 같이 웹 사이트에서 더 이상 페이지를 로드하지 않습니다.

    ![더 이상 기본 페이지를 로드하지 않는 NGINX 웹 사이트](./media/tutorial-restore-files/nginx-broken.png)

6. 다음과 같이 VM에 대한 SSH 세션을 닫습니다.

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>파일 복구 스크립트 생성
파일을 복원하기 위해 Azure Backup은 복구 지점을 로컬 드라이브로 연결하는 VM에서 실행할 스크립트를 제공합니다. 이 로컬 드라이브를 찾아보고, VM에 파일을 직접 복원한 다음, 복구 지점 연결을 해제할 수 있습니다. Azure Backup에서는 일정 및 보존에 할당된 정책에 따라 데이터를 계속 백업합니다.

1. VM에 대한 복구 지점을 나열하려면 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list)를 사용합니다. 이 예제에서는 *myRecoveryServicesVault*에서 보호되는 *myVM*이라는 VM에 대한 최근 복구 지점을 선택합니다.

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. 복구 지점을 VM에 연결하거나 탑재하는 스크립트를 가져오려면 [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp)를 사용합니다. 다음 예제에서는 *myRecoveryServicesVault*에서 보호되는 *myVM*이라는 VM에 대한 스크립트를 가져옵니다.

    *myRecoveryPointName*을 이전 명령에서 가져온 복구 지점 이름으로 바꿉니다.

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    다음 예제와 같이 스크립트가 다운로드되고 암호가 표시됩니다.

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. VM에 스크립트를 전송하려면 SCP(Secure Copy)를 사용합니다. 다운로드한 스크립트의 이름을 제공하고 *publicIpAddress*를 VM의 공용 IP 주소로 바꿉니다. 다음과 같이 SCP 명령의 끝에 후행 `:`를 포함시켜야 합니다.

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>VM에 파일 복원
이제 VM에 복사된 복구 스크립트를 사용하여 복구 지점을 연결하고 파일을 복원할 수 있습니다.

1. SSH를 사용하여 VM에 연결합니다. 다음과 같이 *publicIpAddress*를 VM의 공용 IP 주소로 바꿉니다.

    ```bash
    ssh publicIpAddress
    ```

2. 스크립트가 제대로 실행되도록 하려면 **chmod**를 사용하여 실행 권한을 추가합니다. 사용자 고유의 스크립트 이름을 입력합니다.

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. 복구 지점을 탑재하려면 스크립트를 실행합니다. 사용자 고유의 스크립트 이름을 입력합니다.

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    스크립트가 실행되면 복구 지점에 액세스하기 위해 암호를 입력하라는 메시지가 표시됩니다. 복구 스크립트를 생성한 이전 [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp) 명령의 출력에 표시된 암호를 입력합니다.

    스크립트의 출력에서 복구 지점에 대한 경로가 제공됩니다. 다음 예제 출력에서는 복구 지점이 */home/azureuser/myVM-20170919213536/Volume1*에 탑재되었음을 보여 줍니다.

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. **cp**를 사용하여 탑재된 복구 지점의 NGINX 기본 웹 페이지를 원래 파일 위치로 다시 복사합니다. */home/azureuser/myVM-20170919213536/Volume1* 탑재 지점을 사용자 고유의 위치로 바꿉니다.

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. 웹 브라우저에서 웹 페이지를 새로 고칩니다. 이제 다음 예제와 같이 웹 사이트가 올바르게 다시 로드됩니다.

    ![올바르게 로드된 NGINX 웹 사이트](./media/tutorial-restore-files/nginx-restored.png)

7. 다음과 같이 VM에 대한 SSH 세션을 닫습니다.

    ```bash
    exit
    ```

8. [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp)를 사용하여 VM에서 복구 지점을 분리합니다. 다음 예제에서는 *myRecoveryServicesVault*에서 *myVM*이라는 VM의 복구 지점을 분리합니다.

    *myRecoveryPointName*을 이전 명령에서 가져온 복구 지점 이름으로 바꿉니다.
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 복구 지점을 VM에 연결하고 웹 서버에 대한 파일을 복원했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 복구 지점 나열 및 선택
> * VM에 복구 지점 연결
> * 복구 지점에서 파일 복원

Azure에 Windows Server를 백업하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [Azure에 Windows Server 백업](tutorial-backup-windows-server-to-azure.md)

