---
title: Azure 파일 동기화(미리 보기) 문제 해결 | Microsoft Docs
description: Azure File Sync와 관련된 일반적인 문제를 해결합니다.
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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 7f3d9672e9fc152580f49cf06b431ced890d9f08
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010927"
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Azure 파일 동기화(미리 보기) 문제 해결
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화합니다. Azure File Sync는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서는 Azure File Sync 배포 시 발생할 수 있는 문제를 해결하는 데 도움을 주기 위해 작성되었습니다. 또한 문제를 좀 더 자세히 조사해야 할 경우 시스템에서 중요한 로그를 수집하는 방법을 설명합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. 이 문서의 의견 섹션입니다.
2. [Azure Storage 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft 지원 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청**을 선택합니다.

## <a name="storage-sync-service-object-management"></a>저장소 동기화 서비스 개체 관리
한 구독에서 다른 구독으로 리소스를 이동하는 경우, 파일 동기화(저장소 동기화 서비스) 리소스가 이동되지 않도록 차단됩니다. 

## <a name="agent-installation-and-server-registration"></a>에이전트 설치 및 서버 등록
### <a name="during-server-registration-get-the-error-the-term-find-azurermresource-is-not-recognized-as-the-name"></a>서버 등록을 하는 동안 "'find-AzureRMResource'라는 용어가 이름으로 인식되지 않습니다."라는 오류가 발생합니다.
문제는 find-AzureRMResource cmdlet이 AzureRM v6에서 변경되었다는 것입니다.  동기화 에이전트의 다음 버전은 AzureRM v6를 지원하도록 수정됩니다.  그때까지는 다음을 수행하여 이 문제를 해결할 수 있습니다.
1. taskmgr을 통해 현재 ServerRegistration.exe 중지
2. 관리자 권한으로 PowerShell 명령 프롬프트 실행
3. PS C:\> Uninstall-Module AzureRM
4. PS C:\> install-module -name AzureRM -RequiredVersion 5.7.0
5. C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe를 시작합니다.

<a id="agent-installation-failures"></a>**에이전트 설치 오류 해결**  
Azure File Sync 에이전트 설치가 실패할 경우 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 에이전트 설치 동안 로깅을 켭니다.

```
StorageSyncAgent.msi /l*v Installer.log
```

installer.log를 검토하여 설치 실패의 원인을 확인합니다. 

> [!Note]  
> 컴퓨터를 Microsoft 업데이트를 사용하도록 설정했으며 Windows 업데이트 서비스가 실행되고 있지 않은 경우 에이전트 설치가 실패합니다.

<a id="agent-installation-on-DC"></a>**Active Directory 도메인 컨트롤러에서 에이전트 설치가 실패합니다.** PDC 역할 소유자가 Windows Server 2008 R2 이하 OS 버전에 있는 Active Directory 도메인 컨트롤러에 동기화 에이전트를 설치하려고 하면, 동기화 에이전트를 설치할 수 없는 문제가 발생할 수 있습니다.

이 문제를 해결하려면 PDC 역할을 Windows Server 2012R2 이상을 실행 중인 다른 도메인 컨트롤러로 전송한 다음, 동기화를 설치합니다.

<a id="agent-installation-websitename-failure"></a>**“Storage 동기화 에이전트 마법사 중단” 오류로 인해 에이전트를 설치하는 데 실패했습니다.**  
이 문제는 버전 1.x 에이전트에서 IIS 웹 사이트 기본 이름이 변경된 경우에 발생할 수 있습니다. 이 문제를 해결하려면 2.0.11+ 에이전트를 사용합니다.

<a id="server-registration-missing"></a>**Azure Portal의 등록된 서버 아래에서 서버가 나열되지 않습니다.**  
저장소 동기화 서비스에 대한 **등록된 서버** 아래에서 서버가 나열되지 않으면 다음을 수행합니다.
1. 등록하려는 서버에 로그인합니다.
2. 파일 탐색기를 열고 Storage 동기화 에이전트 설치 디렉터리(기본 위치 C:\Program Files\Azure\StorageSyncAgent) 로 이동합니다. 
3. ServerRegistration.exe를 실행하고 마법사를 완료하여 서버를 Storage 동기화 서비스에 등록합니다.



<a id="server-already-registered"></a>**Azure File Sync 에이전트가 설치되는 동안 "서버가 이미 등록되었습니다."라는 메시지가 표시됩니다.** 

![“This server is already registered”(서버가 이미 등록되었습니다.) 오류 메시지가 있는 서버 등록 대화 상자의 스크린샷](media/storage-sync-files-troubleshoot/server-registration-1.png)

서버가 이전에 Storage 동기화 서비스에 등록된 경우 이 메시지가 표시됩니다. 현재 Storage 동기화 서비스에서 서버 등록을 취소하고 새 Storage 동기화 서비스에 등록하려면 [Azure File Sync로 서버 등록 해제](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 완료하세요.

저장소 동기화 서비스의 **등록된 서버** 아래에서 서버가 나열되지 않으면, 등록을 취소하려는 서버에서 다음 PowerShell 명령을 실행합니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 서버가 클러스터에 속할 경우 선택적 *Reset-StorageSyncServer -CleanClusterRegistration* 매개 변수를 사용하여 클러스터 등록도 제거합니다.

<a id="web-site-not-trusted"></a>**서버를 등록할 때 "웹 사이트를 신뢰할 수 없습니다." 응답이 여러 번 표시됩니다. 그 이유는 무엇일까요?**  
이 문제는 서버 등록 중에 **Internet Explorer 보안 강화** 정책이 사용되도록 설정되었기 때문에 발생합니다. **Internet Explorer 보안 강화** 정책을 사용하지 않도록 적절히 설정하는 방법에 대한 자세한 내용은 [Azure File Sync와 함께 사용하도록 Windows Server 준비](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) 및 [Azure File Sync를 배포하는 방법(미리 보기)](storage-sync-files-deployment-guide.md)을 참조하세요.

## <a name="sync-group-management"></a>동기화 그룹 관리
<a id="cloud-endpoint-using-share"></a>**"지정된 Azure 파일 공유가 이미 다른 클라우드 엔드포인트에서 사용되고 있습니다."라는 오류로 인해 클라우드 엔드포인트를 만드는 데 실패했습니다.**  
Azure 파일 공유가 다른 클라우드 엔드포인트에서 이미 사용되고 있으면 이 문제가 발생합니다. 

이 메시지가 표시되고 Azure 파일 공유가 현재 클라우드 엔드포인트에서 사용되고 있지 않으면 다음 단계를 수행하여 Azure 파일 공유에서 Azure File Sync 메타데이터를 지웁니다.

> [!Warning]  
> 클라우드 엔드포인트에서 현재 사용 중인 Azure 파일 공유에서 메타데이터를 삭제하면 Azure File Sync 작업이 실패합니다. 

1. Azure Portal에서 Azure 파일 공유로 이동합니다.  
2. Azure 파일 공유를 마우스 오른쪽 단추로 클릭하고 **메타데이터 편집**을 선택합니다.
3. **SyncService**를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

<a id="cloud-endpoint-authfailed"></a>**"AuthorizationFailed" 오류로 인해 클라우드 엔드포인트를 만드는 데 실패했습니다.**  
사용자 계정에 클라우드 엔드포인트를 만들 수 있는 충분한 권한이 없으면 이 문제가 발생합니다. 

클라우드 엔드포인트를 만들려면 사용자 계정에 다음과 같은 Microsoft 인증 권한이 있어야 합니다.  
* 읽기: 역할 정의 가져오기
* 쓰기: 사용자 지정 역할 정의 만들기 또는 업데이트
* 읽기: 역할 할당 가져오기
* 쓰기: 역할 할당 만들기

다음과 같은 기본 제공 역할은 필요한 Microsoft 인증 권한을 갖고 있습니다.  
* 소유자
* 사용자 액세스 관리자 사용자의 계정 역할에 필요한 사용 권한이 있는지 확인하려면:  
1. Azure Portal에서 **리소스 그룹**을 선택합니다.
2. 저장소 계정이 있는 리소스 그룹을 선택하고 **IAM(Access Control)** 를 선택합니다.
3. 사용자 계정에 대한 **역할**(예: 소유자 또는 참가자)을 선택합니다.
4. **리소스 공급자** 목록에서 **Microsoft 인증**을 선택합니다. 
    * **역할 할당**에 **읽기** 권한과 **쓰기** 권한이 있어야 합니다.
    * **역할 정의**에 **읽기**  권한과 **쓰기** 권한이 있어야 합니다.

<a id="server-endpoint-createjobfailed"></a>**“MgmtServerJobFailed”(오류 코드: -2134375898) 오류로 인해 서버 끝점을 생성하는 데 실패했습니다.**                                                                                                                    
이 문제는 서버 끝점 경로가 시스템 볼륨에 있고 클라우드 계층화를 사용하도록 설정한 경우 발생합니다. 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 끝점을 만들려면 서버 끝점을 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.

<a id="server-endpoint-deletejobexpired"></a>**“MgmtServerJobExpired” 오류로 인해 서버 끝점을 삭제하는 데 실패했습니다.**                
이 문제는 서버가 오프라인 상태이거나 네트워크에 연결되어 있지 않은 경우 발생합니다. 서버를 더 이상 사용할 수 없는 경우 Azure Portal에서 서버의 등록을 취소하여 서버 끝점을 삭제합니다. 서버 끝점을 삭제하려면 [Azure File Sync에서 서버 등록 취소](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 따릅니다.

<a id="server-endpoint-provisioningfailed"></a>**서버 엔드포인트 속성 페이지를 열거나 클라우드 계층화 정책을 업데이트할 수 없습니다.**

이 문제는 서버 엔드포인트에서 관리 작업이 실패하는 경우 발생합니다. Azure Portal에서 서버 엔드포인트 속성 페이지가 열리지 않는 경우, 서버에서 PowerShell 명령을 사용하여 서버 엔드포인트를 업데이트하면 이 문제가 해결될 수 있습니다. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint -SubscriptionId mysubguid -ResourceGroupName myrgname -StorageSyncServiceName storagesvcname -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint -Id serverendpointid -CloudTiering true -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>동기화
<a id="afs-change-detection"></a>**SMB 또는 포털을 통해 Azure 파일 공유에 직접 파일을 만든 경우 해당 파일이 동기화 그룹의 서버에 동기화되는 데 얼마나 걸리나요?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**서버에서 동기화가 실패함**  
서버에서 동기화가 실패하는 경우:
1. Azure 파일 공유에 동기화하려는 디렉터리에 대한 서버 엔드포인트가 Azure Portal에 있는지 확인합니다.
    
    ![Azure Portal에 클라우드 엔드포인트 및 서버 엔드포인트가 모두 포함된 동기화 그룹의 스크린샷](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
    1. 서버가 인터넷에 연결되어 있는지 확인합니다.
    2. 서버에서 Azure File Sync 서비스가 실행 중인지 확인합니다. 이렇게 하려면 서비스 MMC 스냅인을 열고 Storage 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.

<a id="replica-not-ready"></a>**"0x80c8300f - 복제본이 해당 작업을 수행할 준비가 되지 않았습니다." 오류로 인해 동기화 실패**   
클라우드 엔드포인트를 만들고 데이터가 포함된 Azure 파일 공유를 사용하는 경우 이 문제가 발생할 수 있습니다. Azure 파일 공유에서 변경 검색 작업 실행이 완료되면(최대 24시간이 걸릴 수 있음) 동기화가 올바르게 작동되기 시작합니다.


    > [!NOTE]
    > Azure File Sync periodically takes VSS snapshots to sync files that have open handles.

현재, 리소스를 다른 구독 또는 다른 Azure AD 테넌트로 이동하는 것은 지원되지 않습니다.  구독이 다른 테넌트를 이동되면, 소유권 변경에 따라, Azure 파일 공유에서 서비스에 액세스할 수 없게 됩니다. 테넌트가 변경되면 서버 끝점 및 클라우드 끝점을 삭제하고(재사용할 Azure 파일 공유를 정리하는 방법에 대한 지침은 동기화 그룹 관리 참조) 동기화 그룹을 다시 만듭니다.

<a id="doesnt-have-enough-free-space"></a>**이 PC에 사용 가능한 공간이 충분하지 않음 오류**  
포털에 “이 PC에 사용 가능한 공간이 충분하지 않음” 상태가 표시되는 경우 볼륨에 여유 공간이 1GB 미만인 문제가 발생했을 수 있습니다.  예를 들어 1.5GB 볼륨이 있는 경우 동기화는 5GB만 활용할 수 있습니다. 이 문제가 발생할 경우 서버 엔드포인트에 사용 중인 볼륨의 크기를 확장하세요.

## <a name="cloud-tiering"></a>클라우드 계층화 
클라우드 계층화에는 다음 두 가지 경로가 있습니다.

- 파일이 계층화되는 데 실패할 수 있습니다, 즉 Azure File Sync에서 파일을 Azure Files로 성공적으로 계층화하지 못합니다.
- 파일을 회수하는 데 실패할 수 있습니다. 즉 사용자가 계층화된 파일에 액세스하려고 하면 Azure File Sync 파일 시스템 필터(StorageSync.sys)에서 데이터를 다운로드하지 못합니다.

오류 경로 중 하나를 통해 발생할 수 있는 두 가지 주요 오류 클래스가 있습니다.

- 클라우드 저장소 오류
    - *일시적인 저장소 서비스 가용성 문제* - 자세한 내용은 [Azure Storage에 대한 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)를 참조하세요.
    - *액세스할 수 없는 Azure 파일 공유* - 일반적으로 Azure 파일 공유가 여전히 동기화 그룹의 클라우드 엔드포인트일 때 Azure 파일 공유를 삭제하면 이 오류가 발생합니다.
    - *액세스할 수 없는 저장소 계정* - 일반적으로 동기화 계정의 클라우드 엔드포인트인 Azure 파일 공유가 있는 동안 저장소 계정을 삭제하면 이 오류가 발생합니다. 
- 서버 오류 
    - *Azure File Sync 파일 시스템 필터(StorageSync.sys)가 로드되지 않음* - 계층화/회수 요청에 응답하려면 Azure File Sync 파일 시스템 필터를 로드해야 합니다. 로드되지 않는 필터는 여러 가지 이유로 발생할 수 있지만, 가장 일반적인 이유는 관리자가 필터를 수동으로 언로드했기 때문입니다. Azure File Sync가 제대로 작동하려면 Azure File Sync 파일 시스템 파일 필터가 항상 로드되어야 합니다.
    - *누락되었거나 손상되었거나 끊어진 재분석 지점* - 재분석 지점은 다음 두 부분으로 구성된 파일의 특별한 데이터 구조입니다.
        1. 재분석 태그: Azure File Sync 파일 시스템 필터(StorageSync.sys)가 파일에 대한 IO 작업 일부를 수행해야 하는 운영 체제를 나타냅니다. 
        2. 재분석 데이터: 파일 시스템 필터에 연결된 클라우드 엔드포인트(Azure 파일 공유)에 있는 파일의 URI를 나타냅니다. 
        
        재분석 지점이 손상될 수 있는 가장 일반적인 방법은 관리자가 태그 또는 해당 데이터를 수정하려고 하는 경우입니다. 
    - *네트워크 연결 문제* - 파일을 계층화하거나 회수하려면 서버에 인터넷 연결이 있어야 합니다.

다음 섹션에서는 클라우드 계층화 문제를 해결하고 클라우드 저장소 문제 또는 서버 문제인지 여부를 확인하는 방법에 대해 설명합니다.

<a id="files-fail-tiering"></a>**계층화에 실패한 파일의 문제 해결**  
파일이 Azure Files로 계층화되지 못한 경우:

1. 파일이 Azure 파일 공유에 있는지 확인합니다.

    > [!NOTE]
    > 계층화하려면 파일을 먼저 Azure 파일 공유에 동기화해야 합니다.
2. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
    1. 서버가 인터넷에 연결되어 있는지 확인합니다. 
    2. Azure File Sync 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
        - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

<a id="files-fail-recall"></a>**회수에 실패한 파일 문제 해결**  
파일을 회수하지 못한 경우:
1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 운영 및 진단 이벤트 로그를 검토합니다.
    1. 파일이 Azure 파일 공유에 있는지 확인합니다.
    2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
    3. Azure File Sync 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
        - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

<a id="files-unexpectedly-recalled"></a>**서버에서 예기치 않게 회수되는 파일 문제 해결**  
바이러스 백신, 백업, 그리고 대량의 파일을 읽는 기타 응용 프로그램은 오프라인 특성 건너뛰기 및 해당 파일의 내용 읽기 건너뛰기를 사용하지 않을 경우 의도치 않은 회수가 발생합니다. 이 옵션을 지원하는 제품에 대한 오프라인 파일 건너뛰기는 바이러스 백신 검사 또는 백업 작업 등을 수행하는 동안 의도치 않은 회수를 피하는 데 도움이 됩니다.

오프라인 파일 읽기를 건너뛰도록 솔루션을 구성하는 방법은 소프트웨어 공급업체에 문의하세요.

파일 탐색기에서 파일을 탐색할 경우 등의 기타 시나리오에서도 의도치 않은 회수가 발생할 수 있습니다. 서버의 파일 탐색기에서 클라우드 계층화 파일이 있는 폴더를 열면 의도치 않은 회수가 발생할 수 있습니다. 서버에서 바이러스 백신 솔루션이 사용 가능하게 설정된 경우에 이러한 회수가 발생하기 더 쉽습니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결
서버에서 Azure File Sync에 문제가 발생하는 경우 먼저 다음 단계를 완료합니다.
1. 이벤트 뷰어에서 운영 및 진단 이벤트 로그를 검토합니다.
    - 동기화, 계층화 및 회수 문제가 Applications and Services\Microsoft\FileSync\Agent의 진단 및 운영 이벤트 로그에 기록됩니다.
    - 서버 관리(예: 구성 설정)와 관련된 문제는 Applications and Services\Microsoft\FileSync\Management의 운영 및 진단 이벤트 로그에 기록됩니다.
2. 서버에서 Azure File Sync 서비스가 실행 중인지 확인합니다.
    - 서비스 MMC 스냅인을 열고 Storage 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
3. Azure File Sync 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

문제가 해결되지 않으면 AFSDiag 도구를 실행합니다.
1. AFSDiag 출력이 저장될 디렉터리를 만듭니다(예: C:\output).
2. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 실행합니다(각 명령 후 Enter 키 누름).

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure File Sync 커널 모드 추적 수준에 대해 **1**을 입력하고(더 자세한 추적을 만들기 위해 다르게 지정되지 않은 경우) Enter 키를 누릅니다.
4. Azure File Sync 사용자 모드 추적 수준에 대해 **1**을 입력하고(더 자세한 추적을 만들기 위해 다르게 지정되지 않은 경우) Enter 키를 누릅니다.
5. 문제를 재현합니다. 작업을 완료하면 **D** 키를 입력합니다.
6. 로그 및 추적 파일을 포함하는 .zip 파일은 사용자가 지정한 출력 디렉터리에 저장됩니다.

## <a name="see-also"></a>참고 항목
- [Azure Files 질문과 대답](storage-files-faq.md)
- [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
