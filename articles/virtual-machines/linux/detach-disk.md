---
title: Linux VM에서 데이터 디스크 분리 - Azure | Microsoft Docs
description: CLI 2.0 또는 Azure Portal을 사용하여 Azure의 가상 머신에서 디스크를 분리하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 572fe5bd4d6d79bb9dd94353732e273282e2a0af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Linux 가상 머신에서 데이터 디스크를 분리하는 방법

가상 머신에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 머신에서 디스크가 제거되지만, 저장소에서는 제거되지 않습니다. 

> [!WARNING]
> 디스크를 분리해도 자동으로 삭제되지 않습니다. 프리미엄 저장소를 구독하는 경우 디스크에 대한 저장소 요금이 계속 부과됩니다. 자세한 내용은 [Premium Storage 사용 시 가격 책정 및 청구](../windows/premium-storage.md#pricing-and-billing)를 참조하세요. 
> 
> 

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 머신에 다시 연결할 수 있습니다.  

## <a name="detach-a-data-disk-using-cli-20"></a>CLI 2.0을 사용하여 데이터 디스크 분리

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

디스크가 저장소에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.


## <a name="detach-a-data-disk-using-the-portal"></a>포털을 사용하여 데이터 디스크 분리
1. 왼쪽 메뉴에서 **Virtual Machines**을 선택합니다.
2. 분리할 데이터 디스크가 있는 가상 컴퓨터를 선택하고 **중지**를 클릭하여 VM을 할당 취소합니다.
3. 가상 머신 창에서 **디스크**를 선택합니다.
4. **디스크** 창 상단에서 **편집**을 선택합니다.
5. **디스크** 창에서 분리할 데이터 디스크의 오른쪽 끝에 있는 ![분리 단추 이미지](./media/detach-disk/detach.png) 분리 단추를 클릭합니다.
5. 디스크를 제거한 후에 창 상단에서 저장을 클릭합니다.
6. 가상 머신 창에서 **개요**를 클릭한 다음 창 상단에서 **시작** 단추를 클릭하여 VM을 다시 시작합니다.

디스크가 저장소에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.


## <a name="next-steps"></a>다음 단계
데이터 디스크를 다시 사용하려는 경우 [다른 VM에 연결](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)할 수 있습니다.

