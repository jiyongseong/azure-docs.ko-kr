---
title: 응용 프로그램을 Azure 및 Azure 배포 스택 | Microsoft Docs
description: 하이브리드 CI/CD 파이프라인을 Azure 및 Azure 스택 앱을 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604355"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>자습서: Azure 및 Azure 스택에 앱 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 및 하이브리드 연속 통합/지속적인 배달 (CI/CD) 파이프라인을 사용 하는 Azure 스택에 응용 프로그램을 배포 하는 방법에 알아봅니다.

이 자습서에서는 샘플 환경의 생성 합니다.

> [!div class="checklist"]
> * Visual Studio Team Services (VSTS) 리포지토리에 코드 커밋에 따라 새 빌드를 시작 합니다.
> * 사용자 수용 테스트에 대 한 글로벌 Azure에 응용 프로그램을 자동으로 배포 합니다.
> * 코드 테스트에 통과 자동으로 Azure 스택 앱을 배포 합니다.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>하이브리드 배달의 이점 파이프 빌드

지 속성, 보안 및 안정성은 응용 프로그램 배포의 주요 요소입니다. 이러한 요소는 귀하의 조직에 필수적인 및 개발 팀에 중요 합니다. 하이브리드 CI/CD 파이프라인을 사용 하면 온-프레미스 환경 및 공용 클라우드 전반 빌드 파이프를 통합할 수 있습니다. 하이브리드 배달 모델에서는 응용 프로그램을 변경 하지 않고 배포 위치를 변경할 수도 있습니다.

혼합 접근 방식을 사용 하 여 다른 이점은 다음과 같습니다.

* 온-프레미스 Azure 스택 환경 및 Azure 공용 클라우드 전반 일관성 있는 집합의 개발 도구를 유지할 수 있습니다.  공통 도구 집합을 사용 하면 쉽게 CI/CD 패턴과 사례를 구현할 수입니다.
* Azure 스택 또는 Azure에 배포 된 서비스 및 응용 프로그램은 서로 호환 및 한 위치에서 동일한 코드가 실행 될 수 있습니다. 온-프레미스 및 공용 클라우드 특성 / 기능을 이용할 수 있습니다.

CI 및 CD에 대 한 자세한 정보를 알아보려면:

