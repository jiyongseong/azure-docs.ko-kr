---
title: Windows VM에 관리되는 데이터 디스크 연결 - Azure | Microsoft Docs
description: 리소스 관리자 배포 모델을 사용하여 Azure Portal에서 Windows VM에 신규 관리되는 데이터 디스크를 연결하는 방법입니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 14721b2f2bc7913c2b7eadfc5ee801a223201ea9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Azure Portal에서 Windows VM에 관리되는 데이터 디스크를 연결하는 방법

이 문서에서는 Azure Portal에서 새롭게 관리되는 데이터 디스크를 Windows 가상 머신에 연결하는 방법을 보여줍니다. 이 작업을 수행 하기 전에 다음 팁을 검토하세요.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](sizes.md)를 참조하세요.
* 새 디스크의 경우 Azure가 디스크를 연결할 때 생성하므로 먼저 생성하지 않아도 됩니다.

또한 [Powershell을 사용하여 데이터 디스크를 연결](attach-disk-ps.md)할 수 있습니다.



## <a name="add-a-data-disk"></a>데이터 디스크 추가
1. 왼쪽 메뉴에서 **Virtual Machines**를 클릭합니다.
2. 목록에서 가상 머신을 선택합니다.
3. 가상 머신 페이지에서 **디스크**를 클릭합니다.
4. **디스크** 페이지에서 **+ 데이터 디스크 추가**를 클릭합니다.
5. 새 디스크에 대한 드롭다운에서 **디스크 만들기**를 선택합니다.
6. **관리 디스크 만들기** 페이지에서 디스크의 이름을 입력하고 필요에 따라 다른 설정을 조정합니다. 완료하면 **만들기**를 클릭합니다.
7. **디스크** 페이지에서 **저장**을 클릭하여 VM에 대한 새 디스크 구성을 저장합니다.
6. Azure가 디스크를 만들고 가상 머신에 연결하면 **데이터 디스크** 아래에서 가상 머신의 디스크 설정에 새 디스크가 나열됩니다.


## <a name="initialize-a-new-data-disk"></a>새 데이터 디스크 초기화

1. VM에 연결합니다.
1. VM 내에서 시작 메뉴를 클릭하고 **diskmgmt.msc**를 입력한 다음 **Enter** 키를 누릅니다. 디스크 관리 스냅인이 열립니다.
2. 디스크 관리에서 초기화되지 않은 새 디스크가 있다고 인식하고 **디스크 초기화** 창이 팝업됩니다.
3. 새 디스크가 선택되어 있는지 확인하고 **확인**을 클릭하여 초기화합니다.
4. 새 디스크가 **할당되지 않음**으로 나타납니다. 디스크의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 **새 단순 볼륨**을 선택합니다. **새 단순 볼륨 마법사**가 열립니다.
5. 모든 기본값을 유지하여 마법사를 계속 진행하고 완료하면 **마침**을 선택합니다.
6. 디스크 관리를 닫습니다.
7. 새 디스크를 사용하려면 먼저 포맷해야 한다는 팝업이 표시됩니다. **디스크 포맷**을 클릭합니다.
8. **새 디스크 포맷** 대화 상자에서 설정을 확인하고 **시작**을 클릭합니다.
9. 디스크를 포맷하면 모든 데이터가 지워진다는 경고가 표시됩니다. **확인**을 클릭합니다.
10. 포맷이 완료되면 **확인**을 클릭합니다.

## <a name="use-trim-with-standard-storage"></a>표준 저장소와 TRIM 사용

표준 저장소(HDD)를 사용하는 경우 TRIM을 사용하도록 설정해야 합니다. TRIM은 디스크에서 사용되지 않는 블록을 삭제하므로 실제로 사용 중인 저장소에 대해 청구됩니다. 큰 파일을 만들고 삭제하는 경우 비용을 절감할 수 있습니다. 

TRIM 설정을 확인하도록 이 명령을 실행할 수 있습니다. Windows VM에서 명령 프롬프트를 열어 다음을 입력합니다.

```
fsutil behavior query DisableDeleteNotify
```

명령이 0을 반환하는 경우 TRIM이 올바르게 활성화됩니다. 1을 반환하는 경우, 다음 명령을 실행하여 TRIM을 사용하도록 설정합니다.
```
fsutil behavior set DisableDeleteNotify 0
```

디스크에서 데이터를 삭제한 후 TRIM으로 조각 모음을 실행하여 TRIM 작업이 제대로 플러시되는지 확인할 수 있습니다.

```
defrag.exe <volume:> -l
```

볼륨을 포맷하여 전체 볼륨이 잘리는지도 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
응용 프로그램이 데이터를 저장하는 데 D: 드라이브를 사용해야 하면 [Windows 임시 디스크의 드라이브 문자를 변경](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)할 수 있습니다.
