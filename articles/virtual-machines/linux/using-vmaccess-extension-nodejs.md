---
title: VMAccess 확장을 사용하여 Azure Linux VM에 대한 액세스 권한 재설정 | Microsoft Docs
description: VMAccess 확장을 사용하여 Azure Linux VM에 대한 액세스 권한 재설정
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 5fb130fc2e448f3cbc648991ea6bebd5795bc78b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Azure CLI 1.0에서 VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구
이 문서는 VMAccess VM 확장을 사용하여 디스크를 검사 또는 복구하거나, 사용자 액세스를 다시 설정하거나, 사용자 계정을 관리하거나, Linux의 SSHD 구성을 다시 설정하는 방법을 설명합니다. 이 문서의 내용을 실행하기 위해 필요한 사항:

* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))
* `azure login`으로 로그인된 [Azure CLI](../../cli-install-nodejs.md)
* Azure Resource Manager 모드 `azure config mode arm`으로 *있어야 하는* Azure CLI


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="quick-commands"></a>빠른 명령
Linux VM에서 VMAccess를 사용하는 방법에는 다음 두 가지가 있습니다.

* Azure CLI 1.0 및 필수 매개 변수 사용
* VMAccess에서 처리한 후 관련 작업을 수행하는 원시 JSON 파일 사용

빠른 명령 섹션에서는 Azure CLI 1.0 `azure vm reset-access` 메서드를 사용합니다. 다음 명령 예제에서 "example"이 포함된 값을 사용자 환경의 값으로 바꿉니다.

## <a name="create-a-resource-group-and-linux-vm"></a>리소스 그룹 및 Linux VM 만들기
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Debian VM 만들기
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>루트 암호 다시 설정
루트 암호 재설정 방법:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>SSH 키 다시 설정
루트가 아닌 사용자의 SSH 키를 다시 설정하는 방법:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>사용자 만들기
사용자를 만드는 방법:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>사용자 제거
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>SSHD 재설정
SSHD 구성 재설정 방법:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>자세한 연습
### <a name="vmaccess-defined"></a>VMAccess 정의:
Linux VM의 디스크에 오류가 표시되어 있습니다. 사용자가 Linux VM의 루트 암호를 재설정했거나 SSH 개인 키를 실수로 삭제했습니다. 데이터 센터를 사용할 때는 이러한 경우 데이터 센터로 직접 가서 KVM을 열어 서버 콘솔에 액세스해야 했습니다. Azure VMAccess 확장을 콘솔에 액세스하여 Linux에 대한 액세스 권한을 재설정하거나 디스크 수준 유지 관리를 수행할 수 있는 이 KVM 스위치로 생각하세요.

여기서는 자세한 연습을 위해 원시 JSON 파일을 사용하는 긴 형식의 VMAccess를 사용합니다.  이러한 VMAccess JSON 파일은 Azure 템플릿에서도 호출할 수 있습니다.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>VMAccess를 사용하여 Linux VM의 디스크 검사 또는 복구
VMAccess를 사용하면 Linux VM에 있는 디스크에 fsck를 실행할 수 있습니다.  VMAccess를 사용하여 디스크 검사와 디스크 복구를 수행할 수도 있습니다.

디스크를 검사한 후에 복구하려면 다음 VMAccess 스크립트를 사용합니다.

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>VMAccess를 사용하여 Linux에 대한 사용자 액세스 권한 재설정
Linux VM의 루트에 액세스할 수 없게 된 경우 VMAccess 스크립트를 시작하여 루트 암호를 다시 설정할 수 있습니다.

루트 암호를 다시 설정하려면 다음 VMAccess 스크립트를 사용합니다.

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

루트가 아닌 사용자의 SSH 키를 다시 설정하려면 다음 VMAccess 스크립트를 사용합니다.

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>VMAccess를 사용하여 Linux에서 사용자 계정 관리
VMAccess는 로그인하고 sudo 또는 루트 계정을 사용하지 않고 Linux VM의 사용자를 관리하는 데 사용할 수 있는 Python 스크립트입니다.

사용자를 만들려면 다음 VMAccess 스크립트를 사용합니다.

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

사용자를 삭제하려면 다음 VMAccess 스크립트를 사용합니다.

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>VMAccess를 사용하여 SSHD 구성 다시 설정
Linux VM SSHD 구성을 변경하고 변경 내용을 확인하기 전에 SSH 연결을 닫을 경우 SSH에 다시 로그인하지 못할 수 있습니다.  VMAccess를 사용하면 SSH를 통해 로그인하지 않고도 SSHD 구성을 알려진 정상 구성으로 다시 설정할 수 있습니다.

SSHD 구성을 재설정하려면 이 VMAccess 스크립트를 사용합니다.

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>다음 단계
실행 중인 Linux VM에서 변경을 수행하는 한 가지 방법은 Azure VMAccess 확장을 사용하여 Linux를 업데이트하는 것입니다.  cloud-init 및 Azure 템플릿 등의 도구를 사용하여 부팅 시 Linux VM을 수정할 수도 있습니다.

[가상 머신 확장 및 기능 정보](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Linux VM 확장을 사용하여 Azure Resource Manager 템플릿 작성](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