* [연속 통합 이란?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [지속적인 업데이트 무엇입니까?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>필수 조건

구성 요소가 하이브리드 CI/CD 파이프라인을 구축 하기 위해 준비에서 해야 합니다. 다음 구성 요소를 준비 하는 데 시간이 소요 됩니다.

* Azure OEM/하드웨어 파트너 프로덕션 Azure 스택 배포할 수 있습니다. 모든 사용자가 Azure 스택 개발 키트 (ASDK)를 배포할 수 있습니다.
* Azure 스택 연산자 또한 해야: 응용 프로그램 서비스를 배포, 계획을 만들고 제공, 테 넌 트 구독 만들기 및 Windows Server 2016 이미지를 추가 합니다.

>[!NOTE]
>이미 배포 된 이러한 구성 요소 중 일부를 하는 경우이 자습서를 시작 하기 전에 모든 요구 사항을 충족 해야 합니다.

이 자습서에서는 Azure와 Azure 스택 한 기본 지식이 있다고 가정 합니다. 이 자습서를 시작 하기 전에 자세한 내용은 다음 문서를 참조 합니다.

* [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure 스택 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 만들기는 [웹 앱](https://docs.microsoft.com/azure/app-service/app-service-web-overview) Azure에서. 웹 앱 URL의 기록, 자습서에서 사용 해야 합니다.

### <a name="azure-stack-requirements"></a>Azure 스택 요구 사항

* Azure 스택 통합 시스템을 사용 하거나 Azure 스택 개발 키트 (ASDK)를 배포 합니다. 배포 하려면는 ASDK:
    * [자습서: 설치 관리자를 사용 하 여 ASDK 배포](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) 상세한 배포 지침을 제공 합니다.
    * 사용 하 여 [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) ASDK 배포 후 단계를 자동화 하기 위한 PowerShell 스크립트입니다.

    > [!Note]
    > ASDK 설치 과정이 있으므로 그에 따라 계획을 완료 하려면 대략 7 개 시간.

 * 배포 [앱 서비스](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure 스택에 PaaS 서비스입니다.
 * 만들 [계획/제안](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure 스택의 합니다.
 * 만들기는 [구독 테 넌 트](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure 스택의 합니다.
 * 테 넌 트 구독에 웹 앱을 만듭니다. 새 웹 앱 URL을 사용 하 여 나중에 메모를 확인 합니다.
 * 테 넌 트 구독에 VSTS 가상 컴퓨터를 배포 합니다.
* 가상 컴퓨터 (VM)를 위한.NET 3.5와 함께 Windows Server 2016 이미지를 제공 합니다. 이 VM은 개인 빌드 에이전트와 Azure 스택에 생성 됩니다.

### <a name="developer-tool-requirements"></a>개발자 도구 요구 사항

* 만들기는 [VSTS 작업 영역](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)합니다. 등록 하면 프로젝트가 **MyFirstProject**합니다.
* [Visual Studio 2017 설치](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 및 [VSTS 로그인](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)합니다.
* 프로젝트에 연결 하 고 [로컬로 복제](https://www.visualstudio.com/docs/git/gitquickstart)합니다.

 > [!Note]
 > Azure 스택 환경에 올바른 이미지가 Windows Server 및 SQL Server를 실행 하려면 게시 해야 합니다. 배포 된 응용 프로그램 서비스 있어야 합니다.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>개인 빌드 및 릴리스 에이전트 Visual Studio Team Services 통합을 준비

### <a name="prerequisites"></a>필수 조건

Visual Studio Team Services VSTS ()에 대 한 Azure Resource Manager 서비스 사용자를 사용 하 여 인증 합니다. VSTS 있어야는 **참가자** 역할을 Azure 스택 구독에 리소스를 제공 합니다.

다음 단계 인증을 구성 하는 데 필요한 기능을 설명 합니다.

1. 서비스 사용자를 만들거나 기존 서비스 보안 주체를 사용 합니다.
2. 서비스 사용자에 대 한 인증 키를 만듭니다.
3. 이름 SPN (서비스 사용자)는 참가자 역할의 일부가 될 수 있도록 역할 기반 액세스 제어를 통해 Azure 스택 구독 유효성을 검사 합니다.
4. Azure 스택 끝점 및 SPN 정보를 사용 하 여 VSTS에서 새 서비스 정의 만듭니다.

### <a name="create-a-service-principal"></a>서비스 사용자 만들기

참조는 [서비스 사용자 만들기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) 서비스 사용자를 만들고 다음을 선택 하기 위한 지침 **웹 응용 프로그램/API** 응용 프로그램 종류에 대 한 합니다.

### <a name="create-an-access-key"></a>선택 키 만들기

서비스 사용자 인증에 대 한 키가 있어야 합니다. 다음 단계를 사용 하 여 키를 생성 합니다.

1. Azure Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

    ![응용 프로그램 선택](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. 값을 기록해 **응용 프로그램 ID**합니다. VSTS에서 서비스 끝점을 구성 하는 경우 해당 값을 사용 합니다.

    ![응용 프로그램 UI](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 인증 키를 생성하려면 **설정**을 선택합니다.

    ![응용 프로그램 설정 편집](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 인증 키를 생성하려면 **키**를 선택합니다.

    ![키 설정 구성](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 키에 대 한 설명을 제공 하 고 키의 기간을 설정 합니다. 완료되면 **저장**을 선택합니다.

    ![키 설명 및 기간](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    키의 키를 저장 한 후 **값** 표시 됩니다. 이 값을 나중에 가져올 수 없는이 값을 복사 합니다. 제공 하는 **키 값** 응용 프로그램으로 로그인 하도록 응용 프로그램 id가 있습니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

    ![키 값](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>테 넌 트 ID를 가져옵니다.

서비스 끝점 구성의 일환으로, VSTS 필요는 **테 넌 트 ID** Azure 스택 스탬프에 배포 되는 AAD 디렉터리에 해당 하는 합니다. 다음 단계를 사용 하 여 가져올 테 넌 트 id입니다.

1. **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 테 넌 트에 대 한](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 테넌트 ID를 가져오려면 Azure AD 테넌트에 대한 **속성**을 선택합니다.

    ![테 넌 트 속성 보기](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. **디렉터리 ID**를 복사합니다. 이 값은 테넌트 ID입니다.

    ![디렉터리 ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Azure 스택 구독에 리소스를 배포를 서비스 보안 주체 권한을 부여합니다

구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 역할을 나타내는 응용 프로그램에 대 한 최상의 권한을 결정 합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어, 응용 프로그램의 읽기 역할에 리소스 그룹을 추가 하면 리소스 그룹 및 해당 리소스를 읽을 수 있습니다.

1. 응용 프로그램을 할당하려는 범위 수준으로 이동합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다.

    ![구독 선택](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. **구독**, Visual Studio Enterprise를 선택 합니다.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Visual Studio Enterprise에서 선택 **액세스 제어 (IAM)** 합니다.

    ![액세스 제어 (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. **추가**를 선택합니다.

    ![추가](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. **사용 권한을 추가**, 역할을 선택 하면 응용 프로그램에 할당 하려는 합니다. 이 예제는 **소유자** 역할입니다.

    ![소유자 역할](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 기본적으로 Azure Active Directory 응용 프로그램이 사용 가능한 옵션에 표시되지 않습니다. 응용 프로그램을 찾으려면에 이름을 제공 해야는 **선택** 에 검색에 필드를 추가 합니다. 앱을 선택합니다.

    ![앱 검색 결과](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. **저장**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

### <a name="role-based-access-control"></a>역할 기반 Access Control

Azure 역할 기반 액세스 제어 (RBAC) Azure에 대 한 세분화 된 액세스 관리 기능을 제공 합니다. RBAC를 사용 하 여 사용자가 작업을 수행 하는 데 필요한 액세스 수준을 제어할 수 있습니다. 역할 기반 액세스 제어에 대 한 자세한 내용은 참조 [Azure 구독의 리소스에 대 한 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)합니다.

### <a name="vsts-agent-pools"></a>VSTS 에이전트 풀

각 에이전트를 별도로 관리 하는 대신 에이전트 풀에 에이전트를 구성할 수 있습니다. 에이전트 풀 해당 풀에 대 한 모든 에이전트에 대 한 공유 경계를 정의합니다. 에이전트 풀 VSTS에서 팀 프로젝트에 걸쳐 에이전트 풀을 공유할 수 있는 것을 의미 하는 VSTS 계정을 범위가 지정 됩니다. 에이전트 풀에 대 한 자세한 참조 [에이전트 풀 만들기 및 큐](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)합니다.

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>개인용 액세스 토큰 (P a t) Azure 스택에 대 한 추가 합니다.

개인 액세스 토큰 만들기 VSTS 액세스할 수 있습니다.

1. VSTS 계정에 로그인 한 계정 프로필 이름을 선택 합니다.
2. 선택 **보안 관리** 액세스 토큰 만들기 페이지에 있습니다.

    ![사용자 로그인](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![팀 프로젝트를 선택 합니다.](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![개인용 액세스 토큰 추가](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![토큰 만들기](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 토큰을 복사 합니다.

    > [!Note]
    > 토큰 정보를 저장 합니다. 이 정보는 저장 되지 않습니다 하 고 표시 되지 않게 다시 웹 페이지를 벗어나면 있습니다.

    ![개인용 액세스 토큰](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure 스택에 VSTS 빌드 에이전트 설치 호스팅된 빌드 서버

1. Azure 스택 호스트에 배포 하는 빌드 서버에 연결 합니다.
2. 다운로드 및 배포는 빌드 에이전트는 개인을 사용 하 여 서비스 액세스 토큰 (PAT) 및 VM 관리자 계정으로 실행 합니다.

    ![빌드 에이전트를 다운로드 합니다.](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 추출 된 빌드 에이전트에 대 한 폴더로 이동 합니다. 실행 된 **run.cmd** 상승된 된 명령 프롬프트에서 파일입니다.

    ![추출 된 빌드 에이전트](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![빌드 에이전트 등록](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. run.cmd 완료 되 면 빌드 에이전트 폴더 추가 파일이 업데이트 됩니다. 압축 푼된 내용 사용 하 여 폴더는 다음과 같이 표시 됩니다.

    ![빌드 에이전트 폴더 업데이트](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    VSTS 폴더에 에이전트를 볼 수 있습니다.

## <a name="endpoint-creation-permissions"></a>끝점 만들기 권한

끝점을 만들면 Visual Studio Online (VSTO) 빌드 Azure 스택에 Azure 서비스 앱을 배포할 수 있습니다. VSTS는 Azure 스택을 연결 하는 빌드 에이전트에 연결 합니다.

![Vsto에서 NorthwindCloud 샘플 응용 프로그램](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. VSTO에 로그인 하 고 응용 프로그램 설정 페이지로 이동 합니다.
2. **설정**선택, **보안**합니다.
3. **VSTS 그룹**선택, **의해 끝점 작성자**합니다.

    ![NorthwindCloud 끝점 작성자](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. 에 **멤버** 탭에서 **추가**합니다.

    ![멤버 추가](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. **사용자 및 그룹 추가**사용자 이름을 입력 하 고 사용자 목록에서 해당 사용자를 선택 합니다.
6. **변경 내용 저장**을 선택합니다.
7. 에 **VSTS 그룹** 목록에서 **끝점 관리자**합니다.

    ![NorthwindCloud 끝점 관리자](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. 에 **멤버** 탭에서 **추가**합니다.
9. **사용자 및 그룹 추가**사용자 이름을 입력 하 고 사용자 목록에서 해당 사용자를 선택 합니다.
10. **변경 내용 저장**을 선택합니다.

이제 끝점 정보가 있을 Azure 스택 연결으로 VSTS입니다를 사용할 수 있습니다. Azure 스택에서 빌드 에이전트에서 VSTS, 지침 가져오고 에이전트 Azure 스택와의 통신에 대 한 끝점 정보를 전달 하는 다음 합니다.

![빌드 에이전트](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>응용 프로그램 빌드를 개발 합니다.

이 자습서의이 부분에서는 다음을 수행합니다.

* VSTS 프로젝트에 코드를 추가 합니다.
* 자체 포함 된 웹 응용 프로그램 배포를 만듭니다.
* 연속 배포 프로세스 구성

> [!Note]
 > Azure 스택 환경에 올바른 이미지가 Windows Server 및 SQL Server를 실행 하려면 게시 해야 합니다. 배포 된 응용 프로그램 서비스 있어야 합니다. Azure 스택 연산자 요구 사항에 대 한 앱 서비스 설명서 "전제 조건" 섹션을 검토 합니다.

하이브리드 CI/CD 응용 프로그램 코드와 인프라 코드를 적용할 수 있습니다. 사용 하 여 [웹 같은 Azure 리소스 관리자 템플릿을 ](https://azure.microsoft.com/resources/templates/) 두 클라우드 모두를 배포 하는 VSTS에서 응용 프로그램 코드입니다.

### <a name="add-code-to-a-vsts-project"></a>VSTS 프로젝트에 코드 추가

1. VSTS Azure 스택에 대 한 프로젝트 만들기 권한이 있는 계정으로 로그인 합니다. 다음 화면 캡처 HybridCICD 프로젝트에 연결 하는 방법을 보여 줍니다.

    ![프로젝트에 연결](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **리포지토리 복제** 만들고 열어서 기본 웹 응용 프로그램입니다.

    ![리포지토리 복제](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>두 클라우드 모두에서 응용 프로그램 서비스에 대 한 자체 포함된 웹 앱 배포 만들기

1. 편집 된 **WebApplication.csproj** 파일: 선택 **Runtimeidentifier** 다음 추가 `win10-x64.` 자세한 내용은 참조 [자체 포함된 배포](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 설명서입니다.

    ![Runtimeidentifier 구성](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. 팀 탐색기를 사용 하 여 VSTS에 코드를 확인 합니다.

3. 응용 프로그램 코드는 Visual Studio Team Services에 체크 인 확인 합니다.

### <a name="create-the-build-definition"></a>빌드 정의 만들기

1. VSTS 빌드 정의 만들 수 있는 계정으로 로그인 합니다.
2. 로 이동 된 **웹 응용 프로그램 빌드** 프로젝트에 대 한 페이지입니다.

3. **인수**, 추가 **-r win10 x64** 코드입니다. 이것은.net 자체 포함된 배포를 트리거하는 데 필요 코어입니다.

    ![인수 빌드 정의 추가 합니다.](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. 빌드를 실행 합니다. [자체 포함된 배포 빌드](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 프로세스는 Azure 및 Azure 스택에서 실행할 수 있는 아티팩트를 게시 합니다.

### <a name="use-an-azure-hosted-build-agent"></a>호스트 된 빌드 에이전트를 사용 하 여 Azure

VSTS에서 호스트 된 빌드 에이전트를 사용 하 여 웹 앱 빌드 및 배포에 편리한 옵션입니다. 에이전트 유지 관리 및 업그레이드는 연속적이 고 중단 없이 개발 주기 수 있도록 하는 Microsoft Azure에서 자동으로 수행 됩니다.

### <a name="configure-the-continuous-deployment-cd-process"></a>연속 배포 (CD) 프로세스 구성

Visual Studio Team Services VSTS () 및 Team Foundation Server (TFS) 제공 고도로 구성 가능 하며 관리 하기 쉬운 파이프라인 개발와 같은 여러 환경 릴리스에 대 한 QA (품질 보증), 및 프로덕션이 준비 합니다. 이 프로세스는 응용 프로그램 수명 주기의 특정 단계에서 승인이 필요한 포함할 수 있습니다.

### <a name="create-release-definition"></a>릴리스 정의 만들기

릴리스 정의 만드는 응용 프로그램의 마지막 단계는 빌드 프로세스입니다. 이 릴리스 정의 릴리스를 만들고 빌드를 배포에 사용 됩니다.

1. VSTS에 로그인 하 고 이동 **빌드 및 릴리스** 프로젝트에 대 한 합니다.
2. 에 **릴리스** 탭에서  **\[ +]** 가 선택 하 고 **만들기 릴리스 정의**합니다.

   ![릴리스 정의 만들기](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. **´ ï ´**, 선택 **Azure 앱 서비스 배포**를 선택한 후 **적용**합니다.

    ![템플릿 적용](media\azure-stack-solution-hybrid-pipeline\102.png)

4. **추가 아티팩트**에서 **소스 (빌드 정의)** 풀 다운 메뉴에서 Azure 클라우드 빌드 응용 프로그램을 선택 합니다.

    ![아티팩트 추가](media\azure-stack-solution-hybrid-pipeline\103.png)

5. 에 **파이프라인** 탭을는 **1 단계**, **1 개의 작업** 연결할 **환경 작업 보기**합니다.

    ![파이프라인 보기 작업](media\azure-stack-solution-hybrid-pipeline\104.png)

6. 에 **작업** 탭을 입력으로 Azure는 **환경 이름** 에서 azure 클라우드 Traders 웹 EP를 선택 하 고는 **Azure 구독** 드롭 다운 목록입니다.

    ![환경 변수 설정](media\azure-stack-solution-hybrid-pipeline\105.png)

7. 입력은 **Azure 앱 서비스 이름은**, "northwindtraders"는 다음 화면 캡처에는 합니다.

    ![응용 프로그램 서비스 이름](media\azure-stack-solution-hybrid-pipeline\106.png)

8. 에이전트 단계에 대 한 선택 **호스팅된 VS2017** 에서 **에이전트 큐** 드롭 다운 목록입니다.

    ![호스트 된 에이전트](media\azure-stack-solution-hybrid-pipeline\107.png)

9. **Azure 앱 서비스 배포**, 유효한 선택 **패키지 또는 폴더가** 환경에 대 한 합니다.

    ![패키지 또는 폴더 선택](media\azure-stack-solution-hybrid-pipeline\108.png)

10. **선택 파일 또는 폴더**선택, **확인** 를 **위치**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\109.png)

11. 모든 변경 내용을 저장 하 고 돌아가서 **파이프라인**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\110.png)

12. 에 **파이프라인** 탭에서 **추가 아티팩트**, 선택는 **NorthwindCloud Traders 함 선** 에서 **소스 (빌드 정의)** 드롭 다운 목록입니다.

    ![새 아티팩트 추가](media\azure-stack-solution-hybrid-pipeline\111.png)

13. **´ ï ´**, 다른 환경에 추가 합니다. 선택 **Azure 앱 서비스 배포** 선택한 후 **적용**합니다.

    ![템플릿 선택](media\azure-stack-solution-hybrid-pipeline\112.png)

14. "Azure 스택"으로 입력 된 **환경 이름**합니다.

    ![환경 이름](media\azure-stack-solution-hybrid-pipeline\113.png)

15. 에 **작업** 탭, 찾기 및 Azure 스택을 선택 합니다.

    ![Azure 스택 환경](media\azure-stack-solution-hybrid-pipeline\114.png)

16. **Azure 구독** 드롭 다운 목록에서 Azure 스택 끝점에 대 한 "AzureStack Traders 함 선 EP"를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Azure 스택 웹 응용 프로그램 이름으로 입력 된 **응용 프로그램 서비스 이름**합니다.

    ![응용 프로그램 서비스 이름](media\azure-stack-solution-hybrid-pipeline\116.png)

18. 아래 **에이전트 선택**, "Fir AzureStack-bDouglas"에서 선택 하는 **에이전트 큐** 드롭 다운 목록입니다.

    ![에이전트를 선택 합니다.](media\azure-stack-solution-hybrid-pipeline\117.png)

19. 에 대 한 **Azure 앱 서비스 배포**, 유효한 선택 **패키지 또는 폴더가** 환경에 대 한 합니다. **파일 또는 폴더 선택**선택, **확인** 폴더에 대 한 **위치**합니다.

    ![패키지 또는 폴더를 선택 합니다.](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![위치를 승인 합니다.](media\azure-stack-solution-hybrid-pipeline\119.png)

20. 에 **변수** 탭, 명명 된 변수를 찾을 **VSTS_ARM_REST_IGNORE_SSL_ERRORS**합니다. 변수 값으로 설정 **true**, 해당 범위를 설정 하 고 **Azure 스택**합니다.

    ![변수를 구성](media\azure-stack-solution-hybrid-pipeline\120.png)

21. 에 **파이프라인** 탭을는 **연속 배포 트리거** 집합과 NorthwindCloud Traders 웹 아티팩트에 대 한 아이콘은 **연속 배포 트리거** 를 **활성화**합니다.  "함-NorthwindCloud Traders 선" 아티팩트에 대 한 동일한 작업을 수행 합니다.

    ![연속 배포 트리거 설정](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Azure 스택 환경에 대 한 선택은 **pre-deployment 조건** 아이콘 설정할 트리거 **릴리스 후**합니다.

    ![배포 전 조건 트리거 설정](media\azure-stack-solution-hybrid-pipeline\122.png)

23. 변경 내용을 모두 저장합니다.

> [!Note]
> 릴리스 작업에 대 한 일부 설정을 수 자동으로 정의 된 [환경 변수](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) 템플릿에서 릴리스 정의 만들 때. 작업 설정에서 이러한 설정은 수정할 수 없습니다. 그러나 부모 환경 항목에서 이러한 설정을 편집할 수 있습니다.

## <a name="create-a-release"></a>릴리스 만들기

이제 릴리스 정의 수정 내용을 완료 하면 배포를 시작 하는 시간입니다. 이 작업을 수행 하려면 릴리스 정의에서 릴리스를 만듭니다. 릴리스를 자동으로 만들 수 있습니다. 예를 들어 연속 배포 트리거 릴리스 정의에서 설정 됩니다. 즉, 소스 코드를 수정 새 빌드를 시작 하 고이 통해 새 릴리스 합니다. 그러나이 섹션에서는 수동으로 새 릴리스를 만들는 있습니다.

1. 에 **파이프라인** 탭을 열고는 **릴리스** 드롭 다운 목록을 열고 **릴리스 만들기**합니다.

    ![릴리스 만들기](media\azure-stack-solution-hybrid-pipeline\200.png)

2. 릴리스에 대 한 설명을 입력, 올바른 아티팩트 선택 되어 있는지 확인 한 다음 선택 **만들기**합니다. 몇 분 후 배너와 새 릴리스, 생성 된 릴리스 이름을 링크로 표시 될 나타내는 표시 됩니다. 릴리스 요약 페이지를 보려면이 링크를 선택 합니다.

    ![릴리스 만들기 배너](media\azure-stack-solution-hybrid-pipeline\201.png)

3. 에 대 한 릴리스 요약 페이지 릴리스에 대 한 세부 정보를 표시 합니다. "릴리스-2"에 대 한 다음 화면 캡처에는 **환경** 표시 섹션은 **배포 상태** Azure 스택에 대 한 상태 및 진행 중인 ""으로 Azure는 "SUCCEEDED"에 대 한 합니다. Azure 환경에 대 한 배포 상태로 변경 되 면 "SUCCEEDED", 릴리스 승인에 대 한 사용할 준비가 되어 있는지를 나타내는 배너를 표시 합니다. 배포 보류 중 또는 실패 상태가 시점, 파란색 **(i)** 정보 아이콘이 표시 됩니다. 지연 또는 오류에 대 한 이유를 포함 하는 팝업을 보려면이 아이콘을 마우스로 가리킵니다.

    ![릴리스 요약 페이지](media\azure-stack-solution-hybrid-pipeline\202.png)

목록 등의 다른 보기를 해제, 승인이 보류 중입니다 여부를 나타내는 아이콘이 표시 됩니다. 이 아이콘에 대 한 팝업 환경 이름 및 배포와 관련 된 자세한 세부 정보를 표시 합니다. 관리자가 참조를 해제 하 고 릴리스 승인을 받기 위해 대기 하 고 있는 참조의 전반적인 진행 상황에 대 한 쉽습니다.

### <a name="monitor-and-track-deployments"></a>배포를 모니터링 및 추적

이 섹션에서는 모니터링할 모든 배포를 추적 하는 방법을 보여 줍니다. 두 개의 Azure 앱 서비스 웹 사이트를 배포 하기 위한 릴리스 좋은 예를 제공 합니다.

1. "릴리스 2" 요약 페이지에서 선택 **로그**합니다. 배포 하는 동안이 페이지는 에이전트에서 실시간 로그를 표시 합니다. 왼쪽된 창에 각 환경에 대 한 배포의 각 작업의 상태를 표시 합니다.

    사용자 아이콘을 선택할 수 있습니다는 **동작** 배포를 승인 (또는 거부 됨)는 참조 되 고 제공한 메시지 배포 전 또는 배포 후 승인에 대 한 열입니다.

2. 배포가 완료 되 면 전체 로그 파일은 오른쪽 창에 표시 됩니다. 하나를 선택할 수 **단계** "초기화 작업" 같은 단일 된 단계에 대 한 로그 파일을 보려면 왼쪽된 창에서. 개별 로그를 볼 수 있는 기능 쉽게 추적 하 고 전반적인 배포의 일부를 디버그 합니다. 수도 있습니다 **저장** 는 단계에 대 한 로그 파일 또는 **모든 로그를 zip으로 다운로드**합니다.

    ![릴리스 로그](media\azure-stack-solution-hybrid-pipeline\203.png)

3. 열기는 **요약** 릴리스에 대 한 일반 정보를 탭 합니다. 이 보기는 빌드, 배포 된 환경, 배포 상태 및 릴리스에 대 한 기타 정보에 대 한 세부 정보를 표시 합니다.

4. 환경 링크를 선택 (**Azure** 또는 **Azure 스택**) 기존 하는 방법에 대 한 주소 및 특정 환경에 대 한 배포 보류 중인 정보를 볼 수 있습니다. 이러한 뷰는 동일한 빌드의 두 환경에 배포 되었는지 확인할를 신속 하 게 사용할 수 있습니다.

5. 열기는 **프로덕션 응용 프로그램 배포** 브라우저에서 합니다. 예를 들어 Azure 앱 서비스 웹 사이트에 대 한 URL을 열고 `http://[your-app-name].azurewebsites.net`합니다.

## <a name="next-steps"></a>다음 단계

* Azure 클라우드 패턴에 대 한 자세한 참조 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
