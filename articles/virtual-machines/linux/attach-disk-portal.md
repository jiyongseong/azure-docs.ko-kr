---
title: Linux VM에 데이터 디스크 연결 | Microsoft Docs
description: 포털을 사용하여 새 또는 기존 데이터 디스크를 Linux VM에 연결합니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 4acfe53d68db3192c1f6c3c9e5f91b55bd5df7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>포털을 사용하여 데이터 디스크를 Linux VM에 연결 
이 문서에서는 Azure 포털을 통해 신규 및 기존 디스크를 Linux 가상 머신에 연결하는 방법을 보여줍니다. 또한 [Azure Portal에서 Windows VM에 데이터 디스크를 연결](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다. 

VM에 디스크를 연결하기 전에 다음 팁을 검토합니다.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 프리미엄 저장소를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 머신이 필요합니다. 이러한 가상 머신과 함께 프리미엄 및 표준 디스크를 모두 사용할 수 있습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [Premium Storage: Azure Virtual Machine 작업을 위한 고성능 저장소](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 가상 머신에 연결된 디스크는 실제로 Azure에 저장된 .vhd 파일입니다. 자세한 내용은 [가상 머신용 디스크 및 VHD 정보](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.


## <a name="find-the-virtual-machine"></a>가상 머신 찾기
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **Virtual Machines**를 클릭합니다.
3. 목록에서 가상 머신을 선택합니다.
4. 가상 머신 페이지의 **Essentials**에서 **디스크**를 클릭합니다.
   
    ![디스크 설정 열기](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>새 디스크 연결

1. **디스크** 창에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **이름**에 대한 드롭다운 메뉴를 클릭하고 **디스크 만들기**를 선택합니다.

    ![Azure Managed Disks 만들기](./media/attach-disk-portal/create-new-md.png)

3. 관리되는 디스크에 대한 이름을 입력합니다. 기본 설정을 검토하고 필요에 따라 업데이트한 다음 **만들기**를 클릭합니다.
   
   ![디스크 설정 검토](./media/attach-disk-portal/create-new-md-settings.png)

4. **저장**을 클릭하여 관리되는 디스크를 만들고 VM 구성을 업데이트합니다.

   ![새 Azure Managed Disk 저장](./media/attach-disk-portal/confirm-create-new-md.png)

5. Azure가 디스크를 만들고 가상 머신에 연결하면 가상 머신의 디스크 설정의 **데이터 디스크**아래에 새 디스크가 나열됩니다. 관리되는 디스크는 최상위 수준 리소스이므로 디스크는 리소스 그룹의 루트에 나타납니다.

   ![리소스 그룹의 Azure Managed Disk](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>기존 디스크 연결
1. **디스크** 창에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **이름**에 대한 드롭다운 메뉴를 클릭하여 Azure 구독에 액세스할 수 있는 기존 관리되는 디스크의 목록을 봅니다. 연결할 관리되는 디스크를 선택합니다.

   ![기존 Azure Managed Disk 연결](./media/attach-disk-portal/select-existing-md.png)

3. **저장**을 클릭하여 기존 관리되는 디스크를 연결하고 VM 구성을 업데이트합니다.
   
   ![Azure Managed Disk 업데이트 저장](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure가 디스크를 가상 머신에 연결한 후 가상 머신의 디스크 설정의 **데이터 디스크**아래에 해당 디스크가 나열됩니다.



## <a name="next-steps"></a>다음 단계
[Azure CLI를 사용해서도 데이터 디스크를 연결](add-disk.md)할 수 있습니다.
