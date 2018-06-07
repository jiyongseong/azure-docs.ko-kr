---
title: Azure Automation으로 변경 내용 추적
description: 변경 내용 추적 솔루션을 사용하면 사용자 환경에서 발생하는 소프트웨어 및 Windows 서비스의 변경 내용을 식별할 수 있습니다.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b110f83274b2b42896bd18fb364c355ecc97a028
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258263"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적

이 문서에서는 변경 내용 추적 솔루션을 사용하여 사용자 환경의 변경 내용을 쉽게 식별할 수 있습니다. 이 솔루션은 Windows 및 Linux 소프트웨어, Windows 및 Linux 파일, Windows 레지스트리 키, Windows 서비스 및 Linux 디먼의 변경 내용을 추적합니다. 구성 변경 내용을 식별하면 운영 문제를 쉽게 특정할 수 있습니다.

모니터링되는 서버에 있는 설치된 소프트웨어, Windows 서비스, Windows 레지스트리/파일 및 Linux 디먼에 대한 변경 내용은 처리를 위해 클라우드의 Log Analytics 서비스로 보내집니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 변경 내용 추적 대시보드의 정보를 사용하여 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

변경 내용 추적을 시작하려면 Automation 계정에 대한 변경 내용 추적 및 인벤토리 솔루션을 사용하도록 설정해야 합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.
1. **구성** 아래에서 **변경 내용 추적**을 선택합니다.
1. 기존 Log Analytics 작업 영역을 선택하거나 **새 작업 영역 만들기**를 클릭하고 **사용**을 클릭합니다.

이렇게 하면 Automation 계정에 대한 솔루션을 사용할 수 있습니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸릴 수 있습니다. 솔루션을 사용하도록 설정되면 파란색 배너에서 알려줍니다. **변경 내용 추적** 페이지로 다시 이동하여 솔루션을 관리합니다.

## <a name="configuring-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 구성

솔루션에 컴퓨터를 등록하는 방법을 알아보려면 [Automation 솔루션 온보드](automation-onboard-solutions-from-automation-account.md)를 방문하세요. 변경 내용 추적과 인벤토리 솔루션을 컴퓨터에 탑재한 후 추적할 항목을 구성할 수 있습니다. 새 파일 또는 레지스트리 키를 추적하도록 설정하면 변경 내용 추적 및 인벤토리가 모두 활성화됩니다.

Windows 및 Linux 모두에서 파일의 변경 내용 추적을 위해 파일의 MD5 해시를 사용합니다. 그런 다음, 이 해시를 사용하여 마지막 인벤토리 이후 변경이 있는지 검색합니다.

### <a name="configure-linux-files-to-track"></a>추적할 Linux 파일 구성

다음 단계를 사용하여 Linux 컴퓨터에서 추적할 파일을 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. **설정 편집**(기어 기호)을 클릭합니다.
2. **변경 내용 추적** 페이지에서 **Linux 파일**을 선택한 다음, **+ 추가**를 클릭하여 추적할 새 파일을 추가합니다.
3. **변경 내용 추적에 대해 Linux 파일 추가**에서 추적할 파일 또는 디렉터리에 대한 정보를 입력하고, **저장**을 클릭합니다.

|자산  |설명  |
|---------|---------|
|사용     | 설정이 적용되는지 여부를 결정합니다.        |
|Item Name     | 추적할 파일의 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|경로 입력     | 파일을 확인할 경로입니다. 예: "/etc/* .conf"       |
|경로 유형     | 추적할 항목 유형이며, 가능한 값은 File 및 Directory입니다.        |
|재귀     | 추적할 항목을 찾을 때 재귀가 사용되는지 결정합니다.        |
|sudo 사용     | 항목을 확인할 때 sudo가 사용되는지 여부를 결정합니다.         |
|링크     | 디렉터리를 트래버스할 때 기호화된 링크에서 처리하는 방법을 결정합니다.<br> **Ignore** - 기호화된 링크가 무시되고 참조된 파일/디렉터리를 포함하지 않습니다.<br>**Follow** - 재귀 중에 기호화된 링크를 따르고 참조된 파일/디렉터리도 포함합니다.<br>**관리** - 기호화된 링크를 따르고 반환된 콘텐츠를 변경할 수 있도록 허용합니다.     |

