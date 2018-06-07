---
title: "Azure App Service 배포 자격 증명 | Microsoft Docs"
description: "Azure App Service 배포 자격 증명 사용 방법을 알아봅니다."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service의 배포 자격 증명 구성
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)는 [로컬 Git 배포](app-service-deploy-local-git.md) 및 [FTP/S 배포](app-service-deploy-ftp.md)를 위해 두 가지 유형의 자격 증명을 지원합니다. 이들은 Azure Active Directory 자격 증명과 동일하지 않습니다.

* **사용자 수준 자격 증명** - 전체 Azure 계정에 대한 자격 증명 집합입니다. 모든 구독에서 Azure 계정에 액세스할 수 있는 권한이 있는 모든 앱의 App Service에 배포하는 데 사용할 수 있습니다. 이는 **App Services** > **&lt;app_name>** > **배포 자격 증명**에서 구성하는 기본 자격 증명 집합이며, 포털 GUI에서 볼 수 있는 기본 집합이기도 입니다(예: **개요** 및 **속성**의 [리소스 페이지](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > RBAC(역할 기반 Access Control) 또는 공동 관리자 권한을 통해 Azure 리소스에 대한 액세스 권한을 위임하는 경우 앱에 대한 액세스 권한을 받은 각 Azure 사용자는 액세스 권한이 취소될 때까지 개인 사용자 수준 자격 증명을 사용할 수 있습니다. 이러한 배포 자격 증명은 다른 Azure 사용자와 공유하지 말아야 합니다.
    >
    >

* **앱 수준 자격 증명** - 각 앱마다 하나씩의 자격 증명 집합입니다. 해당 앱에만 배포하는 데 사용할 수 있습니다. 각 앱의 자격 증명은 앱을 만들 때 자동으로 만들어지며 앱의 게시 프로필에 있습니다. 자격 증명을 수동으로 구성할 수는 없지만 언제든지 앱에 대해 다시 설정할 수 있습니다.

    > [!NOTE]
    > RBAC(역할 기반 액세스 제어)를 통해 이러한 자격 증명에 대한 액세스를 제공하려면 웹앱의 참가자 또는 더 높은 권한을 부여해야 합니다. 독자는 게시할 수 없으므로 해당 자격 증명에 액세스할 수 없습니다.
    >
    >

## <a name="userscope"></a>사용자 수준 자격 증명 설정 및 다시 설정

모든 앱의 [리소스 페이지](../azure-resource-manager/resource-group-portal.md#manage-resources)에서 사용자 수준 자격 증명을 구성할 수 있습니다. 이러한 자격 증명을 구성하는 앱에 관계 없이 Azure 계정의 모든 앱과 모든 구독에 적용됩니다. 

사용자 수준 자격 증명을 구성하려면

1. [Azure Portal](https://portal.azure.com)에서 App Service> **&lt;any_app>** > **배포 자격 증명**을 차례로 클릭합니다.

    > [!NOTE]
    > 포털에서 배포 자격 증명 페이지에 액세스하려면 앱이 하나 이상 있어야 합니다. 그러나 [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set)를 사용하면 기존 앱 없이 사용자 수준 자격 증명을 구성할 수 있습니다.

2. 사용자 이름과 암호를 구성한 다음 **저장**을 클릭합니다.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

배포 자격 증명을 설정하면 앱의 **개요**에서 *Git* 배포 사용자 이름을 찾을 수 있습니다.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

그런 다음 앱의 **속성**에서 *FTP* 배포 사용자 이름을 찾을 수 있습니다.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure에서는 사용자 수준 배포 암호가 표시되지 않습니다. 암호를 잊어버리면 암호를 검색할 수 없습니다. 그러나 이 섹션의 단계를 수행하면 자격 증명을 다시 설정할 수 있습니다.
>
>  

## <a name="appscope"></a>앱 수준 자격 증명 정보 가져오기 및 다시 설정
App Service의 각 앱에 대한 앱 수준 자격 증명은 XML 게시 프로필에 저장되어 있습니다.

앱 수준 자격 증명을 가져오려면

1. [Azure Portal](https://portal.azure.com)에서 App Service> **&lt;any_app>** > **개요**를 차례로 클릭합니다.

2. **...추가** > **게시 프로필 가져오기**를 차례로 클릭하고 .PublishSettings 파일 다운로드를 시작합니다.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. .PublishSettings 파일을 열고 `publishMethod="FTP"` 속성의 `<publishProfile>` 태그를 찾습니다. 그런 다음 `userName` 및 `password` 속성을 가져옵니다.
이러한 속성이 앱 수준 자격 증명입니다.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    사용자 수준 자격 증명과 마찬가지로 FTP 배포 사용자 이름은 `<app_name>\<username>` 형식이며, Git 배포 사용자 이름은 선행하는 `<app_name>\`이 없이 `<username>`일뿐 입니다.

앱 수준 자격 증명을 다시 설정하려면

1. [Azure Portal](https://portal.azure.com)에서 App Service> **&lt;any_app>** > **개요**를 차례로 클릭합니다.

2. **...추가** > **게시 프로필 다시 설정**을 차례로 클릭합니다. **예**를 클릭하여 다시 설정을 확인합니다.

    다시 설정 작업은 앞에서 다운로드한 .PublishSettings 파일을 무효화합니다.

## <a name="next-steps"></a>다음 단계

이러한 자격 증명을 사용하여 [로컬 Git](app-service-deploy-local-git.md)에서 또는 [FTP/S](app-service-deploy-ftp.md)를 통해 앱을 배포하는 방법을 알아봅니다.
