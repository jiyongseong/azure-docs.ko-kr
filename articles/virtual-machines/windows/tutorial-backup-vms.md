---
title: 자습서 - Azure Portal에서 Windows 가상 머신 백업 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Backup으로 Windows 가상 머신을 보호하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6ae014597a89c75e4426715227bbb19f1e98a438
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>자습서: Azure의 Windows 가상 머신을 위한 파일 백업 및 복원

정기적으로 백업을 수행하여 데이터를 보호할 수 있습니다. Azure Backup은 지역 중복 복구 자격 증명 모음에 저장되는 복구 지점을 만듭니다. 복구 지점에서 복원하는 경우 전체 VM 또는 특정 파일을 복원할 수 있습니다. 이 문서에서는 Windows Server 및 IIS를 실행하는 VM으로 단일 파일을 복원하는 방법에 대해 설명합니다. 사용할 VM이 아직 없는 경우 [Windows 빠른 시작](quick-create-portal.md)을 사용하여 만들 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 백업 만들기
> * 매일 백업 예약
> * 백업에서 파일 복원

## <a name="backup-overview"></a>Backup 개요

Azure Backup 서비스에서 백업 작업을 시작하면 백업 확장을 트리거하여 특정 시점 스냅숏을 만듭니다. Azure Backup 서비스는 _VMSnapshot_ 확장을 사용합니다. VM을 실행하는 경우 확장은 첫 번째 VM 백업 중에 설치됩니다. VM이 실행되고 있지 않을 경우 Backup 서비스가 기본 저장소의 스냅숏을 생성합니다(VM이 중지되었을 때는 응용 프로그램 쓰기가 수행되지 않음).

Windows VM의 스냅숏을 생성할 때 Backup 서비스는 가상 머신의 디스크에 대한 일관된 스냅숏을 가져오도록 VSS(볼륨 섀도 복사본 서비스)와 조정됩니다. Azure Backup 서비스가 스냅숏을 생성하면 데이터가 자격 증명 모음으로 전송됩니다. 효율성을 극대화하기 위해 이 서비스는 이전 백업 이후에 변경된 데이터 블록만 식별하여 전송합니다.

데이터 전송이 완료되면 스냅숏이 제거되고 복구 지점이 생성됩니다.


## <a name="create-a-backup"></a>백업 만들기
간단한 예약된 매일 백업을 Recovery Services 자격 증명 모음에 만듭니다. 

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **가상 머신**를 선택합니다. 
3. 목록에서 백업할 VM을 선택합니다.
4. VM 블레이드의 **설정** 섹션에서 **Backup**을 클릭합니다. **백업 사용** 블레이드가 열립니다.
5. **Recovery Services 자격 증명 모음**에서 **새로 만들기**를 클릭하고 새 자격 증명 모음의 이름을 제공합니다. 새 자격 증명 모음이 가상 머신과 동일한 리소스 그룹과 위치에 만들어집니다.
6. **Backup 정책**을 클릭합니다. 이 예제에서는 기본값을 그대로 유지하고 **확인**을 클릭합니다.
7. **Backup 사용** 블레이드에서 **Backup 사용**을 클릭합니다. 이렇게 하면 기본 일정에 따라 매일 백업이 만들어집니다.
10. 초기 복구 지점을 만들려면 **Backup** 블레이드에서 **지금 Backup**을 클릭합니다.
11. **지금 Backup** 블레이드에서 달력 모양 아이콘을 클릭하고 달력 컨트롤을 사용하여 이 복구 지점을 유지할 마지막 날을 선택하고 **Backup**을 클릭합니다.
12. VM에 대한 **Backup** 블레이드에서 완료된 복구 지점의 수가 표시됩니다.

    ![복구 지점](./media/tutorial-backup-vms/backup-complete.png)
    
첫 번째 백업에는 약 20분 정도 걸립니다. 백업이 완료되면 이 자습서의 다음 부분으로 진행합니다.

## <a name="recover-a-file"></a>파일 복구

