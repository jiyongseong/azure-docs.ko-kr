---
title: Azure Data Factory의 시각적 작성 | Microsoft Docs
description: Azure Data Factory에서 시각적 작성을 사용하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: shlo
ms.openlocfilehash: a226402f16f98c830c1e1cf34653b8f6513f06b4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성
Azure Data Factory UX(사용자 인터페이스 환경)을 사용하면 코드를 작성할 필요 없이 데이터 팩터리에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다. 시각적 작성을 수행하기 위해 UX를 사용하는 두 가지 방법이 있습니다.

- Data Factory 서비스로 직접 작성합니다.
- 공동 작업, 원본 제어 또는 버전 관리를 위해 VSTS(Visual Studio Team Services) Git 통합으로 작성합니다.

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory 서비스로 직접 작성
Data Factory 서비스를 통한 시각적 작성은 VSTS를 통한 시각적 작성과 두 가지 면에서 다릅니다.

- Data Factory 서비스는 사용자의 변경 사항을 위한 JSON 엔터티를 저장하는 리포지토리를 포함하지 않습니다.
- Data Factory 서비스는 공동 작업 또는 버전 제어에 최적화되지 않습니다.

![Data Factory 서비스 구성 ](media/author-visually/configure-data-factory.png)

UX **제작 캔버스**를 사용하여 Data Factory 서비스를 통해 직접 작성하는 경우 **모두 게시** 모드만 사용할 수 있습니다. 변경한 내용은 Data Factory 서비스에 직접 게시됩니다.

![게시 모드](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>VSTS Git 통합을 통한 작성
VSTS Git 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 공동 작업을 지원합니다. 사용자는 원본 제어, 공동 작업 및 버전 관리 등을 위해 VSTS Git 계정 리포지토리에 데이터 팩터리를 연결할 수 있습니다. 단일 VSTS Git 계정은 여러 리포지토리를 가질 수 있지만 VSTS Git 리포지토리는 단 하나의 데이터 팩터리와 연결할 수 있습니다. VSTS 계정 또는 리포지토리가 없는 경우 [이러한 지침](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)에 따라 리소스를 만듭니다.

> [!NOTE]
> VSTS GIT 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 VSTS GIT 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 VSTS Git 리포지토리 구성
두 가지 방법으로 데이터 팩터리를 사용해서 VSTS GIT 리포지토리를 구성할 수 있습니다.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>구성 방법 1: 시작하기 페이지
Azure Data Factory에서 **시작하기** 페이지로 이동합니다. **코드 리포지토리 구성**을 선택합니다.

![VSTS 코드 리포지토리 구성](media/author-visually/configure-repo.png)

**리포지토리 설정** 구성 창이 나타납니다.

![코드 리포지토리 설정 구성](media/author-visually/repo-settings.png)

창에 다음 VSTS 코드 리포지토리 설정이 표시됩니다.

| 설정 | 설명 | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | VSTS 코드 리포지토리의 유형입니다.<br/>**참고**: GitHub는 현재 지원되지 않습니다. | Visual Studio Team Services Git |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | <your tenant name> |
| **Visual Studio Team Services 계정** | VSTS 계정 이름입니다. VSTS 계정 이름은 `https://{account name}.visualstudio.com`에서 찾을 수 있습니다. [VSTS 계정으로 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | \<계정 이름> |
| **ProjectName** | VSTS 프로젝트 이름입니다. VSTS 프로젝트 이름은 `https://{account name}.visualstudio.com/{project name}`에서 찾을 수 있습니다. | \<VSTS 프로젝트 이름> |
| **RepositoryName** | VSTS 코드 리포지토리 이름입니다. VSTS 프로젝트는 프로젝트 확장에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | \<VSTS 코드 리포지토리 이름> |
| **공동 작업 분기** | 게시하기 위해 사용할 VSTS 공동 작업 분기입니다. 기본값은 `master`입니다. 다른 분기에서 리소스를 게시하려는 경우에 변경합니다. | \<공동 작업 분기 이름> |
| **루트 폴더** | VSTS 공동 작업 분기의 루트 폴더입니다. | \<루트 폴더 이름> |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX **제작 캔버스**에서 기존 데이터 팩터리 리소스를 VSTS Git 리포지토리로 가져올 것인지를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 집합을 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>구성 방법 2: UX 제작 캔버스
Azure Data Factory UX **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다. **Data Factory** 드롭다운 메뉴를 선택한 다음, **코드 리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 <a href="#method1">구성 방법 1</a>의 설명을 참조하세요.

![UX 제작을 위한 코드 리포지토리 설정 구성](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>사용자 버전 제어
_원본 제어_라고도 하는 버전 제어 시스템을 통해 개발자는 코드에 대해 공동 작업을 수행하고, 코드 베이스의 변경 내용을 추적할 수 있습니다. 소스 제어는 개발자가 여러 명인 프로젝트에 반드시 필요한 도구입니다.

데이터 팩터리와 연결된 각 VSTS Git 리포지토리에는 공동 작업 분기가 생성됩니다. (`master`는 기본 공동 작업 분기입니다.) 사용자는 **+ 새 분기**를 클릭하여 기능 분기를 만들고 기능 분기에서 개발을 수행할 수도 있습니다.

![동기화 또는 게시로 코드 변경](media/author-visually/sync-publish.png)

기능 분기에서 기능을 개발할 준비가 되면 **끌어오기 요청 만들기**를 클릭할 수 있습니다. 그러면 끌어오기 요청을 수행하고, 코드 검토를 수행하고, 공동 작업 분기에 변경 내용을 병합할 수 있는 VSTS GIT로 이동합니다. (기본값은 `master`입니다.) 공동 작업 분기에서 Data Factory 서비스에 게시할 수만 있습니다. 

![새 끌어오기 요청 만들기](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>코드 변경 내용 게시
공동 작업 분기에 변경 내용을 병합한 후에(기본값은 `master`) **게시**를 선택하여 마스터 분기의 코드 변경 내용을 Data Factory 서비스에 수동으로 게시합니다.

![Data Factory 서비스에 변경 내용 게시](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> 마스터 분기는 Data Factory 서비스에 배포된 내용을 반영하지 않습니다. 따라서 마스터 분기를 *반드시* Data Factory 서비스에 수동으로 게시해야 합니다.

## <a name="use-the-expression-language"></a>식 언어 사용
Azure Data Factory에서 지원하는 식 언어를 사용하여 속성 값에 대한 식을 지정할 수 있습니다. 

**동적 콘텐츠 추가**를 선택하여 속성 값에 대한 식을 지정합니다.

![식 언어 사용](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>함수 및 매개 변수 사용

Data Factory **식 작성기**에서 파이프라인 및 데이터 집합에 대해 함수를 사용하거나 매개 변수를 지정할 수 있습니다.

지원되는 식에 대한 자세한 내용은 [Azure Data Factory의 식 및 함수](control-flow-expression-language-functions.md)를 참조하세요.

![동적 콘텐츠 추가](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>피드백 제공
기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백**을 선택하세요.

![사용자 의견](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>다음 단계
파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
