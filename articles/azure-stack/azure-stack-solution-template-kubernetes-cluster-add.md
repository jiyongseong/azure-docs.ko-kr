---
title: Azure 스택 마켓플레이스에 Kubernetes 클러스터를 추가 합니다. | Microsoft Docs
description: Azure 스택 마켓플레이스에 Kubernetes 클러스터를 추가 하는 방법을 알아봅니다.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 0ba0e1f3d9e0f1cbb6ba4109a21fc29dc41df5fc
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603456"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Azure 스택 마켓플레이스에 Kubernetes 클러스터 추가

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

> [!note]  
> Azure 스택 Azure 컨테이너 서비스 (ACS) Kubernetes 비공개 미리 보기 버전에서입니다. 이 문서의 지침을 수행 하는 데 필요한 Kubernetes 마켓플레이스 항목에 대 한 액세스를 요청 하려면 [액세스 요청을 제출](https://aka.ms/azsk8)합니다.

사용자에 게 마켓플레이스 항목으로 Kubernetes 클러스터를 제공할 수 있습니다. 사용자가 단일, 통합 작업에서 Kubernetes를 배포할 수 있습니다.

다음 문서는 Azure 리소스 관리자 템플릿을 사용 하 여 배포 하 고 독립 실행형 Kubernetes 클러스터에 대 한 리소스를 프로 비전 살펴봅니다. 시작 하기 전에 Azure 스택 및 전역 Azure 테 넌 트 설정을 확인 합니다. Azure 스택 하는 방법에 대 한 필요한 정보를 수집 합니다. Azure 스택 Marketplace 및 테 넌 트에 필요한 리소스를 추가 합니다. 클러스터 시장에 Kubernetes 클러스터 항목, 사용자 지정 스크립트 및 Ubuntu 서버에 따라 달라 집니다.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>계획, 제공 하는 서비스 및 구독 만들기

계획, 제공 하는 서비스 및 Kubernetes 클러스터 마켓플레이스 항목에 대 한 구독을 만듭니다. 기존 계획을 사용 하 고 제공할 수도 있습니다.

1. 에 로그인 하 고 [관리 포털.](https://adminportal.local.azurestack.external)

2. 기본 계획으로 계획을 만듭니다. 자세한 내용은 [Azure 스택에 계획을 만들](azure-stack-create-plan.md)합니다.

3. 제안을 만듭니다. 자세한 내용은 [Azure 스택에서 제공 하는 서비스를 만들](azure-stack-create-offer.md)합니다.

4. 선택 **제공**를 만든 제품을 찾습니다.

5. 선택 **개요** 제안 블레이드에서 합니다.

6. 선택 **상태 변경**합니다. 선택 **공용**합니다.

7. 선택 **+ 새로 만들기** > **제공 하 고 계획** > **구독** 새 구독을 만들려면 합니다.

    a. 입력 한 **표시 이름**합니다.

    나. 입력 한 **사용자**합니다. 테 넌 트와 연결 된 Azure AD 계정을 사용 합니다.

    다. **공급자 설명**

    d. 설정의 **디렉터리 테 넌 트** Azure 스택에 대 한 Azure AD 테 넌 트에 있습니다. 

    e. 선택 **제공**합니다. 만든 제품의 이름을 선택 합니다. 구독 id를 기록합니다

## <a name="add-an-ubuntu-server-image"></a>Ubuntu 서버 이미지를 추가 합니다.

마켓플레이스를 다음 Ubuntu Server 이미지를 추가 합니다.

1. 에 로그인 하 고 [관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리**합니다.

3. 선택 **+ Azure에서 추가**합니다.

4. `UbuntuServer`을 입력합니다.

5. 다음 프로필을 사용 하 여 서버를 선택 합니다.
    - **게시자**: 정식
    - **제공**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **버전**: 16.04.201802220

    > [!Note]  
    > Ubuntu Server 16.04 LTS의 둘 이상의 버전을 나열할 수 있습니다. 일치 하는 버전을 추가 해야 합니다. Kubernetes 클러스터 항목의 정확한 버전이 필요합니다.

6. 선택 **다운로드 합니다.**

## <a name="add-a-custom-script-for-linux"></a>Linux 용 사용자 지정 스크립트를 추가 합니다.

마켓플레이스에서 Kubernetes 클러스터를 추가 합니다.

1. 열기는 [관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리**합니다.

3. 선택 **+ Azure에서 추가**합니다.

4. `Custom Script for Linux`을 입력합니다.

5. 다음 프로필을 사용 하 여 스크립트를 선택 합니다.
    - **제공**: Linux 2.0에 대 한 사용자 지정 스크립트
    - **버전**: 2.0.3
    - **게시자**: Microsoft Corp

    > [!Note]  
    > Linux 용 사용자 지정 스크립트의 둘 이상의 버전을 나열할 수 있습니다. 일치 하는 버전을 추가 해야 합니다. Kubernetes 클러스터 항목의 정확한 버전이 필요합니다.

6. 선택 **다운로드 합니다.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>마켓플레이스 Kubernetes 클러스터를 추가 합니다.

1. 열기는 [관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리**합니다.

3. 선택 **+ Azure에서 추가**합니다.

4. `Kubernetes Cluster`을 입력합니다.

5. `Kubernetes Cluster`를 선택합니다.

6. 선택 **다운로드 합니다.**

    > [!note]  
    > 마켓플레이스 항목 시장에 나타나는 데 5 분까지 걸릴 수 있습니다.

    ![Kubernetes 클러스터](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>업데이트 하거나 Kubernetes 클러스터를 제거 합니다. 

Kubernetes 클러스터 항목을 업데이트할 때 시장에 있는 항목을 제거 해야 합니다. 그런 다음 마켓플레이스에 Kubernetes 클러스터를 추가 하려면이 문서의 지침을 따를 수 있습니다.

제거 하려면 Kubernetes 클러스터 항목:

1. 현재 항목의 이름과 같은 note `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. PowerShell과 함께 Azure 스택에 연결 합니다.

3. 항목을 제거 하려면 다음 PowerShell cmdlet을 사용 합니다.

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>다음 단계

[Azure 스택 Kubernetes 클러스터 배포](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Azure 스택에서 서비스 제공의 개요](azure-stack-offer-services-overview.md)