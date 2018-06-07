---
title: Azure 스택 사용자에 게 가상 컴퓨터를 사용할 수 있도록 | Microsoft Docs
description: Azure 스택에서 가상 컴퓨터를 사용할 수 있도록 하기 위해 자습서
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Azure 스택 사용자에 게 가상 컴퓨터를 사용할 수 있도록
Azure 스택 클라우드 관리자 (테 넌 트 라고도 함) 사용자가 구독할 수 있는 서비스를 만들 수 있습니다. 구독을 사용 하 여 사용자가 Azure 스택 서비스를 사용할 수 있습니다.

이 문서는 제안을 만듭니다. 한 다음 테스트 하는 방법을 보여 줍니다. 테스트를 위해 사용자로 포털에 로그인 하 제품을 구독 한 후 구독을 사용 하 여 가상 컴퓨터를 만들 됩니다.

학습 내용:

> [!div class="checklist"]
> * 제품 만들기
> * 이미지 추가
> * 제품 테스트


Azure 스택 서비스 구독, 이벤트, 및 계획을 사용 하 여 사용자에 게 배달 됩니다. 사용자가 여러 제품을 구독할 수 있습니다. 제품에는 하나 이상의 계획이 있을 수 있으며 계획에는 하나 이상의 서비스가 있을 수 있습니다.

![구독, 이벤트, 및 계획](media/azure-stack-key-features/image4.png)

자세한 내용은 참고 [주요 기능 및 개념 Azure 스택의](azure-stack-key-features.md)합니다.

## <a name="create-an-offer"></a>제품 만들기

이제 사용자를 위한 준비 작업을 얻을 수 있습니다. 프로세스를 시작 하기 제안을는 계획 및 할당량을 만들려면 먼저 표시 됩니다.

