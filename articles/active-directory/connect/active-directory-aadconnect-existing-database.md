---
title: "기존 ADSync 데이터베이스를 사용하여 Azure AD Connect 설치 | Microsoft Docs"
description: "이 토픽에서는 기존 ADSync 데이터베이스를 사용하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: cychua
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: billmath
ms.openlocfilehash: d65e12350e6302b0f95091f48f71cdc7d4610e2c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>기존 ADSync 데이터베이스를 사용하여 Azure AD Connect 설치
Azure AD Connect는 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. Azure AD Connect로 설치된 기본 SQL Server 2012 Express LocalDB를 사용하거나 사용자 고유의 전체 버전 SQL을 사용할 수 있습니다. 이전에 Azure AD Connect를 설치할 때 ADSync라는 새 데이터베이스가 항상 만들어졌습니다. Azure AD Connect 버전 1.1.613.0(이상)을 사용하면 기존 ADSync 데이터베이스에 연결하여 Azure AD Connect를 설치하는 옵션도 있습니다.

## <a name="benefits-of-using-an-existing-adsync-database"></a>기존 ADSync 데이터베이스 사용 혜택
기존 ADSync 데이터베이스로 연결:

- 자격 증명 정보를 제외하고, ADSync 데이터베이스에 저장된 동기화 구성(사용자 지정 동기화 규칙, 커넥터, 필터링 및 선택적 기능 구성 포함)은 설치 중에 자동으로 복구 및 사용됩니다. Azure AD Connect에서 온-프레미스 AD와 Azure AD를 통해 변경 사항을 동기화하는 데 사용하는 자격 증명은 암호화되며 이전 Azure AD Connect 서버에서만 액세스할 수 있습니다.
- ADSync 데이터베이스에 저장된 ID 데이터(커넥터 공간과 메타 버스와 연결된) 및 동기화 쿠키도 모두 복구됩니다. 새로 설치된 Azure AD Connect 서버는 전체 동기화를 수행해야 하는 대신, 이전 Azure AD Connect 서버가 중지되었던 위치에서 동기화를 계속할 수 있습니다.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>기존 ADSync 데이터베이스를 사용하는 유용한 시나리오
이러한 혜택은 다음과 같은 시나리오에서 유용합니다.


- 기존 Azure AD Connect 배포가 있는 경우. 기존 Azure AD Connect 서버는 더 이상 작동하지 않지만 ADSync 데이터베이스를 포함하는 SQL Server는 여전히 작동합니다. 새 Azure AD Connect 서버를 설치할 수 있으며 기존 ADSync 데이터베이스로 가리킬 수 있습니다. 
- 기존 Azure AD Connect 배포가 있는 경우. ADSync 데이터베이스를 포함하는 SQL Server가 더 이상 작동하지 않습니다. 그러나 데이터베이스는 최근에 백업했습니다. 먼저 새 SQL Server로 ADSync 데이터베이스를 복원할 수 있습니다. 그 후 새 Azure AD Connect 서버를 설치하고 복원된 ADSync 데이터베이스로 가리킬 수 있습니다.
- LocalDB를 사용 중인 기존 Azure AD Connect 배포가 있는 경우. LocalDB에 적용되는 10GB 제한으로 인해 전체 SQL로 마이그레이션하고자 합니다. LocalDB에서 ADSync 데이터베이스를 백업하고 SQL Server로 복원할 수 있습니다. 그 후 새 Azure AD Connect 서버를 다시 설치하고 복원된 ADSync 데이터베이스로 가리킬 수 있습니다.
- 준비 서버를 설치하려고 하고 해당 구성이 현재 활성 서버에 있는 구성과 일치하는지 확인하려고 합니다. ADSync 데이터베이스를 백업하고 다른 SQL Server로 복원할 수 있습니다. 그 후 새 Azure AD Connect 서버를 다시 설치하고 복원된 ADSync 데이터베이스로 가리킬 수 있습니다.

## <a name="prerequisite-information"></a>필수 구성 요소 정보

다음은 계속 진행하기 전에 주목해야 할 중요한 참고 사항입니다.

- 하드웨어 및 필수 구성 요소에서 Azure AD Connect 설치를 위한 필수 조건 및 Azure AD Connect 설치를 위해 필요한 계정 및 권한을 검토합니다. “기존 데이터베이스 사용” 모드를 사용하여 Azure AD Connect를 설치할 때 필요한 권한은 “사용자 지정” 설치와 동일합니다.
- 기존 ADSync 데이터베이스에 대한 Azure AD Connect 배포는 전체 SQL에서만 지원됩니다. SQL Express LocalDB에서는 지원되지 않습니다. 사용하려는 LocalDB에 기존 ADSync 데이터베이스가 있는 경우 먼저 ADSync 데이터베이스(LocalDB)를 백업하고 전체 SQL로 복원해야 합니다. 그런 다음 이 방법을 사용하여 복원된 데이터베이스에 대해 Azure AD Connect를 배포할 수 있습니다.
- 설치에 사용되는 Azure AD Connect 버전은 다음 조건을 충족해야 합니다.
    - 1.1.613.0 이상 및
    - ADSync 데이터베이스와 함께 마지막으로 사용된 Azure AD Connect의 버전 이상이어야 합니다. 설치에 사용되는 Azure AD Connect 버전이 ADSync 데이터베이스와 함께 마지막으로 사용된 버전보다 높은 경우 전체 동기화가 필요할 수도 있습니다.  전체 동기화는 두 버전 간에 스키마 또는 동기화 규칙이 변경된 경우 필요합니다. 
