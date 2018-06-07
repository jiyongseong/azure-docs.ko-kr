---
title: Azure Log Integration 시작 | Microsoft Docs
description: Azure Log Integration 서비스를 설치하고 Azure Storage, Azure 감사 로그 및 Azure Security Center 경고의 로그를 통합하는 방법을 알아봅니다.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/20/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 3e229c4db44fc3c8d16aa2bd0a014fb1acc64a5e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure 진단 로깅 및 Windows 이벤트 전달과의 Azure Log Integration

Azure Log Integration은 SIEM(Security Incident and Event Management) 공급업체에서 [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) 커넥터를 사용할 수 없는 경우 고객에게 대안을 제공합니다. Azure Log Integration을 통해 SIEM에서 Azure 로그를 사용할 수 있으며 모든 자산의 통합 보안 대시보드를 만들 수 있습니다.

> [!NOTE]
> Azure Monitor에 대한 자세한 내용은 [Azure Monitor 시작](../monitoring-and-diagnostics/monitoring-get-started.md)을 참조하세요. Azure Monitor 커넥터의 상태에 대한 자세한 내용은 SIEM 공급업체에 문의하세요.

> [!IMPORTANT]
> 가상 머신 로그를 수집하는 것이 주요 관심사인 경우, 이 옵션은 대부분의 SIEM 공급업체 솔루션에 포함됩니다. SIEM 공급업체의 커넥터를 사용하는 것이 항상 기본 대안입니다.

이 문서는 Azure Log Integration을 시작하는 데 도움을 줍니다. 여기서는 Azure Log Integration 서비스의 설치와 Azure 진단과의 통합을 중점적으로 다룹니다. Azure Log Integration 서비스는 Azure IaaS(Infrastructure as a Service)에 배포된 가상 머신에서 Windows 보안 이벤트 채널의 Windows 이벤트 로그 정보를 수집합니다. 이 서비스는 온-프레미스에서 사용할 수도 있는 *이벤트 전달*과 매우 유사합니다.

> [!NOTE]
> Azure Log Integration의 출력을 SIEM과 통합하는 작업은 SIEM 자체적으로 수행됩니다. 자세한 내용은 [Integrating Azure Log Integration with your On-premises SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)(Azure Log Integration과 온-프레미스 SIEM 통합)을 참조하세요.

Azure Log Integration 서비스는 Windows Server 2008 R2 이상(Windows Server 2016 또는 Windows Server 2012 R2가 기본 설정임)을 실행하는 물리적 또는 가상 머신에서 실행됩니다.

물리적 컴퓨터는 온-프레미스 또는 호스팅 사이트에서 실행될 수 있습니다. 가상 머신에서 Azure 로그 통합 서비스를 실행하도록 선택한 경우 해당 가상 머신은 온-프레미스 또는 Microsoft Azure와 같은 공용 클라우드에 위치할 수 있습니다.

물리적 또는 가상 머신에서 Azure 로그 통합 서비스를 실행하려면 Azure 공용 클라우드에 대한 네트워크 연결이 필요합니다. 이 문서에서는 필요한 구성에 대한 세부 정보를 제공합니다.

## <a name="prerequisites"></a>필수 조건

최소한 Azure Log Integration을 설치하려면 다음 항목이 필요합니다.

* **Azure 구독**. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* WAD(Microsoft Azure 진단) 로깅에 사용할 수 있는 **저장소 계정**. 미리 구성된 저장소 계정을 사용하거나 새 저장소 계정을 만들 수 있습니다. 저장소 계정을 구성하는 방법은 이 문서의 뒷부분에서 설명합니다.

  > [!NOTE]
  > 시나리오에 따라 저장소 계정이 필요하지 않을 수도 있습니다. 이 문서에서 다루는 Azure 진단 시나리오의 경우 저장소 계정이 필요합니다.

