---
title: SQL Server 비즈니스 인텔리전스 | Microsoft Docs
description: 이 항목에서는 클래식 배포 모델로 만든 리소스를 사용하고 Azure VM(Virtual Machines)에서 실행 중인 SQL Server에 사용할 수 있는 BI(비즈니스 인텔리전스) 기능에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 6f1a95e52def9154253192ab9d43d1e7d621cee7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Azure Virtual Machines의 SQL Server Business Intelligence
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

Microsoft Azure Virtual Machine 갤러리에는 SQL Server 설치가 포함된 이미지가 들어 있습니다. 갤러리 이미지에서 지원되는 SQL Server 버전은 온-프레미스 컴퓨터와 가상 머신에 설치할 수 있는 동일한 설치 파일입니다. 이 항목은 가상 머신이 프로비전된 후 필요한 구성 단계 및 이미지에 설치된 SQL Server BI(비즈니스 인텔리전스) 기능을 요약합니다. 이 항목은 BI 기능에 대해 지원되는 배포 토폴로지 및 모범 사례도 설명합니다.

## <a name="license-considerations"></a>라이선스 고려 사항
Microsoft Azure Virtual Machines의 SQL Server에 라이선스를 허여하는 데 다음 두 가지 방법을 사용할 수 있습니다.

1. Software Assurance에 속한 라이선스 이동 혜택. 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 참조하세요.
2. SQL Server가 설치된 Azure Virtual Machines의 시간당 요금. [Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)의 "SQL Server" 섹션을 참조하세요.

라이선스 및 현재 요금에 대한 자세한 내용은 [Virtual Machines 라이선스 FAQ](https://azure.microsoft.com/pricing/licensing-faq/%20/)를 참조하세요.

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Azure Virtual Machine 갤러리에서 사용 가능한 SQL Server 이미지
Microsoft Azure Virtual Machine 갤러리에는 Microsoft SQL Server가 포함된 몇 개의 이미지가 들어 있습니다. 가상 머신 이미지에 설치된 소프트웨어는 운영 체제 버전 및 SQL Server의 버전에 따라 다릅니다. Azure 가상 머신 갤러리에서 사용 가능한 이미지 목록은 자주 변경됩니다.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Azure VM 갤러리의 SQL 이미지](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) 다음 PowerShell 스크립트는 ImageName에 "SQL-Server"가 포함된 Azure 이미지 목록을 반환합니다.

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

SQL Server에서 지원되는 버전 및 기능에 대한 자세한 내용은 다음을 참조하세요.

