---
title: Azure 스택 Kubernetes 클러스터 배포 | Microsoft Docs
description: Azure 스택 Kubernetes 클러스터를 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 7cf865f0ce75d8308d6d42306e8e05852f763cae
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Azure 스택 Kubernetes 클러스터 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

> [!Note]  
> Azure 스택 Azure 컨테이너 서비스 (ACS) Kubernetes 비공개 미리 보기 버전에서입니다. Azure 스택 연산자는이 문서의 지침을 수행 하는 데 필요한 Kubernetes 마켓플레이스 항목에 대 한 액세스를 요청 해야 합니다.

다음 문서를 배포 하 고 단일, 통합 작업에서 Kubernetes에 대 한 리소스를 프로 비전 하는 Azure 리소스 관리자 솔루션 템플릿을 사용 하 여 살펴 봅니다. 필요한 Azure 스택 설치 프로그램에 대 한 필요한 정보를 수집 하 고 서식 파일을 생성을 다음을 클라우드에 배포.

## <a name="kubernetes-and-containers"></a>Kubernetes 및 컨테이너

Azure 컨테이너 서비스 (ACS) Kubernetes Azure 스택에 설치할 수 있습니다. [Kubernetes](https://kubernetes.io) 는 오픈 소스 시스템 배포를 자동화 하기 위한 크기 조정을 하 고 컨테이너에서 응용 프로그램의 관리 합니다. A [컨테이너](https://www.docker.com/what-container) VM에는 이미지에 포함 되어 있습니다. 컨테이너 이미지는 VM와 달리 코드, 특정 라이브러리 및 설정을 실행 하기 위해 런타임 코드를 같은 응용 프로그램을 실행 하는 데 필요한 리소스를 포함 합니다.

Kubernetes를 사용할 수 있습니다.

- 초 후에 배포할 수 있는 확장성이 뛰어난, 업그레이드, 응용 프로그램을 개발 합니다. 
- 응용 프로그램의 설계를 간소화 하 고 다른 투구 응용 프로그램의 안정성을 향상 합니다. [Helm](https://github.com/kubernetes/helm)은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다.
- 쉽게 배율로 응용 프로그램의 상태를 진단 하 고 모니터링 및 업그레이드 기능입니다.

## <a name="prerequisites"></a>필수 조건 

시작 하려면 올바른 사용 권한이 이며 Azure 스택 준비 되어 있는지 확인 합니다.

1. Azure Active Directory (Azure AD) 테 넌 트에 응용 프로그램을 만들 수 있는지 확인 합니다. Kubernetes 배포에 대 한 이러한 권한이 필요 합니다.

    참조에 대 한 지침은 수 있는 권한을 확인, [확인 Azure Active Directory 사용 권한](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)합니다.

2. SSH 공용 및 개인 키 쌍을 스택에 Azure Linux VM에 로그인을 생성 합니다. 클러스터를 만들 때 공개 키가 필요 합니다.

    키를 생성, 참조 [SSH 키 생성](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)합니다.

3. Azure 스택 테 넌 트 포털에서 올바른 구독이 있는지 및 새 응용 프로그램을 추가 하려면 사용할 수 있는 주소 충분 한 공용 IP 있는지 확인 하십시오.

## <a name="create-a-service-principal-in-azure-ad"></a>Azure AD에서 서비스 사용자 만들기

1. 에 로그인 하는 전역 [Azure 포털](http://www.poartal.azure.com)합니다.
2. Azure 스택와 연결 된 Azure AD 테 넌 트를 사용 하 여 서명 했는지 확인 하십시오.
3. Azure AD 응용 프로그램을 만듭니다.

    a. 선택 **Azure Active Directory** > **+ 응용 프로그램 등록** > **새 응용 프로그램 등록**합니다.

    나. 입력 한 **이름** 응용 프로그램의 합니다.

    다. 선택 **웹 응용 프로그램 / API**

    d. 입력 `http://localhost` 에 대 한는 **로그온 URL**합니다.

    다. **만들기**

4. 기록 된 **응용 프로그램 ID**합니다. 클러스터를 만들 때 ID가 필요 합니다. ID로 참조 되 **서비스 사용자 클라이언트 ID**합니다.

5. 선택 **설정** > **키**합니다.

    a. 입력은 **설명**합니다.

    나. 선택 **사용 기간 제한 없음** 에 대 한 **Expires**합니다.

    다. **저장**을 선택합니다. 확인 키 문자열을 확인 하십시오. 클러스터를 만들 때 키 문자열이 필요 합니다. 키가 참조로는 **서비스 사용자 클라이언트 암호**합니다.



## <a name="give-the-service-principal-access"></a>서비스 보안 주체 액세스를 제공 합니다.

주 서버에서 리소스를 만들 수 있도록 구독에 대 한 서비스 보안 주체를 지정 합니다.

1.  에 로그인 하 고 [관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **사용자 구독** > **+ 추가**합니다.

3. 자신이 만든 구독을 선택 합니다.

4. 선택 **액세스 제어 (IAM)** > 선택 **+ 추가**합니다.

5. 선택 된 **소유자** 역할입니다.

6. 만든 서비스에 대 한 주 응용 프로그램 이름을 선택 합니다. 검색 상자에 이름을 입력 해야 합니다.

7. **저장**을 클릭합니다.

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes 클러스터 배포

1. 열기는 [Azure 스택 포털](https://portal.local.azurestack.external)합니다.

2. 선택 **새 +** > **계산** > **Kubernetes 클러스터**합니다.

    ![솔루션 템플릿 배포](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. 선택 **매개 변수** 에 솔루션 템플릿을 배포 합니다.

    ![솔루션 템플릿 배포](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. 입력은 **Linux 관리자 사용자 이름**합니다. DVM와 Kubernetes 클러스터의 일부인 Linux 가상 컴퓨터에 대 한 사용자 이름입니다.

3. 입력은 **SSH 공개 키** Kubernetes 클러스터 및 DVM의 일부분으로 만든 모든 Linux 컴퓨터에 대 한 권한 부여에 사용 합니다.

4. 입력은 **끝점 테 넌 트**합니다. Kubernetes 클러스터에 대 한 리소스 그룹 만들기에 연결 하는 Azure 리소스 관리자 끝점입니다. Azure 스택 연산자에서 통합된 된 시스템에 대 한 끝점을 가져올 해야 합니다. 사용할 수 있습니다에 대 한는 Azure 스택 개발 키트 (ASDK), `https://management.local.azurestack.external`합니다.

5. 입력은 **테 넌 트 ID** 테 넌 트에 대 한 합니다. 이 값을 찾는 데 도움이 필요한 경우 참조 [테 넌 트 ID 가져오기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)합니다. 

6. 입력은 **마스터 프로필 DNS 접두사** 지역에 고유 합니다. 와 같은 지역 고유 이름 이어야 합니다 `k8s-12345`합니다. 하려고 선택 이름과 같은 리소스 그룹으로 이름을 모범 사례입니다.

    > [!Note]  
    > 각 클러스터에 대 한 새롭고 고유한 마스터 프로필 DNS 접두사를 사용 합니다.

7. 클러스터의 에이전트의 수를 입력 합니다. 이 값으로 참조 되는 **에이전트 풀 프로 파일 수**합니다. 있을 수에서 1부터 32

8. 입력은 **주 응용 프로그램 ID 서비스** Kubernetes Azure 클라우드 공급자가 사용 합니다.

9. 입력은 **서비스 보안 주체 클라이언트 암호** 서비스 주 응용 프로그램을 만들 때 만들었으므로 합니다.

10. 입력은 **Kubernetes Azure 클라우드 공급자 버전**합니다. Kubernetes Azure 공급자에 대 한 버전입니다. Azure 스택 각 Azure 스택 버전에 대 한 사용자 지정 Kubernetes 빌드를 해제합니다.

12. **확인**을 선택합니다.

### <a name="specify-the-solution-values"></a>솔루션 값 지정

1. 선택 된 **구독**합니다.

2. 새 리소스 그룹의 이름을 입력 하거나 기존 리소스 그룹을 선택 합니다. 리소스 이름은 대 / 소문자 영숫자 있어야 합니다.

3. 와 같은 리소스 그룹의 위치를 입력 **로컬**합니다.

4. 선택 **만듭니다.**

## <a name="connect-to-your-cluster"></a>클러스터에 연결

이제 클러스터에 연결할 준비가 되었습니다. 해야 합니다는 **kubectl**, Kubernetes 명령줄 클라이언트입니다. 에 연결 하 고 Azure 컨테이너 서비스 설명서에 클러스터 관리에 대 한 지침을 찾을 수 있습니다.   

자세한 내용은 [클러스터에 연결](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster)합니다.

## <a name="next-steps"></a>다음 단계

[(Azure 스택 연산자)에 대 한 마켓플레이스에 Kubernetes 클러스터 추가](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure에서 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)