3. **제품 만들기**

   제안은 공급자를 구입 하거나 구독 하는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다.

   a. [로그인](azure-stack-connect-azure-stack.md) 클라우드 관리자와 클릭으로 포털에 **새로** > **제공 + 계획** > **제공**합니다.
   ![새 제안](media/azure-stack-tutorial-tenant-vm/image01.png)

   나. 에 **새 제공** 섹션에서 입력 **표시 이름** 및 **리소스 이름**를 선택한 다음 새 또는 기존 **리소스 그룹**합니다. 표시 이름은 제품의 친숙한 이름입니다. 클라우드 운영자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure 리소스 관리자 리소스로 제품 작업을 하는 데 사용하는 이름입니다.

   ![표시 이름](media/azure-stack-tutorial-tenant-vm/image02.png)

   다. 클릭 **계획 기본**, 및는 **계획** 섹션에서 클릭 **추가** 제품에는 새 계획을 추가 하려면.

   ![계획 추가](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. 에 **새 계획** 섹션에서 입력 **표시 이름** 및 **리소스 이름**합니다. 표시 이름은는 사용자에 게 표시 하는 계획의 이름입니다. 클라우드 운영자만 리소스 이름을 볼 수 있습니다. 클라우드 운영자는 Azure 리소스 관리자 리소스에 따라 계획을 사용 하려면 사용 하는 이름입니다.

   ![계획 표시 이름입니다.](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. 클릭 **서비스**선택, **Microsoft.Compute**, **Microsoft.Network**, 및 **Microsoft.Storage**, 클릭하고**선택**합니다.

   ![계획 서비스](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. 클릭 **할당량**, 한 다음 할당량 만들려는 첫 번째 서비스를 선택 합니다. IaaS 할당량에 대해서는 계산, 네트워크 및 저장소 서비스에 대 한 다음이 단계를 수행 합니다.

   이 예제에서는 먼저 계산 서비스에 대 한 할당량을 만듭니다. 네임 스페이스 목록에서 선택 된 **Microsoft.Compute** 네임 스페이스 다음를 클릭 하 고 **새 할당량 만들기**합니다.
   
   ![새 할당량 만들기](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. 에 **할당량 만들기** 섹션은 할당량에 대 한 이름을 입력 하 고 누른 할당량에 대 한 원하는 매개 변수를 설정할 **확인**합니다.

   ![할당량 이름](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. 이제, **Microsoft.Compute**를 만든 할당량을 선택 합니다.

   ![할당량을 선택 합니다.](media/azure-stack-tutorial-tenant-vm/image08.png)

   네트워크 및 저장소 서비스에 대해 이러한 단계를 반복 하 고 클릭 **확인** 에 **할당량** 섹션.

   i. 클릭 **확인** 에 **새 계획** 섹션.

   j. 에 **계획** 섹션에서 새 계획을 선택 하 고 **선택**합니다.

   k. 에 **새 제안을** 섹션에서 클릭 **만들기**합니다. 제안을 생성 되 면 알림이 표시 됩니다.

   l. 대시보드 메뉴를 클릭 **제공** 다음 만든 제품을 클릭 합니다.

   m. 클릭 **상태 변경**, 클릭 하 고 **공용**합니다.

   ![공개 상태](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>이미지 추가

가상 컴퓨터를 프로 비전 수 전에 Azure 스택 마켓플레이스 이미지를 추가 해야 합니다. Azure 마켓플레이스에서 Linux 이미지를 포함 하 여 원하는 이미지를 추가할 수 있습니다.

연결 된 시나리오에서 작동 하는 경우와 Azure 스택 인스턴스를 Azure에 등록 하는 경우 다음 다운로드할 수 있습니다 Windows Server 2016 VM 이미지는 Azure 마켓플레이스의 설명 된 단계를 사용 하 여는 [다운로드 마켓플레이스 항목을 Azure에서 Azure 스택](azure-stack-download-azure-marketplace-item.md) 항목입니다.

시장에 다른 항목을 추가 하는 방법에 대 한 정보를 참조 하십시오. [The Azure 스택 마켓플레이스](azure-stack-marketplace.md)합니다.

## <a name="test-the-offer"></a>제품 테스트

구독을 만든 했으므로 테스트할 수 있습니다. 사용자로 로그인 하 고 제품에 등록 하 고 가상 컴퓨터를 추가 합니다.

1. **제품 구독**

   지금 제공 하는 서비스에 가입 하려면 사용자로 포털에 로그인 수 있습니다.

   a. 사용자 및 클릭으로 사용자 포털에 로그인 **구독**합니다.
   - 통합된 된 시스템에 대 한 URL 운영자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식에 포함 될 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
   - Azure 스택 개발 키트를 사용 하는 경우 포털 주소는 https://portal.local.azurestack.external합니다.

   ![구독 가져오기](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   나. 에 **표시 이름** 필드, 구독에 대 한 이름을 입력 하 고을 클릭 **제공**를에 제공 하는 중 하나를 클릭는 **제안을 선택** 섹션을 선택한 다음 클릭  **만들**합니다.

   ![제품 만들기](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   다. 만든 구독을 보려면 클릭 **더 많은 서비스**, 클릭 **구독**, 새 구독을 클릭 합니다.  

   제공 하는 서비스를 구독 한 후 새 구독의 일부인 서비스를 확인 하기 위해 포털을 새로 고칩니다.

2. **가상 머신 프로비전**

   이제 구독을 사용 하 여 가상 컴퓨터를 프로 비전 하려면 사용자로 포털에 로그인 수 있습니다. 

   a. 사용자 포털에 사용자로 로그인 합니다.
      - 통합된 된 시스템에 대 한 URL 운영자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식에 포함 될 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
   - Azure 스택 개발 키트를 사용 하는 경우 포털 주소는 https://portal.local.azurestack.external합니다.

   나.  대시보드 클릭 **새로** > **계산** > **Windows Server 2016 데이터 센터 Eval**, 클릭 하 고 **만들기**.

   다. 에 **기본 사항** 섹션에서 입력 한 **이름**, **사용자 이름**, 및 **암호**, 선택는 **구독**, 만들기는 **리소스 그룹** (또는 기존 템플릿을 선택)을 클릭 한 다음 **확인**합니다.

   d. 에 **크기를 선택** 섹션에서 클릭 **표준 A1**, 클릭 하 고 **선택**합니다.  

   e. 에 **설정** 섹션에서 클릭 **가상 네트워크**합니다. 에 **가상 네트워크 선택** 섹션에서 클릭 **새로 만들기**합니다. 에 **가상 네트워크 만들기** 섹션에서 모든 기본값을 적용 하 고 클릭 **확인**합니다. 에 **설정** 섹션에서 클릭 **확인**합니다.

   ![가상 네트워크 만들기](media/azure-stack-provision-vm/image04.png)

   f. 에 **요약** 섹션에서 클릭 **확인** 가상 컴퓨터를 만듭니다.  

   g. 새 가상 컴퓨터를 보려면 클릭 **모든 리소스**, 다음 가상 컴퓨터를 검색 하 고 해당 이름을 클릭 합니다.

    ![모든 리소스](media/azure-stack-provision-vm/image06.png)

이 자습서에서 배운 내용:

> [!div class="checklist"]
> * 제품 만들기
> * 이미지 추가
> * 제품 테스트

> [!div class="nextstepaction"]
> [사용자에 게 웹, 모바일 및 API 앱 사용할 수 있는 Azure 스택](azure-stack-tutorial-app-service.md)