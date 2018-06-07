---
title: 클래식 Azure VM에 디스크 연결 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 만든 Windows 가상 머신에 데이터 디스크를 연결하고 초기화합니다.
services: virtual-machines-windows, storage
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 48a6b96bd611fcc8fa8219aeef359419255b8cef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 Windows 가상 머신에 데이터 디스크 연결

이 문서에서는 Azure Portal을 사용하여 클래식 배포 모델에서 만든 신규 및 기존 디스크를 Windows 가상 머신에 연결하는 방법을 보여 줍니다.



또한 [Azure 포털에서 Linux VM에 데이터 디스크를 연결](../../linux/attach-disk-portal.md)할 수도 있습니다.

디스크를 연결하기 전에 다음과 같은 팁을 검토합니다.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

* 프리미엄 저장소를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 머신이 필요합니다. 이 가상 머신을 사용하여 프리미엄 및 표준 저장소 계정에서 모두 디스크를 사용할 수 있습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [Premium Storage: Azure Virtual Machine 작업을 위한 고성능 저장소](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

* 새 디스크의 경우 Azure가 디스크를 연결할 때 생성하므로 먼저 생성하지 않아도 됩니다.

또한 [Powershell을 사용하여 데이터 디스크를 연결](../../virtual-machines-windows-attach-disk-ps.md)할 수 있습니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>가상 머신 찾기
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 대시보드에 나열된 리소스에서 가상 머신을 선택합니다.
3. **설정** 아래 왼쪽 창에서 **디스크**를 클릭합니다.

    ![디스크 설정 열기](./media/attach-disk/virtualmachinedisks.png)

[새 디스크](#option-1-attach-a-new-disk) 또는 [기존 디스크](#option-2-attach-an-existing-disk) 중 하나를 연결하려면 다음 지침에 따라 계속합니다.

## <a name="option-1-attach-and-initialize-a-new-disk"></a>옵션 1: 새 디스크 연결 및 초기화

1. **디스크** 블레이드에서 **새 연결**을 클릭합니다.
2. 기본 설정을 검토하고 필요에 따라 업데이트한 다음 **확인**을 클릭합니다.

   ![디스크 설정 검토](./media/attach-disk/attach-new.png)

3. Azure가 디스크를 만들고 가상 머신에 연결하면 가상 머신의 디스크 설정의 **데이터 디스크**아래에 새 디스크가 나열됩니다.

### <a name="initialize-a-new-data-disk"></a>새 데이터 디스크 초기화

1. 가상 머신에 연결합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
2. 가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **파일 및 Storage 서비스**를 선택합니다.

    ![서버 관리자 열기](../media/attach-disk-portal/fileandstorageservices.png)

3. **디스크**를 선택합니다.
4. **디스크** 섹션은 디스크를 나열합니다. 대부분의 경우 가상 머신에는 디스크 0, 디스크 1, 디스크 2가 있습니다. 디스크 0은 운영 체제 디스크이고, 디스크 1은 임시 디스크이며, 디스크 2는 가상 머신에 새로 연결한 데이터 디스크입니다. 데이터 디스크는 파티션을 **알 수 없음**으로 나열합니다.

 디스크를 마우스 오른쪽 단추로 클릭한 다음 **초기화**를 선택합니다.

5. 디스크가 초기화 될 때 모든 데이터를 삭제된다고 알려 줍니다. **예** 를 클릭하여 경고를 확인하고 디스크를 초기화합니다. 완료되면 파티션이 **GPT**로 나열됩니다. 디스크를 다시 마우스 오른쪽 단추로 클릭하고 **새 볼륨**을 선택합니다.

6. 기본값을 사용하여 마법사를 완료합니다. 마법사를 완료하면 새 볼륨이 **볼륨** 섹션에 나열됩니다. 이제 디스크가 온라인 상태이며 데이터를 저장할 준비가 완료되었습니다.

    ![볼륨 초기화됨](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>옵션 2: 기존 디스크 연결
1. **디스크** 블레이드에서 **기존 연결**을 클릭합니다.
2. **기존 디스크 연결**에서 **위치**를 클릭합니다.

   ![기존 디스크 연결](./media/attach-disk/attachexistingdisksettings.png)
3. **Storage 계정**에서 계정 및 .vhd 파일을 보관하는 컨테이너를 선택합니다.

   ![VHD 위치 찾기](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. .vhd 파일을 업로드합니다.
5. **기존 디스크 연결**에서 방금 선택한 파일이 **VHD 파일** 아래에 나열됩니다. **확인**을 클릭합니다.
6. Azure가 디스크를 가상 머신에 연결한 후 가상 머신의 디스크 설정의 **데이터 디스크**아래에 해당 디스크가 나열됩니다.

## <a name="use-trim-with-standard-storage"></a>표준 저장소와 TRIM 사용

표준 저장소(HDD)를 사용하는 경우 TRIM을 사용하도록 설정해야 합니다. TRIM은 디스크에서 사용되지 않는 블록을 삭제하므로 실제로 사용 중인 저장소에 대해 청구됩니다. TRIM을 사용하면 큰 파일의 삭제로 발생하는 사용되지 않는 블록을 포함하여 비용을 절약할 수 있습니다.

TRIM 설정을 확인하도록 이 명령을 실행할 수 있습니다. Windows VM에서 명령 프롬프트를 열어 다음을 입력합니다.

```
fsutil behavior query DisableDeleteNotify
```

명령이 0을 반환하는 경우 TRIM이 올바르게 활성화됩니다. 1을 반환하는 경우, 다음 명령을 실행하여 TRIM을 사용하도록 설정합니다.
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>다음 단계
응용 프로그램이 데이터를 저장하는 데 D: 드라이브를 사용해야 하면 [Windows 임시 디스크의 드라이브 문자를 변경](../../virtual-machines-windows-change-drive-letter.md)할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스
[가상 머신용 디스크 및 VHD에 대하여](../../virtual-machines-linux-about-disks-vhds.md)
