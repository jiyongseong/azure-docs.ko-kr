---
title: "Azure Backup Server를 사용하여 VMware 서버 백업 | Microsoft Docs"
description: "Azure Backup Server를 사용하여 VMware vCenter/ESXi 서버를 Azure 또는 디스크에 백업합니다. 이 문서에서는 VMware 워크로드를 백업(또는 보호)하기 위한 단계별 지침을 제공합니다."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>Azure에 VMware 서버 백업

이 문서에서는 VMware 서버 워크로드를 보호하기 위해 Azure Backup Server를 구성하는 방법에 대해 설명합니다. 이 문서에서는 Azure Backup Server가 이미 설치되어 있다고 가정합니다. Azure Backup Server가 설치되어 있지 않은 경우 [Azure Backup Server를 사용하여 워크로드 백업 준비](backup-azure-microsoft-azure-backup.md)를 참조하세요.

Azure Backup Server는 VMware vCenter Server 버전 6.5, 6.0 및 5.5를 백업하거나 보호할 수 있습니다.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server에 대한 보안 연결 만들기

기본적으로 Azure Backup Server는 HTTPS 채널을 통해 각 vCenter Server와 통신합니다. 보안 통신을 설정하려면 Azure Backup Server에 VMware CA(인증 기관) 인증서를 설치하는 것이 좋습니다. 보안 통신이 필요하지 않고 HTTPS 요구 사항을 비활성화하려는 경우 [보안 통신 프로토콜 사용 안 함](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol)을 참조하세요. Azure Backup Server와 vCenter Server 간에 보안 연결을 만들려면 Azure Backup Server에서 신뢰할 수 있는 인증서를 가져옵니다.

일반적으로 Azure Backup Server 컴퓨터의 브라우저를 사용하여 vSphere Web Client를 통해 vCenter Server에 연결합니다. 처음 Azure Backup Server 브라우저를 사용하여 vCenter Server에 연결하면 연결 보안이 유지되지 않습니다. 다음 이미지에서는 보안이 설정되지 않은 연결을 보여 줍니다.

![VMware 서버에 대한 안전하지 않은 연결의 예](./media/backup-azure-backup-server-vmware/unsecure-url.png)

이 문제를 해결하고 보안 연결을 만들려면 신뢰할 수 있는 루트 CA 인증서를 다운로드합니다.

