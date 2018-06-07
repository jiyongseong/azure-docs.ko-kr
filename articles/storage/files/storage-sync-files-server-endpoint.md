---
title: Azure File Sync(미리 보기) 서버 엔드포인트 추가/제거 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 26e4af814bad988da02d4e0cf36f17e1beec872e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187745"
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Azure File Sync(미리 보기) 서버 엔드포인트 추가/제거
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure 파일에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

*서버 엔드포인트*는 서버 볼륨 또는 볼륨의 루트에 있는 폴더와 같이, *등록된 서버*의 특정 위치를 나타냅니다. 해당 네임스페이스가 겹치지만 않으면 여러 서버 엔드포인트가 같은 볼륨에 있을 수 있습니다(예: F:\sync1 및 F:\sync2). 각 서버 엔드포인트에 대해 개별적으로 클라우드 계층화 정책을 구성할 수 있습니다. 기존 파일 집합이 있는 서버 위치를 동기화 그룹에 서버 엔드포인트로 추가하는 경우 해당 파일은 동기화 그룹의 다른 엔드포인트에 이미 있는 다른 파일에 병합됩니다.

종단 간 Azure 파일 동기화를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화(미리 보기)를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
서버 엔드포인트를 만들려면 먼저 다음 조건이 충족되는지 확인해야 합니다. 
- 서버에 Azure 파일 동기화 에이전트가 설치되고 등록되어 있습니다. Azure 파일 동기화 에이전트를 설치하기 위한 지침은 [Azure 파일 동기화(미리 보기)에서 서버 등록/등록 취소](storage-sync-files-server-registration.md) 문서를 참조하세요. 
- 저장소 동기화 서비스가 배포되었는지 확인합니다. 저장소 동기화 서비스를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화(미리 보기)를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요. 
- 동기화 그룹을 배포했는지 확인합니다. [동기화 그룹을 만드는](storage-sync-files-deployment-guide.md#create-a-sync-group) 방법을 알아보세요.
- 서버가 인터넷에 연결되어 있고 Azure가 액세스 가능한지 확인합니다. 서버와 서비스 간의 모든 통신에 포트 443을 사용합니다.

## <a name="add-a-server-endpoint"></a>서버 엔드포인트 추가
서버 엔드포인트를 추가하려면 원하는 동기화 그룹으로 이동한 후 "서버 엔드포인트 추가"를 선택합니다.

![동기화 그룹 창에서 새 서버 엔드포인트 추가](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

**서버 끝점 추가**에서 다음 정보가 필요합니다.

- **등록된 서버**: 서버 엔드포인트를 만들 서버 또는 클러스터의 이름입니다.
- **경로**: 동기화 그룹의 일부분으로 동기화할 Windows Server의 경로입니다.
- **클라우드 계층화**: 덜 자주 사용하거나 액세스하는 파일을 Azure Files로 계층화되도록 하는 클라우드 계층화를 사용하거나 사용하지 않도록 설정하는 스위치입니다.
- **사용 가능한 볼륨 공간**: 서버 엔드포인트가 있는 볼륨에 예약하려는 사용 가능한 공간의 크기입니다. 예를 들어 단일 서버 엔드포인트가 있는 볼륨에서 사용 가능한 볼륨 공간을 50%로 설정하는 경우 데이터 양의 절반 정도가 Azure Files로 계층화됩니다. 클라우드 계층화를 사용하도록 설정할지 여부에 관계없이, Azure 파일 공유는 항상 동기화 그룹에 데이터의 전체 복사본을 유지합니다.

**만들기**를 선택하여 서버 엔드포인트를 추가합니다. 동기화 그룹의 네임스페이스 내에 있는 파일은 이제 동기화 상태를 유지합니다. 

## <a name="remove-a-server-endpoint"></a>서버 엔드포인트 제거
서버 엔드포인트에 대해 클라우드 계층화를 사용하도록 설정하면 파일을 Azure 파일 공유로 *계층화*됩니다. 이렇게 하면 온-프레미스 파일 공유가 데이터 집합의 전체 복사본이 아닌 캐시로 사용될 수 있으므로 파일 서버의 공간이 효율적으로 사용됩니다. 그러나 **계층화된 파일이 아직 서버에서 로컬로 존재하는 서버 엔드포인트를 제거하면 해당 파일에 액세스할 수 없게 됩니다**. 따라서 온-프레미스 파일 공유에서 파일에 계속 액세스해야 하는 경우 서버 엔드포인트 삭제를 계속하기 전에 Azure Files에서 모든 계층화된 파일을 회수해야 합니다. 

이 작업에는 다음과 같이 PowerShell cmdlet을 사용할 수 있습니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> 서버를 호스트하는 로컬 볼륨에 모든 계층화된 데이터를 기억할 충분한 사용 가능한 공간이 없으면 `Invoke-StorageSyncFileRecall` cmdlet이 실패합니다.  

서버 엔드포인트를 제거하려면

1. 서버가 등록되어 있는 저장소 동기화 서비스로 이동합니다.
2. 원하는 동기화 그룹으로 이동합니다.
3. 저장소 동기화 서비스의 동기화 그룹에서 원하는 서버 엔드포인트를 제거합니다. 이 작업을 수행하려면 동기화 그룹 창에서 관련 서버 엔드포인트를 마우스 오른쪽 단추로 클릭합니다.

    ![동기화 그룹에서 서버 엔드포인트 제거](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화(미리 보기)에서 서버 등록/등록 취소](storage-sync-files-server-registration.md)
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
