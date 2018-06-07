---
title: Azure IoT Central 응용 프로그램 관리 | Microsoft Docs
description: 관리자로서 Azure IoT Central 응용 프로그램을 관리하는 방법을 알아봅니다.
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: b60b9e851a3b6612964e67e7764ad8d43d606b4e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199972"
---
# <a name="how-to-administer-your-application"></a>응용 프로그램을 관리하는 방법

Microsoft Azure IoT Central 응용 프로그램을 만든 후에는 Azure IoT Central 사용자 인터페이스의 **관리** 섹션을 사용하여 응용 프로그램을 관리할 수 있습니다. **관리** 섹션으로 이동하려면 왼쪽 탐색 메뉴에서 **관리**를 선택합니다.

**관리** 섹션에서 다음과 같은 일을 할 수 있습니다.

- 사용자 관리

- 역할 관리

- 대금 청구 정보 보기

- 응용 프로그램 설정 관리

- 평가판 연장

**관리** 섹션에는 다양한 관리 작업의 링크를 제공하는 보조 탐색 메뉴가 있습니다.

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 응용 프로그램에서 **관리자** 역할이 할당되어야 합니다. Azure IoT Central 응용 프로그램을 만드는 사람에게는 자동으로 해당 응용 프로그램의 **관리자** 역할이 할당됩니다. 이 문서의 *사용자 관리* 섹션에서는 다른 사용자에게 **관리자** 역할을 할당하는 방법을 설명합니다.

## <a name="change-application-name"></a>응용 프로그램 이름 변경

응용 프로그램 이름을 변경하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **응용 프로그램 설정** 페이지로 이동합니다.

**응용 프로그램 설정** 페이지에서 **응용 프로그램 이름** 필드에 원하는 이름을 입력한 다음, **저장**을 선택합니다.

## <a name="change-the-application-url"></a>응용 프로그램 URL 변경

응용 프로그램 URL을 변경하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **응용 프로그램 설정** 페이지로 이동합니다.

![응용 프로그램 설정 페이지](media\howto-administer\image0-a.png)

**응용 프로그램 설정** 페이지에서 **URL** 필드에 원하는 URL을 입력한 다음, **저장**을 선택합니다. URL은 200자 이하여야 합니다. 사용할 수 없는 URL인 경우 유효성 검사 오류가 발생합니다.

> [!Note]
> URL을 변경하면 이전 URL을 다른 Azure IoT Central 고객이 사용할 수 있습니다. 이 경우 해당 URL을 더 이상 사용할 수 없습니다. URL을 변경하면 이전 URL이 더 이상 작동하지 않으며 사용자에게 새 URL을 사용하라고 알려야 합니다.

## <a name="change-the-application-image"></a>응용 프로그램 이미지 변경

Azure IoT Central 응용 프로그램에서 이미지를 사용하는 방법에 대한 자세한 내용은 [이미지를 준비하여 Azure IoT Central 응용 프로그램에 업로드](howto-prepare-images.md)를 참조하세요.

## <a name="delete-an-application"></a>응용 프로그램 삭제

응용 프로그램을 삭제하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **응용 프로그램 설정** 페이지로 이동합니다.

**삭제**를 선택합니다.

> [!Note]
> 응용 프로그램을 삭제하면 해당 응용 프로그램과 관련된 모든 데이터가 영구적으로 삭제됩니다. 응용 프로그램을 삭제하려면 응용 프로그램을 만들 때 선택한 Azure 구독의 리소스를 삭제할 수 있는 권한도 필요합니다. 자세한 내용은 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 참조하세요.

## <a name="roles-in-azure-iot-central"></a>Azure IoT Central의 역할

역할을 통해 조직 내에서 다양한 Azure IoT Central 작업을 수행할 수 있는 사람을 제어할 수 있습니다. Azure IoT Central에서는 응용 프로그램 사용자에게 세 가지 역할을 할당할 수 있습니다. 역할은 각 응용 프로그램을 통해 할당됩니다. 동일한 사용자가 서로 다른 응용 프로그램에서 여러 역할을 가질 수 있습니다. 동일한 사용자를 응용 프로그램 내의 여러 역할에 할당할 수 있습니다.

### <a name="administrator"></a>관리자

**관리자** 역할의 사용자는 Azure IoT Central 응용 프로그램의 모든 기능에 액세스할 수 있습니다.

응용 프로그램을 만드는 사용자에게는 자동으로 **관리자** 역할이 할당됩니다. **관리자** 역할의 사용자가 항상 1명 이상 있어야 합니다.

### <a name="application-builder"></a>응용 프로그램 작성자