1. Azure Backup Server의 브라우저에 vSphere Web Client에 대한 URL을 입력합니다. vSphere Web Client 로그인 페이지가 나타납니다.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    관리자 및 개발자를 위한 정보 아래쪽에 **신뢰할 수 있는 루트 CA 인증서 다운로드** 링크가 있습니다.

    ![신뢰할 수 있는 루트 CA 인증서 다운로드 링크](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  vSphere Web Client 로그인 페이지가 표시되지 않으면 브라우저의 프록시 설정을 확인합니다.

2. **신뢰할 수 있는 루트 CA 인증서 다운로드**를 클릭합니다.

    vCenter Server가 로컬 컴퓨터에 파일을 다운로드합니다. 파일 이름은 **download**입니다. 브라우저에 따라 파일을 열거나 저장할 것인지 묻는 메시지가 나타납니다.

    ![인증서 다운로드 시 나타나는 다운로드 메시지](./media/backup-azure-backup-server-vmware/download-certs.png)

3. 파일을 Azure Backup Server의 위치에 저장합니다. 파일을 저장할 때 .zip 파일 이름 확장명을 추가합니다.

    이 파일은 인증서에 대한 정보가 들어 있는 .zip 파일입니다. .zip 확장명으로 압축 풀기 도구를 사용할 수 있습니다.

4. **download.zip**을 마우스 오른쪽 단추로 클릭한 다음 **모두 압축 풀기**를 선택하여 콘텐츠를 추출합니다.

    .zip 파일의 콘텐츠가 **certs** 폴더에 추출됩니다. certs 폴더에 두 가지 형식의 파일이 나타납니다. 루트 인증서 파일은 .0 및 .1과 같은 숫자가 매겨진 시퀀스로 시작하는 확장명을 갖습니다.
    
    CRL 파일은 .r0 또는 .r1과 같은 시퀀스로 시작하는 확장명을 갖습니다. CRL 파일은 인증서와 연결되어 있습니다.

    ![로컬로 추출된 다운로드 파일 ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. **certs** 폴더에서 루트 인증서 파일을 마우스 오른쪽 단추로 클릭한 다음 **이름 바꾸기**를 클릭합니다.

    ![루트 인증서 이름 바꾸기 ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    루트 인증서의 확장명을 .crt로 변경합니다. 확장명을 변경할지 묻는 메시지가 표시되는 경우 **예** 또는 **확인**을 클릭합니다. 그렇지 않은 경우 파일의 대상 기능을 변경합니다. 파일 아이콘이 루트 인증서를 나타내는 아이콘으로 바뀝니다.

6. 루트 인증서를 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **인증서 설치**를 선택합니다.

    **인증서 가져오기 마법사** 대화 상자가 나타납니다.

7. **인증서 가져오기 마법사** 대화 상자에서 **로컬 컴퓨터**를 인증서에 대한 대상으로 선택한 후 **다음**을 클릭하여 계속합니다.

    ![인증서 저장소 대상 옵션 ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    컴퓨터 변경을 허용할지 묻는 메시지가 표시되면 모든 변경 사항에 대해 **예** 또는 **확인**을 클릭합니다.

8. **인증서 저장소** 페이지에서 **모든 인증서를 다음 저장소에 저장**을 선택한 다음 **찾아보기**를 클릭하여 인증서 저장소를 선택합니다.

    ![특정 저장소 위치에 인증서 저장](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    **인증서 저장소 선택** 대화 상자가 나타납니다.

    ![인증서 저장소 폴더 계층 구조](./media/backup-azure-backup-server-vmware/cert-store.png)

9. 인증서 대상 폴더로 **신뢰할 수 있는 루트 인증 기관**을 선택한 다음 **확인**을 클릭합니다.

    ![인증서 대상 폴더](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    **신뢰할 수 있는 루트 인증 기관** 폴더가 인증서 저장소로 확인됩니다. **다음**을 누릅니다.

    ![인증서 저장소 폴더](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. **인증서 가져오기 마법사 완료** 페이지에서 인증서가 원하는 폴더에 있는지 확인한 다음 **마침**을 클릭합니다.

    ![인증서가 적절한 폴더에 있는지 확인](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    대화 상자가 나타나고 성공적인 인증서 가져오기가 확인됩니다.

11. vCenter Server에 로그인하여 연결이 안전한지 확인합니다.

  인증서 가져오기에 실패하고 보안 연결을 설정할 수 없는 경우 VMware vSphere 설명서의 [서버 인증서 가져오기](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html)를 참조하세요.

  조직 내에서 보안 경계가 있고 HTTPS 프로토콜을 사용하지 않으려는 경우 다음 절차를 사용하여 보안 통신을 사용하지 않도록 설정합니다.

### <a name="disable-secure-communication-protocol"></a>보안 통신 프로토콜 사용 안 함

조직에서 HTTPS 프로토콜이 필요하지 않은 경우 다음 단계를 사용하여 HTTPS를 사용하지 않도록 설정합니다. 기본 동작을 사용하지 않으려면 기본 동작을 무시하는 레지스트리 키를 만듭니다.

1. 다음 텍스트를 복사하여 .txt 파일에 붙여넣습니다.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. 파일을 Azure Backup Server 컴퓨터에 저장합니다. 파일 이름의 경우 DisableSecureAuthentication.reg를 사용합니다.

3. 레지스트리 항목을 활성화하려면 파일을 두 번 클릭합니다.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>vCenter Server에서 역할 및 사용자 계정 만들기

vCenter Server에서 역할은 미리 정의된 권한 집합입니다. vCenter Server 관리자는 역할을 생성합니다. 사용 권한을 할당하려면 관리자는 사용자 계정을 역할로 쌍을 묶습니다. vCenter Server 컴퓨터를 백업하는 데 필요한 사용자 자격 증명을 설정하려면 특정 권한이 있는 역할을 만든 다음 사용자 계정을 역할과 연결합니다.

Azure Backup Server는 사용자 이름과 암호를 사용하여 vCenter Server를 인증합니다. Azure Backup Server는 이러한 자격 증명을 모든 백업 작업에 대한 인증으로 사용합니다.

백업 관리자에 대한 vCenter Server 역할 및 해당 권한을 추가하려면:

1. VCenter 서버에 로그인한 다음 vCenter Server **탐색기** 패널에서 **관리**를 클릭합니다.

    ![vCenter Server 탐색기 패널의 관리 옵션](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. **관리**에서 **역할**을 선택한 다음 **역할** 패널에서 역할 추가 아이콘(+ 기호)을 클릭합니다.

    ![역할 추가](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    **역할 만들기** 대화 상자가 나타납니다.

    ![역할 만들기](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. **역할 만들기** 대화 상자의 **역할 이름** 상자에 *BackupAdminRole*을 입력합니다. 역할 이름은 원하는 대로 사용할 수 있지만 이름은 역할의 목적에 대해 쉽게 인식할 수 있어야 합니다.

4. 해당 vCenter 버전에 대한 권한을 선택한 다음 **확인**을 클릭합니다. 다음 표에는 vCenter 6.0 및 vCenter 5.5에 필요한 권한이 나와 있습니다.

  권한을 선택할 때 부모 레이블 옆의 아이콘을 클릭하여 부모를 확장하고 자식 권한을 봅니다. VirtualMachine 권한을 선택하려면 부모 자식 계층으로 여러 개의 수준을 이동해야 합니다. 부모 권한 내 모든 자식 권한을 선택하지 않아도 됩니다.

  ![부모 자식 권한 계층 구조](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  **확인**을 클릭하면 새 역할이 역할 패널에 있는 목록에 나타납니다.

|vCenter 6.0에 대한 권한| vCenter 5.5에 대한 권한|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>vCenter Server 사용자 계정 및 사용 권한 만들기

권한이 있는 역할이 설정된 후 사용자 계정을 만듭니다. 사용자 계정에는 이름과 암호가 있으며 이는 인증에 사용되는 자격 증명을 제공합니다.

1. 사용자 계정을 만들려면 vCenter Server **탐색기** 패널에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 및 그룹 옵션](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **vCenter 사용자 및 그룹** 패널이 나타납니다.

    ![vCenter 사용자 및 그룹 패널](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. **vCenter 사용자 및 그룹** 패널에서 **사용자** 탭을 선택한 다음 사용자 추가 아이콘(+ 기호)을 클릭합니다.

    **새 사용자** 대화 상자가 나타납니다.

3. **새 사용자** 대화 상자에서 사용자의 정보를 추가한 다음 **확인**을 클릭합니다. 이 절차에서 사용자 이름은 BackupAdmin입니다.

    ![새 사용자 대화 상자](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    새 사용자 계정이 목록에 나타납니다.

4. 사용자 계정을 역할과 연결하려면 **탐색기** 패널에서 **전역 권한**을 클릭합니다. **전역 권한** 패널에서 **관리** 탭을 선택한 다음 추가 아이콘(+ 기호)을 클릭합니다.

    ![전역 권한 패널](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    **전역 권한 루트 - 권한 추가** 대화 상자가 나타납니다.

5. **전역 권한 루트 - 권한 추가** 대화 상자에서 **추가**를 클릭하여 사용자 또는 그룹을 선택합니다.

    ![사용자 또는 그룹 선택](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    **사용자/그룹 선택** 대화 상자가 나타납니다.

6. **사용자/그룹 선택** 대화 상자에서 **BackupAdmin**을 선택한 다음 **추가**를 클릭합니다.

    **사용자**에서 *domain\username* 형식이 사용자 계정에 사용됩니다. 다른 도메인을 사용하려면 **도메인** 목록에서 원하는 도메인을 선택합니다.

    ![BackupAdmin 사용자 추가](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    **확인**을 클릭하여 선택한 사용자를 **권한 추가** 대화 상자에 추가합니다.

7. 이제 사용자를 식별하였으므로 사용자를 역할에 할당합니다. **할당된 역할**의 드롭다운 목록에서 **BackupAdminRole**을 선택한 다음 **확인**을 클릭합니다.

    ![사용자를 역할에 할당](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  **전역 권한** 패널의 **관리** 탭에서 새 사용자 계정 및 연결된 역할이 목록에 표시됩니다.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Azure Backup Server에서 vCenter Server 자격 증명 설정

Azure Backup Server에 VMware 서버를 추가하기 전에 [Azure Backup Server용 업데이트 1](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server)을 설치합니다.

1. Azure Backup Server를 열려면 Azure Backup Server 바탕 화면에서 아이콘을 두 번 클릭합니다.

    ![Azure Backup Server 아이콘](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    데스크톱에서 아이콘을 찾을 수 없는 경우 설치된 앱 목록에서 Azure Backup Server를 엽니다. Azure Backup Server 앱 이름은 Microsoft Azure Backup입니다.

2. Azure Backup Server 콘솔에서 **관리**를 클릭하고 **프로덕션 서버**를 클릭한 다음 도구 리본에서 **VMware 관리**를 클릭합니다.

    ![Azure Backup Server 콘솔](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    **자격 증명 관리** 대화 상자가 나타납니다.

    ![Azure Backup Server 자격 증명 관리 대화 상자](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. **자격 증명 관리** 대화 상자에서 **추가**를 클릭하여 **자격 증명 추가** 대화 상자를 엽니다.

4. **자격 증명 추가** 대화 상자에서 새 자격 증명에 대한 이름 및 설명을 입력합니다. 그런 다음 사용자 이름 및 암호를 지정합니다. 이름(*Contoso Vcenter 자격 증명*)은 다음 절차에서 자격 증명을 식별하는 데 사용됩니다. vCenter Server에서 사용되는 것과 동일한 사용자 이름과 암호를 사용합니다. vCenter Server와 Azure Backup Server가 동일한 도메인에 없는 경우 **사용자 이름**에서 도메인을 지정합니다.

    ![Azure Backup Server 자격 증명 추가 대화 상자](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    **추가**를 클릭하여 Azure Backup Server에 새 자격 증명을 추가합니다. 새 자격 증명이 **자격 증명 관리** 대화 상자의 목록에 표시됩니다.
    
    ![Azure Backup Server 자격 증명 관리 대화 상자](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. **자격 증명 관리** 대화 상자를 닫으려면 오른쪽 위 모서리에 있는 **X**를 클릭합니다.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Azure Backup Server에 vCenter Server 추가

프로덕션 서버 추가 마법사를 사용하여 Azure Backup Server에 vCenter Server를 추가합니다.

프로덕션 서버 추가 마법사를 열려면 다음 절차를 완료합니다.

1. Azure Backup Server 콘솔에서 **관리**, **프로덕션 서버**를 차례로 클릭한 다음 **추가**를 클릭합니다.

    ![프로덕션 서버 추가 마법사 열기](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    **프로덕션 서버 추가 마법사** 대화 상자가 나타납니다.

    ![프로덕션 서버 추가 마법사](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. **프로덕션 서버 형식 선택** 페이지에서 **VMware 서버**를 선택하고 **다음**을 클릭합니다.

3. **서버 이름/IP 주소**에서 VMware 서버의 FQDN(정규화된 도메인 이름) 또는 IP 주소를 지정합니다. 모든 ESXi 서버를 동일한 vCenter가 관리하는 경우 vCenter 이름을 사용할 수 있습니다.

    ![VMware 서버 FQDN 또는 IP 주소 지정](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **SSL 포트**에서 VMware 서버와 통신하는 데 사용되는 포트를 입력합니다. 다른 포트가 필요하지 않는 한 기본 포트인 포트 443을 사용합니다.

5. **자격 증명 지정**에서 이전에 만든 자격 증명을 선택합니다.

    ![자격 증명 지정](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. **추가**를 클릭하여 VMware 서버를 **추가된 VMware 서버** 목록에 추가하고 **다음**을 클릭하여 마법사에서 다음 페이지로 이동합니다.

    ![VMWare 서버 및 자격 증명 추가](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **요약** 페이지에서 **추가**를 클릭하여 지정된 VMware 서버를 Azure Backup Server에 추가합니다.

    ![Azure Backup Server에 VMware 서버 추가](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  VMware 서버 백업은 에이전트 없이 진행되는 백업이며 새 서버가 즉시 추가됩니다. **마침** 페이지에 결과가 표시됩니다.

  ![마침 페이지](./media/backup-azure-backup-server-vmware/summary-screen.png)

  VCenter Server의 여러 인스턴스를 Azure Backup Server에 추가하려면 이 섹션의 이전 단계를 반복합니다.

Azure Backup Server에 vCenter Server를 추가한 후 다음 단계로 보호 그룹을 만듭니다. 보호 그룹은 단기 또는 장기 보존에 대한 다양한 세부 정보를 지정하며, 사용자가 백업 정책을 정의 및 적용하는 그룹입니다. 백업 정책은 백업이 발생하는 시기 및 백업되는 항목에 대한 일정입니다.


## <a name="configure-a-protection-group"></a>보호 그룹 구성

System Center Data Protection Manager 또는 Azure Backup Server를 사용해 본 적이 없다면 [디스크 백업 계획](https://technet.microsoft.com/library/hh758026.aspx)을 참조하여 하드웨어 환경을 준비합니다. 적절한 저장소가 있는지 확인한 후 새 보호 그룹 만들기 마법사를 사용하여 VMware Virtual Machines를 추가합니다.

1. Azure Backup Server 콘솔에서 **보호**를 클릭하고 도구 리본에서 **새로 만들기**를 클릭하여 새 보호 그룹 만들기 마법사를 엽니다.

    ![새 보호 그룹 만들기 마법사 열기](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    **새 보호 그룹 만들기** 마법사 대화 상자가 나타납니다.

    ![새 보호 그룹 만들기 마법사 대화 상자](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    **다음**을 클릭하여 **보호 그룹 형식 선택** 페이지로 넘어갑니다.

2. **보호 그룹 형식 선택** 페이지에서 **서버**를 선택하고 **다음**을 클릭합니다. **그룹 구성원 선택** 페이지가 나타납니다.

3. **그룹 구성원 선택** 페이지에 사용 가능한 구성원 및 선택한 구성원이 나타납니다. 보호할 구성원을 선택한 후 **다음**을 클릭합니다.

    ![그룹 구성원 선택](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    구성원을 선택할 때 다른 폴더 또는 VM을 포함하는 폴더를 선택하면 포함되는 폴더 및 VM도 함께 선택됩니다. 상위 폴더에 폴더와 VM이 포함되는 것을 폴더 수준 보호라고 합니다. 폴더 또는 VM을 제거하려면 확인란의 선택을 취소합니다.

    VM 또는 VM을 포함하는 폴더가 이미 Azure에서 보호되는 경우 해당 VM을 다시 선택할 수 없습니다. 즉, VM은 한 번 Azure에서 보호되면 다시 보호할 수 없습니다. 이는 하나의 VM에 대해 중복 복구 지점이 생성되지 않도록 합니다. Azure Backup Server 인스턴스가 이미 구성원을 보호하는지 확인하려는 경우 구성원을 가리켜서 보호하는 서버의 이름을 확인합니다.

4. **데이터 보호 방법 선택** 페이지에서 보호 그룹에 사용할 이름을 입력합니다. 단기 보호(디스크) 및 온라인 보호가 선택됩니다. Azure에 온라인 보호를 사용하려면 디스크에 대한 단기 보호를 사용해야 합니다. **다음**을 클릭하여 단기 보호 범위로 진행합니다.

    ![데이터 보호 방법 선택](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. **단기 목표 지정** 페이지의 **보존 범위**에서 *디스크에 저장된* 복구 지점을 유지할 원하는 일 수를 지정합니다. 복구 지점을 생성할 때 시간 및 일 수를 변경하려면 **수정**을 클릭합니다. 단기 복구 지점은 전체 백업됩니다. 증분 백업하지 않습니다. 단기 목표를 충족하는 경우 **다음**을 클릭합니다.

    ![단기 목표 지정](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. **디스크 할당 검토** 페이지에서 필요한 경우 검토하고 VM에 대한 디스크 공간을 수정합니다. 권장되는 디스크 할당은 **단기 목표 지정** 페이지에서 지정한 보존 범위, 워크로드의 형식 및 보호된 데이터의 크기(3단계에서 식별됨)를 기반으로 합니다.  

  - **데이터 크기:** 보호 그룹의 데이터 크기입니다.
  - **디스크 공간:** 보호 그룹에 권장되는 디스크 공간의 크기입니다. 이 설정을 수정하려는 경우 총 공간을 각 데이터 원본의 예상 확장량보다 약간 더 크게 할당해야 합니다.
  - **데이터 공동 배치:** 공동 배치를 사용하는 경우 보호되는 여러 데이터 원본은 단일 복제본 및 복구 지점 볼륨에 매핑할 수 있습니다. 공동 배치는 모든 워크로드에 지원되는 것은 아닙니다.
  - **자동 증가:** 이 설정을 사용하는 경우 보호되는 그룹의 데이터가 초기 할당량을 초과하면 System Center Data Protection Manager가 25%까지 디스크 크기 증가를 시도합니다.
  - **저장소 풀 세부 정보:** 총 디스크 크기 및 남아 있는 디스크 크기를 포함한 저장소 풀의 상태를 표시합니다.

    ![디스크 할당 검토](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    할당된 공간이 만족스러운 경우 **다음**을 클릭합니다.

7. **복제본 만들기 방법 선택** 페이지에서 초기 복사본 또는 Azure Backup Server에서 보호되는 데이터의 복제본을 생성하는 방법을 지정합니다.

    기본 설정은 **네트워크를 통해 자동으로** 및 **지금**입니다. 기본값을 사용하는 경우 사용량이 적은 시간을 지정하는 것이 좋습니다. **나중에**를 선택하고 날짜와 시간을 지정합니다.

    대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터를 오프라인으로 복제하는 것을 고려하세요.

    선택을 완료한 후 **다음**을 클릭합니다.

    ![복제본 만들기 방법 선택](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. **일관성 확인 옵션** 페이지에서 일관성 확인을 자동화할 방법 및 시기를 선택합니다. 복제 데이터가 일관성을 잃은 경우 또는 설정된 일정에 따라 일관성 확인을 실행할 수 있습니다.

    자동 일관성 확인을 구성하지 않으려면 수동 검사를 실행할 수 있습니다. Azure Backup Server 콘솔의 보호 영역에서 보호 그룹을 마우스 오른쪽 단추로 클릭한 다음 **일관성 확인 수행**을 선택합니다.

    **다음**을 클릭하여 다음 페이지로 이동합니다.

9. **온라인 보호 데이터 지정** 페이지에서 보호하려는 하나 이상의 데이터 원본을 선택합니다. 구성원을 개별적으로 선택하거나 **모두 선택**을 클릭하여 모든 구성원을 선택할 수 있습니다. 구성원을 선택한 후 **다음**을 클릭합니다.

    ![온라인 보호 데이터 지정](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. **온라인 백업 일정 지정** 페이지에서 디스크 백업에서 복구 지점을 생성하기 위한 일정을 지정합니다. 복구 지점이 생성되면 Azure에서 Recovery Services 자격 증명 모음으로 전송됩니다. 온라인 백업 일정에 만족한다면 **다음**을 클릭합니다.

    ![온라인 백업 일정 지정](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. **온라인 보존 정책 지정** 페이지에서 Azure의 백업 데이터를 유지하고자 하는 기간을 지정합니다. 정책을 정의한 후 **다음**을 클릭합니다.

    ![온라인 보존 정책 지정](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Azure에서 데이터를 유지할 수 있는 기간에 대한 시간 제한은 없습니다. Azure에 복구 지점 데이터를 저장하는 경우 보호되는 인스턴스당 9999개 미만의 복구 지점을 사용해야 한다는 점이 유일한 제한입니다. 이 예제에서 보호되는 인스턴스는 VMware 서버입니다.

12. **요약** 페이지에서 보호 그룹 구성원 및 설정에 대한 세부 정보를 검토한 다음 **그룹 만들기**를 클릭합니다.

    ![보호 그룹 구성원 및 설정 요약](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>다음 단계
Azure Backup Server를 사용하여 VMware 워크로드를 보호하고 있다면 Azure Backup Server를 사용하여 [Microsoft Exchange Server](./backup-azure-exchange-mabs.md), [Microsoft SharePoint 팜](./backup-azure-backup-sharepoint-mabs.md) 또는 [SQL Server 데이터베이스](./backup-azure-sql-mabs.md)를 보호하는 것에 관심이 있을 수도 있습니다.

에이전트 등록, 보호 그룹 구성 또는 작업 백업 관련 문제에 관한 정보는 [Azure Backup Server 문제 해결](./backup-azure-mabs-troubleshoot.md)을 참조하세요.
