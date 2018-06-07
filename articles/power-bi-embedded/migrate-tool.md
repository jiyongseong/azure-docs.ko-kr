---
title: Power BI Embedded 마이그레이션 도구 사용 | Microsoft Docs
description: Power BI Embedded 마이그레이션 도구를 사용하여 Power BI 작업 영역 컬렉션의 보고서를 Power BI Embedded로 복사할 수 있습니다.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 4f76b1efb509745653bfde0926f56032030f7d47
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Power BI Embedded 마이그레이션 도구 사용

Power BI Embedded 마이그레이션 도구를 사용하여 Power BI 작업 영역 컬렉션의 보고서를 Power BI Embedded로 복사할 수 있습니다.

작업 영역 컬렉션에서 Power BI 서비스로의 콘텐츠 마이그레이션은 현재 솔루션과 동시에 수행할 수 있으며 가동 중지 시간이 필요하지 않습니다.

## <a name="limitations"></a>제한 사항

* 푸시된 데이터 집합은 다운로드할 수 없으며 Power BI 서비스용 Power BI REST API를 사용하여 다시 만들어야 합니다.
* 2016년 11월 26일 이후에 가져온 PBIX 파일은 다운로드할 수 없습니다.

## <a name="download"></a>다운로드

마이그레이션 도구 샘플을 [GitHub](https://github.com/Microsoft/powerbi-migration-sample)에서 다운로드할 수 있습니다. 저장소의 zip을 다운로드하거나 로컬로 복제할 수 있습니다. 일단 다운로드되면 Visual Studio 내에서 *powerbi-migration-sample.sln*을 열어 마이그레이션 도구를 빌드하고 실행할 수 있습니다.

## <a name="migration-plans"></a>마이그레이션 계획

마이그레이션 계획은 Power BI 작업 영역 컬렉션 내 콘텐츠를 분류하는 메타데이터에 불과하며, Power BI Embedded에 이러한 콘텐츠를 게시하는 방식을 나타냅니다.

### <a name="start-with-a-new-migration-plan"></a>새 마이그레이션 계획 시작

마이그레이션 계획은 Power BI 작업 영역 컬렉션에서 사용할 수 있는 항목의 메타데이터로, Power BI Embedded로 이동할 수 있습니다. 마이그레이션 계획은 XML 파일로 저장됩니다.

먼저 새 마이그레이션 계획을 만들 수 있습니다. 새 마이그레이션 계획을 만들려면 다음을 수행합니다.

1. **파일** > **새 마이그레이션 계획**을 선택합니다.

    ![새 마이그레이션 계획](media/migrate-tool/migrate-tool-plan.png)

2. **Power BI 작업 영역 컬렉션 리소스 그룹 선택** 대화 상자에서 **환경** 드롭다운을 선택하고 프로덕션을 선택합니다.

3. 로그인하라는 메시지가 표시됩니다. Azure 구독 로그인을 사용합니다.

    > [!IMPORTANT]
    > 이 계정은 Power BI에 로그인하는 데 사용하는 Office 365 조직 계정이 **아닙니다**.

4. Power BI 작업 영역 컬렉션 리소스를 저장하는 Azure 구독을 선택합니다.

    ![Azure 구독 선택](media/migrate-tool/migrate-tool-select-resource-group.png)

5. 구독 목록 아래에서 작업 영역 컬렉션을 포함하는 **리소스 그룹**을 선택하고 **선택**을 선택합니다.

    ![리소스 그룹 선택](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. **분석**을 선택합니다. 계획을 시작할 Azure 구독 내의 항목 인벤토리가 표시됩니다.

    ![분석 선택 단추](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > 분석 프로세스는 작업 영역 컬렉션의 수와 작업 영역 컬렉션에 있는 콘텐츠 양에 따라 몇 분 정도 걸릴 수 있습니다.

7. **분석**이 완료되면 마이그레이션 계획을 저장할지 묻는 메시지가 표시됩니다.

이때는 Azure 구독에 마이그레이션 계획을 연결한 상태입니다. 아래 내용을 참조하여 마이그레이션 계획을 사용하는 방법의 흐름을 이해하세요. 여기에는 마이그레이션 분석 및 계획, 다운로드, 그룹 만들기 및 업로드가 포함됩니다.

### <a name="save-your-migration-plan"></a>마이그레이션 계획 저장

나중에 사용할 수 있게 마이그레이션 계획을 저장할 수 있습니다. 이렇게 하면 마이그레이션 계획의 모든 정보를 포함하는 XML 파일이 만들어집니다.

마이그레이션 계획을 저장하려면 다음을 수행합니다.

1. **파일** > **마이그레이션 계획 저장**을 선택합니다.

    ![마이그레이션 계획 저장 메뉴 옵션](media/migrate-tool/migrate-tool-save-plan.png)

2. 파일에 이름을 지정하거나 생성된 파일 이름을 사용한 후 **저장**을 선택합니다.

### <a name="open-an-existing-migration-plan"></a>기존 마이그레이션 계획 열기

저장된 마이그레이션 계획을 열어 마이그레이션 작업을 계속할 수 있습니다.

기존 마이그레이션 계획을 열려면 다음을 수행합니다.

1. **파일** > **기존 마이그레이션 계획 열기**를 선택합니다.

    ![기존 마이그레이션 계획 열기 메뉴 옵션](media/migrate-tool/migrate-tool-open-plan.png)

2. 마이그레이션 파일을 선택하고 **열기**를 선택합니다.

## <a name="step-1-analyze-and-plan-migration"></a>1단계: 마이그레이션 분석 및 계획

**마이그레이션 분석 및 계획** 탭에는 현재 Azure 구독의 리소스 그룹에 포함된 항목이 표시됩니다.

![마이그레이션 분석 및 계획 탭](media/migrate-tool/migrate-tool-step1.png)

*SampleResourceGroup*을 예제로 사용합니다.

### <a name="paas-topology"></a>PaaS 토폴로지

*리소스 그룹 > 작업 영역 컬렉션 > 작업 영역* 목록입니다. 리소스 그룹 및 작업 영역 컬렉션에는 이름을 표시됩니다. 작업 영역에는 GUID가 표시됩니다.

목록의 항목은 (#/#) 형식으로 색 및 숫자도 표시합니다. 이것은 다운로드할 수 있는 보고서의 수를 나타냅니다.

검은색은 모든 보고서를 다운로드할 수 있음을 의미합니다. 빨간색은 일부 보고서를 다운로드할 수 없음을 의미합니다. 왼쪽 숫자는 다운로드할 수 있는 보고서의 총 수를 나타냅니다. 오른쪽 숫자는 그룹 내의 총 보고서 수를 나타냅니다.

PaaS 토폴로지 내에서 항목을 선택하여 보고서 섹션에 보고서를 표시할 수 있습니다.

### <a name="reports"></a>보고서

보고서 섹션에는 사용할 수 있는 보고서가 표시되며 다운로드 가능 여부가 표시됩니다.

![Power BI 작업 영역 컬렉션 내의 보고서 목록](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>대상 구조

**대상 구조**는 다운로드할 위치 및 업로드 방법을 도구에 알려줍니다.

#### <a name="download-plan"></a>다운로드 계획

경로가 자동으로 만들어집니다. 원할 경우 이 경로를 변경할 수 있습니다. 경로를 변경하는 경우 **경로 업데이트**를 선택해야 합니다.

**실제로 다운로드가 수행되지는 않습니다.** 보고서를 다운로드할 위치의 구조를 지정할 뿐입니다.

#### <a name="upload-plan"></a>업로드 계획

여기서는 Power BI 서비스에서 생성되는 앱 작업 영역에 사용할 접두사를 지정할 수 있습니다. 접두사 뒤에는 Azure에 존재하는 작업 영역의 GUID가 나옵니다.

![그룹 이름 접두사 지정](media/migrate-tool/migrate-tool-upload-plan.png)

**Power BI 서비스 내에 그룹이 실제로 만들어지지는 않습니다.** 그룹에 대한 명명 구조만 정의됩니다.

접두사를 변경하는 경우 **업로드 계획 생성**을 선택해야 합니다.

그룹을 마우스 오른쪽 단추로 클릭하고 업로드 계획 내에서 직접 그룹의 이름을 바꾸도록 선택할 수 있습니다.

![그룹 이름 바꾸기 상황에 맞는 메뉴 옵션](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> *그룹*의 이름에는 공백이나 잘못된 문자가 없어야 합니다.

## <a name="step-2-download"></a>2단계: 다운로드

**다운로드** 탭에서 보고서 및 관련된 메타데이터 목록이 표시됩니다. 해당 내보내기 상태와 이전 내보내기 상태를 함께 볼 수 있습니다.

![다운로드 탭](media/migrate-tool/migrate-tool-download-tab.png)

다음 두 가지 옵션을 사용할 수 있습니다.

* 특정 보고서를 선택하고 **다운로드가 선택됨**을 선택합니다.
* **모두 다운로드**를 선택합니다.

![다운로드가 선택됨 단추](media/migrate-tool/migrate-tool-download-options.png)

성공적으로 다운로드하면 *완료* 상태가 표시되고 PBIX 파일이 존재하는 것으로 표시됩니다.

다운로드가 완료된 후 **그룹 만들기** 탭을 선택합니다.

## <a name="step-3-create-groups"></a>3단계: 그룹 만들기

사용할 수 있는 보고서를 다운로드한 후에 **그룹 만들기** 탭으로 이동할 수 있습니다. 이 탭에서는 만든 마이그레이션 계획에 따라 Power BI 서비스 내에서 앱 작업 영역이 만들어집니다. **마이그레이션 분석 및 계획** 내의 **업로드** 탭에 제공한 이름을 사용하여 앱 작업 영역이 만들어집니다.

![그룹 만들기 탭](media/migrate-tool/migrate-tool-create-groups.png)

앱 작업 영역을 만들려면 **선택한 그룹 만들기** 또는 **누락된 모든 그룹 만들기**를 선택할 수 있습니다.

이러한 옵션 중 하나를 선택하면 로그인하라는 메시지가 나타납니다. *앱 작업 영역을 만들 Power BI 서비스에 대한 자격 증명을 사용할 수 있습니다.*

![Power BI 로그인 화면](media/migrate-tool/migrate-tool-create-group-sign-in.png)

이렇게 하면 Power BI 서비스 내에서 앱 작업 영역이 만들어집니다. 앱 작업 영역에 보고서가 업로드되지는 않습니다.

Power BI에 로그인하고 작업 영역에 존재하는지 확인하여 앱 작업 영역이 만들어졌는지 확인할 수 있습니다. 작업 영역에는 아무 것도 들어 있지 않습니다.

![Power BI 서비스 내의 앱 작업 영역](media/migrate-tool/migrate-tool-app-workspace.png)

작업 영역을 만든 후에 **업로드** 탭으로 이동할 수 있습니다.

## <a name="step-4-upload"></a>4단계: 업로드

**업로드** 탭에서 Power BI 서비스에 보고서가 업로드됩니다. 마이그레이션 계획에 따라 대상 그룹 이름과 함께 다운로드 탭에서 다운로드한 보고서 목록이 표시됩니다.

![업로드 탭](media/migrate-tool/migrate-tool-upload-tab.png)

선택한 보고서를 업로드하거나 모든 보고서를 업로드할 수 있습니다. 업로드 상태를 다시 설정하여 항목을 다시 업로드할 수도 있습니다.

같은 이름의 보고서가 있는 경우 수행할 작업을 선택할 수 있는 옵션도 제공됩니다. **중단**, **무시** 및 **덮어쓰기** 중에서 선택할 수 있습니다.

![보고서가 존재하는 경우 수행할 작업에 대한 옵션 드롭다운](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![선택한 결과 업로드](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>중복된 보고서 이름

동일한 이름의 보고서가 있지만 확실히 다른 보고서인 경우 보고서의 **TargetName**을 변경해야 합니다. 마이그레이션 계획 XML을 직접 편집하여 이름을 변경할 수 있습니다.

마이그레이션 도구를 닫은 후 변경을 수행하고 도구 및 마이그레이션 계획을 다시 열어야 합니다.

위 예제에서는 복제된 보고서 중 하나가 같은 이름의 보고서가 있음을 나타내며 실패했습니다. 마이그레이션 계획 XML을 보면 다음 내용을 볼 수 있습니다.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

실패한 항목의 경우 SaaSTargetReportName의 이름을 변경할 수 있습니다.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

그런 후 계획을 다시 열고 마이그레이션 도구에서 실패한 보고서를 업로드합니다.

Power BI로 돌아가면 앱 작업 영역에서 보고서 및 데이터 집합이 업로드된 것을 볼 수 있습니다.

![Power BI 서비스 내의 보고서 목록](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>로컬 PBIX 파일 업로드

로컬 버전의 Power BI Desktop 파일을 업로드할 수 있습니다. 이 도구를 닫고, XML을 편집한 후 **PbixPath** 속성에 로컬 PBIX의 전체 경로를 추가해야 합니다.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

xml을 편집한 후에는 마이그레이션 도구 내에서 해당 계획을 다시 열고 보고서를 업로드합니다.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery 보고서

DirectQuery 보고서에 대한 연결 문자열을 업데이트해야 합니다. 이 작업을 *powerbi.com* 내에서 수행할 수도 있고 Power BI Embedded에서 프로그래밍 방식으로 연결 문자열을 쿼리할 수 있습니다(PaaS). 예를 들어 [PaaS 보고서에서 DirectQuery 연결 문자열 추출](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections)을 참조하세요.

그런 후 Power BI 서비스 내에서 데이터 집합에 대한 연결 문자열을 업데이트하고 데이터 원본에 대한 자격 증명을 설정할 수 있습니다. 다음 예제에서 이 작업을 수행하는 방법을 확인할 수 있습니다.

* [Power BI Embedded에서 DirectQuery 연결 문자열 업데이트](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Power BI Embedded에서 DirectQuery 자격 증명 설정](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>다음 단계

Power BI 작업 영역 컬렉션에서 Power BI Embedded로 보고서를 마이그레이션했으므로 이제 응용 프로그램을 업데이트하고 이 앱 작업 영역에 보고서를 포함할 수 있습니다.

자세한 내용은 [Power BI 작업 영역 컬렉션 콘텐츠를 Power BI Embedded로 마이그레이션하는 방법](migrate-from-power-bi-workspace-collections.md)을 참조하세요.

궁금한 점이 더 있나요? [Power BI 커뮤니티에 질문하세요.](http://community.powerbi.com/)