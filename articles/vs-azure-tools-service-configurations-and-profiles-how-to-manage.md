---
title: 서비스 구성 및 프로필을 관리하는 방법 | Microsoft Docs
description: 서비스 구성 및 프로필 구성 파일로 작업하는 방법에 대해 배우기|배포 환경에 대한 설정을 저장하고 클라우드 서비스에 대한 설정을 게시합니다.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 4f2dce7cdbf81718f9ccf0c1b7d52c6678cc847f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>서비스 구성 및 프로필을 관리하는 방법
## <a name="overview"></a>개요
클라우드 서비스를 게시하면 Visual Studio는 두 종류의 구성 파일, 서비스 구성 및 프로필에 구성 정보를 저장합니다. 서비스 구성(.cscfg 파일)은 Azure 클라우드 서비스용 배포 환경에 대한 설정을 저장합니다. Azure는 클라우드 서비스를 관리하는 경우 이 구성 파일을 사용합니다. 반면에 프로필(.azurePubxml 파일)은 클라우드 서비스에 대한 설정을 저장합니다. 이 설정은 게시 마법사를 사용하고 Visual Studio에서 로컬로 사용되는 경우 선택한 항목의 레코드입니다. 이 항목에서는 구성 파일의 두 형식으로 작업하는 방법을 설명합니다.

## <a name="service-configurations"></a>서비스 구성
각 배포 환경에 대해 사용하도록 여러 서비스 구성을 만들 수 있습니다. 예를 들어, Azure 응용 프로그램 및 프로덕션 환경에 대한 다른 서비스 구성을 실행하고 테스트하는 로컬 환경용 서비스 구성을 만들 수 있습니다.

요구 사항에 따라 이 서비스 구성을 추가, 삭제, 이름 변경 및 수정할 수 있습니다. 다음 그림에 표시된 것처럼 Visual Studio에서 이 서비스 구성을 관리할 수 있습니다.

