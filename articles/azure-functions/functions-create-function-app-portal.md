---
title: Azure Portal에서 함수 앱 만들기 | Microsoft Docs
description: 포털의 Azure App Service에서 새 함수 앱을 만듭니다.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: ''
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 4716d29a7b8c0830e2311398f033f8358ef24529
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Azure Portal에서 함수 앱 만들기

Azure Function 앱에서는 Azure App Service 인프라를 사용합니다. 이 항목에서는 Azure Portal에서 함수 앱을 만드는 방법을 보여 줍니다. 함수 앱은 개별 함수의 실행을 호스팅하는 컨테이너입니다. App Service 호스팅 계획에서 함수 앱을 만들면 함수 앱에서는 모든 기능 및 App Service를 사용하여 App Service의 모든 기능을 사용할 수 있습니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

함수 앱을 만들 때 문자, 숫자 및 하이픈만 포함할 수 있는 유효한 **앱 이름**을 제공해야 합니다. 밑줄(**_**)은 허용되는 문자가 아닙니다.

Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 

함수 앱을 만든 후에 하나 이상의 서로 다른 언어로 개별 함수를 만들 수 있습니다. [포털을 사용하여](functions-create-first-azure-function.md#create-function), [연속 배포](functions-continuous-deployment.md) 또는 [FTP를 사용하여 업로드](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)하여 함수를 만듭니다.

## <a name="service-plans"></a>서비스 계획

Azure Functions에는 소비 계획 및 App Service 계획이라는 두 가지 서비스 계획이 있습니다. 소비 계획은 코드가 실행 중일 때 계산 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음 코드가 실행되지 않을 때 축소합니다. App Service 계획은 App Service의 모든 기능이 함수 앱 액세스 권한을 제공합니다. 함수 앱이 만들어지면 서비스 계획을 선택해야 하며 현재는 변경할 수 없습니다. 자세한 내용은 [Azure Functions 호스팅 계획 선택](functions-scale.md)을 참조하세요.

App Service 계획에서 JavaScript 함수를 실행하려는 경우 코어 수가 더 작은 계획을 선택해야 합니다. 자세한 내용은 [함수에 대한 JavaScript 참조](functions-reference-node.md#choose-single-vcpu-app-service-plans)를 참조하세요.

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

App Service에서 함수 앱을 만들 때 Blob, 큐 및 Table Storage를 지원하는 범용 Azure Storage 계정을 만들거나 연결해야 합니다. 내부적으로 함수는 트리거 관리 및 함수 실행 로깅 등의 작업을 위해 Storage를 사용합니다. Blob 전용 저장소 계정, Azure Premium Storage 및 ZRS 복제를 포함한 범용 저장소 계정과 같은 일부 저장소 계정은 큐 및 테이블을 지원하지 않습니다. 이러한 계정은 함수 앱을 만들 때 Storage 계정 블레이드에서 필터링됩니다.

>[!NOTE]
>소비 호스팅 계획을 사용할 경우 함수 코드 및 바인딩 구성 파일은 기본 저장소 계정의 Azure File Storage에 저장됩니다. 기본 저장소 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

저장소 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#azure-storage-services)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]



