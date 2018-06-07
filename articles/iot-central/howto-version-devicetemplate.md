---
title: Azure IoT Central 앱의 장치 템플릿 버전 관리 이해 | Microsoft Docs
description: 새 버전을 만들어 라이브 연결된 장치에 영향을 주지 않고 장치 템플릿을 반복합니다.
services: iot-central
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 3a399b6b54f8da9c48b4bdfe7e98a527262bb174
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201094"
---
# <a name="create-a-new-device-template-version"></a>새 장치 템플릿 버전 만들기

Microsoft Azure IoT Central을 사용하여 IoT 응용 프로그램을 신속하게 개발할 수 있습니다. 측정값, 설정 또는 속성을 추가하고 편집하고 삭제하여 장치 템플릿 디자인을 신속하게 반복할 수 있습니다. 이러한 변경 내용 중 일부는 현재 연결된 장치를 방해할 수 있습니다. Azure IoT Central은 이처럼 방해가 되는 변경 내용을 식별하여 장치에 업데이트를 안전하게 배포하는 방법을 제공합니다.

장치 템플릿을 만들 때 버전 번호가 있습니다. 기본적으로 버전 번호는 1.0.0입니다. 장치 템플릿을 편집했는데 그 변경 내용이 라이브 연결된 장치에 영향을 미칠 수 있는 경우 Azure IoT Central은 새 장치 템플릿 버전을 생성하라고 요청합니다.

> [!NOTE]
> 장치 템플릿을 만드는 방법에 대한 자세한 내용은 [장치 템플릿 설정](howto-set-up-template.md)을 참조하세요.

## <a name="changes-that-prompt-a-version-change"></a>버전 변경을 요청하는 변경 내용

일반적으로 장치 템플릿의 설정 또는 속성을 변경하면 버전을 변경하라는 메시지가 표시됩니다.

> [!NOTE]
> 연결된 장치가 없거나 장치가 하나만 연결된 경우 장치 템플릿을 변경해도 새 버전을 만들라는 메시지가 표시되지 않습니다.

다음 목록은 새 버전이 필요할 수 있는 사용자 작업을 설명합니다.

* 속성(필수)
    * 필수 속성 추가 또는 삭제
    * 속성의 필드 이름 변경, 장치에서 메시지를 보내기 위해 사용되는 필드 이름.
*  속성(선택 사항)
    * 선택적 속성 삭제
    * 속성의 필드 이름 변경, 장치에서 메시지를 보내기 위해 사용되는 필드 이름.
    * 선택적 속성을 필수 속성으로 변경
*  설정
    * 설정 추가 또는 삭제
    * 설정의 필드 이름 변경, 장치에서 메시지를 보내고 받기 위해 사용되는 필드 이름.

## <a name="what-happens-on-version-change"></a>버전 변경은 어떻게 되나요?

버전이 변경되면 규칙 및 장치 대시보드는 어떻게 되나요?

**규칙**은 속성에 종속된 조건을 포함할 수 있습니다. 이러한 속성 중 하나 이상을 제거하면 새 장치 템플릿 버전에서 규칙이 손상될 수 있습니다. 이러한 특정 규칙으로 이동한 후 조건을 업데이트하여 규칙을 수정할 수 있습니다. 이전 버전에 대한 규칙은 영향을 받지 않고 작동합니다.

**장치 대시보드**는 여러 가지 타일 형식을 포함할 수 있습니다. 타일 중 일부는 설정 및 속성을 포함할 수 있습니다. 타일에 사용되는 속성 또는 설정을 제거하면 타일이 완전히 또는 부분적으로 손상됩니다. 타일로 이동한 후 타일을 제거하거나 타일의 콘텐츠를 업데이트하여 문제를 해결할 수 있습니다.

## <a name="migrate-a-device-across-device-template-versions"></a>장치 템플릿 버전 간에 장치 마이그레이션

여러 버전의 장치 템플릿을 만들 수 있습니다. 시간이 지나면 이러한 장치 템플릿을 사용하는 연결된 장치가 여러 개 생깁니다. 한 버전의 장치 템플릿에서 다른 버전으로 장치를 마이그레이션할 수 있습니다. 다음 단계는 장치를 마이그레이션하는 방법을 설명합니다.

1. **Explorer** 페이지로 이동합니다.
1. 다른 버전으로 마이그레이션할 장치를 선택합니다.
1. **장치 마이그레이션**을 선택합니다.
1. 장치를 마이그레이션할 버전 번호를 선택하고 **마이그레이션**을 선택합니다.

![장치를 마이그레이션하는 방법](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 장치 템플릿 버전을 사용하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](howto-create-telemetry-rules.md)