![서비스 구성 관리](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

역할의 속성 페이지에서 **구성 관리** 대화 상자를 열 수도 있습니다. Azure 프로젝트에서 역할에 대한 속성을 열려면, 해당 역할에 대한 바로 가기 메뉴를 연 다음 **속성**을 선택합니다. **설정** 탭에서 **서비스 구성** 목록을 확장한 다음 **관리**를 선택하여 **구성 관리** 대화 상자를 엽니다.

### <a name="to-add-a-service-configuration"></a>서비스 구성을 추가하려면
1. 솔루션 탐색기에서 Azure 프로젝트에 대한 바로 가기 메뉴를 열고 **구성 관리**를 선택합니다.
   
    **서비스 구성 관리** 대화 상자가 나타납니다.
2. 서비스 구성을 추가하려면, 기존 구성의 복사본을 만들어야 합니다. 이렇게 하려면 이름 목록에서 복사할 구성을 선택한 다음 **복사본 만들기**를 선택합니다.
3. (선택 사항)서비스 구성에 다른 이름을 지정하려면 이름 목록에서 새 서비스 구성을 선택한 다음 **이름 바꾸기**를 선택합니다. **이름** 텍스트 상자에 이 서비스 구성에 사용할 이름을 입력한 다음 **확인**을 선택합니다.
   
    솔루션 탐색기에서 ServiceConfiguration.[새 이름].cscfg라는 새 서비스 구성 파일이 Azure 프로젝트에 추가됩니다.

### <a name="to-delete-a-service-configuration"></a>서비스 구성을 삭제하려면
1. 솔루션 탐색기에서 Azure 프로젝트에 대한 바로 가기 메뉴를 열고 **구성 관리**를 선택합니다.
   
    **서비스 구성 관리** 대화 상자가 나타납니다.
2. 서비스 구성을 삭제하려면 **이름** 목록에서 삭제할 구성을 선택한 다음 **제거**를 선택합니다. 이 구성을 삭제할 것인지를 확인하는 대화 상자가 나타납니다.
3. **삭제**를 선택합니다.
   
     솔루션 탐색기에서 서비스 구성 파일은 Azure 프로젝트에서 제거됩니다.

### <a name="to-rename-a-service-configuration"></a>서비스 구성의 이름을 변경하려면
1. 솔루션 탐색기에서 Azure 프로젝트에 대한 바로 가기 메뉴를 열고 **구성 관리**를 선택합니다.
   
    **서비스 구성 관리** 대화 상자가 나타납니다.
2. 서비스 구성의 이름을 변경하려면 **이름** 목록에서 새 서비스 구성을 선택한 다음 **이름 바꾸기**를 선택합니다. **이름** 텍스트 상자에 이 서비스 구성에 사용할 이름을 입력한 다음 **확인**을 선택합니다.
   
    솔루션 탐색기의 Azure 프로젝트에서 서비스 구성 파일의 이름이 변경됩니다.

### <a name="to-change-a-service-configuration"></a>서비스 구성을 변경하려면
* 서비스 구성을 변경하려는 경우, Azure 프로젝트에서 변경하려는 특정 역할에 대 한 바로 가기 메뉴를 연 다음 **속성**을 선택합니다. 자세한 내용은 [방법: Visual Studio와 함께 Azure 클라우드 서비스에 대한 역할 구성](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) 을 참조하세요.

## <a name="make-different-setting-combinations-by-using-profiles"></a>프로필을 사용하여 여러 다른 설정 조합 만들기
프로필을 사용하여 다양한 용도에 맞는 다른 설정 조합으로 **게시 마법사** 를 자동으로 채울 수 있습니다. 예를 들어, 디버깅용으로 하나의 프로필이 있고 릴리스 빌드용으로 다른 프로필이 있을 수 있습니다. 이 경우 **디버그** 프로필은 **IntelliTrace**를 사용하며 **디버그** 구성이 선택되며, **릴리스** 프로필은 **IntelliTrace**를 사용하지 않도록 설정하고 **릴리스** 구성이 선택됩니다. 또한 다른 프로필로 다른 저장소 계정을 사용하여 서비스를 배포할 수도 있습니다.

처음으로 마법사를 실행하는 경우 기본 프로필이 만들어집니다. Visual Studio는 .azurePubXml 확장명이 있는 파일로 프로필을 저장하며, **프로필** 폴더의 Azure 프로젝트에 추가됩니다. 나중에 마법사를 실행할 때 수동으로 다른 프로필을 지정하면 파일이 자동으로 업데이트됩니다. 다음 절차를 실행하기 전에 한 번 이상 이미 클라우드 서비스를 게시했어야 합니다.

### <a name="to-add-a-profile"></a>프로필을 추가하려면
1. Azure 프로젝트의 바로 가기 메뉴를 열고 **게시**를 클릭합니다.
2. 다음 그림과 같이 **대상 프로필** 목록 옆 **프로필 저장** 단추를 선택합니다. 이렇게 하면 프로필이 만들어 집니다.
   
    ![새 프로필 만들기](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. 프로필을 만든 후 **대상 프로필** 목록에서 **<관리...>** 를 선택합니다.
   
    다음 그림과 같이 **프로필 관리** 대화 상자가 나타납니다.
   
    ![프로필 관리 대화 상자](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. **이름** 목록에서 프로필을 선택한 다음 **복사본 만들기**를 선택합니다.
5. **닫기** 단추를 선택합니다.
   
    새 프로필이 대상 프로필 목록에 나타납니다.
6. **대상 프로필** 목록에서 방금 만든 프로필을 선택합니다. 게시 마법사 설정은 선택한 프로필의 선택 항목으로 채워집니다.
7. **이전** 및 **다음** 단추를 선택하여 게시 마법사의 각 페이지를 표시한 다음 이 프로필에 대한 설정을 사용자 지정합니다. 자세한 내용은 [Azure 응용 프로그램 게시 마법사](http://go.microsoft.com/fwlink/p/?LinkID=623085) 를 참조하세요.
8. 설정 사용자 지정을 완료한 후 **다음** 을 선택하여 설정 페이지로 다시 이동합니다. 이 설정을 사용하여 서비스를 게시하거나 프로필 목록 옆 **저장** 을 선택하면 프로필이 저장됩니다.

### <a name="to-rename-or-delete-a-profile"></a>프로필의 이름을 변경하거나 삭제하려면
1. Azure 프로젝트의 바로 가기 메뉴를 열고 **게시**를 클릭합니다.
2. **대상 프로필** 목록에서 **관리**를 선택합니다.
3. **프로필 관리** 대화 상자에서 삭제할 프로필을 선택한 다음 **제거**를 선택합니다.
4. 나타나는 확인 대화 상자에서 **확인**을 선택합니다.
5. **닫기**를 선택합니다.

### <a name="to-change-a-profile"></a>프로필을 변경하려면
1. Azure 프로젝트의 바로 가기 메뉴를 열고 **게시**를 클릭합니다.
2. **대상 프로필** 목록에서 변경할 프로필을 선택합니다.
3. **이전** 및 **다음** 단추를 선택하여 게시 마법사의 각 페이지를 표시한 다음 이 프로필에 대한 설정을 변경합니다. 자세한 내용은 [Azure 응용 프로그램 게시 마법사](http://go.microsoft.com/fwlink/p/?LinkID=623085) 를 참조하세요.
4. 설정 변경을 완료한 후 **다음**을 선택하여 **설정** 페이지로 다시 이동합니다.
5. (선택 사항) **게시** 를 선택하고 새 설정을 사용하여 클라우드 서비스를 게시합니다. 이번에 클라우드 서비스를 게시하지 않고 게시 마법사를 닫으려는 경우, Visual Studio는 프로필에 변경 내용을 저장할 것인지를 묻습니다.

## <a name="next-steps"></a>다음 단계
Visual Studio에서 Azure 프로젝트의 다른 부분 구성에 대해 알아보려면 [Azure 프로젝트 구성](http://go.microsoft.com/fwlink/p/?LinkID=623075)