실수로 파일을 삭제하거나 변경한 경우 파일 복구를 사용하여 백업 자격 증명 모음에서 파일을 복구할 수 있습니다. 파일 복구는 VM에서 실행되는 스크립트를 사용하여 복구 지점을 로컬 드라이브로 탑재합니다. 이러한 드라이브는 12시간 동안 탑재된 상태로 유지되므로 복구 지점에서 파일을 복사하여 VM에 복원할 수 있습니다.  

이 예제에서는 IIS에 대한 기본 웹 페이지에 사용되는 이미지 파일을 복구하는 방법을 보여 줍니다. 

1. 브라우저를 열고 기본 IIS 페이지를 표시하는 VM의 IP 주소에 연결합니다.

    ![기본 IIS 웹 페이지](./media/tutorial-backup-vms/iis-working.png)

2. VM에 연결합니다.
3. VM에서 **파일 탐색기**를 열고 \inetpub\wwwroot로 이동한 다음, 파일 **iisstart.png**를 삭제합니다.
4. 로컬 컴퓨터에서 브라우저를 새로 고쳐서 기본 IIS 페이지에 대한 이미지가 사라졌는지 확인합니다.

    ![기본 IIS 웹 페이지](./media/tutorial-backup-vms/iis-broken.png)

5. 로컬 컴퓨터에서 새 탭을 열고 [Azure Portal](https://portal.azure.com)로 이동합니다.
6. 왼쪽 메뉴에서 **가상 머신**을 선택하고 목록에서 VM 형식을 선택합니다.
8. VM 블레이드의 **설정** 섹션에서 **Backup**을 클릭합니다. **Backup** 블레이드가 열립니다. 
9. 블레이드 위쪽의 메뉴에서 **파일 복구**를 선택합니다. **파일 복구** 블레이드가 열립니다.
10. **1단계: 복구 지점 선택**의 드롭다운에서 복구 지점을 선택합니다.
11. **2단계: 스크립트를 다운로드하여 파일 찾아보기 및 복구**에서 **실행 파일 다운로드** 단추를 클릭합니다. 파일을 **다운로드** 폴더에 저장합니다.
12. 로컬 컴퓨터에서 **파일 탐색기**를 열고 **다운로드** 폴더로 이동하여 다운로드한 .exe 파일을 복사합니다. 파일 이름은 VM 이름을 앞에 붙이게 됩니다. 
13. VM에서(RDP 연결을 통해) VM의 데스크톱에 .exe 파일을 붙여넣습니다. 
14. VM의 바탕 화면으로 이동한 다음 .exe를 두 번 클릭합니다. 그러면 명령 프롬프트를 시작한 다음, 사용자가 액세스할 수 있는 파일 공유로 복구 지점을 탑재합니다. 공유 만들기가 끝나면 **q**를 입력하여 명령 프롬프트를 닫습니다.
15. VM에서 **파일 탐색기**를 열어 파일 공유에 사용된 드라이브 문자로 이동합니다.
16. \inetpub\wwwroot로 이동하여 파일 공유의 **iisstart.png**를 복사하여 \inetpub\wwwroot에 붙여넣습니다. 예를 들어 F:\inetpub\wwwroot\iisstart.png를 복사하여 c:\inetpub\wwwroot에 붙여넣어 파일을 복구합니다.
17. 로컬 컴퓨터에서 IIS 기본 페이지를 보여 주는 VM의 IP 주소에 연결된 브라우저 탭을 엽니다. CTRL+F5를 눌러 브라우저 페이지를 새로 고칩니다. 이제 이미지가 복원되었음을 확인할 수 있습니다.
18. 로컬 컴퓨터에서 Azure Portal의 브라우저 탭으로 돌아가서 **3단계: 복구 후 디스크 분리**에서 **디스크 분리** 단추를 클릭합니다. 이 단계를 수행하지 않으면 12시간 후에 탑재 지점에 대한 연결이 자동으로 끊깁니다. 12시간이 지나면 새 스크립트를 다운로드하여 새 탑재 지점을 만들어야 합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 백업 만들기
> * 매일 백업 예약
> * 백업에서 파일 복원

가상 머신 모니터링에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [가성 머신 제어](tutorial-govern-resources.md)









