---
title: 'Azure AD Connect 동기화: 필터링 구성 | Microsoft Docs'
description: Azure AD Connect 동기화에서 필터링을 구성하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 0b4b306d1224b5521774b05a110c862b58450eb3
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect 동기화 구성 필터링
필터링을 사용하여 온-프레미스 디렉터리에서 Azure Active Directory(Azure AD)에 표시할 개체를 제어할 수 있습니다. 기본 구성은 모든 도메인에 구성된 포리스트의 모든 개체를 사용합니다. 일반적으로 권장되는 구성입니다. Exchange Online 및 비즈니스용 Skype 등의 Office 365 워크로드를 사용하면 완전한 전체 주소 목록이 도움이 되므로 모든 사람에게 메일을 보내거나 호출할 수 있습니다. 기본 구성을 사용하여 Exchange 또는 Lync의 온-프레미스 구현과 같은 환경을 가져올 수 있습니다.

경우에 따라 기본 구성에 일부 변경을 수행해야 합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

* [여러 Azure AD 디렉터리 토폴로지](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant)를 사용할 계획입니다. 그러면 필터를 적용하여 특정 Azure AD 디렉터리에 동기화할 개체를 제어해야 합니다.
* Azure AD의 사용자 하위 집합만 이용하여 Azure 또는 Office 365에 대한 파일럿을 실행합니다. 작은 파일럿에서는 해당 기능을 보여 주기 위해 완전한 전체 주소 목록이 필요하지 않습니다.
* 많은 서비스 계정과 Azure AD에서 필요 없는 다른 비개인용 계정이 있습니다.
* 규정 준수를 위해 모든 사용자 계정 온-프레미스를 삭제하지 않습니다. 사용하지 않도록 설정하기만 합니다. 하지만 Azure AD에는 활성 계정만 있도록 하고 싶습니다.

이 문서에서는 다양한 필터링 방법을 구성하는 방법을 설명합니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 작업 외의 Azure AD Connect 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 이러한 동작 중 하나는 Azure AD Connect 동기화의 불일치하거나 지원되지 않는 상태가 될 수 있습니다. 결과적으로, Microsoft는 해당 배포에 대해 기술 지원을 제공할 수 없습니다.

## <a name="basics-and-important-notes"></a>기본 사항 및 중요 참고 사항
Azure AD Connect 동기화에서 언제든지 필터링을 사용할 수 있습니다. 디렉터리 동기화의 기본 구성으로 시작하고 필터링을 구성하는 경우 필터링된 개체는 Azure AD에 더 이상 동기화되지 않습니다. 이 변경으로 인해 Azure AD에서 이전에 동기화되었지만 그 후에 필터링된 개체는 Azure AD에서 삭제됩니다.

