---
title: Azure 및 Azure 스택 응용 프로그램과 함께 하이브리드 클라우드 id 구성 | Microsoft Docs
description: Azure 및 Azure 스택 응용 프로그램과 함께 하이브리드 클라우드 id를 구성 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>자습서: Azure와 Azure 스택 응용 프로그램과 하이브리드 클라우드 id 구성

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

전역 Azure 및 Azure 스택에서 응용 프로그램에 대 한 액세스 권한을 부여 하는 두 가지 옵션이 있습니다. Azure 스택 인터넷에 대 한 지속적인 연결에 있는 경우에 Azure Active Directory (Azure AD)를 사용할 수 있습니다. Azure 스택의 인터넷에서 연결에 Azure Directory Federated Services (AD FS)을 사용할 수 있습니다. 서비스 사용자를 사용 하 여 Azure 스택 배포 하거나 Azure 스택에서 Azure 리소스 관리자를 통해 구성을 위해 응용 프로그램에 대 한 액세스 권한을 부여 합니다. 

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> * 전역 Azure 및 Azure 스택에서 하이브리드 id를 설정 합니다.
> * Azure 스택 API에 액세스할 수 있는 토큰을 검색 합니다.

이러한 단계 수 있는 Azure 스택 연산자 권한이 있어야 합니다.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>포털에서 Azure AD에 대 한 서비스 사용자 만들기

Azure AD id 저장소로 사용 하 여 Azure 스택을 배포한 경우에 Azure에 대해 수행 하는 것 처럼 서비스 사용자를 만들 수 있습니다. [이 문서](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) 포털을 통해 단계를 수행 하는 방법을 보여 줍니다. 확인 해야 하는 [필요한 Azure AD 권한](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) 시작 하기 전에.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>PowerShell을 사용 하 여 AD FS에 대 한 서비스 사용자 만들기

AD FS와 Azure 스택을 배포한 경우에 서비스 사용자를 만들고 액세스에 대 한 역할 할당에 해당 id를 사용 하 여 PowerShell에서 로그인 PowerShell을 사용할 수 있습니다. [이 문서](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) PowerShell을 사용 하 여 단계를 수행 하는 방법을 보여 줍니다.

## <a name="using-the-azure-stack-api"></a>Azure 스택 API를 사용 하 여

[이 자습서](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) Azure 스택 API에 액세스할 수 있는 토큰을 검색 하는 과정을 살펴봅니다.

## <a name="connect-to-azure-stack-using-powershell"></a>Powershell을 사용 하 여 Azure 스택에 연결

Azure 스택에 연결 하기 위해이 문서에서 배울 개념을 사용 하는 샘플 스크립트는 다음과 같습니다.

### <a name="prerequisites"></a>필수 조건

Azure 스택 설치 된 Azure Active Directory 구독에 액세스할 수 있습니다에 연결 합니다. Azure 스택 설치를 설정 하지 않은 경우를 설정 하려면 다음이 지침을 사용할 수 있습니다는 [Azure 스택 개발 키트](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy)합니다.

[이 자습서](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) Azure PowerShell을 설치 하 고 Azure 스택 설치에 연결 하는 데 필요한 단계를 안내 합니다.

#### <a name="connect-to-azure-stack-using-code"></a>Azure 스택 코드를 사용 하 여에 연결

코드를 사용 하 여 Azure 스택을 연결 하려면 Azure 스택 설치를 위한 인증 및 그래프 끝점 가져오고 다음 REST 요청을 사용 하 여를 인증 하는 Azure 리소스 관리자 끝점 API을 사용 합니다. 샘플 응용 프로그램을 찾을 수 있습니다 [여기](https://github.com/shriramnat/HybridARMApplication)합니다.

> [!Note]  
선택한 언어에 대 한 Azure SDK의 Azure API 프로필을 지원 하지 않는 한 Azure 스택 SDK 작동 하지 않을 수 있습니다. Azure API 프로필에 대 한 자세한 내용은 이동 [여기](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles)합니다.

## <a name="next-steps"></a>다음 단계

 - Azure 스택에서 identity 처리 되는 방법에 대 한 자세한 참조 [Azure 스택에 대 한 Id 아키텍처](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture)합니다.  
 - Azure 클라우드 패턴에 대 한 자세한 참조 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