> [!NOTE]
> "Manage" 링크 옵션은 권장되지 않습니다. 파일 콘텐츠 검색은 지원되지 않습니다.

### <a name="configure-windows-files-to-track"></a>추적할 Windows 파일 구성

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 파일을 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. **설정 편집**(기어 기호)을 클릭합니다.
2. **변경 내용 추적** 페이지에서 **Windows 파일**을 선택한 다음, **+ 추가**를 클릭하여 추적할 새 파일을 추가합니다.
3. **변경 내용 추적에 대해 Windows 파일 추가**에서 추적할 파일에 대한 정보를 입력하고, **저장**을 클릭합니다.

|자산  |설명  |
|---------|---------|
|사용     | 설정이 적용되는지 여부를 결정합니다.        |
|Item Name     | 추적할 파일의 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|경로 입력     | 파일을 확인할 경로입니다. 예: "c:\temp\myfile.txt"       |

### <a name="configure-windows-registry-keys-to-track"></a>추적할 Windows 레지스트리 키 구성

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 레지스트리 키를 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. **설정 편집**(기어 기호)을 클릭합니다.
2. **변경 내용 추적** 페이지에서 **Windows 레지스트리**를 선택한 다음, **+ 추가**를 클릭하여 추적할 새 레지스트리 키를 추가합니다.
3. **변경 내용 추적에 대해 Windows 레지스트리 추가**에서 추적할 키에 대한 정보를 입력하고 **저장**을 클릭합니다.

|자산  |설명  |
|---------|---------|
|사용     | 설정이 적용되는지 여부를 결정합니다.        |
|Item Name     | 추적할 파일의 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|Windows 레지스트리 키   | 파일을 확인할 경로입니다. 예: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>제한 사항

현재 변경 내용 추적 솔루션은 다음 항목을 지원하지 않습니다.

* Windows 파일 추적을 위한 폴더(디렉터리)
* Windows 파일 추적을 위한 재귀
* Windows 파일 추적을 위한 와일드카드
* Windows 레지스트리 추적을 위한 재귀
* 경로 변수
* 네트워크 파일 시스템
* File Content(파일 내용)

기타 제한 사항은 다음과 같습니다.

* **최대 파일 크기** 열과 값은 현재 구현에서 사용되지 않습니다.
* 30분 수집 주기에서 2500개 이상의 파일을 수집하는 경우 솔루션 성능이 저하될 수 있습니다.
* 네트워크 트래픽이 많으면 변경 레코드를 표시하는 데 최대 6시간이 걸릴 수 있습니다.
* 컴퓨터를 종료하면서 구성이 수정되었으면 컴퓨터에서 이전 구성에 속한 변경 내용을 게시할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

변경 내용 추적 솔루션에 현재 다음과 같은 문제가 있습니다.

* 핫픽스 업데이트가 Windows 10 크리에이터 업데이트 및 Windows Server 2016 Core RS3 컴퓨터에 대해 수집되지 않습니다.

## <a name="change-tracking-data-collection-details"></a>변경 내용 추적 데이터 수집 정보

다음 테이블에서는 변경 형식에 대한 데이터 컬렉션 빈도를 보여 줍니다. 모든 유형에 대해 현재 상태의 데이터 스냅숏은 최소한 24시간마다 새로 고쳐집니다.

| **변경 유형** | **Frequency(빈도)** |
| --- | --- |
| Windows 레지스트리 | 50분 |
| Windows 파일 | 30분 |
| Linux 파일 | 15분 |
| Windows 서비스 | 30분 |
| Linux 데몬 | 5분 |
| Windows 소프트웨어 | 30분 |
| Linux 소프트웨어 | 5분 |

### <a name="registry-key-change-tracking"></a>레지스트리 키 변경 내용 추적