필터링 변경을 시작하기 전에 [예약된 작업을 사용하지 않도록 설정](#disable-scheduled-task)했는지 확인하여 아직 올바른 것으로 확인되지 않은 변경 사항을 실수로 내보내지 않도록 합니다.

필터링은 동시에 많은 개체를 제거할 수 있으므로 모든 변경 사항을 Azure AD로 내보내기 전에 새 필터가 올바른지 확인하려고 합니다. 구성 단계를 완료한 후 변경 사항을 Azure AD로 내보내 적용하기 전에 [확인 단계](#apply-and-verify-changes)를 수행하는 것이 좋습니다.

실수로 많은 개체를 삭제하는 것을 방지하기 위해 “[실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)” 기능이 기본적으로 설정되어 있습니다. 필터링으로 인해 많은 개체를 삭제하는 경우(기본적으로 500개) 이 문서의 단계를 따라 삭제 작업을 Azure AD에 진행해야 합니다.

2015년 11월([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) 전 빌드를 사용하고, 필터 구성을 변경하고, 암호 해시 동기화를 사용하려면 구성을 완료한 다음, 모든 암호를 전체 동기화 트리거해야 합니다. 암호 전체 동기화 트리거하는 방법에 대한 단계는 [모든 암호의 전체 동기화 트리거](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords)를 참조하세요. 빌드 1.0.9125 이상일 경우 정기적인 **전체 동기화** 작업이 암호가 동기화되어야 하는지, 이 추가 단계가 더 이상 필요하지 않은지도 계산합니다.

필터링 오류로 인해 **사용자** 개체가 Azure AD에서 의도치 않게 삭제된 경우에 필터링 구성을 제거하고 Azure AD에 사용자 개체를 다시 만들 수 있습니다. 그런 다음 디렉터리를 다시 동기화할 수 있습니다. 이 작업을 통해 사용자가 Azure AD의 휴지통에서 복원됩니다. 그러나 다른 개체 형식을 삭제 취소할 수 없습니다. 예를 들어 보안 그룹을 실수로 삭제하고 ACL에 리소스로 사용한 경우 그룹 및 해당 ACL은 복구할 수 없습니다.

Azure AD Connect는 범위 내로 간주되었던 개체만 삭제합니다. Azure AD의 개체가 다른 동기화 엔진으로 만들어졌으며 이러한 개체가 범위에 없는 경우 필터링을 추가해도 제거되지 않습니다. 예를 들어 Azure AD의 전체 디렉터리의 완전한 사본을 만든 DirSync 서버로 시작하는 경우와 처음부터 활성화된 필터링과 병행하여 새 Azure AD Connect 동기화 서버를 설치하는 경우 DirSync로 만든 추가 개체는 제거되지 않습니다.

Azure AD Connect를 설치하거나 최신 버전으로 업그레이드할 때 필터링 구성은 유지됩니다. 언제나 가장 좋은 방법은 첫 번째 동기화 주기가 실행되기 전에 최신 버전으로 업그레이드한 후 구성이 의도치 않게 변경되지 않았는지 확인하는 것입니다.

둘 이상의 포리스트가 있는 경우 이 항목에서 설명한 필터링 구성이 모든 포리스트에 적용되어야 합니다(모든 포리스트에 동일하게 구성하려는 경우).

### <a name="disable-the-scheduled-task"></a>예약형 작업 사용 안 함
30분 마다 동기화 주기를 트리거하는 기본 제공 스케줄러를 사용하지 않으려면 다음 단계를 수행합니다.

1. PowerShell 프롬프트로 이동합니다.
2. `Set-ADSyncScheduler -SyncCycleEnabled $False` 를 실행하여 스케줄러를 비활성화합니다.
3. 이 문서의 설명대로 변경합니다.
4. `Set-ADSyncScheduler -SyncCycleEnabled $True` 를 실행하여 스케줄러를 다시 활성화합니다.

**Azure AD Connect 1.1.105.0 전 빌드를 사용하는 경우**  
3시간 마다 동기화 주기를 트리거하는 예약형 작업을 사용하지 않으려면 다음 단계를 수행합니다.

1. **시작** 메뉴에서 **작업 Scheduler**를 시작합니다.
2. **작업 Scheduler 라이브러리** 바로 아래에서 **Azure AD Sync Scheduler**라는 작업을 찾아 마우스 오른쪽 단추로 클릭하고 **사용 안 함**을 선택합니다.  
   ![작업 Scheduler](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. 이제 구성을 변경하고 **Synchronization Service Manager** 콘솔에서 수동으로 동기화 엔진을 실행할 수 있습니다.

필터링 변경을 모두 완료한 후 돌아와서 해당 작업을 **사용**으로 다시 설정해야 합니다.

## <a name="filtering-options"></a>필터링 옵션
다음 필터링 구성 형식을 디렉터리 동기화 도구에 적용할 수 있습니다.

* [**그룹 기반**](#group-based-filtering): 단일 그룹 기반의 필터링은 설치 마법사를 사용하여 초기 설치 시에만 구성할 수 있습니다.
* [**도메인 기반**](#domain-based-filtering): 이 옵션을 사용하면 Azure AD로 동기화할 도메인을 선택할 수 있습니다. 또한 Azure AD Connect 동기화를 설치한 후 온-프레미스 인프라를 변경하는 경우 동기화 엔진 구성에서 도메인을 추가하고 제거할 수 있습니다.
* [**조직 구성 단위(OU) 기반**](#organizational-unitbased-filtering): 이 옵션을 사용하면 Azure AD로 동기화하는 OU를 선택할 수 있습니다. 이 옵션은 선택된 OU의 모든 개체 형식에 대해 설정됩니다.
* [**특성 기반**](#attribute-based-filtering): 이 옵션을 사용하면 개체의 특성 값을 기반으로 개체를 필터링할 수 있습니다. 또한 다른 개체 형식별로 다르게 필터링할 수 있습니다.

동시에 여러 필터링 옵션을 사용할 수 있습니다. 예를 들어 OU 기반 필터링을 사용하여 하나의 OU에 개체만 포함시킬 수 있습니다. 동시에 특성 기반 필터링을 사용하여 추가로 개체를 필터링할 수 있습니다. 여러 필터링 메서드를 사용하면 필터는 필터 간 논리적 “AND”를 사용합니다.

## <a name="domain-based-filtering"></a>도메인 기반 필터링
이 섹션에서는 도메인 필터를 구성하는 단계를 제공합니다. Azure AD Connect를 설치한 후 포리스트 내에 도메인을 추가 또는 삭제했다면 필터링 구성도 업데이트해야 합니다.

도메인 기반 필터링을 변경하는 기본 방법은 설치 마법사를 실행하여 [도메인 및 OU 필터링](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)을 변경하는 것입니다. 설치 마법사는 이 항목에 설명된 모든 작업을 자동화합니다.

어떤 이유로 인해 설치 마법사를 실행할 수 없는 경우에만 다음 단계를 수행합니다.

도메인 기반 필터링 구성은 다음 단계로 구성됩니다.

1. 동기화에 포함시키려는 [도메인을 선택](#select-domains-to-be-synchronized)합니다.
2. 각각의 추가되고 제거된 도메인에 대해 [실행 프로필](#update-run-profiles)을 조정합니다.
3. [변경 사항을 적용하고 확인합니다](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>동기화할 도메인을 선택합니다.
도메인 필터를 설정하려면 다음 단계를 수행합니다.

1. **ADSyncAdmins** 보안 그룹의 멤버인 계정을 사용하여 Azure AD Connect 동기화를 실행하는 서버에 로그인합니다.
2. **시작** 메뉴에서 **동기화 서비스**를 시작합니다.
3. **커넥터**를 선택하고 **커넥터** 목록에서 **Active Directory Domain Services** 형식을 가진 커넥터를 선택합니다. **작업**에서 **속성**을 선택합니다.  
   ![커넥터 속성](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. **디렉터리 파티션 구성**을 클릭합니다.
5. **디렉터리 파티션 선택** 목록에서 필요에 따라 도메인을 선택 및 선택 취소합니다. 동기화하려는 파티션만 선택되어 있는지 확인합니다.  
   ![파티션](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   온-프레미스 Active Directory 인프라를 변경하고 포리스트에서 도메인을 추가하거나 제거한 경우 **새로 고침** 단추를 클릭하여 업데이트된 목록을 가져옵니다. 새로 고칠 때 자격 증명 요청 메시지가 표시됩니다. Windows Server Active Directory에 대한 읽기 권한으로 모든 자격 증명을 제공합니다. 대화 상자에 미리 채워져 있는 사용자일 필요는 없습니다.  
   ![새로 고침 필요](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. 완료하면 **확인**을 클릭하여 **속성** 대화 상자를 닫습니다. 포리스트에서 도메인을 제거한 경우 도메인이 제거되었다는 메시지가 팝업되고 해당 구성은 정리됩니다.
7. [실행 프로필](#update-run-profiles)을 계속 조정합니다.

### <a name="update-the-run-profiles"></a>실행 프로필 업데이트
도메인 필터를 업데이트한 경우 실행 프로필도 업데이트해야 합니다.

1. 이전 단계에서 변경한 커넥터가 **커넥터** 목록에서 선택되어 있는지 확인합니다. **작업**에서 **실행 프로필 구성**을 선택합니다.  
   ![커넥터 실행 프로필 1](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. 다음 프로필을 찾아서 식별합니다.
    * 전체 가져오기
    * 전체 동기화
    * 델타 가져오기
    * 델타 동기화
    * 내보내기
3. 각 프로필에 대해 **추가된** 도메인과 **제거된** 도메인을 조정합니다.
    1. 각각의 5개 프로필에 대해 **추가된** 각 도메인에 다음 단계를 수행합니다.
        1. 실행 프로필을 선택하고 **새 단계**를 클릭합니다.
        2. **구성 단계** 페이지의 **형식** 드롭다운 메뉴에서 구성할 프로필과 같은 이름의 단계 유형을 선택합니다. 그런 후 **Next** 를 클릭합니다.  
        ![커넥터 실행 프로필 2](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. **커넥터 구성** 페이지의 **파티션** 드롭다운 메뉴에서 도메인 필터에 추가한 파티션의 이름을 선택합니다.  
        ![커넥터 실행 프로필 3](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. **실행 프로필 구성** 대화 상자를 닫으려면 **마침**을 클릭합니다.
    2. 각각의 5개 프로필에 대해 **제거된** 각 도메인에 다음 단계를 수행합니다.
        1. 실행 프로필을 선택합니다.
        2. **파티션** 특성의 **값**이 GUID이면 실행 단계를 선택하고 **단계 삭제**를 클릭합니다.  
        ![커넥터 실행 프로필 4](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. 변경 내용을 확인합니다. 동기화하려는 각 도메인이 각 실행 프로필의 단계로 나열된 것이어야 합니다.
4. **실행 프로필 구성** 대화 상자를 닫으려면 **확인**을 클릭합니다.
5.  구성을 완료하려면 **전체 가져오기** 및 **델타 동기화**를 실행해야 합니다. [변경 사항 적용 및 확인](#apply-and-verify-changes) 섹션을 계속 읽습니다.

## <a name="organizational-unitbased-filtering"></a>조직 구성 단위 기반 필터링
도메인 기반 필터링을 변경하는 기본 방법은 설치 마법사를 실행하여 [도메인 및 OU 필터링](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)을 변경하는 것입니다. 설치 마법사는 이 항목에 설명된 모든 작업을 자동화합니다.

어떤 이유로 인해 설치 마법사를 실행할 수 없는 경우에만 다음 단계를 수행합니다.

조직 구성 단위 기반 필터링을 구성하려면 다음 단계를 수행합니다.

1. **ADSyncAdmins** 보안 그룹의 멤버인 계정을 사용하여 Azure AD Connect 동기화를 실행하는 서버에 로그인합니다.
2. **시작** 메뉴에서 **동기화 서비스**를 시작합니다.
3. **커넥터**를 선택하고 **커넥터** 목록에서 **Active Directory Domain Services** 형식을 가진 커넥터를 선택합니다. **작업**에서 **속성**을 선택합니다.  
   ![커넥터 속성](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. **디렉터리 파티션 구성**을 클릭하고 구성하려는 도메인을 선택한 후 **컨테이너**를 클릭합니다.
5. 메시지가 표시되면 온-프레미스 Active Directory에 대한 읽기 권한으로 모든 자격 증명을 제공합니다. 대화 상자에 미리 채워져 있는 사용자일 필요는 없습니다.
6. **컨테이너 선택** 대화 상자에서 클라우드 디렉터리와 동기화하지 않을 OU의 선택을 취소하고 **확인**을 클릭합니다.  
   ![컨테이너 선택 대화 상자에 있는 OU](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * 성공적으로 Azure AD에 동기화하려면 Windows 10 컴퓨터를 위해 **컴퓨터** 컨테이너를 선택해야 합니다. 컴퓨터에 연결된 도메인이 다른 OU에 있는 경우 해당 사항이 선택되어 있는지 확인합니다.
   * 트러스트된 여러 포리스트가 있는 경우 **ForeignSecurityPrincipals** 컨테이너를 선택해야 합니다. 이 컨테이너를 통해 크로스 포리스트 보안 그룹 멤버 자격을 확인할 수 있습니다.
   * 장치 쓰기 저장 기능을 사용하도록 설정한 경우 **RegisteredDevices** OU를 선택해야 합니다. 그룹 쓰기 저장 등 다른 쓰기 저장 기능을 사용하는 경우 이러한 위치를 선택해야 합니다.
   * 사용자, iNetOrgPersons, 그룹, 연락처 및 컴퓨터가 있는 다른 OU를 선택합니다. 그림에서 이러한 모든 OU가 ManagedObjects OU에 있습니다.
   * 그룹 기반 필터링을 사용하는 경우 그룹이 위치한 OU가 포함되어야 합니다.
   * 필터링 구성을 완료한 후에 추가된 새로운 OU를 동기화할지 여부를 구성할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.
7. 완료하면 **확인**을 클릭하여 **속성** 대화 상자를 닫습니다.
8. 구성을 완료하려면 **전체 가져오기** 및 **델타 동기화**를 실행해야 합니다. [변경 사항 적용 및 확인](#apply-and-verify-changes) 섹션을 계속 읽습니다.

### <a name="synchronize-new-ous"></a>새 OU 동기화
필터링을 구성한 후 생성된 새 OU는 기본적으로 동기화되어 있습니다. 이 상태는 선택된 확인란으로 표시됩니다. 일부 하위 OU를 선택 취소할 수도 있습니다. 이 작업을 수행하려면 파랑 확인란(기본 상태)이 흰색이 될 때까지 상자를 클릭합니다. 그런 다음 동기화하지 않으려는 하위 OU를 선택 취소합니다.

모든 하위 OU가 동기화되면 파란색 확인 표시가 있는 흰색 상자가 표시됩니다.  
![모든 상자가 선택된 OU](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

일부 하위 OU가 선택 취소되면 흰색 확인 표시가 있는 회색 상자가 표시됩니다.  
![일부 하위 OU가 선택 취소된 OU](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

이 구성을 사용하여 ManagedObjects에서 만든 새 OU가 동기화됩니다.

Azure AD Connect 설치 마법사는 항상 이 구성을 만듭니다.

### <a name="dont-synchronize-new-ous"></a>새 OU 동기화 안 함
필터링 구성을 완료한 후에 새 OU를 동기화하지 않도록 동기화 엔진을 구성할 수 있습니다. 확인 표시가 없는 회색 상자로 UI에 나타납니다. 이 작업을 수행하려면 확인 표시가 없는 흰색이 될 때까지 상자를 클릭합니다. 그런 다음 동기화하려는 하위 OU를 선택합니다.

![루트가 선택 취소된 OU](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

이 구성을 사용하여 ManagedObjects에서 만든 새 OU가 동기화되지 않습니다.

## <a name="attribute-based-filtering"></a>특성 기반 필터링
다음 단계 작업을 위해 2015년 11월 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) 이상 빌드를 사용하고 있는지 확인합니다.

특성 기반 필터링은 개체를 필터링하는 가장 유연한 방법입니다. [선언적 프로비전](active-directory-aadconnectsync-understanding-declarative-provisioning.md) 기능을 사용하여 개체를 Azure AD에 동기화하는 경우의 거의 모든 측면을 제어할 수 있습니다.

Active Directory에서 메타버스로의 [인바운드](#inbound-filtering) 및 메타버스에서 Azure AD로의 [아웃바운드](#outbound-filtering)에 적용될 수 있습니다. 가장 쉽게 유지 관리할 수 있어서 인바운드 필터링을 적용하는 것이 좋습니다. 평가가 수행되기 전 둘 이상의 포리스트에서 개체를 연결해야 하는 경우에는 아웃바운드 필터링만 사용해야 합니다.

### <a name="inbound-filtering"></a>인바운드 필터링
인바운드 필터링은 동기화할 값으로 설정되지 않은 메타버스 특성 cloudFiltered가 Azure AD로 이동하는 개체에 있어야 하는 기본 구성을 사용합니다. 이 특성 값이 **True**로 설정되면 개체가 동기화되지 않습니다. 의도적으로 **False**로 설정되지 않아야 합니다. 다른 규칙에 값을 적용할 수 있는지 확인하려면 이 특성에 **True** 또는 **NULL**(없는 경우) 값만 있어야 합니다.

인바운드 필터링에서 **범위** 기능을 사용하여 개체를 동기화할지 여부를 확인합니다. 여기서 해당 조직의 요구 사항에 맞게 조정합니다. 범위 모듈에 동기화 규칙이 범위에 있는지 확인하는 **그룹** 및 **절**이 있습니다. 그룹에 하나 이상의 절이 포함됩니다. 여러 절 간에는 논리적 “AND”가 있으며 여러 그룹 간에는 논리적 “OR”이 있습니다.

예:   
![범위](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
**(department = IT) OR (department = Sales AND c = US)**로 표시됩니다.

다음 샘플 및 단계에서 사용자 개체를 예로 사용하지만 모든 개체 형식에 대해서 사용할 수 있습니다.

다음 샘플에서 우선 순위 값은 50부터 시작합니다. 사용되지 않은 모든 숫자를 사용할 수 있지만 100보다는 작아야 합니다.

#### <a name="negative-filtering-do-not-sync-these"></a>부정 필터링: "다음을 동기화 안 함"
다음 예제에서는 **extensionAttribute15**가 **NoSync** 값을 가진 모든 사용자를 필터링(동기화 안 함)합니다.

1. **ADSyncAdmins** 보안 그룹의 멤버인 계정을 사용하여 Azure AD Connect 동기화를 실행하는 서버에 로그인합니다.
2. **시작** 메뉴에서 **동기화 규칙 편집기**를 시작합니다.
3. **인바운드**가 선택되어 있는지 확인하고 **새 규칙 추가**를 클릭합니다.
4. "*In from AD – User DoNotSyncFilter*"와 같이 설명이 포함된 이름을 규칙에 지정합니다. 올바른 포리스트를 선택하고, **CS 개체 형식**으로 **사용자**를 선택하고 **MV 개체 형식**으로 **개인**을 선택합니다. **링크 형식**에서 **조인**을 선택합니다. **우선 순위**에서 현재 다른 동기화 규칙에서 사용하지 않는 값(예: 50)을 입력한 후 **다음**을 클릭합니다.  
   ![인바운드 1 설명](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. **범위 지정 필터**에서 **그룹 추가**를 클릭하고 **절 추가**를 클릭합니다. **특성**에서 **ExtensionAttribute15**를 선택합니다. **연산자**가 **EQUAL**로 설정되어 있는지 확인하고 **값** 상자에 값 **NoSync**를 입력합니다. **다음**을 클릭합니다.  
   ![인바운드 2 범위](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. **조인**을 비워두고 **다음**을 클릭합니다.
7. **변환 추가**를 클릭하고 **상수**로 **FlowType**을 선택하고 **대상 특성**으로 **cloudFiltered**로 선택합니다. **소스** 텍스트 상자에서 **True**를 입력합니다. **추가** 를 클릭하여 규칙을 저장합니다.  
   ![인바운드 3 변환](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. 구성을 완료하려면 **전체 동기화**를 실행해야 합니다. [변경 사항 적용 및 확인](#apply-and-verify-changes) 섹션을 계속 읽습니다.

#### <a name="positive-filtering-only-sync-these"></a>긍정 필터링: "다음만 동기화"
긍정 필터링을 표현하는 것은 회의실과 같이 동기화가 명확하지 않은 개체도 고려해야 하므로 더 어려울 수 있습니다. 또한 기본 규칙 **AD에서 - 사용자 참가**의 기본 필터도 재정의됩니다. 사용자 지정 필터를 만들 때는 Azure AD Connect의 중요 한 시스템 개체, 복제 충돌 개체, 특별한 사서함 및 서비스 계정을 포함하지 않도록 해야 합니다.

긍정 필터링 옵션에는 두 가지 동기화 규칙이 필요합니다. 동기화할 개체의 올바른 범위를 가진 규칙이 하나 이상 필요합니다. 아직 동기화해야 하는 개체로 식별되지 않은 모든 개체를 필터링하는 두 번째 포괄적인 동기화 규칙도 필요합니다.

다음 예제에서는 department 특성에 **Sales**값이 있는 사용자 개체만 동기화합니다.

1. **ADSyncAdmins** 보안 그룹의 멤버인 계정을 사용하여 Azure AD Connect 동기화를 실행하는 서버에 로그인합니다.
2. **시작** 메뉴에서 **동기화 규칙 편집기**를 시작합니다.
3. **인바운드**가 선택되어 있는지 확인하고 **새 규칙 추가**를 클릭합니다.
4. "*In from AD – User Sales sync*"와 같이 설명이 포함된 이름을 규칙에 지정합니다. 올바른 포리스트를 선택하고, **CS 개체 형식**으로 **사용자**를 선택하고 **MV 개체 형식**으로 **개인**을 선택합니다. **링크 형식**에서 **조인**을 선택합니다. **우선 순위**에서 현재 다른 동기화 규칙에서 사용하지 않는 값(예: 51)을 입력한 후 **다음**을 클릭합니다.  
   ![인바운드 4 설명](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. **범위 지정 필터**에서 **그룹 추가**를 클릭하고 **절 추가**를 클릭합니다. **특성**에서 **부서**를 선택합니다. 연산자가 **EQUAL**로 설정되어 있는지 확인하고 **값** 상자에 값 **Sales**를 입력합니다. **다음**을 클릭합니다.  
   ![인바운드 5 범위](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. **조인**을 비워두고 **다음**을 클릭합니다.
7. **변환 추가**를 클릭하고 **FlowType**으로 **상수**를 선택하고 **대상 특성**으로 **cloudFiltered**를 선택합니다. **소스** 상자에 **False**를 입력합니다. **추가** 를 클릭하여 규칙을 저장합니다.  
   ![인바운드 6 변환](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   이는 cloudFiltered를 명시적으로 **False**로 설정하는 특수한 경우입니다.
8. 이제 범용 동기화 규칙을 만들어야 합니다. “*In from AD – User Catch-all filter*”와 같이 설명이 포함된 이름을 규칙에 지정합니다. 올바른 포리스트를 선택하고, **CS 개체 형식**으로 **사용자**를 선택하고 **MV 개체 형식**으로 **개인**을 선택합니다. **링크 형식**에서 **조인**을 선택합니다. **우선 순위**에서 현재 다른 동기화 규칙에서 사용하지 않는 값(예: 99)을 입력합니다. 이전 동기화 규칙보다 더 높은 우선 순위(더 낮은 우선 순위) 값을 선택했습니다. 하지만 추가 부서 동기화를 시작하려는 경우 더 많은 필터링 동기화 규칙을 나중에 추가할 수 있도록 약간의 공간도 남겨두었습니다. **다음**을 클릭합니다.  
   ![인바운드 7 설명](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. **범위 지정 필터**를 비워 두고 **다음**을 클릭합니다. 빈 필터는 규칙이 모든 개체에 적용되어야 한다는 것을 나타냅니다.
10. **조인**을 비워두고 **다음**을 클릭합니다.
11. **변환 추가**를 클릭하고 **FlowType**으로 **상수**를 선택하고 **대상 특성**으로 **cloudFiltered**를 선택합니다. **소스** 상자에 **True**를 입력합니다. **추가** 를 클릭하여 규칙을 저장합니다.  
    ![인바운드 3 변환](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. 구성을 완료하려면 **전체 동기화**를 실행해야 합니다. [변경 사항 적용 및 확인](#apply-and-verify-changes) 섹션을 계속 읽습니다.

필요에 따라 동기화에 더 많은 개체를 포함하는 첫 번째 형식의 규칙을 더 많이 만들 수 있습니다.

### <a name="outbound-filtering"></a>아웃바운드 필터링
일부 경우는 개체가 메타버스에 연결한 후에만 필터링을 할 필요가 있습니다. 예를 들어 개체가 동기화되었는지 확인하기 위해 리소스 포리스트에서 메일 특성을 살펴보아야 하거나 계정 포리스트에서 userPrincipalName 특성을 살펴보아야 할 경우가 있습니다. 이러한 경우 아웃바운드 규칙에서 필터를 만듭니다.

이 예제에서는 메일과 userPrincipalName이 @contoso.com으로 끝나는 사용자만 동기화하도록 필터링을 변경합니다.

1. **ADSyncAdmins** 보안 그룹의 멤버인 계정을 사용하여 Azure AD Connect 동기화를 실행하는 서버에 로그인합니다.
2. **시작** 메뉴에서 **동기화 규칙 편집기**를 시작합니다.
3. **규칙 형식**에서 **아웃바운드**를 클릭합니다.
4. 사용하는 Connect의 버전에 따라 **Out to AAD – User Join** 또는 **Out to AAD - User Join SOAInAD**라는 규칙을 찾고 **편집**을 클릭합니다.
5. 팝업에서 **예** 를 선택하여 규칙의 복사본을 만듭니다.
6. **설명** 페이지에서 50과 같은 사용하지 않는 값으로 **우선 순위**를 변경합니다.
7. 왼쪽 탐색에서 **범위 지정 필터**를 클릭한 다음 **절 추가**를 클릭합니다. **특성**에서 **메일**을 선택합니다. **연산자**에서 **ENDSWITH**를 선택합니다. **값**에서 **@contoso.com**를 입력하고 **절 추가**를 클릭합니다. **특성**에서 **userPrincipalName**을 선택합니다. **연산자**에서 **ENDSWITH**를 선택합니다. **값**에서**@contoso.com**을 입력합니다.
8. **저장**을 클릭합니다.
9. 구성을 완료하려면 **전체 동기화**를 실행해야 합니다. [변경 사항 적용 및 확인](#apply-and-verify-changes) 섹션을 계속 읽습니다.

## <a name="apply-and-verify-changes"></a>변경 사항을 적용하고 확인합니다
구성을 변경한 후 이 변경 사항을 시스템에 이미 있는 개체에 적용해야 합니다. 또한 현재 동기화 엔진에 없는 개체를 처리할 수 있어야 합니다(동기화 엔진은 원본 시스템을 다시 읽어 해당 콘텐츠를 확인해야 합니다).

**도메인** 또는 **조직 구성 단위** 필터링을 사용하여 구성을 변경한 경우 **전체 가져오기**를 수행하고, 이어서 **델타 동기화**를 수행합니다.

**특성** 필터링을 사용하여 구성을 변경한 경우 **전체 동기화**를 수행해야 합니다.

다음 단계를 수행합니다.

1. **시작** 메뉴에서 **동기화 서비스**를 시작합니다.
2. **커넥터**를 선택합니다. **커넥터** 목록에서 이전에 구성을 변경한 커넥터를 선택합니다. **작업**에서 **실행**을 선택합니다.  
   ![커넥터 실행](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. **실행 프로필**에서 이전 섹션에서 언급된 작업을 선택합니다. 두 작업을 실행해야 할 경우 첫 번째 작업이 완료된 후 두 번째 작업을 실행합니다. (선택한 커넥터의 경우 **상태** 열은 **Idle**입니다.)

동기화 후 모든 변경 사항을 내보낼 준비가 됩니다. Azure AD에서 실제로 변경하기 전에 모든 변경 사항이 올바른지 확인하려고 합니다.

1. 명령 프롬프트를 시작하고 `%Program Files%\Microsoft Azure AD Sync\bin`로 이동합니다.
2. `csexport "Name of Connector" %temp%\export.xml /f:x`을 실행합니다.  
   동기화 서비스에 커넥터의 이름이 있습니다. Azure AD에 "contoso.com – AAD"와 유사한 이름이 있습니다.
3. `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`을 실행합니다.
4. 이제 %temp%에 Microsoft Excel에서 검사할 수 있는 export.csv 라는 파일이 있습니다. 이 파일은 내보낼 수 있는 모든 변경 내용을 포함합니다.
5. 내보내려는 변경 사항이 예정될 때까지 데이터 또는 구성에 필요한 변경을 수행하고 이러한 단계(가져오기, 동기화 및 확인)를 다시 실행합니다.

성공적으로 작업을 마친 후 변경 사항을 Azure AD로 내보냅니다.

1. **커넥터**를 선택합니다. **커넥터**목록에서 Azure AD Connector를 선택합니다. **작업**에서 **실행**을 선택합니다.
2. **실행 프로필**에서 **내보내기**를 선택합니다.
3. 구성 변경으로 인해 많은 개체가 삭제되는 경우 그 숫자가 구성된 임계값(기본적으로 500)보다 클 때 내보내기에 오류가 표시됩니다. 이 오류가 표시되면 일시적으로 “[실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)” 기능을 사용하지 않도록 설정해야 합니다.

이제 다시 스케줄러를 사용하도록 설정합니다.

1. **시작** 메뉴에서 **작업 Scheduler**를 시작합니다.
2. **작업 Scheduler 라이브러리** 바로 아래에서 **Azure AD Sync Scheduler**라는 작업을 찾아 마우스 오른쪽 단추로 클릭하고 **사용**을 선택합니다.

## <a name="group-based-filtering"></a>그룹 기반 필터링
[사용자 지정 설치](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)를 사용하여 Azure AD Connect를 처음 설치하면 그룹 기반 필터링을 구성할 수 있습니다. 이 방식은 소수의 개체만 동기화되는 파일럿 배포를 위해 사용됩니다. 그룹 기반 필터링을 사용하지 않도록 설정하면 다시 사용하도록 설정할 수 없습니다. 사용자 지정 구성에서 그룹 기반 필터링을 사용하는 것은 *지원되지 않습니다*. 설치 마법사를 통해 이 기능을 구성하는 것만 지원됩니다. 파일럿을 완료한 다음 이 항목의 다른 필터링 옵션 중 하나를 사용하는 것이 좋습니다. 그룹 기반 필터링과 함께 OU 기반 필터링을 사용하는 경우 그룹 및 멤버 개체가 있는 OU가 포함되어야 합니다.

여러 AD 포리스트를 동기화할 경우 각 AD 커넥터에 서로 다른 그룹을 지정하여 그룹 기반 필터링을 구성할 수 있습니다. 하나의 AD 포리스트에서 사용자를 동기화하려고 하는데 이 사용자가 다른 AD 포리스트에 해당 개체를 하나 이상 갖고 있는 경우, 사용자 개체와 해당하는 모든 개체가 그룹 기반 필터링 범위 내에 있는지 확인해야 합니다. 예:

* 하나의 포리스트에 있는 사용자가 다른 포리스트에서 해당 FSP(외부 보안 주체) 개체를 갖는 경우, 두 가지 개체 모두 그룹 기반 필터링 범위 내에 있어야 합니다. 그렇지 않으면 사용자가 Azure AD에 동기화되지 않습니다.

* 하나의 포리스트에 있는 사용자가 다른 포리스트에서 해당 리소스 계정(예: 연결된 사서함)을 갖고 있고, 사용자와 리소스 계정을 연결하기 위해 Azure AD Connect가 구성된 경우, 두 가지 개체 모두 그룹 기반 필터링 범위 내에 있어야 합니다. 그렇지 않으면 사용자가 Azure AD에 동기화되지 않습니다.

* 하나의 포리스트에 있는 사용자가 다른 포리스트에서 해당 메일 연락처를 갖고 있고, 사용자와 메일 연락처를 연결하기 위해 Azure AD Connect가 구성된 경우, 두 가지 개체 모두 그룹 기반 필터링 범위 내에 있어야 합니다. 그렇지 않으면 사용자가 Azure AD에 동기화되지 않습니다.


## <a name="next-steps"></a>다음 단계
- [Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.
- [Azure AD와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