**응용 프로그램 작성자** 역할의 사용자는 Azure IoT Central 응용 프로그램에서 응용 프로그램 관리를 제외한 모든 일을 할 수 있습니다.

### <a name="application-operator"></a>응용 프로그램 운영자

**응용 프로그램 운영자** 역할의 사용자는 **응용 프로그램 작성자** 페이지에 액세스할 수 없습니다. 이들은 응용 프로그램을 관리할 수 없습니다.

## <a name="manage-users"></a>사용자 관리

응용 프로그램 관리자는 응용 프로그램의 역할에 사용자를 할당할 수 있습니다.

### <a name="add-users"></a>사용자 추가

모든 사용자는 Azure IoT Central 응용 프로그램에 로그인하고 액세스하려면 사용자 계정이 있어야 합니다. Azure IoT Central에서 지원되는 계정은 MSA(Microsoft 계정) 및 AAD(Azure Active Directory) 계정입니다. Azure Active Directory 그룹은 현재 Azure IoT Central에서 지원되지 않습니다.

자세한 내용은 [Microsoft 계정 도움말](https://support.microsoft.com/products/microsoft-account?category=manage-account) 및 [빠른 시작: Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조하세요.

1. Azure IoT Central 응용 프로그램에 사용자 계정을 추가하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **사용자** 페이지로 이동합니다.

    ![사용자 목록](media\howto-administer\image1.png)

1. **사용자** 페이지에서 **+ 사용자 추가**를 선택하여 사용자를 추가합니다.

    ![사용자 추가](media\howto-administer\image2.png)

1. Azure IoT Central 응용 프로그램에 사용자를 추가할 때 **역할** 드롭다운에서 사용자의 역할을 선택합니다. 역할에 대한 자세한 정보는 이 문서의 *Azure IoT Central의 역할* 섹션을 참조하세요.

    ![역할 선택](media\howto-administer\image3.png)

    > [!NOTE]
    >  사용자를 대량으로 추가하려면 세미콜론을 구분 기호로 사용하여 추가할 모든 사용자의 사용자 ID를 입력합니다. **역할** 드롭다운에서 역할을 선택하고 **저장**을 선택합니다.

1. 사용자를 추가하면 **사용자** 페이지에 해당 사용자에 대한 항목이 표시됩니다.

    ![사용자 목록](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>사용자에게 할당된 역할 편집

한 번 할당된 역할은 변경할 수 없습니다. 사용자에게 할당된 역할을 변경하려면 사용자를 삭제하고 다시 추가한 후 다른 역할을 할당해야 합니다.

### <a name="delete-users"></a>사용자 삭제

사용자를 삭제하려면 **사용자** 페이지에서 확인란을 하나 이상 선택한 후 **삭제**를 선택합니다.

## <a name="view-your-bill"></a>청구서 보기

청구서를 보려면 **관리** 섹션의 **청구** 페이지로 이동하여 **청구**를 선택합니다. 새 탭에서 Azure 청구 페이지가 열리고 각 Azure IoT Central 응용 프로그램에 대한 청구서를 볼 수 있습니다.

## <a name="convert-your-trial-to-a-paid-application"></a>평가판을 유료 응용 프로그램으로 변환

IoT Central을 평가한 후 평가판을 유료 응용 프로그램으로 변환할 수 있습니다. 이 셀프 서비스 프로세스를 완료하려면 다음 단계를 수행합니다.

1. 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **청구** 페이지로 이동합니다. 평가판을 연장하지 않은 경우 다음과 같은 페이지가 표시됩니다.

    ![평가판 상태](media/howto-administer/freetrial.png)

2. **유료 응용 프로그램으로 변환**을 클릭합니다. 평가판을 연장하지 않은 경우 다음과 같은 팝업이 표시됩니다.

    팝업에서 적절한 Azure Active Directory 테넌트를 선택한 다음, IoT Central 응용 프로그램에 사용하려는 Azure 구독을 선택합니다.

    ![평가판 연장](media/howto-administer/extend.png)

3. **변환**을 클릭하면 평가판이 유료 응용 프로그램으로 변환되고 대금 청구가 시작됩니다.

## <a name="extend-your-free-trial"></a>평가판 연장

기본적으로 모든 평가판은 7일 동안 사용할 수 있습니다. 평가판 기간을 30일로 연장하려면 다음 단계를 수행합니다.

1. 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **청구** 페이지로 이동합니다.

1. **평가판 연장**을 클릭합니다. 팝업에서 적절한 Azure Active Directory 테넌트를 선택한 다음, IoT Central 응용 프로그램에 사용할 Azure 구독을 선택합니다.

1. **연장**을 클릭합니다. 이제 평가판이 30일 동안 유효합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램을 관리하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [장치 템플릿 설정](howto-set-up-template.md)