* [SQL Server 버전](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [SQL Server 2016 버전에서 지원되는 기능](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>SQL Server Virtual Machine 갤러리 이미지에 설치된 BI 기능
다음 표는 SQL Server에 대한 일반적인 Microsoft Azure Virtual Machine 갤러리 이미지에 설치된 비즈니스 인텔리전스 기능을 요약합니다.

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| SQL Server BI 기능 | 갤러리 이미지에 설치 | 메모 |
| --- | --- | --- |
| **Reporting Services 기본 모드** |예 |설치되었지만 보고서 관리자 URL을 비롯한 구성이 필요합니다. [Reporting Services 구성](#configure-reporting-services)섹션을 참조하세요. |
| **Reporting Services SharePoint 모드** |아니오 |Microsoft Azure Virtual Machine 갤러리 이미지는 SharePoint 또는 SharePoint 설치 파일을 포함하지 않습니다. <sup>1</sup> |
| **Analysis Services 다차원 및 데이터 마이닝(OLAP)** |예 |설치되고 기본 Analysis Services 인스턴스로 구성되었습니다. |
| **Analysis Services 테이블 형식** |아니요 |SQL Server 2012, 2014 및 2016 이미지에서 지원되지만 기본적으로 설치되지 않습니다. Analysis Services의 다른 인스턴스를 설치합니다. 이 항목의 다른 SQL Server 서비스 및 기능 설치 섹션을 참조하세요. |
| **SharePoint용 Analysis Services 파워 피벗** |아니오 |Microsoft Azure Virtual Machine 갤러리 이미지는 SharePoint 또는 SharePoint 설치 파일을 포함하지 않습니다. <sup>1</sup> |

<sup>1</sup> SharePoint 및 Azure Virtual Machines에 대한 추가 정보는 [SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx) 및 [Microsoft Azure Virtual Machines에 SharePoint 배포](https://www.microsoft.com/download/details.aspx?id=34598)를 참조하세요.

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) 서비스 이름에 "SQL"을 포함하는 설치된 서비스 목록을 가져오려면 다음 PowerShell 명령을 실행합니다.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>일반 권장 사항 및 모범 사례
* SQL Server Enterprise Edition을 사용할 경우 가상 머신의 최소 권장 크기는 **A3** 입니다. **A4** 가상 머신 크기는 Analysis Services 및 Reporting Services의 SQL Server BI 배포에 권장됩니다.
  
    현재 VM 크기에 대한 자세한 내용은 [Azure에 대한 Virtual Machine 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 디스크 관리에 대한 모범 사례는 **C**: 및 **D**: 이외의 드라이브에서 데이터를 저장하고 파일을 기록 및 백업하는 것입니다. 예를 들어 데이터 디스크 **E**: 및 **F**:를 만듭니다.
  
  * 기본 드라이브 **C**:에 대한 드라이브 캐싱 정책은 데이터 작업에 적합하지 않습니다.
  * **D**: 드라이브는 페이지 파일에 주로 사용되는 임시 드라이브입니다. **D**: 드라이브는 지속되지 않고 Blob 저장소에 저장되지 않습니다. 관리 작업(예: 가상 머신 크기 변경)은 **D**: 드라이브를 재설정합니다. tempdb를 비롯한 데이터베이스 파일의 경우 **D**: 드라이브를 사용하지 **않는** 것이 좋습니다.
    
    디스크 만들기 및 연결에 대한 자세한 내용은 [Virtual Machine에 데이터 디스크를 연결하는 방법](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.
* 사용하지 않으려는 서비스를 중지하거나 제거합니다. 예를 들어 가상 머신이 Reporting Services에만 사용되는 경우 Analysis Services 및 SQL Server Integration Services를 중지하거나 제거합니다. 다음 이미지는 기본적으로 시작되는 서비스의 예입니다.
  
    ![SQL Server 서비스](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > 지원되는 BI 시나리오에서는 SQL Server 데이터베이스 엔진이 필요합니다. 단일 서버 VM 토폴로지에서는 데이터베이스 엔진이 동일한 VM에서 실행되어야 합니다.
  
    자세한 내용은 [Reporting Services 제거](https://msdn.microsoft.com/library/hh479745.aspx) 및 [Analysis Services의 인스턴스 제거](https://msdn.microsoft.com/library/ms143687.aspx)를 참조하세요.
* **Windows 업데이트**에서 새 '중요 업데이트'를 확인합니다. Microsoft Azure Virtual Machines 이미지는 자주 새로 고쳐집니다. 그러나 중요 업데이트는 VM 이미지가 마지막으로 새로 고쳐진 후 **Windows 업데이트**에서 사용 가능해질 수 있습니다.

## <a name="example-deployment-topologies"></a>배포 토폴로지 예
다음은 Microsoft Azure Virtual Machines를 사용하는 배포 예입니다. 이 다이어그램의 토폴로지는 SQL Server BI 기능 및 Microsoft Azure Virtual Machines와 함께 사용할 수 있는 가능한 토폴로지 중 몇 가지일 뿐입니다.

### <a name="single-virtual-machine"></a>단일 Virtual Machine
Analysis Services, Reporting Services, SQL Server 데이터베이스 엔진 및 데이터 원본이 단일 가상 머신에 있습니다.

![가상 컴퓨터 1을 사용하는 bi iass 시나리오](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>두 Virtual Machines
* Analysis Services, Reporting Services, SQL Server 데이터베이스 엔진이 단일 가상 머신에 있습니다. 이 배포는 보고서 서버 데이터베이스를 포함합니다.
* 데이터 원본은 두 번째 VM에 있습니다. 두 번째 VM은 SQL Server 데이터베이스 엔진을 데이터 원본으로 포함합니다.

![가상 머신 2를 사용하는 bi iaas 시나리오](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>혼합된 Azure – Azure SQL 데이터베이스의 데이터
* Analysis Services, Reporting Services, SQL Server 데이터베이스 엔진이 단일 가상 머신에 있습니다. 이 배포는 보고서 서버 데이터베이스를 포함합니다.
* 데이터 원본은 Azure SQL 데이터베이스입니다.

![bi iaas 시나리오 vm 및 데이터 원본으로 AzureSQL 사용](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>하이브리드 – 데이터 온-프레미스
* 이 배포 예에서는 Analysis Services, Reporting Services, SQL Server 데이터베이스 엔진이 단일 가상 머신에서 실행됩니다. 가상 머신이 보고서 서버 데이터베이스를 호스트합니다. 가상 머신은 Azure 가상 네트워킹 또는 일부 다른 VPN 터널링 솔루션을 통해 온-프레미스 도메인에 가입되어 있습니다.
* 데이터 원본은 온-프레미스입니다.

![bi iaas 시나리오 vm 및 온-프레미스 데이터 원본](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Reporting Services 기본 모드 구성
SQL Server에 대한 가상 머신 갤러리 이미지에는 Reporting Services 기본 모드가 설치되어 있지만 보고서 서버는 구성되어 있지 않습니다. 이 섹션의 단계에서 Reporting Services 보고서 서버를 구성합니다. Reporting Services 기본 모드 구성에 대한 자세한 내용은 [Reporting Services 기본 모드 보고서 서버(SSRS)](https://msdn.microsoft.com/library/ms143711.aspx)를 참조하세요.

> [!NOTE]
> Windows PowerShell 스크립트를 사용하여 보고서 서버를 구성하는 방법과 유사한 내용은 [PowerShell을 사용하여 기본 모드 보고서 서버로 Azure VM 만들기](../classic/ps-sql-report.md)를 참조하세요.

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Virtual Machine에 연결 및 Reporting Services 구성 관리자 시작
Azure Virtual Machine에 연결하는 데 다음과 같은 두 가지 일반적인 워크플로를 사용할 수 있습니다.

* 연결하려면 가상 머신의 이름을 클릭하고 **연결**을 클릭합니다. 원격 데스크톱 연결이 열리고 컴퓨터 이름이 자동으로 채워집니다.
  
    ![Azure 가상 머신에 연결](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Windows 원격 데스크톱 연결을 통해 가상 머신에 연결합니다. 원격 데스크톱의 사용자 인터페이스에서:
  
  1. 컴퓨터 이름으로 **클라우드 서비스 이름** 을 입력합니다.
  2. 콜론(:)과 TCP 원격 데스크톱 끝점에 대해 구성된 공용 포트 번호를 입력합니다.
     
      Myservice.cloudapp.net:63133
     
      자세한 내용은 [클라우드 서비스란?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/)을 참조하세요.


**Reporting Services 구성 관리자 시작**

**Windows Server 2012/2016**에서:

1. **시작** 화면에서 **Reporting Services**를 입력하여 앱 목록을 봅니다.
2. **Reporting Services 구성 관리자**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭합니다.

**Windows Server 2008 R2**에서:

1. **시작**을 클릭한 다음 **모든 프로그램**을 클릭합니다.
2. **Microsoft SQL Server 2016**을 클릭합니다.
3. **구성 도구**를 클릭합니다.
4. **Reporting Services 구성 관리자**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭합니다.

또는

1. **시작**을 클릭합니다.
2. **프로그램 및 파일 검색** 대화 상자에서 **reporting services**를 입력합니다. VM에서 Windows Server 2012를 실행하는 경우 Windows Server 2012 시작 화면에 **reporting services** 를 입력합니다.
3. **Reporting Services 구성 관리자**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭합니다.
   
    ![SSRS 구성 관리자 검색](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Reporting Services 구성
**서비스 계정 및 웹 서비스 URL:**

1. **서버 이름**이 로컬 서버 이름인지 확인하고 **연결**을 클릭합니다.
2. **보고서 서버 데이터베이스 이름**이 비어 있는지 확인합니다. 구성이 완료되면 데이터베이스가 만들어집니다.
3. **보고서 서버 상태**가 **시작됨**인지 확인합니다. Windows Server Manager에서 서비스를 확인하려는 경우 해당 서비스는 **SQL Server Reporting Services** Windows 서비스입니다.
4. **서비스 계정** 을 클릭하고 필요에 따라 계정을 변경합니다. 가상 머신이 도메인에 가입되지 않은 환경에서 사용되는 경우 기본 제공 **ReportServer** 계정이면 충분합니다. 서비스 계정에 대한 자세한 내용은 [서비스 계정](https://msdn.microsoft.com/library/ms189964.aspx)을 참조하세요.
5. 왼쪽 창에서 **웹 서비스 URL** 을 클릭합니다.
6. **적용** 을 클릭하여 기본값을 구성합니다.
7. **보고서 서버 웹 서비스 URL**을 기록해 둡니다. 기본 TCP 포트는 80이고 URL의 일부입니다. 이후 단계에서는 포트에 대한 Microsoft Azure Virtual Machine Scale Sets 끝점을 만듭니다.
8. **결과** 창에서 작업이 성공적으로 완료되었는지 확인합니다.

**데이터베이스:**

1. 왼쪽 창에서 **데이터베이스** 를 클릭합니다.
2. **데이터베이스 변경**을 클릭합니다.
3. **새 보고서 서버 데이터베이스 만들기** 가 선택되었는지 확인한 후 다음을 클릭합니다.
4. **서버 이름**을 확인하고 **연결 테스트**를 클릭합니다.
5. 결과가 **연결 테스트에 성공했습니다.** 인 경우 **확인**을 클릭한 후 **다음**을 클릭합니다.
6. 데이터베이스 이름이 **ReportServer**이고 **보고서 서버 모드**가 **기본**인지 확인한 후 **다음**을 클릭합니다.
7. **다음** on the **다음** 을 클릭합니다.
8. **다음** on the **다음** 을 클릭합니다.
9. **다음** on the **다음** 을 클릭합니다.

**2012 및 2014용 웹 포털 URL 또는 보고서 관리자 URL:**

1. 왼쪽 창에서 2014 및 2012용 **웹 포털 URL** 또는 **보고서 관리자 URL**을 클릭합니다.
2. **Apply**를 클릭합니다.
3. **결과** 창에서 작업이 성공적으로 완료되었는지 확인합니다.
4. **종료**를 클릭합니다.

보고서 서버 사용 권한에 대한 자세한 내용은 [기본 모드 보고서 서버에 대한 사용 권한 부여](https://msdn.microsoft.com/library/ms156014.aspx)를 참조하세요.

### <a name="browse-to-the-local-report-manager"></a>로컬 보고서 관리자로 이동
구성을 확인하려면 VM에서 보고서 관리자로 이동합니다.

1. VM에서 관리자 권한으로 Internet Explorer를 시작합니다.
2. VM에서 http://localhost/reports로 이동합니다.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>2014 및 2012용 원격 웹 포털 또는 보고서 관리자에 연결하려면
원격 컴퓨터에서 가상 컴퓨터의 2014 및 2012용 보고서 관리자 또는 웹 포털에 연결하려는 경우 새 가상 컴퓨터 TCP 끝점을 만듭니다. 기본적으로 보고서 서버는 **포트 80**에서 HTTP 요청을 수신합니다. 다른 포트를 사용하도록 보고서 서버 URL을 구성하는 경우 다음 지침에서 해당 포트 번호를 지정해야 합니다.

1. TCP 포트 80의 Virtual Machine에 대해 끝점을 만듭니다. 자세한 내용은 이 문서의 [Virtual Machine 끝점 및 방화벽 포트](#virtual-machine-endpoints-and-firewall-ports) 섹션을 참조하세요.
2. 가상 머신의 방화벽에서 포트 80을 엽니다.
3. Azure Virtual Machine **DNS 이름**을 URL의 서버 이름으로 사용하는 보고서 관리자 또는 웹 포털로 이동합니다. 예: 
   
    **보고서 서버**: http://uebi.cloudapp.net/reportserver  **웹 포털**: http://uebi.cloudapp.net/reports
   
    [보고서 서버 액세스를 위한 방화벽 구성](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>보고서를 만들고 Azure Virtual Machine에 게시하려면
다음 표에는 온-프레미스 컴퓨터의 기존 보고서를 Microsoft Azure Virtual Machine에 호스트된 보고서 서버에 게시하는 데 사용 가능한 일부 옵션이 요약되어 있습니다.

* **보고서 작성기**: 가상 컴퓨터는 SQL 2014 및 2012용 Microsoft SQL Server 보고서 작성기의 ClickOnce 버전을 포함합니다. SQL 2016이 있는 가상 머신에서 처음으로 보고서 작성기를 시작하려면:
  
  1. 관리자 권한으로 브라우저를 시작합니다.
  2. 웹 포털로 이동하고 가상 컴퓨터에서 오른쪽 위에 있는 **다운로드** 아이콘을 선택합니다.
  3. **보고서 작성기**를 선택합니다.
     
     자세한 내용은 [보고서 작성기 시작](https://msdn.microsoft.com/library/ms159221.aspx)을 참조하세요.
* **SQL Server Data Tools**: VM: SQL Server Data Tools가 가상 컴퓨터에 설치되어 있으므로 가상 컴퓨터에서 **보고서 서버 프로젝트** 및 보고서를 만드는 데 사용할 수 있습니다. SQL Server Data Tools는 보고서를 가상 머신의 보고서 서버에 게시할 수 있습니다.
* **SQL Server Data Tools: 원격**: 로컬 컴퓨터에서 SQL Server Data Tools로 Reporting Services 보고서가 포함된 Reporting Services 프로젝트를 만듭니다. 웹 서비스 URL에 연결하도록 프로젝트를 구성합니다.
  
    ![SSRS 프로젝트의 SSDT 프로젝트 속성](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* 보고서가 포함되는 .VHD 하드 드라이브를 만든 다음 드라이브를 업로드하고 연결합니다.
  
  1. 로컬 컴퓨터에서 보고서가 포함되는 .VHD 하드 드라이브를 만듭니다.
  2. 관리 인증서를 만들고 설치합니다.
  3. Add-AzureVHD cmdlet [Windows Server VHD 만들기 및 Azure에 업로드](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 사용하여 VHD 파일을 Azure에 업로드합니다.
  4. 가상 머신에 디스크를 연결합니다.

## <a name="install-other-sql-server-services-and-features"></a>다른 SQL Server 서비스 및 기능 설치
테이블 형식 모드에서 Analysis Services와 같은 추가 SQL Server 서비스를 설치하려면 SQL Server 설치 마법사를 실행합니다. 설치 파일은 가상 머신의 로컬 디스크에 있습니다.

1. **시작**을 클릭한 다음 **모든 프로그램**을 클릭합니다.
2. **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** 또는 **Microsoft SQL Server 2012**를 클릭한 다음 **구성 도구**를 클릭합니다.
3. **SQL Server 설치 센터**를 클릭합니다.

또는 C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe, C:\SQLServer_11.0_full\setup.exe를 실행합니다.

> [!NOTE]
> 처음으로 SQL Server 설치 프로그램을 실행하면 더 많은 설치 파일이 다운로드될 수 있으며 가상 머신을 재부팅하고 SQL Server 설치를 다시 시작해야 할 수도 있습니다.
> 
> Microsoft Azure Virtual Machine에서 선택된 이미지를 반복적으로 사용자 지정해야 하는 경우 사용자 고유의 SQL Server 이미지를 생성하는 것이 좋습니다. Analysis Services SysPrep 기능은 SQL Server 2012 SP1 CU2에서 사용하도록 설정되어 있습니다. 자세한 내용은 [SysPrep을 사용하여 SQL Server 설치에 대한 고려 사항](https://msdn.microsoft.com/library/ee210754.aspx) 및 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Analysis Services 테이블 형식 모드를 설치하려면
이 섹션의 단계는 Analysis Services 테이블 형식 모드의 설치를 **요약** 합니다. 자세한 내용은 

* [테이블 형식 모드에서 Analysis Services 설치](https://msdn.microsoft.com/library/hh231722.aspx)
* [테이블 형식 모델링(Adventure Works 자습서)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Analysis Services 테이블 형식 모드를 설치하려면:**

1. SQL Server 설치 마법사에서 왼쪽 창의 **설치**를 클릭한 다음 **새 SQL Server 독립 실행형 설치 또는 기존 설치에 기능 추가**를 클릭합니다.
   
   * **폴더 찾아보기**가 표시되면 c:\SQLServer_13.0_full, c:\SQLServer_12.0_full 또는 c:\SQLServer_11.0_full로 이동한 다음 **확인**을 클릭합니다.
2. 제품 업데이트 페이지에서 **다음** 을 클릭합니다.
3. **설치 유형** 페이지에서 **SQL Server 새로 설치**를 선택하고 **다음**을 클릭합니다.
4. **설치 역할** 페이지에서 **SQL Server 기능 설치**를 클릭합니다.
5. **기능 선택** 페이지에서 **Analysis Services**를 클릭합니다.
6. **인스턴스 구성** 페이지에서 설명이 포함된 이름(예: **Tabular**)을 **명명된 인스턴스** 및 **인스턴스 ID** 텍스트 상자에 입력합니다.
7. **Analysis Services 구성** 페이지에서 **테이블 형식 모드**를 선택합니다. 관리자 권한 목록에 현재 사용자를 추가합니다.
8. SQL Server 설치 마법사를 완료하고 닫습니다.

## <a name="analysis-services-configuration"></a>Analysis Services 구성
### <a name="remote-access-to-analysis-services-server"></a>Analysis Services 서버에 대한 원격 액세스
Analysis Services 서버는 Windows 인증만 지원합니다. SQL Server Management Studio 또는 SQL Server Data Tools 같은 클라이언트 응용 프로그램에서 원격으로 Analysis Services에 액세스하려면 가상 머신이 Azure 가상 네트워킹을 사용하여 로컬 도메인에 가입되어야 합니다. 자세한 내용은 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)를 참조하세요.

Analysis Services의 **기본 인스턴스**는 TCP 포트 **2383**에서 수신합니다. 가상 머신 방화벽에서 포트를 엽니다. 또한 Analysis Services의 클러스터된 명명된 인스턴스도 **2383**포트에서 수신합니다.

Analysis Services의 **명명된 인스턴스** 의 경우 포트 액세스를 관리하려면 SQL Server Browser 서비스가 필요합니다. SQL Server Browser 기본 구성은 포트 **2382**입니다.

가상 머신 방화벽에서 포트 **2382** 를 열고 정적 Analysis Services 명명된 인스턴스 포트를 만듭니다.

1. VM에서 이미 사용 중인 포트 및 해당 포트를 사용하는 프로세스를 확인하려면 관리자 권한으로 다음 명령을 실행합니다.
   
        netstat /ao
2. SQL Server Management Studio에서 테이블 형식 AS 인스턴스 일반 속성의 'Port' 값을 업데이트하여 정적 Analysis Services 명명된 인스턴스 포트를 만듭니다. 자세한 내용은 [Analysis Services 액세스를 허용하도록 Windows 방화벽 구성](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed)의 "기본 또는 명명된 인스턴스에 고정된 포트 사용"을 참조하세요.
3. Analysis Services 서비스의 테이블 형식 인스턴스를 다시 시작합니다.

자세한 내용은 이 문서의 **Virtual Machine 끝점 및 방화벽 포트** 섹션을 참조하세요.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Virtual Machine 끝점 및 방화벽 포트
이 섹션은 가상 머신 방화벽에서 만들려는 Microsoft Azure Virtual Machine 끝점 및 열려는 포트를 요약합니다. 다음 표는 가상 머신 방화벽에서 끝점을 만들려는 **TCP** 포트 및 열려는 포트를 요약합니다.

* 단일 VM을 사용하고 다음 두 항목에 해당하는 경우에는 VM의 방화벽에서 VM 끝점을 만들 필요가 없으며 포트를 열 필요가 없습니다.
  
  * VM의 SQL Server 기능에 원격으로 연결하지 않습니다. VM에 원격 데스크톱 연결을 설정하고 VM에서 로컬로 SQL Server 기능에 액세스하는 것은 SQL Server 기능에 대한 원격 연결로 간주되지 않습니다.
  * Azure 가상 네트워킹이나 다른 VPN 터널링 솔루션을 통해 온-프레미스 도메인에 VM을 가입하지 않습니다.
* 가상 머신이 도메인에 가입되지 않았지만 VM의 SQL Server 기능에 원격으로 연결하려는 경우:
  
  * VM의 방화벽에서 포트를 엽니다.
  * 표시된 포트(*)에 가상 머신 끝점을 만듭니다.
* Azure 가상 네트워킹 같은 VPN 터널을 사용하여 가상 머신이 도메인에 가입되어 있는 경우 끝점이 필요하지 않습니다. 그러나 VM의 방화벽에서 포트를 엽니다.
  
  | 포트 | type | 설명 |
  | --- | --- | --- |
  | **80** |TCP |보고서 서버 원격 액세스(*) |
  | **1433** |TCP |SQL Server Management Studio(*) |
  | **1434** |UDP |SQL Server Browser입니다. VM이 도메인에 가입된 경우 필요합니다. |
  | **2382** |TCP |SQL Server Browser입니다. |
  | **2383** |TCP |SQL Server Analysis Services 기본 인스턴스 및 클러스터된 명명된 인스턴스입니다. |
  | **사용자 정의** |TCP |선택하는 포트 번호에 대해 정적 Analysis Services 명명된 인스턴스 포트를 만든 다음 방화벽에서 포트 번호의 차단을 해제합니다. |

끝점을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

* 끝점 만들기:[Virtual Machine에 끝점을 설정하는 방법](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* SQL Server: [Azure에서 SQL Server Virtual Machine 프로비전](../sql/virtual-machines-windows-portal-sql-server-provision.md)의 "SQL Server Management Studio를 사용하여 가상 머신에 연결하는 구성 단계 완료"를 참조하세요.

다음 다이어그램에서는 VM의 기능 및 구성 요소에 대한 원격 액세스를 허용하기 위해 VM 방화벽에서 열 포트를 보여 줍니다.

![Azure VM에서 bi 응용 프로그램에 대해 열 포트](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>리소스
* Azure Virtual Machine 환경에서 사용되는 Microsoft 서버 소프트웨어에 대한 지원 정책을 검토합니다. 다음 항목은 BitLocker, 장애 조치(failover) 클러스터링 및 네트워크 부하 분산 등의 기능에 대한 지원을 요약합니다. [Azure Virtual Machines에 대한 Microsoft 서버 소프트웨어 지원](http://support.microsoft.com/kb/2721672).
* [Azure Virtual Machines의 SQL Server 개요](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Azure에서 SQL Server Virtual Machine 프로비전](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Virtual Machine에 데이터 디스크를 연결하는 방법](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Analysis Services 인스턴스의 서버 모드 확인](https://msdn.microsoft.com/library/gg471594.aspx)
* [다차원 모델링(Adventure Works 자습서)](https://technet.microsoft.com/library/ms170208.aspx)
* [Azure 설명서 센터](https://azure.microsoft.com/documentation/)
* [하이브리드 환경에서 Power BI 사용](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Microsoft SQL Server Connect를 통해 피드백 및 연락처 정보 제출](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>커뮤니티 콘텐츠
* [PowerShell을 사용한 Azure SQL Database 관리](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

