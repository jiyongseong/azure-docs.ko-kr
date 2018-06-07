---
title: Log Analytics에 구성 관리자 연결 | Microsoft Docs
description: 이 문서는 구성 관리자를 Log Analytics에 연결하고 데이터 분석을 시작하는 단계를 보여줍니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 5ff0687fe99f0853e29e5f0d814a8555c367027c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Log Analytics에 구성 관리자 연결
System Center Configuration Manager 환경을 Azure Log Analytics에 연결하여 장치 수집 데이터를 동기화하고 Log Analytics 및 Azure Automation에서 이러한 컬렉션을 참조할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

Log Analytics는 System Center Configuration Manager 현재 분기, 1606 이상 버전을 지원합니다.  

## <a name="configuration-overview"></a>구성 개요
다음 단계는 Log Analytics와 Configuration Manager 통합을 구성하는 단계를 요약하여 보여 줍니다.  

1. Azure Portal에서 Configuration Manager를 웹 응용 프로그램 및/또는 Web API 앱으로 등록하고 Azure Active Directory에서 등록할 때 사용한 클라이언트 ID와 클라이언트 암호 키가 있는지 확인합니다. 이 단계를 수행하는 방법에 대한 자세한 내용은 [포털 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)를 참조하세요.
2. Azure Portal에서 [Configuration Manager(등록된 웹앱)에 Log Analytics에 대한 액세스 권한을 제공](#grant-configuration-manager-with-permissions-to-log-analytics)합니다.
3. 구성 관리자에서 [DMS 연결 추가 마법사를 사용하여 연결을 추가](#add-an-oms-connection-to-configuration-manager)합니다.
4. 구성 관리자에서 암호 또는 클라이언트 비밀 키가 만료되거나 분실된 경우 [연결 속성을 업데이트](#update-oms-connection-properties)합니다.
5. Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에서 [Microsoft Monitoring Agent를 다운로드 및 설치](#download-and-install-the-agent)합니다. 에이전트가 Configuration Manager 데이터를 Log Analytics 작업 영역으로 전송합니다.
6. Log Analytics에서 컴퓨터 그룹으로 [구성 관리자의 컬렉션을 가져옵니다](#import-collections).
7. Log Analytics에서 [컴퓨터 그룹](log-analytics-computer-groups.md)으로 구성 관리자에서 데이터를 봅니다.

[Microsoft Operations Management Suite에 구성 관리자의 데이터 동기화](https://technet.microsoft.com/library/mt757374.aspx)에서 구성 관리자를 OMS에 연결하는 방법에 대해 자세히 읽을 수 있습니다.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager에 Log Analytics에 대한 사용 권한 제공
다음 절차에서는 Log Analytics 작업 영역의 *Contributor* 역할을 Configuration Manager에 대해 이전에 만든 AD 응용 프로그램 및 서비스 주체에 부여합니다.  작업 영역이 아직 없는 경우 진행하기 전에 [Azure Log Analytics에서 작업 영역 만들기](log-analytics-quick-create-workspace.md)를 참조하세요.  이를 통해 Configuration Manager는 Log Analytics 작업 영역을 인증하고 연결할 수 있습니다.  

> [!NOTE]
> Configuration Manager의 Log Analytics에 사용 권한을 지정해야 합니다. 그렇지 않고 Configuration Manager에서 구성 마법사를 사용하면 오류 메시지가 표시됩니다.
>

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Log Analytics 작업 영역 목록에서 수정할 작업 영역을 선택합니다.
3. 왼쪽 창에서 **액세스 제어(IAM)**를 선택합니다.
4. [액세스 제어] 페이지에서 **추가**를 클릭하면 **권한 추가** 창이 나타납니다.
5. **권한 추가** 창의 **역할** 드롭다운 목록 아래에서 **참가자** 역할을 선택합니다.  
6. **다음에 대한 액세스 할당** 드롭다운 목록 아래에서 이전에 AD에서 만든 Configuration Manager 응용 프로그램을 선택한 다음, **확인**을 클릭합니다.  

## <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치
Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 호스팅하는 컴퓨터에 Microsoft Monitoring Agent를 설치하는 데 사용할 수 있는 방법을 이해하려면 [Azure에서 Log Analytics 서비스에 Windows 컴퓨터 연결](log-analytics-agent-windows.md) 문서를 검토하세요.  

## <a name="add-an-oms-connection-to-configuration-manager"></a>구성 관리자에 OMS 연결 추가
OMS 연결을 추가하려면 구성 관리자 환경에 온라인 모드를 위해 구성된 [서비스 연결 지점](https://technet.microsoft.com/library/mt627781.aspx)이 있어야 합니다.

1. 구성 관리자의 **관리** 작업 영역에서 **OMS 커넥터**를 선택합니다. 그러면 **OMS 연결 추가 마법사**가 열립니다. **다음**을 선택합니다.
2. **일반** 화면에서 아래의 작업을 완료했는지와 각 항목에 대한 상세 정보가 있는지 확인하고 **다음**을 선택합니다.

   1. Azure Portal에서 Configuration Manager를 웹 응용 프로그램 및/또는 웹 API 앱으로 등록했는지 여부 및 [등록 시 지정한 클라이언트 ID](../active-directory/active-directory-integrating-applications.md)가 있는지 여부
   2. Azure Portal에서 Azure Active Directory에 등록된 앱의 앱 비밀 키를 만들었는지 여부  
   3. Azure Portal에서 등록된 웹앱에 OMS에 대한 액세스 권한을 제공했는지 여부  
      ![OMS 마법사 일반 페이지 연결](./media/log-analytics-sccm/sccm-console-general01.png)
3. **Azure Active Directory** 화면에서 **테넌트**, **클라이언트 ID**, **클라이언트 암호 키**를 지정하여 Log Analytics에 대한 연결 설정을 구성하고 **다음**을 선택합니다.  
   ![OMS Wizard Azure Active Directory 페이지에 연결](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. 나머지 절차를 성공적으로 완료한 경우 이 페이지에 **OMS 연결 구성** 화면이 자동으로 나타납니다. **Azure 구독**, **Azure 리소스 그룹**, **Operations Management Suite 작업 영역**에 대한 연결 설정 정보가 나타납니다.  
   ![OMS 연결 마법사의 OMS 연결 페이지](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. 이 마법사는 사용자가 입력한 정보를 사용하여 Log Analytics 서비스에 연결합니다. 서비스와 동기화하려는 장치 컬렉션을 선택한 다음, **추가**를 클릭합니다.  
   ![컬렉션 선택](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. **요약** 화면에서 연결 설정을 확인하고 **다음**을 선택합니다. **진행률** 화면에 연결 상태와 **완료**가 차례로 나타납니다.

> [!NOTE]
> 계층의 최고 계층 사이트를 Log Analytics에 연결해야 합니다. 독립 실행형 기본 사이트를 Log Analytics에 연결한 다음, 환경에 중앙 관리 사이트를 추가할 경우 새 계층 안에서 연결을 삭제하고 다시 만들어야 합니다.
>
>

Configuration Manager를 Log Analytics에 연결한 후에는 컬렉션을 추가 또는 제거하고 연결의 속성을 확인할 수 있습니다.

## <a name="update-log-analytics-connection-properties"></a>Log Analytics 연결 속성 업데이트
암호 또는 클라이언트 암호 키가 만료되거나 분실된 경우 Log Analytics 연결 속성을 수동으로 업데이트해야 합니다.

1. Configuration Manager에서 **Cloud Services**로 이동한 다음 **OMS 커넥터**를 선택하여 **OMS 연결 속성** 페이지를 엽니다.
2. 이 페이지에서 **Azure Active Directory** 탭을 클릭하여 **테넌트**, **클라이언트 ID**, **클라이언트 비밀 키 만료**를 클릭합니다. **클라이언트 비밀 키**가 만료되었는지 **확인**합니다.

## <a name="import-collections"></a>컬렉션 가져오기
Configuration Manager에 OMS 연결을 추가하고 Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치한 후 다음 단계는 컴퓨터 그룹으로 Log Analytics의 Configuration Manager 컬렉션을 가져오는 것입니다.

계층 구조에서 장치 컬렉션을 가져오기 위한 초기 구성을 완료한 후에는 멤버 자격을 최신으로 유지하기 위해 3시간마다 컬렉션 멤버 정보가 검색됩니다. 언제든지 이를 비활성화하도록 선택할 수 있습니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 작업 영역 목록에서 Configuration Manager를 등록할 작업 영역을 선택합니다.  
3. **고급 설정**을 선택합니다.<br><br> ![Log Analytics 고급 설정](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. **컴퓨터 그룹**을 선택한 다음, **SCCM**을 선택합니다.  
5. **구성 관리자 컬렉션 멤버 자격 가져오기**를 선택한 다음 **저장**을 클릭합니다.  
   ![컴퓨터 그룹 - SCCM 탭](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>구성 관리자의 데이터 보기
Configuration Manager에 OMS 연결을 추가하고 Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치하면 에이전트의 데이터가 Log Analytics로 전송됩니다. Log Analytics에서 Configuration Manager 컬렉션이 [컴퓨터 그룹](log-analytics-computer-groups.md)으로 나타납니다. **Settings\Computer Groups** 아래 **Configuration Manager** 페이지에서 그룹을 볼 수 있습니다.

컬렉션을 가져오면 컬렉션 멤버 자격이 있는 컴퓨터 중 삭제된 컴퓨터 수를 확인할 수 있습니다. 또한 가져온 컬렉션 수도 확인할 수 있습니다.

![컴퓨터 그룹 - SCCM 탭](./media/log-analytics-sccm/sccm-computer-groups02.png)

각 항목을 클릭하면 가져온 그룹 전체 또는 각 그룹에 속하는 전체 컴퓨터가 표시된 검색이 열립니다. [로그 검색](log-analytics-log-searches.md)을 사용하여 구성 관리자 데이터의 자세한 분석을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [로그 검색](log-analytics-log-searches.md)을 사용하여 구성 관리자 데이터에 대한 자세한 정보를 볼 수 있습니다.
