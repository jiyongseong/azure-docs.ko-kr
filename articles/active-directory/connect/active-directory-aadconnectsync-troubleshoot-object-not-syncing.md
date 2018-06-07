---
title: Azure AD와 동기화되지 않은 개체 문제 해결 | Microsoft Docs
description: 개체가 Azure AD와 동기화되지 않는 이유를 해결합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 5bc0fd3854488704f5368bfe1134d8fa9c959c9b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Azure AD와 동기화되지 않는 개체 문제 해결

개체가 Azure AD와 예상대로 동기화되지 않을 경우 몇 가지 이유로 인한 것일 수 있습니다. Azure AD에서 오류 전자 메일을 받았거나 Azure AD Connect Health에 오류 메시지가 표시될 경우 [내보내기 오류 문제 해결](active-directory-aadconnect-troubleshoot-sync-errors.md)을 대신 읽어보세요. 하지만 개체가 Azure AD에 없는 문제가 발생하는 경우 이 항목을 참조하세요. 여기서는 온-프레미스 구성 요소 Azure AD Connect 동기화에서 오류를 찾는 방법을 설명합니다.

>[!IMPORTANT]
>버전 <verison> 이상인 AAD(Azure Active Directory) Connect 배포의 경우 개체 동기화 문제를 해결하는 데 마법사에서 [문제 해결 작업](active-directory-aadconnect-troubleshoot-objectsync.md)을 사용할 수 있습니다. 

오류를 찾기 위해 다음 순서대로 몇 가지 위치를 확인해보겠습니다.

