---
title: "Azure에서 Azure 스택 마켓플레이스 항목을 추가 합니다. | Microsoft Docs"
description: "Azure 스택 마켓플레이스로 Windows Server Azure 기반 가상 컴퓨터 이미지를 추가 하는 방법을 설명 합니다."
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
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Azure에서 Azure 스택 마켓플레이스 항목을 추가 하는 자습서:

Azure 스택 연산자로 가장 먼저 사용자가 가상 컴퓨터 (VM)을 배포할 수 있도록 하기 위해 수행 해야 Azure 스택 마켓플레이스에 VM 이미지를 추가 하는 것입니다. 기본적으로 Azure 스택 마켓플레이스에 게시는 아무 것도 있지만에서 항목을 다운로드할 수 있습니다 [Azure 마켓플레이스 항목의 큐 레이트 목록을](.\.\azure-stack-marketplace-azure-items.md) 검증을 거친 Azure 스택에 실행 된 합니다. 연결 된 시나리오에서 작동 하는 Azure를 사용한 Azure 스택 인스턴스를 등록 한 경우이 옵션을 사용 합니다.

이 자습서에서는 Azure 스택 마켓플레이스로 Azure marketplace의 Windows Server 2016 VM 이미지를 추가합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Windows Server VM Azure 스택 마켓플레이스 항목을 추가 합니다.
> * VM 이미지를 사용할 수 확인 
> * VM 이미지를 테스트 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 설치는 [Azure 스택 호환 Azure PowerShell 모듈](asdk-post-deploy.md#install-azure-stack-powershell)
- 다운로드는 [Azure 스택 도구](asdk-post-deploy.md#download-the-azure-stack-tools)
- [등록 된 ASDK](asdk-register.md) Azure 구독

## <a name="add-a-windows-server-vm-image"></a>Windows Server VM 이미지를 추가 합니다.
Azure 마켓플레이스의 이미지를 다운로드 하 여 Azure 스택 마켓플레이스로 Windows Server 2016 이미지를 추가할 수 있습니다. 연결 된 시나리오에서 작동 하는 경우 이미 있으면이 옵션을 사용 하 여 [Azure 스택 인스턴스 등록](asdk-register.md) azure입니다.

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리** > **Azure에서 추가**합니다. 

   ![Azure에서 추가](media/asdk-marketplace-item/azs-marketplace.png)

3. 찾기 및 검색은 **Windows Server 2016 Datacenter** 이미지입니다.

4. 선택 된 **Windows Server 2016 Datacenter** 이미지를 클릭 한 **다운로드**합니다.

   ![Azure에서 이미지를 다운로드 합니다.](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> VM 이미지를 다운로드 하 고 Azure 스택 마켓플레이스에 게시 하려면 1 시간 정도 걸립니다. 

이미지가 게시 되 고 아래에서 사용 가능한 됩니다 다운로드가 완료 되 면 **마켓플레이스 관리**합니다. 이미지에서 제공 됩니다. **계산** 새 가상 컴퓨터를 만들 수 있습니다.

## <a name="verify-the-marketplace-item-is-available"></a>마켓플레이스 항목을 사용할 수 확인
Azure 스택 marketplace에서 새 VM 이미지를 사용할 수 있는지 확인 하려면 다음이 단계를 사용 합니다.

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **더 많은 서비스** > **마켓플레이스 관리**합니다. 

3. Windows Server 2016 데이터 센터 VM 이미지에 성공적으로 추가 되었는지 확인 합니다.

   ![Azure에서 다운로드 한 이미지](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>VM 이미지를 테스트 합니다.
Azure 스택 연산자로 사용할 수 있습니다는 [관리자 포털](https://adminportal.local.azurestack.external) 테스트를 만들려면 이미지 유효성을 검사 하는 VM 수 있게 된 했습니다. 

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 클릭 **새** > **계산** > **Windows Server 2016 데이터 센터** > **만들**합니다.  
 ![마켓플레이스 이미지에서 VM 만들기](media/asdk-marketplace-item/new-compute.png)

3. 에 **기본 사항** 블레이드에서 다음 정보를 입력 하 고 클릭 **확인**:
  - **이름**: 테스트-v m-1
  - **사용자 이름**: AdminTestUser
  - **Password**: AzS-TestVM01
  - **구독**: 기본 공급자 구독 허용
  - **리소스 그룹**: rg-vm-테스트
  - **위치**: 로컬

4. 에 **크기를 선택** 블레이드에서 클릭 **표준 A1**, 클릭 하 고 **선택**합니다.  

5. 에 **설정** 블레이드에서 기본값을 적용 하 고 클릭 **확인**

6. **요약** 블레이드에서 클릭 **확인** 가상 컴퓨터를 만듭니다.  
> [!NOTE]
> 가상 컴퓨터 배포를 완료 하려면 몇 분이 걸립니다.

7. 새 VM을 보려면 클릭 **가상 컴퓨터**, 다음 검색 **테스트-v m-1** 해당 이름을 클릭 합니다.
    ![표시 되는 첫 번째 테스트 VM 이미지](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>리소스 정리
성공적으로, VM에 로그인 하 고 테스트 이미지가 제대로 작동 하는지 확인 한, 후 테스트 리소스 그룹을 삭제 해야 합니다. 이렇게 하면 단일 노드 ASDK 설치에 사용할 수 있는 제한 된 리소스 확보 됩니다.

필요 없는 경우 다음이 단계를 수행 하 여 리소스 그룹, VM 및 모든 관련된 리소스를 삭제 합니다.

1. 에 로그인 하 고 [ASDK 관리자 포털](https://adminportal.local.azurestack.external)합니다.
2. 클릭 **리소스 그룹** > **테스트-vm-rg** > **리소스 그룹 삭제**합니다.
3. 형식 **테스트-vm-rg** 리소스 그룹 이름 및 클릭으로 **삭제**합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Windows Server VM Azure 스택 마켓플레이스 항목을 추가 합니다.
> * VM 이미지를 사용할 수 확인 
> * VM 이미지를 테스트 합니다.

Azure 스택 제공 및 계획을 만드는 방법에 알아보려면 다음 자습서를 진행 합니다.

> [!div class="nextstepaction"]
> [Azure 스택 IaaS 서비스 제공](asdk-offer-services.md)