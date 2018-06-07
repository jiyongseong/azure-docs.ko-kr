---
title: "Office 365 서비스에 대한 Azure Active Directory 조건부 액세스 장치 정책 | Microsoft Docs"
description: "조건부 액세스 장치 정책을 프로비전하여 회사 리소스를 더 안전하게 유지하면서 사용자 규정 준수와 서비스 액세스를 유지하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2b586aee9559b62d4cc8b21ab88ab193e61e7c14
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Office 365 서비스에 대한 Active Directory 조건부 액세스 장치 정책

조건부 액세스에서는 여러 부분이 작동해야 합니다. 여기에는 다른 요인들 중에서도 다단계 인증 사용자, 인증된 장치 및 준수 장치가 포함됩니다. 이 문서에서는 주로 조직에서 Office 365 서비스에 대한 액세스를 제어하는 데 사용할 수 있는 장치 기반 조건에 중점을 둡니다. 

회사 사용자는 개인 장치에서 회사 또는 학교의 Exchange 및 SharePoint Online과 같은 Office 365 서비스에 액세스하려고 합니다. 액세스가 안전하기를 원합니다. 조건부 액세스 장치 정책을 프로비전하여 회사 리소스를 더 안전하게 유지하면서 준수 장치를 사용하는 사용자의 서비스에 대한 액세스 권한을 부여할 수 있습니다. Microsoft Intune 조건부 액세스 포털에서 Office 365에 대한 조건부 액세스 정책을 설정할 수 있습니다.

Azure AD(Azure Active Directory)는 조건부 액세스 정책을 적용하여 Office 365 서비스에 대한 액세스를 보호합니다. 비준수 장치 사용자가 Office 365 서비스에 액세스할 수 없도록 차단하는 조건부 액세스 정책을 만들 수 있습니다. 사용자는 회사의 장치 정책을 준수해야 서비스에 대한 액세스 권한을 부여받을 수 있습니다. 또는 사용자가 Office 365 서비스에 대한 액세스 권한을 얻기 위해 장치를 등록해야 하는 정책을 만들 수 있습니다. 정책은 조직의 모든 사용자에게 적용되거나 몇 가지 대상 그룹으로 제한될 수 있습니다. 시간이 지남에 따라 더 많은 대상 그룹을 정책에 추가할 수 있습니다.

장치 정책을 적용하기 위한 필수 조건은 사용자가 Azure AD 장치 등록 서비스에 장치를 등록해야 한다는 것입니다. Azure AD 장치 등록 서비스에 등록된 장치에 대해 다단계 인증을 설정하도록 선택할 수 있습니다. Azure Active Directory 장치 등록 서비스에는 다단계 인증을 사용하는 것이 좋습니다. 다단계 인증이 설정되면 Azure AD 장치 등록 서비스에 장치를 등록한 사용자는 2단계 인증으로 어려움을 겪습니다.

## <a name="how-does-a-conditional-access-policy-work"></a>조건부 액세스 정책의 작동 방식

사용자가 지원되는 장치 플랫폼에서 Office 365 서비스에 대한 액세스를 요청하면 Azure AD에서 사용자와 장치를 인증합니다. 사용자가 서비스에 설정된 정책을 준수하는 경우에만 Azure AD에서 서비스에 대한 액세스 권한을 부여합니다. 장치를 등록하지 않은 사용자에게는 장치를 등록하고 회사의 Office 365 서비스에 액세스하기 위한 규정을 준수하는 방법에 대한 지침이 제공됩니다. iOS 및 Android 장치 사용자는 Intune 회사 포털 응용 프로그램을 사용하여 장치를 등록해야 합니다. 사용자가 장치를 등록하면 Azure AD에 해당 장치가 등록되고 장치 관리 및 규정 준수에 대해 등록됩니다. Office 365 서비스의 모바일 장치 관리를 위해 Microsoft Intune에서 Azure AD 장치 등록 서비스를 사용해야 합니다. 장치 정책이 적용될 때 사용자가 Office 365 서비스에 액세스하려면 장치 등록이 필요합니다.

사용자가 성공적으로 등록한 장치는 신뢰할 수 있게 됩니다. Azure AD는 인증된 사용자에게 회사 응용 프로그램에 대한 Single Sign-On 액세스를 제공합니다. Azure AD는 사용자가 처음으로 액세스를 요청할 때뿐만 아니라 사용자가 액세스 요청을 갱신할 때마다도 서비스에 대한 액세스 권한을 부여하는 조건부 액세스 정책을 적용합니다. 로그인 자격 증명이 변경되거나, 장치를 분실하거나 도난당하거나, 갱신 요청 시 정책 조건이 충족되지 않은 경우 서비스에 대한 사용자 액세스는 거부됩니다.

## <a name="deployment-considerations"></a>배포 고려 사항

Azure AD 장치 등록 서비스를 사용하여 장치를 등록해야 합니다.

온-프레미스 사용자가 인증될 때 AD FS(Active Directory Federation Service) 버전 1.0 이상이 필요합니다. ID 공급자가 다단계 인증을 수행할 수 없는 경우 작업 공간 연결에 대한 다단계 인증이 실패합니다. 예를 들어 AD FS 2.0에서는 다단계 인증을 사용할 수 없습니다. 온-프레미스 AD FS가 다단계 인증과 함께 작동하는지와 Azure AD 장치 등록 서비스에 대한 다단계 인증을 설정하기 전에 유효한 다단계 인증 방법이 준비되어 있는지를 확인합니다. 예를 들어 Windows Server 2012 R2의 AD FS에는 다단계 인증 기능이 있습니다. 또한 Azure AD 장치 등록 서비스에 대한 다단계 인증을 설정하기 전에 AD FS 서버에 유효한 추가 인증(다단계 인증) 방법을 설정해야 합니다. AD FS에서 지원되는 다단계 인증 방법에 대한 자세한 내용은 [AD FS에 대한 추가 인증 방법 구성](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)을 참조하세요.

## <a name="next-steps"></a>다음 단계

*   일반적인 질문에 대한 답변은 [Azure Active Directory 조건부 액세스 FAQ](active-directory-conditional-faqs.md)를 참조하세요.