* **두 대의 시스템**: 
  * Azure Log Integration 서비스를 실행하는 컴퓨터. 이 컴퓨터는 나중에 SIEM으로 가져오는 모든 로그 정보를 수집합니다. 이 시스템에 해당되는 사항:
    * 온-프레미스에 있거나 Microsoft Azure에 호스트될 수 있습니다.  
    * Windows Server 2008 R2 SP1 이상의 x64 버전을 실행하고 Microsoft .NET 4.5.1이 설치되어 있어야 합니다. 설치된 .NET 버전을 확인하려면 [설치된 .NET Framework 버전 확인](https://msdn.microsoft.com/library/hh925568)을 참조하세요.  
    * Azure 진단 로깅에 사용되는 Azure Storage 계정에 대한 연결이 있어야 합니다. 연결을 구성하는 방법은 이 문서의 뒷부분에서 설명합니다.
  * 모니터링할 컴퓨터. [Azure Virtual Machine](../virtual-machines/virtual-machines-windows-overview.md)으로 실행 중인 VM입니다. 이 컴퓨터의 로깅 정보는 Azure Log Integration 서비스 컴퓨터에 전송됩니다.

Azure Portal을 사용하여 가상 머신을 만드는 방법에 대한 빠른 데모를 보려면 아래 비디오를 시청하세요.<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>배포 고려 사항

테스트하는 동안 최소 운영 체제 요구 사항을 충족하는 시스템을 사용할 수 있습니다. 프로덕션 환경의 경우 부하로 인해 강화 또는 스케일 아웃 계획을 세워야 할 수도 있습니다.

Azure Log Integration 서비스의 인스턴스를 여러 개 실행할 수 있습니다. 그러나 물리적 또는 가상 머신별로 서비스 인스턴스를 하나만 실행할 수 있습니다. 또한 WAD용 Azure 진단 저장소 계정의 부하를 분산할 수 있습니다. 인스턴스에 제공할 구독 수는 용량을 기준으로 합니다.

> [!NOTE]
> 현재, Azure Log Integration 컴퓨터(즉, Azure Log Integration 서비스를 실행하는 컴퓨터)의 인스턴스를 확장할 시기 또는 저장소 계정이나 구독에 대한 구체적인 권장 사항은 없습니다. 확장 결정은 이러한 각 영역에서의 성능 관찰을 기반으로 해야 합니다.

성능을 향상을 위해 Azure Log Integration 서비스를 강화하는 옵션도 제공됩니다. 다음 성능 메트릭은 Azure Log Integration 서비스를 실행하기 위해 선택한 컴퓨터의 크기를 조정하는 데 도움이 될 수 있습니다.

* 8-프로세서(코어) 컴퓨터에서는 Azure Log Integration의 인스턴스 하나가 하루에 약 2,400만 개의 이벤트를 처리할 수 있습니다(시간당 약 백만 개 이벤트).
* 4-프로세서(코어) 컴퓨터에서는 Azure Log Integration의 인스턴스 하나가 하루에 약 1,500만 개의 이벤트를 처리할 수 있습니다(시간당 약 62,500개 이벤트).

## <a name="install-azure-log-integration"></a>Azure Log Integration 설치

Azure Log Integration을 설치하려면 [Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) 설치 파일을 다운로드합니다. 설치 프로세스를 완료합니다. Microsoft로 원격 분석 정보를 제공할지 여부를 선택합니다.

Azure Log Integration 서비스는 서비스가 설치된 컴퓨터에서 원격 분석 데이터를 수집합니다.  

수집되는 원격 분석 데이터는 다음과 같습니다.

* Azure Log Integration 실행 중에 발생하는 예외
* 처리된 쿼리 및 이벤트 수에 대한 메트릭
* 어떤 Azlog.exe 명령줄 옵션이 사용되었는지에 대한 통계 

> [!NOTE]
> Microsoft가 원격 분석 데이터를 수집하도록 허용하는 것이 좋습니다. **Microsoft가 원격 분석 데이터를 수집하도록 허용** 확인란의 선택을 취소하여 원격 분석 데이터의 수집을 해제할 수 있습니다.
>

![원격 분석 확인란이 선택된 설치 창 스크린샷](./media/security-azure-log-integration-get-started/telemetry.png)


설치 프로세스는 아래 비디오에 나와 있습니다.<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>설치 후 및 유효성 검사 단계

기본 설치를 완료한 후에는 설치 후 및 유효성 검사 단계를 수행할 수 있습니다.

1. 관리자 권한으로 PowerShell을 엽니다. 그런 후 C:\Program Files\Microsoft Azure Log Integration으로 이동합니다.
2. Azure Log Integration cmdlet을 가져옵니다. 이러한 cmdlet을 가져오려면 스크립트 `LoadAzlogModule.ps1`을 실행합니다. `.\LoadAzlogModule.ps1`을 입력한 후 Enter 키를 누릅니다(이 명령에서 **.\\** 사용). 아래 그림과 같이 표시됩니다.

  ![LoadAzlogModule.ps1 명령의 출력 스크린샷](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. 이제 특정 Azure 환경을 사용하도록 Azure Log Integration을 구성합니다. *Azure 환경*은 작업하려는 Azure 클라우드 데이터 센터의 유형입니다. Azure 환경이 여러 개 있지만 현재, 관련 옵션은 **AzureCloud** 또는 **AzureUSGovernment**입니다. 관리자 권한으로 PowerShell을 실행하여 C:\Program Files\Microsoft Azure Log Integration\ 위치에 있는지 확인합니다. 그런 후 다음 명령을 실행합니다.

  `Set-AzlogAzureEnvironment -Name AzureCloud`(**AzureCloud**)
  
  US Government Azure 클라우드를 사용하려면 **-Name** 변수로 **AzureUSGovernment**를 사용합니다. 현재, 다른 Azure 클라우드는 지원되지 않습니다.  

  > [!NOTE]
  > 명령이 성공하면 피드백이 수신되지 않습니다. 

4. 시스템을 모니터링하려면 Azure 진단에 사용 중인 저장소 계정의 이름이 필요합니다. Azure Portal에서 **가상 머신**으로 이동합니다. 모니터링하는 가상 머신을 찾습니다. **속성** 섹션에서 **진단 설정**을 선택합니다.  그런 다음, **에이전트**를 선택합니다. 지정된 저장소 계정 이름을 기록합니다. 이후 단계에서 이 계정 이름이 필요합니다.

  ![Azure 진단 설정 창 스크린샷](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![게스트 수준 모니터링 사용 단추 스크린샷](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > 가상 머신을 만들 때 모니터링을 사용하도록 설정하지 않은 경우 이전 이미지에 나와 있는 것처럼 사용하도록 설정할 수 있습니다.

5. 이제 Azure Log Integration 컴퓨터로 돌아갑니다. Azure Log Integration을 설치한 컴퓨터에서 Storage 계정에 연결되어 있는지 확인합니다. Azure Log Integration 서비스를 실행하는 컴퓨터에서 모니터링되는 각 시스템에서 Azure 진단이 기록한 정보를 검색하려면 저장소 계정에 대한 액세스 권한이 필요합니다. 연결을 확인하려면 
  1. [Azure Storage 탐색기를 다운로드](http://storageexplorer.com/)합니다.
  2. 설치를 완료합니다.
  3. 설치가 완료되면 **다음**을 선택합니다. **Microsoft Azure 저장소 탐색기 시작** 확인란을 선택된 상태로 둡니다.  
  4. Azure에 로그인합니다.
  5. Azure 진단용으로 구성된 저장소 계정이 표시되는지 확인합니다. 

    ![저장소 탐색기의 저장소 계정 스크린샷](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. 저장소 계정 아래에 몇 가지 옵션이 나타납니다. **테이블**에 **WADWindowsEventLogsTable**라는 테이블이 표시되어야 합니다.

  가상 머신을 만들 때 모니터링을 사용하도록 설정하지 않은 경우 앞서 설명된 것처럼 사용하도록 설정할 수 있습니다.


## <a name="integrate-azure-diagnostics-logging"></a>Azure 진단 로깅 통합

이 단계에서는 로그 파일이 포함된 저장소 계정에 연결하도록 Azure Log Integration 서비스를 실행하는 컴퓨터를 구성합니다.

이 단계를 완료하려면 다음과 같은 몇 가지 항목이 필요합니다.  
* **FriendlyNameForSource**: Azure 진단의 정보를 저장하도록 가상 머신을 구성한 저장소 계정에 적용할 수 있는 친숙한 이름입니다.
* **StorageAccountName**: Azure 진단을 구성할 때 지정한 저장소 계정의 이름입니다.  
* **StorageKey**: Azure 진단 정보가 이 가상 머신에 저장되는 저장소 계정의 저장소 키입니다.  

저장소 키를 가져오려면 다음 단계를 수행합니다.
1. [Azure 포털](http://portal.azure.com)로 이동합니다.
2. 탐색 창에서 **모든 서비스**를 선택합니다.
3. **필터** 상자에 **저장소**를 입력합니다. 그런 후 **저장소 계정**을 선택합니다.

  ![모든 서비스의 저장소 계정을 표시하는 스크린샷](./media/security-azure-log-integration-get-started/filter.png)

4. 저장소 계정 목록이 나타납니다. 로그 저장소에 할당한 계정을 두 번 클릭합니다.

  ![저장소 계정 목록을 표시하는 스크린샷](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. **설정** 아래에서 **액세스 키**를 선택합니다.

  ![메뉴의 액세스 키 옵션을 표시하는 스크린샷](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. **key1**을 복사하여 다음 단계에서 액세스할 수 있는 안전한 위치에 저장합니다.
7. Azure Log Integration을 설치한 서버에서 관리자 권한으로 명령 프롬프트 창을 엽니다. (관리자로 PowerShell이 아닌 명령 프롬프트 창을 열어야 합니다.)
8. C:\Program Files\Microsoft Azure Log Integration으로 이동합니다.
9. 다음 명령을 실행합니다. `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  예:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  이벤트 XML에 구독 id를 표시하려면 이름에 구독 ID를 추가합니다.

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  예: 
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> 최대 60분 정도 기다렸다가 저장소 계정에서 가져온 이벤트를 확인합니다. 이벤트를 보려면 Azure Log Integration에서 **이벤트 뷰어** > **Windows 로그** > **전달된 이벤트**를 선택합니다.

다음 비디오에서는 이전 단계를 다룹니다.<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>전달된 이벤트 폴더에 데이터가 나타나지 않는 경우
1시간이 지난 후에도 전달된 이벤트 폴더에 데이터가 나타나지 않으면 다음 단계를 완료합니다.

1. Azure Log Integration 서비스를 실행하는 컴퓨터를 확인합니다. Azure에 액세스할 수 있는지 확인합니다. 연결을 테스트하려면 브라우저에서 [Azure Portal](http://portal.azure.com)로 이동합니다.
2. 사용자 계정 AzLog에 users\Azlog 폴더에 대한 쓰기 권한이 있는지 확인합니다.
  1. 파일 탐색기를 엽니다.
  2. C:\users로 이동합니다.
  3. C:\users\Azlog를 마우스 오른쪽 단추로 클릭합니다.
  4. **보안**을 선택합니다.
  5. **NT Service\Azlog**를 선택합니다. 계정에 대한 사용 권한을 확인합니다. 이 탭에서 계정이 누락되거나 적절한 사용 권한이 표시되지 않은 경우 이 탭에서 계정 권한을 부여할 수 있습니다.
3. `Azlog source list` 명령을 실행할 때 `Azlog source add` 명령에 추가된 저장소 계정이 출력에 표시되는지 확인합니다.
4. Azure Log Integration 서비스에서 오류가 보고되었는지 확인하려면 **이벤트 뷰어** > **Windows 로그** > **응용 프로그램**으로 이동합니다.

설치 및 구성 중에 문제가 있는 경우 [지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 만들 수 있습니다. 서비스로 **Log Integration**을 선택합니다.

또 다른 지원 옵션은 [Azure Log Integration MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)입니다. 이 MSDN 포럼에서 커뮤니티는 Azure Log Integration을 최대한 활용하기 위한 방법에 대한 질문에 대답하고 유용한 정보를 공유하여 지원을 제공할 수 있습니다. 또한 Azure Log Integration 팀에서 이 포럼을 모니터링합니다. 아울러 가능할 때마다 도움을 드립니다.

## <a name="next-steps"></a>다음 단계
Azure Log Integration에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 로그에 대한 Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) 이 다운로드 센터에는 Azure Log Integration에 대한 세부 정보, 시스템 요구 사항 및 설치 지침이 포함되어 있습니다.
* [Azure Log Integration 소개](security-azure-log-integration-overview.md) 이 문서에서는 Azure Log Integration, 주요 기능 및 작동 원리를 소개합니다.
* [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure Log Integration을 구성하는 방법을 보여 줍니다. SIEM 구성 요소를 구성하는 방법에 대한 최신 지침을 제공합니다. 자세한 내용은 SIEM 공급업체에 문의하세요.
* [Azure Log Integration FAQ(질문과 대답)](security-azure-log-integration-faq.md). 이 FAQ는 Azure Log Integration에 대해 자주 묻는 질문과 대답입니다.
* [Azure Log Integration에 Azure Security Center 경고 통합](../security-center/security-center-integrating-alerts-with-log-integration.md) 이 문서에서는 Azure 진단 및 Azure 활동 로그에서 수집된 보안 센터 경고 및 가상 머신 보안 이벤트를 동기화하는 방법을 보여 줍니다. Azure Log Analytics 또는 SIEM 솔루션을 사용하여 로그를 동기화합니다.
* [Azure 진단 및 Azure 감사 로그에 대한 새로운 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.
