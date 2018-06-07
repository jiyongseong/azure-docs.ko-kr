---
title: Azure Active Directory 인증 구성 - SQL | Microsoft Docs
description: Azure Active Directory를 구성한 후에 Azure AD 인증을 사용하여 SQL Database, 관리되는 인스턴스 및 SQL Data Warehouse에 연결하는 방법을 알아봅니다.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: 5451046eb2bfc611db863d18cee93a248e651f88
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194252"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>SQL Database, 관리되는 인스턴스 또는 SQL Data Warehouse에서의 Azure Active Directory 인증 구성 및 관리

이 문서에서는 Azure AD를 만들고 채운 후 Azure SQL Database 및 SQL Data Warehouse에서 Azure AD를 사용하는 방법을 보여 줍니다. 개요는 [Azure Active Directory 인증](sql-database-aad-authentication.md)을 참조하세요.

>  [!NOTE]  
>  Azure VM에서 실행되는 SQL Server에 연결하는 경우 Azure Active Directory 계정은 사용할 수 없습니다. 대신 도메인 Active Directory 계정을 사용합니다.

## <a name="create-and-populate-an-azure-ad"></a>Azure AD 만들기 및 채우기
Azure AD를 만들고 사용자 및 그룹으로 채웁니다. Azure AD는 초기 Azure AD 관리되는 도메인일 수 있습니다. Azure AD는 Azure AD와 페더레이션된 온-프레미스 Active Directory Domain Services일 수도 있습니다.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/active-directory-aadconnect.md), [Azure AD에 고유한 도메인 이름 추가](../active-directory/active-directory-domains-add-azure-portal.md), [이제 Microsoft Azure에서 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD 디렉터리 관리](../active-directory/active-directory-administer.md), [Windows PowerShell을 사용한 Azure AD 관리](/powershell/azure/overview?view=azureadps-2.0) 및 [포트 및 프로토콜이 필요한 하이브리드 ID](..//active-directory/connect/active-directory-aadconnect-ports.md)를 참조하세요.

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory에 Azure 구독 연결 또는 추가

1. 해당 디렉터리를 데이터베이스를 호스트하는 Azure 구독에서 신뢰할 수 있는 디렉터리로 만들어 Azure Active Directory에 데이터베이스를 연결합니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](../active-directory/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.
2. Azure Portal의 디렉터리 전환기를 사용하여 도메인과 연결된 구독으로 전환합니다.

   **추가 정보:** 모든 Azure 구독은 Azure AD 인스턴스와 트러스트 관계가 있습니다. 이는 Azure 구독이 사용자, 서비스, 장치를 인증하는 해당 디렉터리를 신뢰함을 의미합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 구독은 하나의 디렉터리만 신뢰합니다. 구독이 디렉터리와 갖는 이 트러스트 관계는 구독이 Azure의 다른 모든 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다르며 구독의 하위 리소스와 더 유사합니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하여 디렉터리 사용자를 계속 관리할 수 있습니다. 리소스에 대한 자세한 내용은 [Azure의 리소스 액세스 이해](../active-directory/active-directory-b2b-admin-add-users.md)를 참조하세요. 이러한 신뢰 관계에 대한 자세한 내용은 [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../active-directory/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure SQL Server에 대한 Azure AD 관리자 만들기
각각의 Azure SQL Server(SQL Database 또는 SQL Data Warehouse를 호스트하는)는 전체 Azure SQL Server의 관리자인 단일 서버 관리자 계정으로 시작됩니다. Azure AD 계정인 두 번째 SQL Server 관리자를 만들어야 합니다. 이 주체는 마스터 데이터베이스에 포함된 데이터베이스 사용자로 생성됩니다. 관리자인 서버 관리자 계정은 모든 사용자 데이터베이스에서 **db_owner** 역할의 멤버이며 각 사용자 데이터베이스에 **dbo** 사용자로 들어갑니다. 서버 관리자 계정에 대한 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

Azure Active Directory와 함께 지역에서 복제를 사용할 때 Azure Active Directory 관리자는 주 서버와 보조 서버 모두에 대해 구성되어야 합니다. 서버에 Azure Active Directory 관리자가 없는 경우 다음 Azure Active Directory 로그인 및 사용자는 서버에 "연결할 수 없음" 오류를 수신합니다.

> [!NOTE]
> Azure AD 계정을 기반으로 하지 않는 사용자(Azure SQL Server 관리자 계정 포함)는 Azure AD에서 제안된 데이터베이스 사용자에 대한 유효성 검사 권한이 없으므로 Azure AD 기반 사용자를 만들 수 없습니다.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>관리되는 인스턴스에 대한 Azure Active Directory 관리자를 프로비전합니다.

> [!IMPORTANT]
> 관리되는 인스턴스를 프로비전하는 경우에만 다음 단계를 수행합니다. 이 작업은 Azure AD에서 전역/회사 관리자만 실행할 수 있습니다. 다음 단계에서는 디렉터리에서 서로 다른 권한을 가진 사용자에 대해 사용 권한을 부여하는 프로세스를 설명합니다.

관리되는 인스턴스는 보안 그룹 구성원 자격을 통한 사용자 인증 또는 새로운 사용자의 생성 같은 태스크를 성공적으로 수행하기 위해 Azure AD를 읽을 수 있는 권한이 필요합니다. 이를 위해서는 관리되는 인스턴스에 대한 권한을 부여하여 Azure AD를 읽어야 합니다. 이 작업은 포털 및 PowerShell의 두 가지 방법으로 수행할 수 있습니다. 다음 단계에서는 두 방법을 모두 안내합니다.

1. Azure Portal의 상단 오른쪽 끝에서 해당 연결을 선택하여 가능한 Active Directory 목록을 드롭다운합니다. 
2. 정확한 Active Directory를 기본 Azure AD로 선택합니다. 

   이 단계는 Active Directory와 연결된 구독을 관리되는 인스턴스와 연결하여 동일한 구독이 Azure AD 및 관리되는 인스턴스 둘 다에 사용되도록 합니다.
3. 관리되는 인스턴스로 이동한 후 Azure AD 통합에 사용할 인스턴스를 선택합니다.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4.  Active Directory 관리자 페이지 맨 위의 배너를 선택합니다. Azure AD의 글로벌/회사 관리자 권한으로 로그인된 경우 Azure Portal에서 또는 PowerShell을 사용하여 이 작업을 수행할 수 있습니다.

    ![권한 부여-포털](./media/sql-database-aad-authentication/grant-permissions.png)

    ![권한 부여-powershell](./media/sql-database-aad-authentication/grant-permissions-powershell.png)
    
    Azure AD의 글로벌/회사 관리자 권한으로 로그인된 경우 Azure Portal에서 또는 PowerShell 스크립트를 실행하여 이 작업을 수행할 수 있습니다.

5. 작업이 성공적으로 완료되면 다음과 같은 알림이 오른쪽 위 모서리에 표시됩니다.

    ![성공](./media/sql-database-aad-authentication/success.png)

6.  이제 관리되는 인스턴스에 대한 Azure AD 관리자를 선택할 수 있습니다. 이에 대해 Active Directory 관리자 페이지에서 **관리자 설정** 명령을 선택합니다.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. 관리자 추가 페이지에서 사용자를 검색하고 관리자가 될 사용자 또는 그룹을 선택한 다음, **선택**을 선택합니다. 

   Active Directory 관리 페이지에 해당 Active Directory에 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. [Azure AD 기능 및 제한 사항](sql-database-aad-authentication.md#azure-ad-features-and-limitations) 에서 지원되는 관리자 목록을 참조하세요. RBAC(역할 기반 액세스 제어)는 Azure Portal에만 적용되며 SQL Server에 전파되지 않습니다.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Active directory 관리자 페이지 위쪽에서 **저장**을 선택합니다.

    ![저장](./media/sql-database-aad-authentication/save.png)

    관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 Active Directory 관리자 상자에 표시됩니다.

> [!IMPORTANT]
> Azure AD 관리자를 설정하는 경우, 새 관리자 이름(사용자 또는 그룹)이 SQL Server 인증 사용자로 가상 master 데이터베이스에 있으면 안 됩니다. 새 관리자 이름이 있으면 Azure AD 관리자 설정은 실패하며, 생성 작업을 롤백하고 해당 관리자(이름)이 이미 존재한다는 것을 나타냅니다. SQL Server 인증 사용자는 Azure AD에 속하지 않기 때문에 Azure AD 인증을 사용하여 서버에 연결하려는 작업은 모두 실패합니다.

> [!TIP]
> 나중에 관리자를 제거하려면, Active Directory 관리자 페이지 위쪽에서 **관리자 제거**를 선택한 다음, **저장**을 선택합니다.
 
## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Azure SQL Database 서버에 대한 Azure Active Directory 관리자를 프로비전합니다

> [!IMPORTANT]
> Azure SQL Database 서버 또는 Data Warehouse를 프로비전하는 경우 다음 단계를 수행합니다.

다음 두 절차는 Azure Portal에서나 PowerShell을 사용하여 Azure SQL Server에 대한 Azure Active Directory 관리자를 프로비전하는 방법을 보여 줍니다.

### <a name="azure-portal"></a>Azure portal
1. [Azure Portal](https://portal.azure.com/)의 상단 오른쪽 끝에서 해당 연결을 선택하여 가능한 Active Directory 목록을 드롭다운합니다. 정확한 Active Directory를 기본 Azure AD로 선택합니다. 이 단계는 구독 연결 Active Directory를 Azure SQL Server와 연결하여 동일한 구독이 두 Azure AD 및 SQL Server에 사용되게 합니다. (Azure SQL Server는 Azure SQL Database 또는 Azure SQL Data Warehouse에서 호스트할 수 있습니다.)   
    ![choose-ad][8]   
    
2. 왼쪽 배너에서 **모든 서비스**를 선택하고, 필터 종류에 **SQL Server**를 입력합니다. **Sql Server**를 선택합니다. 

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)    

    >[!NOTE]
    > 이 페이지에서 **SQL Server**를 선택하기 전에 이름 옆에 있는 **별모양**을 선택하면 범주를 *즐겨 찾기에 추가*하고 왼쪽 탐색 모음에 **SQL Server**를 추가할 수 있습니다. 

1. **SQL Server** 페이지에서 **Active Directory 관리자**를 선택합니다.   
2. **Active Directory 관리자** 페이지에서 **관리자 설정**을 선택합니다.   
    ![active directory 선택](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. **관리자 추가** 페이지에서 사용자를 검색하고 관리자가 될 사용자 또는 그룹을 선택한 다음, **선택**을 선택합니다. Active Directory 관리자 페이지에는 Active Directory의 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. [SQL Database 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)의 **Azure AD 기능 및 제한 사항** 섹션에서 지원되는 관리자 목록을 참조하세요. 역할 기반 액세스 제어(RBAC)는 포털에만 적용되며 SQL Server에 전파되지 않습니다.   
    ![관리자 선택](./media/sql-database-aad-authentication/select-admin.png)  
    
5. **Active directory 관리자** 페이지 위쪽에서 **저장**을 선택합니다.   
    ![관리자 저장](./media/sql-database-aad-authentication/save-admin.png)   

관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 **Active Directory 관리자** 상자에 표시됩니다.

   > [!NOTE]
   > Azure AD 관리자를 설정하는 경우, 새 관리자 이름(사용자 또는 그룹)이 SQL Server 인증 사용자로 가상 master 데이터베이스에 있으면 안 됩니다. 새 관리자 이름이 있으면 Azure AD 관리자 설정은 실패하며, 생성 작업을 롤백하고 해당 관리자(이름)이 이미 존재한다는 것을 나타냅니다. SQL Server 인증 사용자는 Azure AD에 속하지 않기 때문에 Azure AD 인증을 사용하여 서버에 연결하려는 작업은 모두 실패합니다.
   > 


나중에 관리자를 제거하려면 **Active Directory 관리자** 페이지 위쪽에서 **관리자 제거**를 선택한 다음, **저장**을 선택합니다.

### <a name="powershell"></a>PowerShell
PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.

Azure AD 관리자를 프로비전하려면 다음 Azure PowerShell 명령을 실행합니다.

* Connect-AzureRmAccount
* Select-AzureRmSubscription

Azure AD 관리자 프로비전 및 관리에 사용되는 Cmdlet

| Cmdlet 이름 | 설명 |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 프로비전합니다. (현재 구독 설정에서 수행되어야 함). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 제거합니다. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |현재 Azure SQL Server 또는 Azure SQL Data Warehouse에 대해 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |

각 명령에 대한 자세한 내용을 확인하려면 PowerShell 명령 get-help를 사용합니다(예: ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``).

다음 스크립트는 리소스 그룹 **Group-23**에서 **demo_server** 서버에 대해 이름이 **DBA_Group**(개체 ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`)인 Azure AD 관리자 그룹을 프로비전합니다.

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 입력 매개 변수에는 Azure AD 표시 이름이나 사용자 계정 이름이 허용됩니다. 예를 들어 ``DisplayName="John Smith"`` 또는 ``DisplayName="johns@contoso.com"``입니다. Azure AD 그룹에는 Azure AD 표시 이름만 지원됩니다.

> [!NOTE]
> Azure PowerShell 명령 ```Set-AzureRmSqlServerActiveDirectoryAdministrator```는 지원되지 않는 사용자에 대한 Azure AD 관리자 프로비전을 차단하지 않습니다. 지원되지 않는 사용자를 프로비전할 수는 있지만 데이터베이스에 연결할 수는 없습니다. 
> 

다음 예제에서는 **ObjectID**옵션을 사용합니다.

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName**이 고유하지 않을 경우 Azure AD **ObjectID**가 필수입니다. **ObjectID** 및 **DisplayName** 값을 검색하려면 Azure 클래식 포털의 Active Directory 섹션을 사용하고 사용자 또는 그룹의 속성을 확인합니다.
> 

다음 예제에서는 Azure SQL Server에 대해 현재 Azure AD 관리자 정보를 반환합니다.

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

다음 예제에서는 Azure AD 관리자를 제거합니다.

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

REST API를 사용하여 Azure Active Directory 관리자를 프로비전할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조 및 Azure SQL Database에 대한 작업](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>CLI  
또한 다음 CLI 명령을 호출하여 Azure AD 관리자를 구축할 수도 있습니다.
| 명령 | 설명 |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 프로비전합니다. (현재 구독 설정에서 수행되어야 함). |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 제거합니다. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |현재 Azure SQL Server 또는 Azure SQL Data Warehouse에 대해 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 업데이트합니다. |

CLI 명령에 대한 자세한 내용은 [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server)을 참조하세요.  


## <a name="configure-your-client-computers"></a>클라이언트 컴퓨터 구성
모든 클라이언트 컴퓨터에서 Azure AD를 사용하여 Azure SQL Database 또는 Azure SQL Data Warehouse에 연결하는 응용 프로그램 또는 사용자를 통해 다음 소프트웨어를 설치해야 합니다.

* [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)에서 .NET Framework 4.6 이상
* SQL Server용 Azure Active Directory 인증 라이브러리(**ADALSQL.DLL**)는 다운로드 센터( [Microsoft SQL Server용 Microsoft Active Directory 인증 라이브러리)](http://www.microsoft.com/download/details.aspx?id=48742)에서 여러 언어로 제공됩니다(x86 및 amd64 모두 해당).

다음을 통해 이러한 요구 사항을 충족할 수 있습니다.

* [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 또는 [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx)를 설치하면 .NET Framework 4.6 요구 사항에 부합합니다.
* SSMS이 x86 버전의 **ADALSQL.DLL**을 설치합니다.
* SSDT가 amd64 버전의 **ADALSQL.DLL**을 설치합니다.
* [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 의 최신 Visual Studio는 .NET Framework 4.6 요구 사항을 만족하지만, 필요한 amd64 버전의 **ADALSQL.DLL**을 설치하지 않습니다.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기

Azure Active Directory 인증에는 포함된 데이터베이스 사용자로 만들 데이터베이스 사용자가 필요합니다. Azure AD ID를 기반으로 하는 포함된 데이터베이스 사용자는 마스터 데이터베이스에 로그인이 없는 데이터베이스 사용자이며, 데이터베이스와 연결된 Azure AD 디렉터리의 ID에 매핑됩니다. Azure AD ID는 개별 사용자 계정 또는 그룹일 수 있습니다. 포함된 데이터베이스 사용자에 대한 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

> [!NOTE]
> 데이터베이스 사용자(관리자 예외)는 Azure Portal을 사용하여 만들 수 없습니다. RBAC 역할은 SQL Server, SQL Database 또는SQL Data Warehouse에 전파되지 않습니다. Azure RBAC 역할은 Azure 리소스 관리에 사용되며 데이터베이스 사용 권한에는 적용되지 않습니다. 예를 들어 **SQL Server 참여자** 역할은 SQL Database 또는 SQL Data Warehouse에 연결 권한을 부여하지 않습니다. TRANSACT-SQL 문을 사용하여 데이터베이스에 직접 액세스 권한을 부여해야 합니다.
>

Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 만들려면 **ALTER ANY USER** 이상의 권한이 있는 사용자인 Azure AD ID를 통해 데이터베이스에 연결합니다. 그런 다음 아래 TRANSACT-SQL 구문을 사용합니다.

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name*은 Azure AD 사용자의 사용자 계정 이름이거나 Azure AD 그룹의 표시 이름일 수 있습니다.

**예:** Azure AD 페더레이션 또는 관리 도메인 사용자를 나타내는 포함된 데이터베이스 사용자를 만드는 방법
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Azure AD 또는 페더레이션 도메인 그룹을 나타내는 포함된 데이터베이스 사용자를 만들려면 보안 그룹의 표시 이름을 제공하십시오.
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Azure AD 토큰을 사용하여 연결할 응용 프로그램을 나타내는 포함된 데이터베이스 사용자를 만들려면 다음을 실행합니다.

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Azure 구독과 연결된 Azure Active Directory 이외의 Azure Active Directory에서 사용자를 직접 만들 수 없습니다. 그러나 연결된 Active Directory에서 가져온 사용자(외부 사용자로 알려짐)인 다른 Active Directory의 멤버는 테넌트 Active Directory의 Active Directory 그룹에 추가할 수 있습니다. 해당 AD 그룹에 대해 포함된 데이터베이스 사용자를 만들면 외부 Active Directory의 사용자는 SQL Database에 액세스할 수 있습니다.   

Azure Active Directory 기반의 포함된 데이터베이스 사용자 만들기와 관련한 자세한 내용은 [CREATE USER(Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.

> [!NOTE]
> Azure SQL Server에 대한 Azure Active Directory 관리자를 제거하면 Azure AD 인증 사용자가 서버에 연결되지 않도록 할 수 있습니다. 필요한 경우 사용할 수 없는 Azure AD 사용자는 SQL Database 관리자가 수동으로 삭제할 수 있습니다.   

>  [!NOTE]
>  **연결 시간 초과 만료됨**을 수신하면 연결 문자열의 `TransparentNetworkIPResolution` 매개 변수를 false로 설정하지 않아도 됩니다. 자세한 내용은 [.NET Framework 4.6.1의 연결 시간 초과 문제 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)을 참조하세요.   

   
데이터베이스 사용자를 만들 때 해당 사용자는 **연결** 권한을 부여 받으며 **공용** 역할의 멤버로서 해당 데이터베이스에 연결할 수 있습니다. 처음에 이 사용자에게 제공되는 권한은 **공용** 역할에 부여된 권한이거나 사용자가 속해 있는 Azure AD 그룹에 부여된 권한뿐입니다. Azure AD 기반의 포함된 데이터베이스 사용자를 프로비전한 후에는 다른 사용자 유형에 권한을 부여하는 것과 같은 방식으로 이 사용자에게 추가 권한을 부여할 수 있습니다. 일반적으로 데이터베이스 역할에 권한을 부여하고 역할에 사용자를 추가합니다. 자세한 내용은 [데이터베이스 엔진의 권한 기초](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)를 참조하세요. 특수 SQL Database 역할에 대한 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.
관리되는 도메인에 외부 사용자로 가져온 페더레이션된 도메인 사용자 계정은 관리되는 도메인 ID를 사용해야 합니다.

> [!NOTE]
> Azure AD 사용자는 데이터베이스 메타데이터에서 E 형식(EXTERNAL_USER) 및 그룹의 경우 X 형식(EXTERNAL_GROUPS)으로 표시됩니다. 자세한 내용은 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)를 참조하세요. 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>SSMS 또는 SSDT를 사용하여 사용자 데이터베이스 또는 데이터 웨어하우스에 연결  
Azure AD 관리자가 제대로 설정되었는지 확인하려면 Azure AD 관리자 계정을 사용하여 **master** 데이터베이스에 연결합니다.
Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 프로비전하려면 해당 데이터베이스에 대한 액세스가 있는 Azure AD ID로 데이터베이스에 연결합니다.

> [!IMPORTANT]
> Azure Active Directory 인증에 대한 지원은 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 Visual Studio 2015의 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)에서 사용 가능합니다. SSMS의 2016년 8월 릴리스에는 관리자가 전화 통화, 문자 메시지, PIN이 있는 스마트 카드 또는 모바일 앱 알림을 사용하여 Multi-Factor Authentication을 요구할 수 있도록 하는 Active Directory 유니버설 인증도 포함되어 있습니다.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>SSMS 또는 SSDT를 사용하여 연결하는 데 Azure AD ID 사용  

다음 절차에서는 SQL Server Management Studio 또는 SQL Server Database Tools를 사용하여 SQL Database를 Azure AD ID에 연결하는 방법을 보여 줍니다.

### <a name="active-directory-integrated-authentication"></a>Active Directory 통합 인증

페더레이션된 도메인의 Azure Active Directory 자격 증명을 사용하여 Windows에 로그인한 경우 이 방법을 사용합니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진 연결**) 대화 상자의 **인증** 상자에서 **Active Directory 통합**을 선택합니다. 연결에 대한 기존 자격 증명이 있으므로 암호 입력이 필요하지 않습니다.   

    ![AD 통합 인증 선택][11]
2. **옵션** 단추를 선택하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. **AD 도메인 이름 또는 테넌트 ID** 옵션은 **MFA 연결 옵션이 있는 유니버설**에서만 지원되며 그 밖의 경우는 회색으로 표시됩니다.  

    ![데이터베이스 이름 선택][13]

## <a name="active-directory-password-authentication"></a>Active Directory 암호 인증

Azure AD 관리 도메인을 사용하여 Azure AD 사용자 이름과 연결할 때 이 방법을 사용합니다. 원격 작업 등, 도메인 액세스 없이 페더레이션된 계정에도 이 방법을 사용할 수 있습니다.

이 방법을 사용하여 Azure AD가 있는 SQL DB/DW를 페더레이션된 Azure AD 사용자의 네이티브에 대해 인증합니다.
기본 사용자는 Azure AD에서 명시적으로 만들어지고 사용자 이름 및 암호를 사용하여 인증되지만, 페더레이션된 사용자는 도메인이 Azure AD와 함께 페더레이션된 Windows 사용자입니다. 후자의 방법(사용자 및 암호 사용)은 사용자가 자신의 Windows 자격 증명을 사용하려고 하지만 자신의 로컬 컴퓨터가 도메인에 가입되어 있지 않은 경우(예: 원격 액세스 사용) 사용할 수 있습니다. 이 경우 Windows 사용자는 자신의 도메인 계정과 암호를 표시할 수 있으며 페더레이션된 자격 증명을 사용하여 SQL DB/DW를 인증할 수 있습니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진 연결**) 대화 상자의 **인증** 상자에서 **Active Directory - 암호**를 선택합니다.
2. **사용자 이름** 상자에 **username@domain.com** 형식으로 Azure Active Directory 사용자 이름을 입력합니다. Azure Active Directory의 계정이거나, Azure Active Directory와 페더레이션된 도메인의 계정이어야 합니다.
3. **암호** 상자에 Azure Active Directory 계정이나 페더레이션된 도메인 계정의 사용자 암호를 입력합니다.

    ![AD 암호 인증 선택][12]
4. **옵션** 단추를 선택하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. (이전 옵션의 그래픽을 참조하세요.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>클라이언트 응용 프로그램에서 연결하는 데 Azure AD ID 사용

다음 절차에서는 클라이언트 응용 프로그램에서 SQL Database를 Azure AD ID에 연결하는 방법을 보여 줍니다.

###  <a name="active-directory-integrated-authentication"></a>Active Directory 통합 인증

Windows 통합 인증을 사용하려면 도메인의 Active Directory를 Azure Active Directory와 페더레이션해야 합니다. 데이터베이스에 연결되는 클라이언트 응용 프로그램(또는 서비스)은 사용자의 도메인 자격 증명으로 도메인에 가입된 컴퓨터에서 실행되어야 합니다.

통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 데이터베이스 연결 문자열의 인증 키워드가 Active Directory 통합으로 설정되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

연결 문자열 키워드 ``Integrated Security=True``는 Azure SQL Database 연결에 지원되지 않습니다. ODBC 연결을 설정할 때는 공백을 제거하고 인증을 'ActiveDirectoryIntegrated'로 설정해야 합니다.

### <a name="active-directory-password-authentication"></a>Active Directory 암호 인증

통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 인증 키워드가 Active Directory 암호로 설정되어 있어야 합니다. 연결 문자열에는 사용자 ID/UID 및 암호/PWD 키워드와 값이 포함되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

[Azure AD 인증 GitHub 데모](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)에서 사용할 수 있는 데모 코드 샘플을 사용한 Azure AD 인증 방법에 대해 자세히 알아보세요.

## <a name="azure-ad-token"></a>Azure AD 토큰
이 인증 방법을 사용하면 AAD(Azure Active Directory)에서 토큰을 가져와 Azure SQL Database 또는 AzureSQL Data Warehouse에 연결할 수 있습니다. 이는 인증서 기반 인증을 비롯한 정교한 시나리오를 지원합니다. Azure AD 토큰 인증을 사용하려면 다음 네 가지 기본 단계를 완료해야 합니다.

1. Azure Active Directory에 응용 프로그램을 등록하고 코드에 대한 클라이언트 ID를 가져옵니다. 
2. 응용 프로그램을 나타내는 데이터베이스 사용자를 만듭니다(이전 6단계에서 완료).
3. 응용 프로그램을 실행하는 클라이언트 컴퓨터에서 인증서를 만듭니다.
4. 인증서를 응용 프로그램의 키로 추가합니다.

샘플 연결 문자열:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

자세한 내용은 [SQL Server 보안 블로그](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)를 참조하세요. 인증서 추가에 대한 정보는 [Azure Active Directory에서 인증서 기반 인증 시작](../active-directory/active-directory-certificate-based-authentication-get-started.md)을 참조하세요.

### <a name="sqlcmd"></a>sqlcmd

다음 문은 [다운로드 센터](http://go.microsoft.com/fwlink/?LinkID=825643)에서 사용할 수 있는 sqlcmd 버전 13.1을 사용하여 연결합니다.

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>다음 단계
- SQL Database의 액세스 및 제어에 대한 개요는 [SQL Database 액세스 및 제어](sql-database-control-access.md)를 참조하세요.
- SQL Database의 로그인, 사용자 및 데이터베이스 역할에 대한 개요는 [로그인, 사용자 및 데이터베이스 역할](sql-database-manage-logins.md)을 참조하세요.
- 데이터베이스 보안 주체에 대한 자세한 내용은 [보안 주체](https://msdn.microsoft.com/library/ms181127.aspx)를 참조하세요.
- 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할](https://msdn.microsoft.com/library/ms189121.aspx)을 참조하세요.
- SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](sql-database-firewall-configure.md)을 참조하세요.

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

