---
title: Azure IoT Central 응용 프로그램 만들기 | Microsoft Docs
description: 관리자로서 Azure IoT Central 응용 프로그램을 만드는 방법을 알아봅니다.
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 83879c6b81985f61b9fcff665e9f764c1346592e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200812"
---
# <a name="create-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램 만들기

[응용 프로그램 만들기](https://apps.microsoftiotcentral.com/create) 페이지에서 Microsoft Azure IoT Central 응용 프로그램을 만들 수 있습니다. Azure IoT Central 응용 프로그램을 만들려면 이 페이지의 모든 필드를 완료한 다음, **만들기**를 선택해야 합니다. 이 문서에는 각 필드에 대한 자세한 내용이 포함되어 있습니다.

![응용 프로그램 만들기 페이지](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>결제 계획

평가판 또는 유료 응용 프로그램을 만들 수 있습니다. 이 페이지에서 평가판 및 유료 응용 프로그램에 대해 자세히 알아보세요.

## <a name="application-name"></a>응용 프로그램 이름

응용 프로그램 이름은 **응용 프로그램 관리자** 페이지 그리고 각 Azure IoT Central 응용 프로그램 내에서 표시됩니다. Azure IoT Central 응용 프로그램의 이름으로 원하는 이름을 선택할 수 있습니다. 본인에게 그리고 조직 내 다른 사람들에게 의미 있는 이름을 선택하면 됩니다.

## <a name="application-url"></a>응용 프로그램 URL

응용 프로그램 URL은 응용 프로그램의 링크입니다. 브라우저에서 URL의 책갈피를 저장하거나 다른 사람들과 공유할 수 있습니다.

응용 프로그램의 이름을 입력하면 응용 프로그램 URL이 자동으로 생성됩니다. 원한다면 응용 프로그램의 다른 URL을 선택해도 됩니다. 각 Azure IoT URL URL은 고유해야 합니다. 선택한 URL이 이미 사용 중인 경우 오류 메시지가 표시됩니다.

## <a name="directory"></a>디렉터리

유료 응용 프로그램만 해당합니다.

Azure IoT Central 응용 프로그램을 만들 Azure Active Directory 테넌트를 선택합니다. Azure Active Directory 테넌트에는 사용자 ID, 자격 증명 및 기타 조직 정보가 포함됩니다. 단일 Azure Active Directory 테넌트에 여러 Azure 구독을 연결할 수 있습니다.

Azure Active Directory 테넌트가 없는 경우 Azure 구독을 만들면 자동으로 하나가 생성됩니다.

자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)를 참조하세요.

## <a name="azure-subscription"></a>Azure 구독

Azure 구독을 사용하여 Azure 서비스 인스턴스를 만들 수 있습니다. Azure IoT Central은 액세스 권한이 있는 모든 Azure 구독을 자동으로 찾아서 **응용 프로그램 만들기** 페이지의 드롭다운에 표시합니다. 새 Azure IoT Central 응용 프로그램을 만들 Azure 구독을 선택하세요.

Azure 구독이 아직 없는 경우 이 페이지에서 만들 수 있습니다. Azure 구독을 만든 후 다시 **응용 프로그램 만들기** 페이지로 돌아갑니다. **Azure 구독** 드롭다운에 새 구독이 표시됩니다.

자세한 내용은 [Azure 구독](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)을 참조하세요.

## <a name="region"></a>지역

유료 응용 프로그램만 해당합니다.

Azure IoT Central 응용 프로그램을 만들 지역을 선택합니다. 일반적으로 최적의 성능을 얻으려면 장치와 물리적으로 가장 가까운 지역을 선택해야 합니다.

자세한 내용은 [Azure 지역](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions)을 참조하세요.

Azure IoT Central을 사용할 수 있는 지역은 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 페이지에서 확인할 수 있습니다.

> [!Note]
> 일단 지역을 선택하면 응용 프로그램을 다른 지역으로 이동할 수 없습니다.

## <a name="application-template"></a>응용 프로그램 템플릿

새 Azure IoT Central 응용 프로그램에 사용 가능한 응용 프로그램 템플릿 중 하나를 선택할 수 있습니다. 응용 프로그램 템플릿에는 시작하는 데 도움이 되는 장치 템플릿이나 대시보드 같은 미리 정의된 항목이 포함될 수 있습니다.

| 응용 프로그램 템플릿 | 설명 |
| -------------------- | ----------- |
| 사용자 지정 응용 프로그램   | 사용자 고유의 장치 템플릿 및 장치로 채울 빈 응용 프로그램을 만듭니다. |
| 샘플 Contoso       | 간단한 연결된 장치를 만들 수 있는 장치 템플릿이 포함된 응용 프로그램을 만듭니다. 이 템플릿을 사용하여 Azure IoT Central 탐색을 시작하세요. |
| 샘플 Devkits       | MXChip 또는 Raspberry Pi 장치를 연결할 수 있는 장치 템플릿을 사용하여 응용 프로그램을 만듭니다. 다음 장치 중 하나에서 코드를 실험하는 장치 개발자인 경우 이 템플릿을 사용하세요. |

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [응용 프로그램 관리](howto-administer.md)