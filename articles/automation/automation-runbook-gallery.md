---
title: Azure Automation용 Runbook 및 모듈 갤러리
description: Microsoft 및 커뮤니티의 Runbook과 모듈을 Azure Automation 환경에 설치하여 사용할 수 있습니다.  이 글에서는 이러한 리소스에 액세스하는 방법과 자신의 Runbook을 갤러리에 올리는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 61d267898bab5e5df394d4097eacb33ada809518
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355953"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation용 Runbook 및 모듈 갤러리
Azure Automation에서 사용자 고유의 Runbook 및 모듈을 만드는 대신 Microsoft 및 커뮤니티에서 이미 구성한 다양한 시나리오에 액세스할 수 있습니다.  이러한 시나리오는 수정 없이 그대로 사용하거나, 이를 기초로 특정 요구 사항에 맞게 편집하여 사용할 수 있습니다.

Runbook은 [Runbook 갤러리](#runbooks-in-runbook-gallery)에서, 모듈은 [PowerShell 갤러리](#modules-in-powerShell-gallery)에서 가져올 수 있습니다.  또한 자신이 개발한 시나리오를 공유하여 커뮤니티에 기여할 수 있습니다.

## <a name="runbooks-in-runbook-gallery"></a>Runbook 갤러리의 Runbook
[Runbook 갤러리](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation)는 Azure Automation으로 가져올 수 있는 Microsoft 및 커뮤니티에서 제작한 다양한 Runbook을 제공합니다. [TechNet 스크립트 센터](https://gallery.technet.microsoft.com/scriptcenter/site/upload)에서 호스트되는 갤러리에서 Runbook을 다운로드하거나, Azure Portal의 갤러리에서 Runbook을 직접 가져올 수 있습니다.

Azure Portal을 사용하는 Runbook 갤러리에서만 직접 가져올 수 있습니다. Windows PowerShell을 사용하여 이 함수를 수행할 수 없습니다.

> [!NOTE]
> 프로덕션 환경에서 설치 및 실행할 때는 Runbook 갤러리에서 가져올 Runbook 내용의 유효성을 검사해야 하며 세심한 주의가 필요합니다.
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Azure 포털을 사용하여 Runbook 갤러리에서 Runbook을 가져오려면
1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화**에서 **Runbook 갤러리**를 클릭합니다.
3. 원하는 갤러리 항목을 찾아 선택하여 세부 정보를 확인합니다. 왼쪽에서 게시자 및 형식에 대한 추가 검색 매개 변수를 입력할 수 있습니다.
   
    ![갤러리 찾아보기](media/automation-runbook-gallery/browse-gallery.png)
5. **TechNet 스크립트 센터** 의 항목을 확인하려면 [소스 프로젝트 보기](http://gallery.technet.microsoft.com/)를 클릭합니다.
6. 항목을 가져오려면 해당 항목을 클릭하여 세부 정보를 확인한 다음 **가져오기** 단추를 클릭합니다.
   
    ![가져오기 단추](media/automation-runbook-gallery/gallery-item-detail.png)
7. 선택적으로 Runbook의 이름을 변경한 다음 **확인** 을 클릭하여 해당 Runbook을 가져옵니다.
8. 이 Runbook은 Automation 계정의 **Runbook** 탭에 표시됩니다.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Runbook 갤러리에 Runbook 추가
Microsoft에서는 다른 고객에게 유용하다고 생각하는 Runbook을 Runbook 갤러리에 추가하도록 장려하고 있습니다.  다음 세부 정보를 고려하여 [스크립트 센터에 업로드](http://gallery.technet.microsoft.com/site/upload) 를 선택하면 Runbook을 추가할 수 있습니다.

* *범주*에 **Windows Azure**를 지정하고 *하위 범주*에 **Automation**를 지정해야 Runbook이 마법사에 표시됩니다.  
* 단일.ps1 또는 .graphrunbook 파일을 업로드해야 합니다.  Runbook에 필요한 모듈, 자식 Runbook 또는 자산이 있는 경우 Runbook의 제출 설명과 내용 섹션에 해당 항목을 나열해야 합니다.  여러 Runbook이 필요한 시나리오인 경우 각 Runbook을 개별적으로 업로드하고 설명마다 관련 Runbook의 이름을 나열합니다. 동일한 태그를 사용하여 동일한 범주에 표시되는지 확인합니다. 시나리오가 작동하기 위해 다른 Runbook이 필요하다는 설명을 사용자가 읽고 알 수 있어야 합니다.
* **그래픽 Runbook** (그래픽 워크플로가 아닌)을 게시하는 경우 "GraphicalPS" 태그를 추가합니다. 
* **코드 섹션 삽입** 아이콘을 사용하여 PowerShell 또는 PowerShell 워크플로 코드 조각을 설명에 삽입합니다.
* Runbook 갤러리 결과에 업로드 요약이 표시되므로 사용자가 해당 Runbook의 기능을 파악하는 데 도움이 되는 자세한 정보를 제공해야 합니다.
* 업로드에 1~3개의 다음 태그를 할당해야 합니다.  Runbook이 마법사에서 태그와 일치하는 범주 아래에 나열됩니다.  이 목록에 없는 태그는 모두 마법사에서 무시됩니다. 일치하는 태그를 지정하지 않으면 [기타] 범주 아래에 Runbook이 나열됩니다.
  
  * Backup
  * 용량 관리
  * 변경 제어
  * 규정 준수
  * 개발/테스트 환경
  * 재해 복구
  * 모니터링
  * 패치
  * 프로비전
  * 재구성
  * VM 수명 주기 관리
* Automation은 갤러리를 한 시간마다 업데이트하므로 제출한 내용이 즉시 확인되지는 않습니다.

## <a name="modules-in-powershell-gallery"></a>PowerShell 갤러리의 모듈
PowerShell 모듈에는 Runbook에 사용할 수 있는 cmdlet이 있으며, Azure Automation에서 설치할 수 있는 기존 모듈을 [PowerShell 갤러리](http://www.powershellgallery.com)에서 사용할 수 있습니다.  Azure Portal에서 이 갤러리를 실행하여 Azure Automation에 직접 설치하거나, 수동으로 다운로드하여 설치할 수 있습니다.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Azure Portal을 사용하여 Automation 모듈 갤러리에서 모듈을 가져오려면
1. Azure Portal에서 Automation 계정을 엽니다.
2. **공유 리소스** 아래에서 **모듈**을 선택하여 모듈 목록을 엽니다.
4. 페이지 위쪽에서 **갤러리 찾아보기**를 클릭합니다.
   
    ![모듈 갤러리](media/automation-runbook-gallery/modules-blade.png) <br>
5. **갤러리 찾아보기** 페이지에서 다음 필드로 검색할 수 있습니다.
   
   * 모듈 이름
   * 태그
   * 작성자
   * Cmdlet/DSC 리소스 이름
6. 관심이 있는 모듈을 찾아 선택하여 세부 내용을 확인합니다.  
   특정 모듈을 상세히 검색하면 PowerShell 갤러리로 다시 연결되는 링크, 필요한 모든 종속성, 모듈이 포함하는 모든 cmdlet 및/또는 DSC 리소스를 포함하여 모듈에 대한 자세한 정보를 볼 수 있습니다.
   
    ![PowerShell 모듈 세부 정보](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Azure Automation에 직접 모듈을 설치하려면 **가져오기** 단추를 클릭합니다.
8. 가져오기 단추를 클릭하면 **가져오기** 창에서 가져오려는 모듈 이름이 표시됩니다. 모든 종속성이 설치되면 **확인** 단추가 활성화됩니다. 종속성이 없는 경우 종속성을 가져와야만 이 모듈을 가져올 수 있습니다.
9. **가져오기** 페이지에서 **확인**을 클릭하여 모듈을 가져옵니다. Azure Automation에서 모듈을 계정에 가져오는 동안 모듈 및 cmdlet에 대한 메타데이터를 추출합니다. 각 활동을 추출해야 하므로 몇 분이 걸릴 수 있습니다.
10. 모듈이 배포 중임을 알리는 초기 알림 및 완료 시의 다른 알림이 표시됩니다.
11. 모듈을 가져온 후에는 사용 가능한 작업이 표시되고 Runbook 및 DSC(필요한 상태 구성)에서 해당 리소스를 사용할 수 있습니다.

> [!NOTE]
> PowerShell 코어만 지원하는 모듈은 Azure Automation에서 지원되지 않으며, Azure Portal에서 가져오거나 PowerShell 갤러리에서 직접 배포할 수 없습니다.

## <a name="requesting-a-runbook-or-module"></a>Runbook 또는 모듈 요청 중
[사용자 음성](https://feedback.azure.com/forums/246290-azure-automation/)에 요청을 보낼 수 있습니다.  Runbook을 작성하는 데 도움이 필요하거나 PowerShell에 대한 질문이 있으면 [포럼](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)에 게시하세요.

## <a name="next-steps"></a>다음 단계
* Runbook 작성을 시작하려면 [Azure Automation에서 Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)
* Runbook용 PowerShell 및 PowerShell 워크플로 간의 차이점을 이해하려면 [PowerShell 워크플로 학습](automation-powershell-workflow.md)