레지스트리 키의 변경 내용을 모니터링하는 목적은 타사 코드 및 맬웨어가 활성화될 수 있는 확장성 지점을 정확하게 찾기 위한 것입니다. 다음 목록에서는 미리 구성된 레지스트리 키의 목록을 보여 줍니다. 이러한 키는 구성되어 있지만 사용할 수 없습니다. 이러한 레지스트리 키를 추적하려면 각 레지스트리 키를 사용하도록 설정해야 합니다.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 프로세스 내에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;시작 시 실행되는 스크립트를 모니터링합니다.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;종료 시 실행되는 스크립트를 모니터링합니다.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;사용자가 Windows 계정에 로그인하기 전에 로드되는 키를 모니터링합니다. 키는 64비트 컴퓨터에서 실행되는 32비트 프로그램에 사용됩니다.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;응용 프로그램 설정의 변경 내용을 모니터링합니다.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 프로세스 내에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 프로세스 내에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;아이콘 오버레이 처리기 등록을 모니터링합니다.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 아이콘 오버레이 처리기 등록을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 64비트 컴퓨터에서 실행되는 32비트 프로그램에 대해 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새 Internet Explorer 확장을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새 Internet Explorer 확장을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;wavemapper, wave1 및 wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. SYSTEM.INI 파일의 [drivers] 섹션과 유사합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 wavemapper, wave1 and wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. SYSTEM.INI 파일의 [drivers] 섹션과 유사합니다.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;알려졌거나 일반적으로 사용되는 시스템 DLL 목록을 모니터링합니다. 이 시스템은 시스템 DLL의 트로이 목마 버전을 삭제하여 사용자가 취약한 응용 프로그램 디렉터리에 대한 권한을 악용하지 못하게 합니다.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 운영 체제에 대한 대화형 로그온 지원 모델인 Winlogon에서 이벤트 알림을 받을 수 있는 패키지 목록을 모니터링합니다.|

## <a name="use-change-tracking"></a>변경 내용 추적 사용

솔루션을 사용하도록 설정되면 Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택하여 모니터링되는 컴퓨터에 대한 변경 내용 요약을 볼 수 있습니다.

컴퓨터에 대한 변경 내용을 확인한 다음, 각 이벤트에 대한 세부 정보를 살펴볼 수 있습니다. 차트 위쪽의 드롭다운을 사용하여 변경 유형 및 시간 범위에 따라 차트 및 세부 정보를 제한할 수 있습니다. 또한 차트를 클릭하고 끌어서 사용자 지정 시간 범위를 선택할 수도 있습니다.

![변경 내용 추적 대시보드의 이미지](./media/automation-change-tracking/change-tracking-dash01.png)

변경 내용 또는 이벤트를 클릭하면 해당 변경 내용에 대한 자세한 정보가 표시됩니다. 예제에서 알 수 있듯이 서비스의 시작 유형은 수동에서 자동으로 변경되었습니다.

![변경 내용 추적 세부 정보의 이미지](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>로그 검색

포털에서 제공하는 세부 정보 외에도 로그에 대한 검색을 수행할 수 있습니다. **변경 내용 추적** 페이지가 열린 상태에서 **Log Analytics**를 클릭하면 **로그 검색** 페이지가 열립니다.

### <a name="sample-queries"></a>샘플 쿼리

다음 표에서는 이 솔루션에서 수집된 변경 레코드에 대한 로그 검색 샘플을 제공합니다.

|쿼리  |설명  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "자동"<br>&#124; where SvcState == "중지됨"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Auto로 설정되었지만 Stopped로 보고된 Windows 서비스에 대한 최근의 인벤토리 레코드를 표시합니다.<br>결과는 해당 SoftwareName 및 Computer에 대한 최근의 레코드로 제한됩니다.      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "소프트웨어" and ChangeCategory == "제거됨"<br>&#124; order by TimeGenerated desc|제거된 소프트웨어에 대한 변경 레코드를 표시합니다.|

## <a name="next-steps"></a>다음 단계

솔루션 사용에 대한 자세한 내용은 변경 내용 추적에 대한 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [환경 변경 문제 해결](automation-tutorial-troubleshoot-changes.md)

* [Log Analytics에서 로그 검색](../log-analytics/log-analytics-log-searches.md) 을 사용하여 자세한 변경 내용 추적 데이터를 볼 수 있습니다.
