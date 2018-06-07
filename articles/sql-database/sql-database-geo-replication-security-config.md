---
title: 재해 복구를 위해 Azure SQL Database 보안 구성 | Microsoft Docs
description: 데이터베이스를 복원하거나 보조 서버로 장애 조치 후 보안을 구성하고 관리하기 위한 보안 고려 사항을 설명합니다.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: 6b4b599fee9a8ff5b4674cf26ca19b22230d6364
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>지역 복원 또는 장애 조치를 위해 Azure SQL Database 보안 구성 및 관리 

이 항목은 [활성 지역 복제](sql-database-geo-replication-overview.md) 구성 및 제어에 대한 인증 요구 사항 및 보조 데이터베이스에 대한 사용자 액세스 설정이 필요한 단계를 설명합니다. 또한 [지역 복원](sql-database-recovery-using-backups.md#geo-restore)을 사용한 후에 복구된 데이터베이스에 대한 액세스를 활성화하는 방법도 설명합니다. 복구 옵션에 대한 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

> [!NOTE]
> [활성 지역 복제](sql-database-geo-replication-overview.md)는 현재 모든 서비스 계층의 모든 데이터베이스에 대해 제공됩니다.
>  

## <a name="disaster-recovery-with-contained-users"></a>포함된 사용자를 사용한 재해 복구
master 데이터베이스에서 로그인에 매핑되어야 하는 기존 사용자와 달리 포함된 사용자는 데이터베이스 자체에서 완전히 관리됩니다. 두 가지 이점이 있습니다. 재해 복구 시나리오에서 데이터베이스는 사용자를 관리하므로 사용자는 추가 구성 없이 지역 복원을 사용하여 복구된 새로운 주 데이터베이스 또는 데이터베이스에 계속해서 연결할 수 있습니다. 로그인 관점에서 이 구성에는 잠재적인 확장성 및 성능 이점이 있습니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요. 

기본 절충점은 대규모로 재해 복구 프로세스를 관리하는 것이 좀 더 어렵다는 점입니다. 동일한 로그인을 사용하는 여러 데이터베이스가 있는 경우 여러 데이터베이스에 포함된 사용자를 사용하여 자격 증명을 유지 관리하면 포함된 사용자의 이점이 없어질 수 있습니다. 예를 들어 암호 회전 정책에 따라 마스터 데이터베이스에 한 번 로그인하기 위해 암호를 변경하는 대신 여러 데이터베이스에 변경 내용이 일관되어야 합니다. 이러한 이유로 동일한 사용자 이름 및 암호를 사용하는 여러 데이터베이스가 있는 경우 포함된 사용자를 사용하는 것은 좋지 않습니다. 

## <a name="how-to-configure-logins-and-users"></a>로그인 및 사용자를 구성하는 방법
로그인 및 사용자를 사용하는 경우(포함된 사용자 대신) 마스터 데이터베이스에 동일한 로그인이 존재하는지 보장하는 추가 단계를 수행해야 합니다. 다음 섹션에서는 관련된 단계 및 추가 고려 사항을 간략히 설명합니다.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>보조 또는 복구된 데이터베이스에 대한 사용자 액세스 설정
보조 데이터베이스를 읽기 전용 보조 데이터베이스로 사용할 수 있고 새로운 주 데이터베이스 또는 지역 복원 기능을 사용하여 복구된 데이터베이스에 적절하게 액세스할 수 있기 위해 복구하기 전에 대상 서버의 마스터 데이터베이스에 적절한 보안 구성이 있어야 합니다.

각 단계에 대한 특정 사용 권한은 이 항목의 뒷 부분에서 설명됩니다.

지역에서 복제 보조 데이터베이스에 대한 사용자 액세스를 준비하는 작업은 지역에서 복제를 구성하는 일환으로 수행되어야 합니다. 지역 복원된 데이터베이스에 대한 사용자 액세스를 준비하는 작업은 원래 서버가 온라인인 경우 언제든지 수행되어야 합니다(예: DR 드릴의 일부).

> [!NOTE]
> 적절히 구성된 로그인 액세스가 없는 서버에 장애 조치 또는 지역 복원을 수행하는 경우 서버 관리자 계정으로 제한됩니다.
> 
> 

대상 서버에서 로그인을 설정하는 작업은 아래에서 설명할 세 가지 단계를 포함합니다.

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. 주 데이터베이스에 대한 액세스를 사용하여 로그인을 확인합니다.
프로세스의 첫 번째 단계는 대상 서버에 중복되어야 하는 로그인을 결정하는 것입니다. 이는 원본 서버의 논리적 master 데이터베이스에 있는 하나와 주 데이터베이스 자체에 있는 하나로 이루어진 SELECT 문 쌍으로 수행됩니다.

서버 관리자 또는 **LoginManager** 서버 역할의 멤버만 다음 SELECT 문을 사용하여 원본 서버에서 로그인을 결정할 수 있습니다. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

db_owner 데이터베이스 역할의 멤버, dbo 사용자 또는 서버 관리자만 주 데이터베이스에서 모든 데이터베이스 사용자 계정을 결정할 수 있습니다.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. 1단계에서 확인된 로그인에 대한 SID를 찾습니다.
이전 섹션에서 쿼리의 출력을 비교하고 SID와 일치하여 서버 로그인을 데이터베이스 사용자에 매핑할 수 있습니다. 일치하는 SID가 있는 데이터베이스 사용자를 가진 로그인은 해당 데이터베이스 사용자 계정으로 해당 데이터베이스에 대한 사용자 액세스를 가집니다. 

다음 쿼리를 사용하여 데이터베이스에서 모든 사용자 계정 및 해당 SID를 볼 수 있습니다. db_owner 데이터베이스 역할의 멤버 또는 서버 관리자만 이 쿼리를 실행할 수 있습니다.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** 및 **sys** 사용자는 *NULL* SID가 있으며 **게스트** SID는 **0x00**입니다. 데이터베이스 작성자가 **DbManager**의 멤버가 아닌 서버 관리자인 경우 **dbo** SID는 *0x01060000000001648000000000048454*로 시작할 수 있습니다.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. 대상 서버의 로그인을 만듭니다.
마지막 단계는 대상 서버 또는 서버로 이동하고 적절한 SID를 사용하여 로그인을 생성하는 것입니다. 기본 구문은 다음과 같습니다.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> 주 데이터베이스가 아닌 보조 데이터베이스에 사용자 액세스 권한을 부여하려는 경우 주 서버의 사용자 로그인을 변경하고 다음 구문을 사용하여 수행할 수 있습니다.
> 
> ALTER LOGIN <login name> DISABLE
> 
> DISABLE은 암호를 변경하지 않으므로 필요한 경우 항상 사용하도록 설정할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* 데이터베이스 액세스 및 로그인 관리에 대한 자세한 내용은 [SQL Database 보안: 데이터베이스 액세스 및 로그인 보안 관리](sql-database-manage-logins.md)를 참조하세요.
* 포함된 데이터베이스 사용자에 대한 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.
* 활성 지역 복제를 사용 및 구성하는 방법에 대한 자세한 내용은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
* 지역 복원을 사용하는 방법에 대한 내용은 [지역 복원](sql-database-recovery-using-backups.md#geo-restore)을 참조하세요.

