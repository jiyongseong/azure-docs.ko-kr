---
title: 포함 파일
description: 포함 파일
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
## <a name="create-a-service-principal"></a>서비스 주체 만들기

컨테이너 레지스트리에 액세스할 수 있는 서비스 주체를 만들려면 다음 스크립트를 사용합니다. `ACR_NAME` 변수를 컨테이너 레지스트리 이름으로 업데이트하고 선택적으로 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 명령의 `--role` 값을 사용하여 다른 권한을 부여합니다. 이 스크립트를 사용하려면 [Azure CLI](/cli/azure/install-azure-cli)가 설치되어 있어야 합니다.

스크립트를 실행한 후 서비스 주체의 **ID**와 **암호**를 기록해 둡니다. 자격 증명이 있으면 컨테이너 레지스트리를 서비스 주체로 인증하도록 응용 프로그램과 서비스를 구성할 수 있습니다.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>기존 서비스 주체 사용

기존 서비스 주체에 레지스트리 액세스 권한을 부여하려면 서비스 주체에 새 역할을 할당해야 합니다. 새 서비스 주체를 만들 때처럼 풀, 푸시 및 풀, 소유자 액세스를 부여할 수 있습니다.

다음 스크립트는 `SERVICE_PRINCIPAL_ID` 변수에 지정한 서비스 주체에게 [az role assignment create][az-role-assignment-create] 명령을 사용하여 *풀* 권한을 부여합니다. 다른 수준의 액세스 권한을 부여하려면 `--role` 값을 조정합니다.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
