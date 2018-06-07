---
title: 'Azure Active Directory B2C: 위협 관리 | Microsoft Docs'
description: Azure Active Directory B2C에서 서비스 거부 공격 및 암호 공격에 대한 검색 및 완화 기법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.openlocfilehash: 5ab699b0dccd772ec905699d94dedaca0eefcdad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: 위협 관리

위협 관리에는 시스템 및 네트워크에 대한 공격으로부터 보호를 계획하는 작업이 포함됩니다. 서비스 거부 공격은 의도한 사용자가 리소스를 사용할 수 없게 만듭니다. 암호 공격으로 인해 리소스에 대한 무단 액세스가 발생할 수 있습니다. Azure AD B2C(Azure Active Directory B2C)에는 여러 가지 방법으로 이러한 위협으로부터 데이터를 보호할 수 있는 기본 제공 기능이 있습니다.

## <a name="denial-of-service-attacks"></a>서비스 거부 공격

Azure AD B2C는 SYN 쿠키, 속도 및 연결 제한과 같은 검색 및 완화 기술을 사용하여 서비스 거부 공격으로부터 기본 리소스를 보호합니다.

## <a name="password-attacks"></a>암호 공격

또한 Azure AD B2C에는 암호 공격에 대한 완화 기술도 제공되어 있습니다. 완화에는 무차별 암호 대입 공격 및 사전 암호 공격이 포함됩니다. 사용자가 설정한 암호는 적절한 복잡성을 유지해야 합니다. 다양한 신호를 사용하여 Azure AD B2C는 요청의 무결성을 분석합니다. Azure AD B2C는 의도한 사용자와 해커 및 봇네트 간을 지능적으로 구분하도록 설계되었습니다. Azure AD B2C는 공격 가능성이 있을 때 입력된 암호에 따라 계정을 잠그는 정교한 전략을 제공합니다.

자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/trustcenter/security/threatmanagement)를 참조하세요.
