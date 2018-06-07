---
title: "Azure 스택 VM 템플릿을 사용 하 여이 자습서에서는 만듭니다 | Microsoft Docs"
description: "사용 하는 ASDK predfined 템플릿과 GitHub 사용자 지정 템플릿을 사용 하는 VM을 만드는 방법을 설명 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: ec44fc879abfe2a457e80f27db972ac4d7570dbd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>자습서: 커뮤니티 서식 파일을 사용 하는 VM 만들기
Azure 스택 연산자 또는 사용자를 만들 수 있습니다를 사용 하 여 VM을 [사용자 지정 GitHub 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) 스택 Azure marketplace에서 직접 배포 하지 않고 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 스택 퀵 스타트 템플릿에 대 한 자세한 내용은 
> * 사용자 지정 GitHub 서식 파일을 사용 하 여 VM 만들기
> * Minikube를 시작 하 고 응용 프로그램 설치

## <a name="learn-about-azure-stack-quickstart-templates"></a>Azure 스택 퀵 스타트 템플릿에 대 한 자세한 내용은
Azure 스택 퀵 스타트 서식 파일에 저장 됩니다는 [공개 AzureStack 퀵 스타트 템플릿 리포지토리의](https://github.com/Azure/AzureStack-QuickStart-Templates) GitHub에서 합니다. 이 저장소에 Microsoft Azure 스택 개발 키트 (ASDK)와 테스트 된 Azure 리소스 관리자 배포 템플릿에 포함 되어 있습니다. 보다 쉽게 Azure 스택 평가 하 고 ASDK 환경을 사용할 수 있도록 사용할 수 있습니다. 

시간이 지남에 따라 많은 GitHub 사용자 400 개 이상의 배포 템플릿의 거 대 한 컬렉션 생성 저장소에 작성 되었습니다. 이 저장소는 Azure 스택에 다양 한 종류의 환경 배포 하는 방법을 보다 잘 이해 하려면 좋은 시작 지점입니다. 

>[!IMPORTANT]
> 이러한 템플릿 중 일부는 커뮤니티의 회원이 및 Microsoft가 아닌 만들어집니다. 각 템플릿에 사용권 계약에 따라 Microsoft가 아니라 해당 소유자에 의해 사용이 허가 됩니다. Microsoft는 이러한 서식 파일을 담당 하지 않으며 보안, 호환성 또는 성능을 검사 하지 않습니다. 커뮤니티 서식 파일은 Microsoft 지원 프로그램 또는 서비스, 지원 되지 않으며 "어떠한 종류의 보증도 없이" 있는 그대로 사용할 수 있습니다.

Azure 리소스 관리자 템플릿을 GitHub에 기여 하 고 원하는 프로그램 기여도를 확인 해야는 [azure-빠른 시작-템플릿 리포지토리](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다.

GitHub 리포지토리 및 그에 기여 하는 방법에 대 한 자세한 내용은 참조는 [리포지토리의 추가 정보 파일](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)합니다. 


## <a name="create-a-vm-using-a-custom-github-template"></a>사용자 지정 GitHub 서식 파일을 사용 하 여 VM 만들기
이 예제에서는 자습서의 [101 vm-linux minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Minikube kubenetes 클러스터를 관리 하기를 실행 하는 AzureStack에 Ubuntu 16.04 가상 컴퓨터를 배포 하는 데 Azure 스택 퀵 스타트 템플릿이 사용 됩니다.

Minikube는 쉽게 Kubernetes를 로컬로 실행 하는 도구입니다. Minikube 일상적인 사용자가 함께 개발 또는 Kubernetes 사용을 고려 하는 VM 내부에 단일 노드 Kubernetes 클러스터를 실행 합니다. 단순, Linux VM에서 실행 중인 하나의 노드만 Kubernetes 클러스터를 지원 합니다. 실행 하는 모든 기능을 갖춘 Kubernetes 클러스터 가져오려는 가장 빠르고 가장 명확한 방법입니다. 개발자가 개발 하 고 자신의 로컬 컴퓨터에 대해 해당 Kubernetes 기반 응용 프로그램 배포를 테스트할 수 있습니다. 아키텍처 측면에서 Minikube VM 마스터와 에이전트 노드 구성 요소를 모두 로컬로 실행 합니다.
- 마스터 노드 구성 요소 서버 etcd API 서버, 스케줄러 등 LocalKube 이라는 단일 Linux 프로세스에서 실행 됩니다.
- 에이전트 노드 구성 요소는 일반 에이전트 노드에서 넘어가는 그대로 docker 컨테이너 내부의 실행 됩니다. 응용 프로그램 배포 관점에서 차이가 없습니다 Minikube 또는 일반 Kubernetes 클러스터 응용 프로그램을 배포할 때입니다.

이 템플릿은 다음과 같은 구성 요소를 설치합니다.

- 16.04 Ubuntu LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Ubuntu VM 이미지 (이 예제의 Ubuntu Server 16.04 LTS) 이미 받아야 [스택 Azure 마켓플레이스 추가](asdk-marketplace-item.md) 다음이 단계를 시작 하기 전에.

1.  클릭 **+ 새로 만들기** > **사용자 지정** > **템플릿 배포**합니다.

    ![](media/asdk-create-vm-template/1.PNG) 

2. 클릭 **템플릿 편집**합니다.

   ![](media/asdk-create-vm-template/2.PNG) 

3.  클릭 **퀵 스타트 템플릿**합니다.

       ![](media/asdk-create-vm-template/3.PNG)

4. 선택 **101 vm-linux minikube** 를 사용 하 여 사용 가능한 템플릿에서 **´ ï ´** 드롭다운 목록으로 이동한 다음 클릭 **확인**합니다.  

   ![](media/asdk-create-vm-template/4.PNG)

5. 그렇지 않은 경우이 수행 하거나 완료 되 면 JSON 서식 파일을 수정 하려는 경우 **저장** 편집 템플릿 대화 상자를 닫습니다.

   ![](media/asdk-create-vm-template/5.PNG) 

6.  클릭 **매개 변수**, 입력 또는 필요에 따라 사용 가능한 필드를 수정 하 고 클릭 **확인**합니다. 구독을 사용 하 여, 또는 기존 리소스 그룹 이름을 선택 누른 선택 **만들기** 을 템플릿 배포를 시작 합니다.

       ![](media/asdk-create-vm-template/6.PNG)

7. 구독을 사용 하 여, 또는 기존 리소스 그룹 이름을 선택 누른 선택 **만들기** 을 템플릿 배포를 시작 합니다.

   ![](media/asdk-create-vm-template/7.PNG)

8. 리소스 그룹 배포에는 사용자 지정 템플릿 기반 VM을 만드는 몇 분 정도 걸립니다. 알림을 통해 및 리소스 그룹 속성에서 설치 상태를 모니터링할 수 있습니다. 

   ![](media/asdk-create-vm-template/8.PNG)

   >[!NOTE]
   > VM은 배포가 완료 되 면 실행 됩니다. 

## <a name="start-minikube-and-install-an-application"></a>Minikube를 시작 하 고 응용 프로그램 설치
Linux VM을 성공적으로 만든 후, minikube를 시작 하 고 응용 프로그램 설치 로깅할 수 있습니다. 

1. 배포가 완료 된 후 클릭 **연결** Linux VM에 연결 하는 데 사용할 수 있는 공용 IP 주소를 봅니다. 

   ![](media/asdk-create-vm-template/9.PNG)

2. 관리자 권한 명령 프롬프트에서 실행 **mstsc.exe** 원격 데스크톱 연결을 열고 이전 단계에서 검색 된 Linux VM의 공용 IP 주소에 연결 합니다. XRDP 로그인 대화 상자가 나타나면 VM을 만들 때 지정한 자격 증명을 사용 합니다.

   ![](media/asdk-create-vm-template/10.PNG)

3. 터미널 에뮬레이터를 열고 다음 명령을 minikube를 시작 하려면 입력 합니다.

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/asdk-create-vm-template/11.PNG)

4. 웹 브라우저를 열고 kubernetes 대시보드 주소를 방문 합니다. 축 하 합니다, 지금 설치 하는 완벽 하 게 작업 kubernetes minikube를 사용 하 여!

   ![](media/asdk-create-vm-template/12.PNG)

5. 샘플 응용 프로그램을 배포 하려는 kubernetes의 공식 설명서 페이지를 방문 면 이미 만들었으므로 하나 위의 "Minikube 클러스터 만들기" 섹션을 건너뛰십시오. 단순히 섹션 "Node.js 응용 프로그램 만들기"에 이동 https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure 스택 퀵 스타트 템플릿에 대 한 자세한 내용은 
> * 사용자 지정 GitHub 서식 파일을 사용 하 여 VM 만들기
> * Minikube를 시작 하 고 응용 프로그램 설치


> [!div class="nextstepaction"]
> [고급 평가 작업에 알아보기](asdk-advanced-eval.md)