1. [작업 로그](#operations): 가져오기 및 동기화 동안 동기화 엔진에서 식별된 오류를 찾습니다.
2. [커넥터 공간](#connector-space-object-properties): 누락된 개체 및 동기화 오류를 찾습니다.
3. [메타버스](#metaverse-object-properties): 데이터 관련 문제를 찾습니다.

이러한 단계를 시작하기 전에 [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)를 시작합니다.

## <a name="operations"></a>작업
Synchronization Service Manager의 작업 탭에서 문제 해결을 시작합니다. 작업 탭에서는 최근 작업의 결과를 보여 줍니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

위쪽 절반에서 모든 실행이 시간순으로 표시됩니다. 기본적으로 작업 로그는 지난 7일에 대한 정보를 유지하지만 이 설정은 [스케줄러](active-directory-aadconnectsync-feature-scheduler.md)에 따라 변동될 수 있습니다. 성공 상태를 표시하지 않는 실행을 찾아보려고 합니다. 헤더를 클릭하여 정렬을 변경할 수 있습니다.

**상태** 열은 가장 중요한 정보이며 실행에 대해 가장 심각한 문제를 보여 줍니다. 다음은 조사할 우선 순위에 따른 가장 일반적인 상태에 대한 간단한 요약입니다(여기서 *는 여러 가능한 오류 문자열을 나타냄).

| 상태 | 주석 |
| --- | --- |
| stopped-* |실행을 완료하지 못했습니다. 예를 들어 원격 시스템이 다운되어 연결할 수 없는 경우입니다. |
| stopped-error-limit |5000개보다 많은 오류가 있습니다. 많은 오류로 인해 실행이 자동으로 중지되었습니다. |
| completed-\*-errors |실행이 완료되었지만 조사해야 할 오류가 있습니다(5,000개 미만). |
| completed-\*-warnings |실행이 완료되었지만 일부 데이터가 예상된 상태가 아닙니다. 오류가 발생하는 경우 이 메시지는 일반적으로 증상일 뿐입니다. 오류를 해결할 때까지 경고를 조사하지 않습니다. |
| 성공 |문제가 없습니다. |

행을 선택하면 해당 실행의 세부 정보가 표시되도록 아래쪽이 업데이트됩니다. 아래 맨 왼쪽에 **#단계**라는 목록이 있을 수 있습니다. 이 목록은 각 도메인을 단계로 나타내는 포리스트에 여러 도메인이 있는 경우에만 표시됩니다. 도메인 이름은 **파티션**머리글 아래에서 찾을 수 있습니다. **동기화 통계**아래에서 처리된 변경 내용의 수에 대한 자세한 정보를 찾을 수 있습니다. 링크를 클릭하여 변경된 개체의 목록을 가져올 수 있습니다. 오류가 있는 개체가 있으면 해당 오류는 **동기화 오류**아래에 표시됩니다.

### <a name="troubleshoot-errors-in-operations-tab"></a>작업 탭에서 오류 문제 해결
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
오류가 발생하는 경우 오류 개체 및 오류 자체가 모두 자세한 정보를 제공하는 링크입니다.

오류 문자열(그림의**sync-rule-error-function-triggered** )을 클릭하여 시작합니다. 먼저 개체의 개요가 나타납니다. 실제 오류를 확인하려면 **스택 추적**단추를 클릭합니다. 이 추적은 오류에 대한 디버그 수준 정보를 제공합니다.

**호출 스택 정보** 상자에서 마우스 오른쪽 단추를 클릭하고 **모두 선택** 및 **복사**를 차례로 선택합니다. 그런 다음 스택을 복사하고 메모장 등 즐겨 사용하는 편집기에서 오류를 볼 수 있습니다.

* 오류가 **SyncRulesEngine**에서 발생한 경우 호출 스택 정보가 개체에 대한 모든 특성의 목록을 먼저 제공합니다. **InnerException =>** 머리글이 표시될 때까지 아래로 스크롤합니다.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  다음 줄에서는 오류를 보여 줍니다. 위의 그림에서 오류는 생성된 사용자 지정 동기화 규칙 Fabrikam에서 발생했습니다.

오류 자체가 충분한 정보를 제공하지 않는 경우 이제 데이터 자체에 대해 살펴봅니다. 개체 식별자가 있는 링크를 클릭하고 [커넥터 공간 가져온 개체](#cs-import) 문제를 계속 해결할 수 있습니다.

## <a name="connector-space-object-properties"></a>커넥터 공간 개체 속성
[작업](#operations) 탭에서 오류가 확인되지 않으면 다음 단계는 Active Directory에서 메타버스 및 Azure AD까지 커넥터 공간 개체를 확인하는 것입니다. 이 경로에서 문제가 발생한 위치를 찾아야 합니다.

### <a name="search-for-an-object-in-the-cs"></a>CS에서 개체 검색

**Synchronization Service Manager**에서 **커넥터**를 클릭하고 Active Directory Connector를 선택한 후 **커넥터 공간 검색**을 선택합니다.

**범위**에서 **RDN**(CN 특성에 대해 검색하려는 경우) 또는 **DN 또는 앵커**(distinguishedName 특성에 대해 검색하려는 경우)를 선택합니다. 값을 입력하고 **검색**을 클릭합니다.  
![커넥터 공간 검색](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

찾으려는 개체를 찾지 못할 경우 [도메인 기반 필터링](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) 또는 [OU 기반 필터링](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering)으로 필터링되었을 수 있습니다. 필터링이 예상대로 구성되었는지 확인하려면 [필터링 구성](active-directory-aadconnectsync-configure-filtering.md) 항목을 읽어보세요.

또 다른 유용한 검색 방법은 Azure AD Connect를 선택하고 **범위**에서 **보류 중인 가져오기**를 선택한 후 **추가** 확인란을 선택하는 것입니다. 이 검색은 온-프레미스 개체와 연결될 수 없는 Azure AD의 모든 동기화된 개체를 제공합니다.  
![커넥터 공간 검색 고아](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
이러한 개체는 다른 동기화 엔진 또는 다른 필터링 구성을 갖는 동기화 엔진에서 만들어졌습니다. 이 보기는 더 이상 관리되지 않는 **고아** 개체 목록입니다. 이 목록을 검토하고 [Azure AD PowerShell](https://aka.ms/aadposh) cmdlet을 사용하여 이러한 개체를 제거하는 것이 좋습니다.

### <a name="cs-import"></a>CS 가져오기
cs 개체를 열면 위쪽에 여러 개의 탭이 표시됩니다. **가져오기** 탭은 가져온 후에 준비된 데이터를 표시합니다.  
![CS 개체](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
**이전 값**은 커넥터에 현재 저장된 값을 표시하고 **새 값**은 원본 시스템에서 받았지만 아직 적용되지 않은 값을 표시합니다. 개체에 오류가 발생하는 경우 변경 내용이 처리되지 않습니다.

**오류**  
![CS 개체](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
**동기화 오류** 탭은 개체에 문제가 있는 경우에만 표시됩니다. 자세한 내용은 [동기화 오류 해결](#troubleshoot-errors-in-operations-tab)을 참조하세요.

### <a name="cs-lineage"></a>CS 계보
계보 탭은 커넥터 공간 개체가 메타버스 개체와 관련된 방법을 보여 줍니다. 커넥터가 연결된 시스템에서 마지막으로 변경 내용을 가져온 시점 및 메타버스에 데이터를 채우는 데 적용된 규칙을 볼 수 있습니다.  
![CS 계보](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
**Action** 열에서 **Provision** 작업과 함께 하나의 **인바운드** 동기화 규칙이 있는 것을 볼 수 있습니다. 이는 이 커넥터 공간 개체가 있는 한 메타버스 개체가 그대로 유지됨을 나타냅니다. 대신 동기화 규칙 목록에 방향이 **아웃바운드** 및 **프로비전**인 동기화 규칙이 표시된 경우 이는 메타버스 개체가 삭제될 때 이 개체가 삭제됨을 나타냅니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
또한 **PasswordSync** 열에서 **True** 값이 있는 동기화 규칙으로 인바운드 커넥터 공간이 암호 변경에 영향을 줄 수 있다는 것을 볼 수 있습니다. 이 암호는 아웃바운드 규칙을 통해 Azure AD로 전송됩니다.

계보 탭에서 [메타버스 개체 속성](#mv-attributes)을 클릭하여 메타버스를 가져올 수 있습니다.

모든 탭의 아래쪽에 두 개의 단추인 **미리 보기**와 **로그**가 있습니다.

### <a name="preview"></a>미리 보기
미리 보기 페이지는 단일 개체를 동기화하는 데 사용됩니다. 이는 일부 고객 동기화 규칙 문제를 해결하고 단일 개체에서 변경 내용의 영향을 보려는 경우에 유용합니다. **전체 동기화**와 **델타 동기화** 중에서 선택할 수 있습니다. 또한 변경 내용만을 메모리에 유지하는 **미리 보기 생성**과 메타버스를 업데이트했으며 모든 변경 내용을 대상 커넥터 공간에 준비하는 **커밋 미리 보기** 중에서 선택할 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
개체 및 특정 특성 흐름에 적용되는 규칙을 검사할 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>로그
로그 페이지를 사용하여 암호 동기화 상태와 기록을 볼 수 있습니다. 자세한 내용은 [암호 해시 동기화 문제 해결](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md)을 참조하세요.

## <a name="metaverse-object-properties"></a>메타버스 개체 속성
일반적으로는 원본 Active Directory [커넥터 공간](#connector-space)에서 검색을 시작하는 것이 좋습니다. 하지만 메타버스에서 검색을 시작할 수도 있습니다.

### <a name="search-for-an-object-in-the-mv"></a>MV에서 개체 검색
**Synchronization Service Manager**에서 **메타버스 검색**을 클릭합니다. 사용자를 찾는 쿼리를 만듭니다. accountName(sAMAccountName) 및 UserPrincipalName 등의 일반적인 특성을 검색할 수 있습니다. 자세한 내용은 [메타버스 검색](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)을 참조하세요.
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

**검색 결과** 창에서 개체를 클릭합니다.

개체를 찾지 못한 경우 메타버스에 아직 도달하지 않은 것입니다. Active Directory [커넥터 공간](#connector-space-object-properties)에서 개체를 계속 검색합니다. 개체가 메타버스로 들어오지 못하게 하는 동기화 오류가 있거나 필터가 적용된 것일 수 있습니다.

### <a name="mv-attributes"></a>MV 특성
특성 탭에서는 특성 값과 기여한 커넥터를 볼 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

개체가 동기화되지 않을 경우 메타버스에서 다음 특성을 찾습니다.
- 특성 **cloudFiltered**가 있고 **true**로 설정되어 있나요? 그렇다면 [특성 기반 필터링](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering)의 단계에 따라 필터링된 것입니다.
- 특성 **sourceAnchor**가 있나요? 없으면 계정-리소스 포리스트 토폴로지가 있나요? 개체가 연결된 사서함으로 식별되는 경우(특성 **msExchRecipientTypeDetails**의 값이 2임) sourceAnchor는 Active Directory 계정이 사용하도록 설정된 포리스트에서 제공한 것입니다. 마스터 계정을 제대로 가져와 동기화했는지 확인합니다. 마스터 계정은 개체에 대한 [커넥터](#mv-connectors)에 나열되어야 합니다.

### <a name="mv-connectors"></a>MV 커넥터
커넥터 탭은 개체의 표현이 있는 모든 커넥터 공간을 표시합니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
다음에 대한 커넥터가 있어야 합니다.

- 사용자가 표시되는 각 Active Directory 포리스트. 이 표시에는 foreignSecurityPrincipals 및 Contact 개체가 포함될 수 있습니다.
- Azure AD의 커넥터.

Azure AD에 대한 커넥터가 없으면 [MV 특성](#mv-attributes)을 읽어 Azure AD로 프로비전되기 위한 기준을 확인합니다.

이 탭을 사용하여 [커넥터 공간 개체](#connector-space-object-properties)로 이동할 수도 있습니다. 행을 선택하고 **속성**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
