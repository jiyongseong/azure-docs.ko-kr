---
title: SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치 | Microsoft Docs
description: 이 항목에서는 SQL dbo 권한만이 있는 계정을 사용하여 설치를 허용하는 Azure AD Connect에 대한 업데이트를 설명합니다.
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: billmath
ms.openlocfilehash: 9cb32d137334141183831e703fb11cd3e6bd5a73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치
최신 Azure AD Connect 빌드에 앞서 SQL을 필요로 하는 구성을 배포할 때 관리 위임은 지원되지 않습니다.  Azure AD Connect를 설치하려는 사용자는 SQL server에서 SA(서버 관리자) 권한이 있어야 합니다.

최신 Azure AD Connect 릴리스를 사용하면 이제는 SQL 관리자가 대역 외에서 데이터베이스를 프로비전한 후 데이터베이스 소유권이 있는 Azure AD Connect 관리자가 설치할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
이 기능을 사용하려면 몇 가지 움직이는 부분들이 있으며 각 부분은 조직 내의 다른 관리자와 관련되어 있을 수 있습니다.  다음 표는 이 기능을 사용하여 Azure AD Connect를 배포할 때에 개별 역할 및 각 부분의 의무를 요약하여 보여줍니다.

|역할|설명|
|-----|-----|
|도메인 또는 Forest AD 관리자|Azure AD Connect가 동기화 서비스를 실행하는 데 사용하는 도메인 수준 서비스 계정을 만듭니다.  서비스 계정에 관한 자세한 내용은 [계정 및 권한](active-directory-aadconnect-accounts-permissions.md)을 참조하세요.
|SQL 관리자|ADSync 데이터베이스를 만들고 Azure AD Connect 관리자 및 도메인/포리스트 관리자가 만든 서비스 계정에 대한 login + dbo 액세스 권한을 부여합니다.|
Azure AD Connect 관리자|Azure AD Connect를 설치하고 사용자 정의 설치를 하는 중에 서비스 계정을 지정합니다.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>SQL 위임된 권한을 사용하여 Azure AD Connect를 설치하는 단계
대역 외 데이터베이스를 프로비전하고 데이터베이스 소유자 권한으로 Azure AD Connect를 설치하려면 다음 단계를 사용합니다.

>[!NOTE]
>필요하지는 않더라도 데이터베이스를 만들 때 Latin1_General_CI_AS 데이터 정렬을 선택하는 것이 **좋습니다**.


1.  SQL 관리자에게 대/소문자를 구분하지 않는 데이터 정렬 순서 **(Latin1_General_CI_AS)** 로 ADSync 데이터베이스를 만듭니다.  데이터베이스 이름은 **ADSync**로 지정해야 합니다.  Azure AD Connect가 설치될 때 복구 모델, 호환성 수준 및 포함 유형이 올바른 값으로 업데이트됩니다.  그러나 SQL 관리자가 데이터 정렬 순서를 올바르게 설정해야합니다. 그렇지 않으면 Azure AD Connect가 설치를 차단합니다.  SA를 복구하려면 데이터베이스를 삭제하고 다시 만들어야 합니다.</br>
![데이터 정렬](media/active-directory-aadconnect-sql-delegation/sql4.png)
2.  Azure AD Connect 관리자와 도메인 서비스 계정에 다음 권한을 부여합니다.
    - SQL 로그인 
    - **데이터베이스 소유자(dbo)** 권한.  </br>
![권한](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  Azure AD Connect를 설치할 때 Azure AD Connect 관리자에게 사용해야 하는 SQL 서버와 인스턴스 이름을 나타내는 이메일을 보냅니다.

## <a name="additional-information"></a>추가 정보
데이터베이스가 프로비전되면 Azure AD Connect 관리자는 편한 때에 온-프레미스 동기화를 설치 및 구성할 수 있습니다.  

**/UseExistingDatabase** 플래그는 미리 만든 데이터베이스를 사용할 때 필요합니다.  복구 상황에서만 사용되는 것은 아닙니다.

Azure AD Connect의 새로운 설치를 지원할 뿐 아니라 이 기능은 **/UseExistingDatabase** 플래그와 관련된 모든 시나리오에 대한 위임을 가능하게 합니다.  기존 데이터베이스와 함께 Azure AD Connect를 설치하는 방법에 대한 자세한 내용은 [기존 ADSync 데이터베이스를 사용하여 Azure AD Connect 설치](active-directory-aadconnect-existing-database.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [기본 설정을 사용하여 Azure AD Connect 시작](active-directory-aadconnect-get-started-express.md)
- [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
- [기존 ADSync 데이터베이스를 사용하여 Azure AD Connect 설치](active-directory-aadconnect-existing-database.md)  
