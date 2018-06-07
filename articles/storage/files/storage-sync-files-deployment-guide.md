---
title: "Azure File Sync 배포(미리 보기) | Microsoft Docs"
description: "Azure File Sync를 배포하는 방법을 처음부터 끝까지 알아봅니다."
services: storage
documentationcenter: 
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
ms.openlocfilehash: d5864b8df85a5b3cec086d4cb2edc6d288f1639a
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="deploy-azure-file-sync-preview"></a>Azure File Sync 배포(미리 보기)
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화합니다. Azure File Sync는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에 설명된 단계를 완료하기 전에 [Azure Files 배포에 대한 계획](storage-files-planning.md) 및 [Azure File Sync 배포에 대한 계획](storage-sync-files-planning.md)을 읽어보는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소
* Azure File Sync를 배포하려는 동일한 지역에 Azure Storage 계정 및 Azure 파일 공유가 있어야 합니다. 자세한 내용은 다음을 참조하세요.
    - [지역 가용성](storage-sync-files-planning.md#region-availability)에서 Azure File Sync를 참조하세요.
    - [저장소 계정 만들기](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)에서 저장소 계정을 만드는 방법에 대한 단계별 설명을 참조하세요.
    - [파일 공유 만들기](storage-how-to-create-file-share.md)에서 파일 공유를 만드는 방법에 대한 단계별 설명을 참조하세요.
* Azure File Sync와 동기화할 Windows Server 또는 Windows Server 클러스터의 지원되는 인스턴스가 하나 이상 있어야 합니다. 지원되는 Windows Server 버전에 대한 자세한 내용은 [Windows Server와의 상호 운용성](storage-sync-files-planning.md#azure-file-sync-interoperability)을 참조하세요.

## <a name="deploy-the-storage-sync-service"></a>저장소 동기화 서비스 배포 
저장소 동기화 서비스는 Azure File Sync의 최상위 Azure 리소스입니다. 저장소 동기화 서비스를 배포하려면 [Azure Portal](https://portal.azure.com/)로 이동하여 *새로 만들기*를 클릭한 후 Azure File Sync를 검색합니다. 검색 결과에서 **Azure File Sync(미리 보기)**를 선택한 후 **만들기**를 선택하여 **저장소 동기화 배포** 탭을 엽니다.

열리는 창에 다음 정보를 입력합니다.

- **이름**: 저장소 동기화 서비스의 고유 이름(구독별)입니다.
- **구독**: 저장소 동기화 서비스를 만들 구독입니다. 조직의 구성 전략에 따라 하나 이상의 구독에 대한 액세스 권한이 있을 수 있습니다. Azure 구독은 각 클라우드 서비스(예: Azure Files)에 대한 비용을 청구하는 가장 기본적인 컨테이너입니다.
- **리소스 그룹**: 리소스 그룹은 저장소 계정 또는 저장소 동기화 서비스와 같은 Azure 리소스의 논리적 그룹입니다. Azure File Sync를 위한 새 리소스 그룹을 생성하거나 기존 리소스 그룹을 사용할 수 있습니다. (HR 리소스 또는 특정 프로젝트의 리소스를 그룹화하듯이 리소스 그룹을 컨테이너로 사용하여 조직의 리소스를 논리적으로 격리하는 것이 좋습니다.)
- **위치**: Azure File Sync를 배포할 지역입니다. 이 목록에는 지원되는 지역만 표시됩니다.

작업이 끝나면 **만들기**를 선택하여 저장소 동기화 서비스를 배포합니다.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Azure File Sync에 사용할 Windows Server 준비
장애 조치(Failover) 클러스터의 서버 노드를 비롯하여 Azure File Sync에 사용할 모든 서버에 대해 다음 단계를 완료합니다.

1. **Internet Explorer 보안 강화 구성**을 사용하지 않도록 설정합니다. 초기 서버 등록에만 필요합니다. 서버가 등록된 후에 사용하도록 다시 설정할 수 있습니다.
    1. [서버 관리자]를 엽니다.
    2. **로컬 서버**를 클릭합니다.  
        ![서버 관리자 UI 왼쪽에 있는 "로컬 서버"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. **속성** 하위 창에서 **IE 보안 강화 구성** 링크를 선택합니다.  
        ![서버 관리자 UI의 "IE 보안 강화 구성" 창](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. **Internet Explorer 보안 강화 구성** 대화 상자에서 **관리자**와 **사용자**에 대해 **사용 안 함**을 선택합니다.  
        ![“해제"가 선택된 Internet Explorer 보안 강화 구성 팝업 창](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. PowerShell 5.1을 실행하고 있는지 확인합니다.\* (PowerShell 5.1은 Windows Server 2016에서 기본값입니다.) **$PSVersionTable** 개체의 **PSVersion** 속성 값을 확인하여 PowerShell 5.1.\*을 실행하고 있는지 확인할 수 있습니다.

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    대부분의 Windows Server 2012 R2 설치의 경우처럼 PSVersion 값이 5.1.\*보다 낮으면 [WMF(Windows Management Framework) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)을 다운로드한 후 설치하여 쉽게 업그레이드할 수 있습니다. Windows Server 2012 R2에 대해 다운로드하고 설치할 적절한 패키지는 **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**입니다.

3. [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)합니다. 최신 버전의 Azure PowerShell 모듈을 사용하는 것이 좋습니다.

## <a name="install-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트 설치
Azure File Sync 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. [Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257)에서 에이전트를 다운로드할 수 있습니다. 다운로드가 완료되면 MSI 패키지를 두 번 클릭하여 Azure File Sync 에이전트 설치를 시작합니다.

> [!Important]  
> 장애 조치(Failover) 클러스터에서 Azure File Sync를 사용하려는 경우 Azure File Sync 에이전트를 클러스터의 모든 노드에 설치해야 합니다.


Azure File Sync 에이전트 설치 패키지는 너무 많은 추가 프롬프트 없이 비교적 빠르게 설치됩니다. 다음을 수행하는 것이 좋습니다.
- 문제 해결 및 서버 유지 관리를 간소화하려면 기본 설치 경로(C:\Program Files\Azure\StorageSyncAgent)를 유지하세요.
- Azure File Sync를 최신 상태로 유지하도록 Microsoft 업데이트를 사용하도록 설정하세요. 기능 업데이트 및 핫픽스를 비롯한 Azure File Sync 에이전트에 대한 모든 업데이트는 Microsoft 업데이트에서 수행됩니다. Azure File Sync에 대한 최신 업데이트를 설치하는 것이 좋습니다. 자세한 내용은 [Azure File Sync 업데이트 정책](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)을 참조하세요.

Azure File Sync 에이전트 설치를 마치면 서버 등록 UI가 자동으로 열립니다. 이 서버를 Azure File Sync에 등록하는 방법을 알아보려면 다음 섹션을 참조하세요.

## <a name="register-windows-server-with-storage-sync-service"></a>저장소 동기화 서비스에 Windows Server 등록
저장소 동기화 서비스에 Windows Server를 등록하면 서버(또는 클러스터)와 저장소 동기화 서비스 간에 트러스트 관계가 설정됩니다. Azure File Sync 에이전트 설치 후 서버 등록 UI가 자동으로 열립니다. 그렇지 않은 경우 파일 위치(C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe)에서 수동으로 열 수 있습니다. 서버 등록 UI가 열리면 **로그인**을 선택하여 시작합니다.

로그인한 후에 다음 정보를 묻는 메시지가 나타납니다.

![서버 등록 UI 스크린샷](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 구독**: 저장소 동기화 서비스가 포함된 구독입니다([저장소 동기화 서비스 배포](#deploy-the-storage-sync-service) 참조). 
- **리소스 그룹**: 저장소 동기화 서비스를 포함하는 리소스 그룹입니다.
- **저장소 동기화 서비스**: 등록하려는 저장소 동기화 서비스의 이름입니다.

적절한 정보를 선택한 후 **등록**을 선택하여 서버 등록을 완료합니다. 등록 프로세스의 일부로 추가 로그인을 요구하는 메시지가 표시됩니다.

## <a name="create-a-sync-group"></a>동기화 그룹 만들기
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나 이상의 클라우드 엔드포인트와 Windows Server의 경로를 나타내는 하나의 서버 엔드포인트를 포함해야 합니다. 동기화 그룹을 만들려면 [Azure Portal](https://portal.azure.com/)에서 저장소 동기화 서비스로 이동한 후 **+동기화 그룹**을 선택합니다.

![Azure Portal에서 새 동기화 그룹 만들기](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

열리는 창에서 다음 정보를 입력하여 클라우드 엔드포인트가 있는 동기화 그룹을 만듭니다.

- **동기화 그룹 이름**: 만들 동기화 그룹의 이름입니다. 이 이름은 저장소 동기화 서비스 내에서 고유해야 하지만 사용자에게 논리적인 어떤 이름도 될 수 있습니다.
- **구독**: [저장소 동기화 서비스 배포](#deploy-the-storage-sync-service)에서 저장소 동기화 서비스를 배포한 구독입니다.
- **저장소 계정**: **저장소 계정 선택**을 선택하면 동기화할 Azure 파일 공유가 있는 저장소 계정을 선택할 수 있는 또 다른 창이 나타납니다.
- **Azure 파일 공유**: 동기화할 Azure 파일 공유의 이름입니다.

서버 엔드포인트를 추가하려면 새로 만든 동기화 그룹으로 이동한 후 **서버 엔드포인트 추가**를 클릭합니다.

![동기화 그룹 창에서 새 서버 엔드포인트 추가](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

**서버 엔드포인트 추가** 창에 다음 정보를 입력하여 서버 엔드포인트를 만듭니다.

- **등록된 서버**: 서버 엔드포인트를 만들 서버 또는 클러스터의 이름입니다.
- **경로**: 동기화 그룹의 일부분으로 동기화할 Windows Server 경로입니다.
- **클라우드 계층화**: 클라우드 계층화를 사용하거나 사용하지 않도록 설정할 스위치입니다. 클라우드 계층화를 사용하면 드물게 사용하거나 액세스하는 파일은 Azure 파일로 계층화할 수 있습니다.
- **사용 가능한 볼륨 공간**: 서버 엔드포인트가 있는 볼륨에서 예약할 여유 공간의 크기입니다. 예를 들어 단일 서버 엔드포인트가 있는 볼륨에서 사용 가능한 볼륨 공간을 50%로 설정하는 경우 데이터 양의 절반 정도가 Azure Files로 계층화됩니다. 클라우드 계층화를 사용하도록 설정할지 여부에 관계없이, Azure 파일 공유는 항상 동기화 그룹에 데이터의 전체 복사본을 유지합니다.

서버 엔드포인트를 추가하려면 **만들기**를 선택합니다. 이제 Azure 파일 공유 및 Windows Server에서 파일이 동기화 상태로 유지됩니다. 

> [!Important]  
> 동기화 그룹의 클라우드 엔드포인트 또는 서버 엔드포인트를 변경할 수 있고, 파일이 동기화 그룹의 다른 엔드포인트와 동기화되도록 할 수 있습니다. 클라우드 엔드포인트(Azure 파일 공유)를 직접 변경하는 경우 변경 사항은 먼저 Azure File Sync 변경 내용 검색 작업으로 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 자세한 내용은 [Azure Files 질문과 대답](storage-files-faq.md#afs-change-detection)을 참조하세요.

## <a name="onboarding-with-azure-file-sync"></a>Azure File Sync를 사용한 온보딩
전체 파일 충실도와 ACL(액세스 제어 목록)을 유지하면서 가동 중지 시간 없이 Azure File Sync에 처음 온보딩하는 권장 단계는 다음과 같습니다.
 
1.  저장소 동기화 서비스를 배포합니다.
2.  동기화 그룹을 만듭니다.
3.  전체 데이터 집합이 있는 서버에 Azure File Sync 에이전트를 설치합니다.
4.  해당 서버를 등록하고 공유에 서버 끝점을 만듭니다. 
5.  전체 업로드를 Azure 파일 공유(클라우드 끝점)에 동기화합니다.  
6.  초기 업로드가 완료된 후 나머지 서버에 각각 Azure File Sync 에이전트를 설치합니다.
7.  나머지 서버에 각각 새 파일 공유를 만듭니다.
8.  원하는 경우 클라우드 계층화 정책을 사용하여 새 파일 공유에 서버 끝점을 만듭니다. 이 단계를 수행하려면 초기 설정에 사용할 수 있는 추가 저장소가 필요합니다.
9.  실제 데이터 전송 없이 Azure File Sync 에이전트가 전체 네임스페이스를 신속하게 복원하도록 합니다. 전체 네임스페이스가 동기화된 후 동기화 엔진은 서버 끝점에 대한 클라우드 계층화 정책에 따라 로컬 디스크 공간을 채웁니다. 
10. 동기화를 완료하고 필요에 따라 토폴로지를 테스트합니다. 
11. 사용자와 응용 프로그램을 이 새로운 공유로 리디렉션합니다.
12. 필요에 따라 서버에서 중복 공유를 삭제할 수 있습니다.
 
초기 온보딩을 위한 추가 저장소가 없으며 기존 공유에 연결하려는 경우 Azure 파일 공유의 데이터를 사전 시드할 수 있습니다. 이 접근 방법은 가동 중지 시간이 허용되고 초기 온보딩 프로세스 중에 서버 공유의 데이터가 변경되지 않는 것이 확실한 경우에만 사용하는 것이 좋습니다. 
 
1.  온보딩 프로세스 중에 서버의 데이터를 변경할 수 없도록 합니다.
2.  SMB를 통한 데이터 전송 도구(예: Robocopy, 직접 SMB 복사)를 사용하여 서버 데이터로 Azure 파일 공유를 사전 시드합니다. AzCopy는 SMB를 통해 데이터를 업로드하지 않으므로 사전 시드에 사용할 수 없습니다.
3.  원하는 서버 끝점에서 기존 공유를 가리켜 Azure File Sync 토폴로지를 만듭니다.
4.  모든 끝점에서 동기화가 조정 프로세스를 완료하도록 합니다. 
5.  조정이 완료되면 변경 내용을 위해 공유를 열 수 있습니다.
 
현재 사전 시드 방법에는 다음과 같은 몇 가지 제한 사항이 있습니다. 
- 파일의 전체 충실도가 유지되지 않습니다. 예를 들어 파일의 ACL과 타임스탬프가 유실됩니다.
- 동기화 토폴로지가 완전히 작동되어 실행하기 전의 서버 데이터 변경으로 인해 서버 끝점에서 충돌이 발생할 수 있습니다.  
- 클라우드 끝점이 생성된 후 Azure File Sync는 초기 동기화를 시작하기 전에 클라우드의 파일을 검색하는 프로세스를 실행합니다. 이 프로세스를 완료하는 데 걸리는 시간은 네트워크 속도, 사용 가능한 대역폭, 파일 및 폴더 수와 같은 다양한 요인에 따라 달라집니다. 미리 보기 릴리스의 대략적인 추정에서 검색 프로세스는 약 10개 파일/초의 속도로 실행됩니다. 따라서 클라우드에 데이터를 사전 시드할 경우 사전 시드가 빠르게 실행되더라도 시스템이 정상적으로 실행되기까지 오랜 시간이 걸릴 수 있습니다.


## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>DFS 복제(DFS-R) 배포를 Azure File Sync로 마이그레이션
DFS-R 배포를 Azure File Sync로 마이그레이션하려면

1. 바꾸려는 DFS-R 토폴로지를 나타내는 동기화 그룹을 만듭니다.
2. 마이그레이션할 DFS-R 토폴로지의 전체 데이터 집합이 있는 서버에서 시작합니다. 해당 서버에 Azure File Sync를 설치합니다.
3. 해당 서버를 등록하고 마이그레이션할 첫 번째 서버에 대한 서버 엔드포인트를 만듭니다. 클라우드 계층화를 활성화하지 마세요.
4. Azure 파일 공유(클라우드 엔드포인트)에 대한 모든 데이터 동기화를 수행합니다.
5. 나머지 DFS-R 서버에 각각 Azure File Sync 에이전트를 설치하고 등록합니다.
6. DFS-R을 사용하지 않도록 설정합니다. 
7. 각 DFS-R 서버에 서버 엔드포인트를 만듭니다. 클라우드 계층화를 활성화하지 마세요.
8. 동기화를 완료하고 필요에 따라 토폴로지를 테스트합니다.
9. DFS-R을 사용 중지합니다.
10. 이제 클라우드 계층화를 원하는 모든 서버 엔드포인트에서 사용할 수 있습니다.

자세한 내용은 [분산 파일 시스템(DFS)과의 Azure File Sync interop](storage-sync-files-planning.md#distributed-file-system-dfs)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure File Sync 서버 끝점 추가 또는 제거](storage-sync-files-server-endpoint.md)
- [Azure File Sync로 서버 등록 또는 등록 취소](storage-sync-files-server-registration.md)
