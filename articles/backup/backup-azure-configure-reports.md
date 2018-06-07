---
title: "Azure Backup에 대한 보고서 구성"
description: "이 문서에서는 Recovery Services 자격 증명 모음을 사용하여 Azure Backup에 대한 Power BI 보고서를 구성하는 방법을 설명합니다."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51d85da1bda0d9483883301b71c20e405724b9b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
# <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성
이 문서에서는 Recovery Services 자격 증명 모음을 사용하여 Azure Backup에 대한 보고서를 구성하고 Power BI를 사용하여 이러한 보고서에 액세스하는 단계를 설명합니다. 이러한 단계를 수행한 후 Power BI로 직접 이동하여 모든 보고서를 확인하고, 보고서를 사용자 지정 및 생성할 수 있습니다. 

## <a name="supported-scenarios"></a>지원되는 시나리오
1. Azure Backup 보고서는 Azure Recovery Services 에이전트를 사용한 클라우드로 파일/폴더 백업 및 Azure 가상 머신 백업에 대해 지원됩니다.
2. Azure SQL, DPM 및 Azure Backup Server에 대한 보고서는 현재 지원되지 않습니다.
3. 각 자격 증명 모음에 대해 동일한 저장소 계정이 구성된 경우 여러 자격 증명 모음과 구독의 보고서를 볼 수 있습니다. 선택한 Storage 계정이 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.
4. Power BI에서 보고서에 대해 예약된 새로 고침 빈도는 24시간입니다. Power BI에서 보고서의 임시 새로 고침을 수행할 수도 있으며, 이 경우 고객 저장소 계정의 최신 데이터가 보고서 렌더링에 사용됩니다. 
5. Azure Backup 보고서는 현재 국가별 클라우드에서 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건
1. [Azure 저장소 계정](../storage/common/storage-create-storage-account.md#create-a-storage-account)을 만들어 보고서에 대해 구성합니다. 이 저장소 계정은 보고서 관련 데이터를 저장하는 데 사용됩니다.
2. [Power BI 계정을 만들어](https://powerbi.microsoft.com/landing/signin/) Power BI 포털에서 보고서를 확인, 사용자 지정 및 생성합니다.
3. 아직 등록되지 않은 경우 리소스 공급자 **Microsoft.insights**를 저장소 계정 구독 및 Recovery Services 자격 증명 모음 구독에 등록하여 보고 데이터가 저장소 계정으로 흐르도록 합니다. 동일한 작업을 수행하려면 Azure Portal > 구독 > 리소스 공급자로 이동한 다음 이 공급자를 선택해서 등록해야 합니다. 

## <a name="configure-storage-account-for-reports"></a>보고서에 대한 저장소 계정 구성
Azure Portal을 사용하여 Recovery Services 자격 증명 모음에 대한 저장소 계정을 구성하려면 다음 단계를 따르세요. 이는 일회성 구성이며 저장소 계정이 구성되면 Power BI로 직접 이동하여 콘텐츠 팩을 확인하고 보고서를 활용할 수 있습니다.
1. 이미 Recovery Services 자격 증명 모음이 열려 있으면 다음 단계로 진행합니다. Recovery Services 자격 증명 모음이 열려 있지 않지만 Azure Portal에 있는 경우 **모든 서비스**를 클릭합니다.

   * 리소스 목록에서 **Recovery Services**를 입력합니다.
   * 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services 자격 증명 모음**이 표시되면 클릭합니다.

      ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Recovery Services 자격 증명 모음의 목록이 표시됩니다. Recovery Services 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

     선택한 자격 증명 모음 대시보드가 열립니다.
2. 자격 증명 모음 아래에 나타나는 항목 목록에서 모니터링 및 보고서 섹션 아래의 **Backup 보고서**를 클릭하여 보고서에 대한 저장소 계정을 구성합니다.

      ![백업 보고서 메뉴 항목 선택 2단계](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. 백업 보고서 블레이드에서 **진단 설정** 링크를 클릭합니다. 그러면 고객 저장소 계정에 데이터를 푸시하는 데 사용되는 Diagnostics 설정 UI가 열립니다.

      ![진단 사용 3단계](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. **진단 사용** 링크를 클릭합니다. 그러면 저장소 계정을 구성하는 UI가 열립니다. 

      ![진단 사용 4단계](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. **이름** 필드에 설정 이름을 입력하고 **Storage 계정에 보관** 확인란을 선택하여 보고 데이터가 저장소 계정으로 흐르기 시작하도록 합니다.

      ![진단 사용 5단계](./media/backup-azure-configure-reports/select-setting-name.png)
6. Storage 계정 선택을 클릭하고 목록에서 보고 데이터를 저장하는 데 사용할 관련 구독 및 저장소 계정을 선택한 다음 **확인**을 클릭합니다.

      ![저장소 계정 선택 6단계](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. 로그 섹션 아래에서 **AzureBackupReport** 확인란을 선택하고 슬라이더를 이동하여 이 보고 데이터의 보존 기간을 선택합니다. 저장소 계정의 보고 데이터는 이 슬라이더를 사용하여 선택한 기간 동안 유지됩니다.

      ![저장소 계정 저장 7단계](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. 모든 변경 내용을 검토하고 위 그림에 표시된, 맨 위의 **저장** 단추를 클릭합니다. 이 작업을 수행하면 모든 변경 내용이 저장되며, 이제 보고 데이터를 저장하는 데 사용할 저장소 계정이 구성되었습니다.

9. 이제 진단 설정 테이블에는 자격 증명 모음에 사용하도록 설정된 새 설정이 표시됩니다. 표시되지 않으면 업데이트된 설정을 확인하기 위해 테이블을 새로 고칩니다.

      ![진단 설정 보기 9단계](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> 저장소 계정을 저장하여 보고서를 구성한 후에는 초기 데이터 푸시가 완료될 때까지 **24시간 동안 대기**해야 합니다. 이 시간 이후에만 Power BI에서 Azure Backup 콘텐츠 팩을 가져와야 합니다. 자세한 내용은 [FAQ 섹션](#frequently-asked-questions)을 참조하세요. 
>
>

## <a name="view-reports-in-power-bi"></a>Power BI에서 보고서 보기 
Recovery Services 자격 증명 모음을 사용하여 보고서에 대한 저장소 계정을 구성한 후 보고 데이터가 흐름을 시작하기까지 약 24시간 정도 걸립니다. 저장소 계정을 설정하고 24시간 후에 Power BI에서 보고서를 보려면 다음 단계를 따르세요.
1. Power BI에 [로그인](https://powerbi.microsoft.com/landing/signin/)합니다.
2. **데이터 가져오기**를 클릭한 다음, 콘텐츠 팩 라이브러리의 **서비스**에서 **가져오기**를 클릭합니다. [콘텐츠 팩에 액세스하려면 Power BI 설명서](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/)에 있는 단계를 따르세요.

     ![콘텐츠 팩 가져오기](./media/backup-azure-configure-reports/content-pack-import.png)
3. 검색 창에서 **Azure Backup**을 입력하고 **지금 가져오기**를 클릭합니다.

      ![콘텐츠 팩 가져오기](./media/backup-azure-configure-reports/content-pack-get.png)
4. 위의 5단계에서 구성한 저장소 계정 이름을 입력하고 **다음** 단추를 클릭합니다.

    ![저장소 계정 이름 입력](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. 이 저장소 계정에 대한 저장소 계정 키를 입력합니다. Azure Portal에서 저장소 계정으로 이동하면 [저장소 액세스 키를 확인하고 복사](../storage/common/storage-create-storage-account.md#manage-your-storage-account)할 수 있습니다. 

     ![저장소 계정 입력](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. **로그인** 단추를 클릭합니다. 로그인에 성공하면 **데이터를 가져오는 중** 알림이 표시됩니다.

    ![콘텐츠 팩 가져오기](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    일정 시간 후에 가져오기가 완료되면 **성공** 알림이 표시됩니다. 저장소 계정에 많은 데이터가 있는 경우 콘텐츠 팩을 가져오는 데 시간이 좀 더 걸릴 수도 있습니다.
    
    ![콘텐츠 팩 가져오기 성공](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. 데이터를 성공적으로 가져왔으면 **Azure Backup** 콘텐츠 팩이 탐색 창의 **앱**에 표시됩니다. 이제 목록에 Azure Backup 대시보드, 보고서 및 데이터 집합이 나타나고 새로 가져온 보고서에는 노란색 별이 표시됩니다. 

     ![Azure Backup 콘텐츠 팩](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. 대시보드에서 **Azure Backup**을 클릭합니다. 일련의 고정 키 보고서가 표시됩니다.

      ![Azure Backup 대시보드](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. 전체 보고서 집합을 보려면 대시보드에서 아무 보고서나 클릭합니다.

      ![Azure Backup 작업 상태](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. 보고서의 각 탭을 클릭하여 해당 영역의 보고서를 확인합니다.

      ![Azure Backup 보고서 탭](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>질문과 대답
1. **보고 데이터가 저장소 계정으로 흐름기 시작했는지 확인하려면 어떻게 하나요?**
    
    구성된 저장소 계정으로 이동한 다음 컨테이너를 선택할 수 있습니다. 컨테이너에 insights-logs-azurebackupreport에 대한 항목이 있으면 보고 데이터가 흐르기 시작한 것입니다.

2. **Power BI에서 Azure Backup 콘텐츠 팩 및 저장소 계정으로 데이터 푸시가 수행되는 빈도는 어느 정도인가요?**

   0일 사용자의 경우 저장소 계정에 데이터를 푸시하는 데 약 24시간 정도 걸립니다. 이 초기 푸시가 완료된 후에는 아래 그림에 표시된 빈도로 데이터가 새로 고쳐집니다. 
      * **작업, 경고, Backup 항목, 자격 증명 모음, 보호된 서버 및 정책**과 관련된 데이터는 기록 시 고객 저장소 계정에 푸시됩니다.
      * **저장소**와 관련된 데이터는 24시간마다 고객 Storage 계정에 푸시됩니다.
   
    ![Azure Backup 보고서 데이터 푸시 빈도](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI에는 [하루에 한 번 예약된 새로 고침](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)이 있습니다. Power BI에서 콘텐츠 팩에 대한 데이터를 수동으로 새로 고칠 수 있습니다.

3. **보고서를 유지할 수 있는 기간은 어느 정도인가요?** 

   저장소 계정을 구성하는 동안 저장소 계정의 보고 데이터 보존 기간을 선택할 수 있습니다(위의 보고서에 대한 저장소 계정 구성 섹션에서 6단계 사용). 뿐만 아니라 [Excel에서 보고서를 분석](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)하고 요구에 따라 더 오랜 보존 기간 동안 저장할 수 있습니다. 

4. **저장소 계정을 구성하면 보고서에서 내 데이터를 모두 볼 수 있나요?**

   **"저장소 계정을 구성"**한 후 생성된 모든 데이터는 저장소 계정에 푸시되며 보고서에서 확인할 수 있습니다. 그러나 **진행 중인 작업은 보고를 위해 푸시되지 않습니다**. 작업이 완료되거나 실패하면 보고서에 전송됩니다.

5. **보고서를 보는 데 사용할 저장소 계정을 이미 구성한 경우 다른 저장소 계정을 사용하도록 구성을 변경할 수 있나요?** 

   예, 다른 저장소 계정을 가리키도록 구성을 변경할 수 있습니다. Azure Backup 콘텐츠 팩에 연결하는 동안 새로 구성된 저장소 계정을 사용해야 합니다. 또한 다른 저장소 계정이 구성된 후 새 데이터는 이 저장소 계정으로 흐릅니다. 하지만 구성을 변경하기 전의 오래된 데이터는 계속해서 이전 저장소 계정에 유지됩니다.

6. **여러 자격 증명 모음 및 구독의 보고서를 볼 수 있나요?** 

   예, 다양한 자격 증명 모음에 동일한 저장소 계정을 구성하여 자격 증명 모음 간 보고서를 볼 수 있습니다. 여러 구독의 자격 증명 모음에 대해 동일한 저장소 계정을 구성할 수도 있습니다. 그런 다음 Power BI에서 Azure Backup 콘텐츠 팩에 연결하는 동안 이 저장소 계정을 사용하여 보고서를 볼 수 있습니다. 그러나 선택한 저장소 계정이 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.
   
## <a name="troubleshooting-errors"></a>문제 해결 오류
| 오류 세부 정보 | 해결 방법 |
| --- | --- |
| 백업 보고서의 저장소 계정을 설정한 후에도 **저장소 계정**에 계속 **구성되지 않음**이 표시됩니다. | 저장소 계정을 성공적으로 구성한 경우 이 문제가 있더라고 보고 데이터는 전송됩니다. 이 문제를 해결하려면 Azure Portal > All services > Diagnostic settings > RS vault > Edit Setting으로 이동합니다. 이전에 구성한 설정을 삭제하고 동일한 블레이드로부터 새 설정을 만듭니다. 이때 **이름** 필드를 **서비스**로 설정합니다. 이렇게 하면 구성된 저장소 계정이 표시됩니다. |
|Power BI에서 Azure Backup 콘텐츠 팩을 가져오면 **404- container is not found** 오류가 나타납니다. | 이 문서에서 제시된 대로 복구 서비스 자격 증명 모음에서 보고서를 구성한 후 24시간 동안 기다려야 Power BI에서 올바르게 표시됩니다. 24시간 전에 보고서에 액세스하려는 경우 이 유효한 보고서를 표시하기에는 아직 완전한 데이터가 제공되지 않았기 때문에 이 오류가 나타납니다. |

## <a name="next-steps"></a>다음 단계
이제 저장소 계정을 구성하고 Azure Backup 콘텐츠 팩을 가져왔으므로 다음 단계는 이러한 보고서를 사용자 지정하고 보고 데이터 모델을 사용하여 보고서를 만드는 것입니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure Backup 보고 데이터 모델 사용](backup-azure-reports-data-model.md)
* [Power BI에서 보고서 필터링](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Power BI에서 보고서 만들기](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

