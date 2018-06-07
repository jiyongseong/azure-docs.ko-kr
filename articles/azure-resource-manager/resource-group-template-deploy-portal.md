---
title: Azure Portal을 사용하여 Azure 리소스 배포 | Microsoft Docs
description: Azure 포털과 Azure 리소스 관리를 사용하여 리소스를 배포합니다.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: c2232e1ce92eb6198b22bad22b8c3e10bab6bf12
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>리소스 관리자 템플릿과 Azure 포털로 리소스 배포

이 항목에서는 [Azure Resource Manager](resource-group-overview.md)에서 [Azure 포털](https://portal.azure.com)을 사용하여 Azure 리소스를 배포하는 방법을 보여 줍니다. 리소스 관리에 관한 자세한 내용은 [포털을 통한 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.

## <a name="create-resource-group"></a>리소스 그룹 만들기

1. 빈 리소스 그룹을 만들려면 **리소스 그룹**을 선택합니다.

   ![리소스 그룹 선택](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. 리소스 그룹에서 **추가**를 선택합니다.

   ![리소스 그룹 추가](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. 이름과 위치를 지정하고 필요한 경우 구독을 선택합니다. 그룹 리소스가 리소스에 대한 메타데이터를 저장하기 때문에 리소스 그룹에 대한 위치를 제공해야 합니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 상주할 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.

   ![그룹값 설정](./media/resource-group-template-deploy-portal/set-group-properties.png)

   속성 설정을 완료한 후 **만들기**를 선택합니다.

1. 새 리소스 그룹을 보려면 **새로 고침**을 선택합니다.

   ![리소스 그룹 새로 고침](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Marketplace에서 리소스 배포

리소스 그룹을 만들면 Marketplace에서 리소스 그룹으로 리소스를 배포할 수 있습니다. Marketplace는 일반적인 시나리오에 사전 정의된 솔루션을 제공합니다.

1. 배포를 시작하려면 **리소스 만들기**를 선택합니다.

   ![새 리소스](./media/resource-group-template-deploy-portal/new-resources.png)

1. 배포할 리소스의 유형을 찾습니다.

   ![리소스 종류 선택](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. 배포하려는 특정 솔루션이 보이지 않는다면 Marketplace를 검색할 수 있습니다. 예를 들어 Wordpress 솔루션을 찾으려면 **Wordpress**라고 입력을 시작하고 원하는 옵션을 선택합니다.

   ![Marketplace 검색](./media/resource-group-template-deploy-portal/search-resource.png)

1. 선택한 리소스 유형에 따라 배포하기 전에 관련 속성의 컬렉션을 설정합니다. 모든 유형에 대해 대상 리소스 그룹을 선택해야 합니다. 다음 이미지는 웹앱을 만들고 앞에서 만든 리소스 그룹에 배포하는 방법을 보여 줍니다.

   ![리소스 그룹 만들기](./media/resource-group-template-deploy-portal/select-existing-group.png)

   또는 리소스를 배포할 때 리소스 그룹을 만들도록 결정할 수 있습니다. **새로 만들기** 를 선택하고 리소스 그룹에 이름을 지정합니다.

   ![새 리소스 그룹 만들기](./media/resource-group-template-deploy-portal/select-new-group.png)

1. 배포가 시작됩니다. 배포에 몇 분 정도 걸릴 수 있습니다. 배포가 완료되면 알림이 표시됩니다.

   ![알림 보기](./media/resource-group-template-deploy-portal/view-notification.png)

1. 리소스를 배포한 후에 **추가**를 선택하여 더 많은 리소스를 리소스 그룹에 추가할 수 있습니다.

   ![리소스 추가](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>사용자 지정 템플릿에서 배포

배포를 실행하려고 하지만 Marketplace에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿을 만드는 방법에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조하세요.

1. Portal을 통해 사용자 지정 템플릿을 배포하려면 **리소스 만들기**를 선택하고 옵션에서 선택할 수 있을 때까지 **템플릿 배포**를 검색합니다.

   ![템플릿 배포 검색](./media/resource-group-template-deploy-portal/search-template.png)

1. **만들기**를 선택합니다.

   ![만들기 선택](./media/resource-group-template-deploy-portal/show-template-option.png)

1. 템플릿을 만들기 위한 몇 가지 옵션이 표시됩니다. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

   ![옵션 보기](./media/resource-group-template-deploy-portal/see-options.png)

1. 사용자 지정할 수 있는 빈 템플릿이 표시됩니다.

   ![템플릿 만들기](./media/resource-group-template-deploy-portal/blank-template.png)

1. JSON 구문을 수동으로 편집하거나 [빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/)에서 미리 작성된 템플릿을 선택할 수 있습니다. 그러나 이 문서에서는 **리소스 추가** 옵션을 사용합니다.

   ![템플릿 편집](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. **저장소 계정**을 선택하고 이름을 입력합니다. 값 입력을 완료한 후 **확인**을 선택합니다.

   ![저장소 계정 선택](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. 편집기에서 리소스 종류에 해당하는 JSON이 자동으로 추가됩니다. 이 JSON에는 저장소 계정 유형을 정의하기 위한 매개 변수가 포함되어 있습니다. **저장**을 선택합니다.

   ![템플릿 표시](./media/resource-group-template-deploy-portal/show-json.png)

1. 이제 템플릿에 정의되어 있는 리소스를 배포할 수 있습니다. 배포하려면 약관에 동의하고 **구매**를 선택합니다.

   ![템플릿 배포](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>계정에 저장된 템플릿에서 리소스 배포

포털을 사용하면 Azure 계정에 템플릿을 저장했다가 나중에 다시 배포할 수 있습니다. 저장된 템플릿에 관한 자세한 내용은 [Azure 포털에서 개인 템플릿 시작하기](../marketplace-consumer/mytemplates-getstarted.md)를 참조하세요.

1. 저장된 템플릿을 찾으려면 **추가 서비스**를 선택합니다.

   ![더 많은 서비스](./media/resource-group-template-deploy-portal/more-services.png)

1. **템플릿**을 검색하고 해당 옵션을 선택합니다.

   ![템플릿 검색](./media/resource-group-template-deploy-portal/find-templates.png)

1. 계정에 저장된 템플릿 목록에서 작업하고자 하는 템플릿을 선택합니다.

   ![저장된 템플릿](./media/resource-group-template-deploy-portal/saved-templates.png)

1. **배포** 를 선택하고 저장된 템플릿을 다시 배포합니다.

   ![저장된 템플릿 배포](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>다음 단계
* 감사 로그를 보려면 [Resource Manager로 작업 감사](resource-group-audit.md)를 참조하세요.
* 배포 오류를 해결하려면 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.
* 배포 또는 리소스 그룹에서 템플릿을 검색하려면 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