- 사용되는 ADSync 데이터베이스는 비교적 최신인 동기화 상태를 포함해야 합니다. 기존 ADSync 데이터베이스를 사용한 마지막 동기화 작업은 지난 3주 이내여야 합니다.
- “기존 데이터베이스 사용” 메서드를 사용하여 Azure AD Connect를 설치하는 경우 이전 Azure AD Connect 서버에 구성된 로그인 메서드는 유지되지 않습니다. 또한 설치하는 동안 로그인 방법을 구성할 수 없습니다. 로그인 방법은 설치가 완료된 후에만 구성할 수 있습니다.
- 동일한 ADSync 데이터베이스를 공유하는 여러 Azure AD Connect 서버를 사용할 수 없습니다. “기존 데이터베이스 사용” 메서드를 사용하면 새 Azure AD Connect 서버와 함께 기존 ADSync 데이터베이스를 다시 사용할 수 있습니다. 공유는 지원하지 않습니다.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>“기존 데이터베이스 사용” 모드를 통해 Azure AD Connect를 설치하는 단계
1.  Windows 서버에 Azure AD Connect 설치 관리자(AzureADConnect.MSI)를 다운로드합니다. Azure AD Connect 설치 관리자를 두 번 클릭하여 Azure AD Connect 설치를 시작합니다.
2.  MSI 설치가 완료되면 Azure AD Connect 마법사가 기본 모드 설치를 시작합니다. 종료 아이콘을 클릭하여 화면을 닫습니다.
![시작](media/active-directory-aadconnect-existing-database/db1.png)
3.  새 명령 프롬프트 또는 PowerShell 세션을 시작합니다. <drive>\program files\Microsoft Azure AD Connect로 이동합니다. .\AzureADConnect.exe /useexistingdatabase 명령을 실행하여 Azure AD Connect 마법사를 “기존 데이터베이스 사용” 설치 모드로 시작합니다.
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Azure AD Connect 시작 화면이 표시됩니다. 사용 조건 및 개인 정보 취급 방침에 동의하면 **계속**을 클릭합니다.
![시작](media/active-directory-aadconnect-existing-database/db3.png)
5.  **필수 구성 요소 설치** 화면에서 **기존 SQL Server 사용** 옵션을 사용할 수 있습니다. ADSync 데이터베이스를 호스팅하는 SQL Server의 이름을 지정합니다. ADSync 데이터베이스를 호스팅하는 데 사용되는 SQL 엔진 인스턴스가 SQL Server에 있는 기본 인스턴스가 아닌 경우 SQL 엔진 인스턴스 이름을 지정해야 합니다. 또한 SQL 검색을 사용하지 않는 경우 SQL 엔진 인스턴스 포트 번호를 지정해야 합니다. 예:         
![시작](media/active-directory-aadconnect-existing-database/db4.png)           

6.  **Azure AD에 연결** 화면에서 Azure AD 디렉터리의 전역 관리자의 자격 증명을 제공해야 합니다. 기본 onmicrosoft.com 도메인의 계정을 사용하는 것이 좋습니다. 이 계정은 Azure AD에서 서비스 계정을 만드는 데에만 사용되며 마법사를 완료한 후에는 사용되지 않습니다.
![연결](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  **디렉터리 연결** 화면에서 디렉터리 동기화에 대해 구성된 기존 AD 포리스트가 옆에 빨간색 십자가 아이콘과 함께 나열됩니다. 온-프레미스 AD 포리스트에서 변경 내용을 동기화하려면 AD DS 계정이 필요합니다. 자격 증명이 암호화되고 이전 Azure AD Connect 서버에서만 해독할 수 있으므로 Azure AD Connect 마법사는 ADSync 데이터베이스에 저장된 AD DS 계정의 자격 증명을 검색하는 데 사용할 수 없습니다. **자격 증명 변경**을 클릭하여 AD 포리스트에 대한 AD DS 계정을 지정합니다.
![Directories](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  팝업 대화 상자에서 (i) 엔터프라이즈 관리자 자격 증명을 제공하고 Azure AD Connect에서 AD DS 계정을 만들도록 하거나 (ii) 직접 AD DS 계정을 만들고 Azure AD Connect에 해당 자격 증명을 제공할 수 있습니다. 옵션을 선택하고 필요한 자격 증명을 제공한 후 **확인**을 클릭하여 팝업 대화 상자를 닫습니다.
![시작](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  자격 증명이 제공되면 빨간색 십자가 아이콘이 녹색 체크 표시 아이콘으로 바뀝니다. **다음**을 누릅니다.
![시작](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. **구성 준비** 화면에서 **설치**를 클릭합니다.
![시작](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. 설치가 완료되면 Azure AD Connect 서버가 자동으로 준비 모드에 사용할 수 있게 설정됩니다. 준비 모드를 해제하기 전에 예기치 않은 변경 내용에 대한 서버 구성 및 보류 중인 내보내기를 검토하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

- Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](active-directory-aadconnect-whats-next.md)할 수 있습니다.
- [실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md)를 설치하여 사용할 수 있는 이러한 기능에 대해 자세히 알아봅니다.
- 공통 항목인 [스케줄러 및 동기화를 트리거하는 방법](active-directory-aadconnectsync-feature-scheduler.md)에 대해 자세히 알아봅니다.
- [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
