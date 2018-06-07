---
title: DMS를 사용하여 Azure SQL Database Managed Instance로 마이그레이션 | Microsoft Docs
description: Azure Database Migration Service를 사용하여 SQL Server 온-프레미스에서 Azure SQL Database 관리되는 인스턴스로 마이그레이션하는 방법에 대해 알아봅니다.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: bb7cc17c36809975e26c8da8beda004a0b0cfd9e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774151"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>DMS를 사용하여 Azure SQL Database Managed Instance로 SQL Server 마이그레이션
Azure Database Migration Service를 사용하여 온-프레미스 SQL Server 인스턴스에서 [Azure SQL Database 관리되는 인스턴스](../sql-database/sql-database-managed-instance.md)로 마이그레이션할 수 있습니다. 수동 작업이 필요한 추가적인 방법은 [SQL Server 인스턴스를 Azure SQL Database 관리되는 인스턴스로 마이그레이션](../sql-database/sql-database-managed-instance-migrate.md) 문서를 참조하세요.

> [!IMPORTANT]
> SQL Server에서 Azure SQL Database 관리되는 인스턴스로의 마이그레이션 프로젝트는 미리 보기 상태이며 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)의 적용을 받습니다.

이 자습서에서는 Azure Database Migration Service를 사용하여 **Adventureworks2012** 데이터베이스를 SQL Server의 온-프레미스 인스턴스에서 Azure SQL Database 관리되는 인스턴스로 마이그레이션합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 마이그레이션 보고서를 다운로드합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 VNET을 만듭니다. 이를 통해 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 온-프레미스 원본 서버에서 사이트 간 연결을 제공합니다. [Azure Database Migration Service를 사용한 Azure SQL DB 관리되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지에 대해 알아봅니다](https://aka.ms/dmsnetworkformi).
- Azure VNET(Virtual Network) 네트워크 보안 그룹 규칙이 443, 53, 9354, 445, 12000과 같은 통신 포트를 차단하지 않는지 확인합니다. Azure VNET NSG 트래픽 필터링에 대한 자세한 정보는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
- [소스 데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있도록 방화벽을 통해 UDP 포트 1434에 액세스할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 SMB 포트 445를 통한 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
- [Azure Portal에서 Azure SQL Database Managed Instance 만들기](https://aka.ms/sqldbmi) 문서의 세부 지침에 따라 Azure SQL Database Managed Instance를 만듭니다.
- 원본 SQL Server와 대상 관리되는 인스턴스를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.
- Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 네트워크 공유를 만듭니다.
- 원본 SQL Server 인스턴스를 실행 중인 서비스 계정에 본인이 만든 네트워크 공유에 대한 쓰기 권한이 있고, 원본 서버의 컴퓨터 계정에 동일한 공유에 대한 읽기/쓰기 액세스 권한이 있는지 확인하세요.
- 이전에 만든 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure Database Migration Service는 사용자 자격 증명을 가장하여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드합니다.
- [저장소 탐색기를 사용하여 Azure Blob Storage 리소스 관리](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 문서의 단계를 사용하여 Blob 컨테이너를 만들고 해당 SAS URI를 검색합니다. SAS URI를 만드는 동안 정책 창에서 모든 권한(읽기, 쓰기, 삭제, 나열)을 선택해야 합니다. 이 세부 정보는 Azure SQL Database 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 데 사용되는 백업 파일을 업로드할 수 있도록 저장소 계정 컨테이너에 대한 액세스 권한을 Azure Database Migration Service에 제공합니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고 **모든 서비스**를 선택한 후 **구독**을 선택합니다.

    ![포털 구독 표시](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, **Azure Database Migration Service**를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. 기존 가상 네트워크(VNET)를 선택하거나 새로 만듭니다.
 
    VNET은 원본 SQL Server 및 대상 Azure SQL Database 관리되는 인스턴스에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다.

    Azure Portal에서 VNET을 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](https://aka.ms/DMSVnet) 문서를 참조하세요.

    자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL DB 관리되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지](https://aka.ms/dmsnetworkformi) 문서를 참조하세요.

5. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.
   
    ![DMS 서비스 만들기](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 인스턴스를 검색하고 인스턴스를 선택합니다.
 
3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database 관리되는 인스턴스**를 선택합니다.

   ![DMS 프로젝트 만들기](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. **만들기**를 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되지 않은 경우 인스턴스가 시작될 때SQL Server가 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 SSL 연결은 강력한 보안을 제공하지 않습니다. 메시지 가로채기(man-in-the-middle) 공격을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명 인증서를 사용하는 SSL을 신뢰해서는 안 됩니다.

   ![원본 세부 정보](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. **저장**을 선택합니다.

4. **원본 데이터베이스 선택** 화면에서 마이그레이션할 **Adventureworks2012** 데이터베이스를 선택합니다.

   ![원본 데이터베이스 선택](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1.  **대상 세부 정보** 화면에서 대상에 대한 연결 세부 정보를 지정합니다. 대상은 **AdventureWorks2012** 데이터베이스가 마이그레이션될 미리 프로비저닝된 Azure SQL Database 관리되는 인스턴스입니다.

    Azure SQL Database 관리되는 인스턴스를 아직 프로비저닝하지 않은 경우 인스턴스를 프로비저닝하는 데 도움이 되는 링크에 대해 **아니요**를 선택합니다. 프로젝트 생성은 계속 진행할 수 있으며, Azure SQL Database 관리되는 인스턴스가 준비되면 이 특정 프로젝트로 돌아가서 마이그레이션을 실행합니다.   
 
       ![대상 선택](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  **저장**을 선택합니다.

3.  **프로젝트 요약** 화면에서 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.
 
    ![마이그레이션 프로젝트 요약](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  **저장**을 선택합니다.   

## <a name="run-the-migration"></a>마이그레이션 실행

1.  최근에 저장된 프로젝트를 선택하고, **+ 새 활동**을 선택한 다음, **마이그레이션 실행**을 선택합니다.

    ![새 활동 만들기](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  메시지가 표시되면 원본 및 대상 서버에 대한 자격 증명을 입력한 다음, **저장**을 선택합니다.

3.  **원본 데이터베이스 선택** 화면에서 마이그레이션할 원본 데이터베이스를 선택합니다.

    ![원본 데이터베이스 선택](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  **저장**을 선택한 다음, **마이그레이션 설정 구성** 화면에서 다음 세부 정보를 입력합니다.

    | | |
    |--------|---------|
    |**네트워크 위치 공유** | Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 로컬 네트워크 공유입니다. 원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 네트워크 공유에 대한 쓰기 권한이 있어야 합니다. 네트워크 공유에 있는 서버의 FQDN 또는 IP 주소를 입력합니다(예: '\\\servername.domainname.com\backupfolder' 또는 '\\\IP address\backupfolder').|
    |**사용자 이름** | Azure Database Migration Service가 가장하여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드할 수 있는 Windows 사용자 이름입니다. |
    |**암호** | 사용자에 대한 암호입니다. |
    |**저장소 계정 설정** | Azure SQL Database 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 데 사용되며 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공하는 SAS URI입니다. [Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아봅니다](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![마이그레이션 설정 구성](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  **저장**을 선택한 다음, **마이그레이션 요약** 화면의 **활동 이름** 텍스트 상자에서 마이그레이션 활동의 이름을 지정합니다.

6. **유효성 검사 옵션** 섹션을 확장하여 **유효성 검사 옵션 선택** 화면을 표시하고, 마이그레이션된 데이터베이스의 쿼리 정확도를 검사할지 여부를 지정한 다음, **저장**을 선택합니다.  

    ![마이그레이션 요약](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 상태는 **보류 중**입니다.

   ![마이그레이션 작업 보류 중](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 상태가 **완료됨**으로 표시될 때까지 디스플레이를 업데이트합니다.
 
   ![마이그레이션 작업 완료됨](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. 마이그레이션이 완료되면 **보고서 다운로드**를 선택하여 마이그레이션 프로세스와 관련된 세부 정보가 나열된 보고서를 받습니다.
 
3. 대상 Azure SQL Database 관리되는 인스턴스 환경에서 대상 데이터베이스를 확인합니다.

## <a name="next-steps"></a>다음 단계

- T-SQL RESTORE 명령을 사용하여 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 방법을 보여 주는 자습서는 [복원 명령을 사용하여 백업을 관리되는 인스턴스에 복원](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)을 참조하세요.
- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](../sql-database/sql-database-managed-instance.md)을 참조하세요.
- 관리되는 인스턴스에 앱을 연결하는 내용은 [응용 프로그램 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하세요.
