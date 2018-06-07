---
title: "StorSimple 가상 배열 업데이트 0.6 릴리스 정보| Microsoft Docs"
description: "업데이트 0.6을 실행하는 StorSimple 가상 배열에 대한 중대한 미해결 문제 및 해결 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: af202cf652300ee7897eb2dede33f38058fc2837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple 가상 배열 업데이트 0.6 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 Microsoft Azure StorSimple 가상 배열 업데이트에 대한 중요한 미해결 문제 및 해결된 문제를 식별합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. StorSimple 가상 배열을 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

업데이트 0.6은 소프트웨어 버전 **10.0.10293.0**에 해당합니다.

> [!IMPORTANT]
> - 업데이트는 작업 중단 업데이트이며 장치를 다시 시작합니다. I/O가 진행 중인 경우 장치에 가동 중지 시간이 발생합니다. 업데이트를 적용하는 방법에 대한 자세한 내용은 [업데이트 0.6 설치](storsimple-virtual-array-install-update-06.md)를 참조하세요.
>
> - 중요 보안 수정이 포함되어 있으므로 즉시 업데이트 0.6을 설치하는 것이 좋습니다.


## <a name="whats-new-in-the-update-06"></a>업데이트 0.6의 새로운 기능
업데이트 0.6은 중요 업데이트이므로 즉시 배포되어야 합니다. 이 업데이트에는 다음 수정이 포함되어 있습니다. 

