---
title: 사용자 지정 개발 응용 프로그램에 대한 특정 필드를 작성하는 방법 | Microsoft Docs
description: 사용자 지정 개발 응용 프로그램을 Azure AD에 등록할 때 특정 필드를 작성하는 방법에 관한 지침
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cd4313efb5d08842ba12ec00e6e5160214800d56
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34055620"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>사용자 지정 개발 응용 프로그램에 대한 특정 필드를 작성하는 방법

이 아티클에서는 [Azure Portal](https://portal.azure.com)에서 응용 프로그램 등록 양식에 사용 가능한 모든 필드에 대해 간략하게 설명합니다.

## <a name="register-a-new-application"></a>새 응용 프로그램 등록

-   새 응용 프로그램을 등록하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

-   왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.

-   **앱 등록**을 선택하고 **추가**를 클릭합니다.

-   그러면 응용 프로그램 등록 양식이 열립니다.

## <a name="fields-in-the-application-registration-form"></a>응용 프로그램 등록 양식의 필드


| 필드            | 설명                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name             | 응용 프로그램 이름입니다. 최소 4자 이상이어야 합니다.                |
| 응용 프로그램 형식 | **웹앱/웹 API**: 웹 응용 프로그램, 웹 API 또는 둘 다를 나타내는 응용 프로그램 
| |**네이티브**: 사용자 장치 또는 컴퓨터에 설치할 수 있는 응용 프로그램           |
| 로그온 URL      | 사용자가 응용 프로그램을 사용하기 위해 로그인하는 URL                                  |

위 필드를 입력하면 응용 프로그램이 Azure Portal에 등록되고 응용 프로그램 페이지로 리디렉션됩니다. 응용 프로그램 창의 **설정** 단추를 누르면 응용 프로그램을 사용자 지정하는 더 많은 필드가 있는 설정 페이지가 열립니다. 다음 표에 설정 페이지의 모든 필드가 설명되어 있습니다. 웹 응용 프로그램을 만들었는지 네이티브 응용 프로그램을 만들었는지 여부에 따라 이러한 필드의 하위 집합만 표시됩니다.

| 필드           | 설명                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 응용 프로그램 UI  | 응용 프로그램을 등록하면 Azure AD에서 응용 프로그램에 응용 프로그램 ID를 할당합니다. 응용 프로그램 ID는 Graph API 같은 리소스에 액세스뿐만 아니라 Azure AD에 인증 요청 시 응용 프로그램을 고유하게 식별하는 데 사용될 수 있습니다.                                                          |
| 앱 ID URI      | 고유한 URI여야 하며 일반적으로 형식은 **https://&lt;tenant\_name&gt;/&lt;application\_name&gt;입니다.** 이 URI는 토큰이 발급되는 리소스를 지정하는 고유한 식별자로서 권한 부여 흐름 중 사용됩니다. 또한 발급된 액세스 토큰의 'aud' 클레임이 됩니다. |
| 새 로고 업로드 | 응용 프로그램에 대한 로고를 업로드하는 데 사용할 수 있습니다. 로고는 .bmp, .jpg 또는 .png 형식이어야 하며 파일 크기는 100KB 미만이어야 합니다. 이미지 크기는 215x215 픽셀이어야 합니다(중앙 이미지 크기는 94x94 픽셀).                                                       |
| 홈페이지 URL   | 응용 프로그램 등록 시 지정된 로그온 URL입니다.                                                                                                                                                                                                                                              |
| 로그아웃 URL      | Single Sign-Out 로그아웃 URL입니다. 사용자가 다른 등록된 응용 프로그램을 사용하여 Azure AD의세션을 지우면 Azure AD에서 이 URL로 로그아웃 요청을 전송합니다.                                                                                                                                       |
| 다중 테넌트  | 이 스위치는 여러 테넌트에서 응용 프로그램을 사용할 수 있는지 여부를 지정합니다. 일반적으로 외부 조직에서 테넌트를 등록하고 조직의 데이터에 대한 액세스 권한을 부여하여 응용 프로그램을 사용할 수 있습니다.                                                                   |
| 회신 URL      | 회신 URL은 Azure AD에서 응용 프로그램이 요청한 토큰을 반환하는 끝점입니다.                                                                                                                                                                                                          |
| 리디렉션 URI   | 네이티브 응용 프로그램의 경우 성공적인 권한 부여 후에 사용자에게 전송되는 URI입니다. Azure AD는 OAuth 2.0 요청에서 응용 프로그램이 제공하는 리디렉션 URI가 포털의 등록된 값 중 하나와 일치하는지 확인합니다.                                                            |
| 구성            | 키를 만들어 사용자 조작 없이 Azure AD에서 보호하는 웹 API에 프로그래밍 방식으로 액세스할 수 있습니다. \*\*키\*\* 페이지에서 키 설명 및 만료 날짜를 입력하고 저장하여 키를 생성합니다. 나중에 액세스하지 못하므로 키를 안전한 곳에 보관하세요.             |

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 관리](manage-apps/what-is-application-management.md)