- **Windows 보안 수정** - 이 릴리스에는 **Windows 중요 보안 수정**이 있습니다. 보안 문제 및 관련된 수정에 대한 자세한 내용은 다음 보안 업데이트를 검토하세요.
    - [2016년 12월 Windows 8.1 및 Windows Server 2012 R2용 보안 전용 품질 업데이트](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017년 3월 Windows 8.1 및 Windows Server 2012 R2용 보안 전용 품질 업데이트](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [2017년 5월 9일-KB4019213(보안 전용 업데이트)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **복원 수정** - 이전 릴리스에는 복원이 완료되지 못하게 하는 버그가 있었습니다. 이 버그는 이 릴리스에서 수정되었습니다.


## <a name="issues-fixed-in-the-update-06"></a>업데이트 0.6에서 해결된 문제

다음 표에서는 이 릴리스에서 수정된 문제를 간략하게 설명합니다.

| 번호 | 기능 | 문제 |
| --- | --- | --- |
| 1 |보안| 이 릴리스에는 중요 Windows 보안 업데이트가 포함되어 있습니다. 이 업데이트를 즉시 설치하는 것이 좋습니다.|
| 2 |복원| 복원하는 동안 복원 작업을 완료하지 못하게 하는 경합 상태가 있었습니다. 버그 수정을 통해 이 경합 상태를 해결했습니다.|


## <a name="known-issues-in-the-update-06"></a>업데이트 0.6의 알려진 문제

다음 표에서는 이전 릴리스에서 언급된 문제 릴리스를 포함하여 StorSimple 가상 배열에 대해 알려진 문제를 간략하게 제공합니다.

| 번호 | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |업데이트 |미리 보기 릴리스에서 만든 가상 장치는 지원되는 일반 사용 가능 버전으로 업데이트될 수 없습니다. |이러한 가상 장치는 재해 복구(DR) 워크플로를 사용하여 일반 사용 가능 릴리스로 장애 조치되어야 합니다. |
| **2.** |프로비전된 데이터 디스크 |특정한 크기의 데이터 디스크를 프로비전하고 해당 StorSimple 가상 장치를 만들고 나면, 데이터 크기를 확장하거나 축소하지 말아야 합니다. 이를 시도하면 장치의 로컬 계층에서 모든 데이터가 손실됩니다. | |
| **3.** |그룹 정책 |장치가 도메인에 가입될 때 그룹 정책을 적용하면 장치 작업에 부정적인 영향이 있을 수 있습니다. |가상 배열이 Active Directory용 자체 OU(조직 구성 단위)에 있으며 GPO(그룹 정책 개체)가 적용되지 않았는지 확인합니다. |
| **4.** |로컬 웹 UI |Internet Explorer (IE ESC)에서 향상된 보안 기능이 활성화된 경우 문제 해결 또는 유지 관리와 같은 일부 로컬 웹 UI 페이지가 적절하게 작동하지 않을 수 있습니다. 해당 페이지의 단추도 작동하지 않을 수 있습니다. |Internet Explorer의 보안 강화 기능을 해제하십시오. |
| **5.** |로컬 웹 UI |Hyper-V 가상 컴퓨터에서 웹 UI의 네트워크 인터페이스는 10Gbps 인터페이스로 표시됩니다. |이러한 동작은 Hyper-V를 반영합니다. Hyper-V는 가상 네트워크 어댑터에 10Gbps를 항상 표시합니다. |
| **6.** |계층화된 볼륨 또는 공유 |StorSimple 계층화된 볼륨에서 응용 프로그램에 대한 바이트 범위 잠금은 지원되지 않습니다. 바이트 범위 잠금을 사용하도록 설정하면 StorSimple 계층화가 실행되지 않습니다. |권장된 조치는 다음과 같습니다. <br></br>응용 프로그램 논리에서 바이트 범위 잠금을 해제합니다.<br></br>계층화된 볼륨이 아니라 로컬로 고정된 볼륨에 이 응용 프로그램에 대한 데이터를 배치하도록 선택합니다.<br></br>*주의*: 로컬로 고정된 볼륨을 사용하고 바이트 범위 잠금을 사용할 경우 복원이 완료되기 전에 로컬로 고정된 볼륨이 온라인 상태가 될 수 있습니다. 이러한 경우, 복원이 진행 중이면, 복원이 완료될 때까지 반드시 기다려야 합니다. |
| **7.** |계층화된 공유 |큰 파일로 작업하면 계층화가 매우 느려질 수 있습니다. |큰 파일을 사용하는 경우 가장 큰 파일이 공유 크기의 3% 보다 작은 것이 좋습니다. |
| **8.** |공유에 사용된 용량 |공유에 데이터가 없어도 공유 사용량이 표시될 수 있습니다. 이 사용량은 공유에 사용된 용량에 메타데이터가 포함되기 때문입니다. | |
| **9.** |재해 복구 |파일 서버에 대한 재해 복구는 원본 장치의 도메인과 같은 도메인에만 수행할 수 있습니다. 다른 도메인의 대상 장치로 재해 복구는 이번 릴리스에서 지원되지 않습니다. |이후 릴리스에서 구현됩니다. 자세한 내용은 [StorSimple 가상 배열에 대한 장애 조치(Failover) 및 재해 복구](storsimple-virtual-array-failover-dr.md)를 참조하세요. |
| **10.** |Azure PowerShell |이번 릴리스에서는 Azure PowerShell을 통해 StorSimple 가상 장치를 관리할 수 없습니다. |가상 장치에 대한 모든 관리는 Azure Portal 및 로컬 웹 UI를 통해 수행됩니다. |
| **11.** |암호 변경 |가상 배열 장치 콘솔은 en-us 키보드 형식의 입력만 허용합니다. | |
| **12.** |CHAP |CHAP 자격 증명은 일단 한 번 만들면 제거할 수 없습니다. 또한 CHAP 자격 증명을 수정하면 볼륨을 오프라인 상태로 만든 다음 온라인 상태로 변경해야 변경사항이 적용됩니다. |이 문제는 이후 릴리스에서 해결됩니다. |
| **13.** |iSCSI 서버 |iSCSI 볼륨에 대해 표시되는 '사용된 저장소'가 StorSimple Device Manager 서비스와 iSCSI 호스트에서 다를 수 있습니다. |iSCSI 호스트에는 파일 시스템 보기가 있습니다.<br></br>볼륨이 최대 크기일 때 장치에서는 블록이 할당된 것을 봅니다. |
| **14.** |파일 서버 |폴더의 파일에 연결된 ADS(대체 데이터 스트림)가 있는 경우 ADS는 재해 복구, 복제 및 항목 수준 복구를 통해 백업 또는 복원되지 않습니다. | |
| **15.** |파일 서버 |기호 링크는 지원되지 않습니다. | |
| **16.** |파일 서버 |Windows EFS(파일 시스템 암호화)에 의해 보호되는 파일을 StorSimple Virtual Array 파일 서버에 복사하거나 저장하지 마세요. 그러면 지원되지 않는 구성이 발생합니다.  | |
| **17.** |업데이트 |로컬 UI를 통해 핫픽스를 설치하려고 할 때 오류 코드: 2359302(16진수 0x240006)가 표시되면 해당 핫픽스가 이미 장치에 설치되어 있음을 의미합니다.   | |

## <a name="next-step"></a>다음 단계
StorSimple 가상 배열에 [업데이트 0.6 설치](storsimple-virtual-array-install-update-06.md)

## <a name="references"></a>참조
이전 릴리스 정보를 찾으시나요? 다음으로 이동합니다.

* [StorSimple 가상 배열 업데이트 0.5 릴리스 정보](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.4 릴리스 정보](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.3 릴리스 정보](storsimple-ova-update-03-release-notes.md)
* [StorSimple 가상 배열 업데이트 0.1 및 0.2 릴리스 정보](storsimple-ova-update-01-release-notes.md)
* [StorSimple 가상 배열 일반 공급 릴리스 정보](storsimple-ova-pp-release-notes.md)

