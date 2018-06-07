---
title: "Azure Active Directory Domain Services에 Windows Server VM 조인 | Microsoft Docs"
description: "Azure Resource Manager 템플릿을 사용하여 Windows Server 가상 컴퓨터를 관리되는 도메인에 조인합니다."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: b90198696b7bdb9277fd2f2b8e8e727af42c5cfa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 관리되는 도메인에 Windows Server 가상 컴퓨터 조인
이 문서에서는 Resource Manager 템플릿을 사용하여 Azure AD Domain Services 관리되는 도메인에 Windows Server 가상 컴퓨터를 조인하는 방법에 대해 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.
1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.
4. 관리되는 도메인의 IP 주소를 가상 네트워크에 대한 DNS 서버로 구성했는지 확인합니다. 자세한 내용은 [Azure 가상 네트워크에 대한 DNS 설정을 업데이트하는 방법](active-directory-ds-getting-started-dns.md)을 참조하세요.
5. [Azure AD Domain Services 관리되는 도메인에 암호를 동기화](active-directory-ds-getting-started-password-sync.md)하는 데 필요한 단계를 완료합니다.


## <a name="install-and-configure-required-tools"></a>필요한 도구 설치 및 구성
다음 옵션 중 하나를 사용하여 이 문서에서 설명하는 단계를 수행할 수 있습니다.
* **Azure PowerShell**: [설치 및 구성](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure 플랫폼 간 명령줄 인터페이스**: [설치 및 구성](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>옵션 1: 새 Windows Server VM 프로비전 및 관리되는 도메인에 조인
**빠른 시작 템플릿 이름**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Windows Server 가상 컴퓨터를 배포하고 관리되는 도메인에 조인하려면 다음 단계를 수행합니다.
1. [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)으로 이동합니다.
2. **Deploy to Azure**를 클릭합니다.
3. **사용자 지정 배포** 페이지에서 가상 컴퓨터를 프로비전하는 데 필요한 정보를 제공합니다.
4. 가상 컴퓨터를 프로비전할 **Azure 구독**을 선택합니다. Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다.
5. 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹을 만듭니다.
6. 새 가상 컴퓨터를 배포할 **위치**를 선택합니다.
7. **기존 VNET 이름**에서 Azure AD Domain Services 관리되는 도메인을 배포한 가상 네트워크를 지정합니다.
8. **기존 서브넷 이름**에서 이 가상 컴퓨터를 배포하려는 가상 네트워크의 서브넷을 지정합니다. 가상 네트워크의 게이트웨이 서브넷은 선택하지 마세요. 또한 관리되는 도메인이 배포된 전용 서브넷도 선택하지 마세요.
9. **DNS 레이블 접두사**에서 프로비전되는 가상 컴퓨터의 호스트 이름을 지정합니다. 예를 들어 'contoso-win'과 같습니다.
10. 가상 컴퓨터에 적합한 **VM 크기**를 선택합니다.
11. **조인할 도메인**에서 관리되는 도메인의 DNS 도메인 이름을 지정합니다.
12. **도메인 사용자 이름**에서 관리되는 도메인에 VM을 조인하는 데 사용해야 하는 관리되는 도메인의 사용자 계정 이름을 지정합니다.
13. **도메인 암호**에서 'domainUsername' 매개 변수에서 참조하는 도메인 사용자 계정의 암호를 지정합니다.
14. 선택 사항: 가상 컴퓨터를 추가할 사용자 지정 OU에 대한 **OU 경로**를 지정할 수 있습니다. 이 매개 변수에 대한 값을 지정하지 않으면 가상 컴퓨터가 관리되는 도메인의 기본 **AAD DC 컴퓨터** OU에 추가됩니다.
15. **VM Admin Username** 필드에서 가상 컴퓨터에 대한 로컬 관리자 계정 이름을 지정합니다.
16. **VM 관리자 암호** 필드에서 가상 컴퓨터에 대한 로컬 관리자 암호를 지정합니다. 가상 컴퓨터에 대한 강력한 로컬 관리자 암호를 제공하여 해당 암호가 무차별 암호 대입 공격으로부터 보호되도록 합니다.
17. **위에 명시된 사용 약관에 동의함**을 클릭합니다.
18. **구매**를 클릭하여 가상 컴퓨터를 프로비전합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일에는 도메인 계정의 암호뿐만 아니라 가상 컴퓨터의 로컬 관리자 암호도 포함됩니다. 파일 공유 또는 다른 공유 위치에 이 파일을 남기지 않도록 합니다. 가상 컴퓨터 배포가 완료되면 이 파일을 삭제하는 것이 좋습니다.
>

배포가 성공적으로 완료되면 새로 프로비전된 Windows 가상 컴퓨터가 관리되는 도메인에 조인됩니다.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>옵션 2: 관리되는 도메인에 기존 Windows Server VM 조인
**빠른 시작 템플릿**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

관리되는 도메인에 기존 Windows Server 가상 컴퓨터를 조인하려면 다음 단계를 수행합니다.
1. [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)으로 이동합니다.
2. **Deploy to Azure**를 클릭합니다.
3. **사용자 지정 배포** 페이지에서 가상 컴퓨터를 프로비전하는 데 필요한 정보를 제공합니다.
4. 가상 컴퓨터를 프로비전할 **Azure 구독**을 선택합니다. Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다.
5. 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹을 만듭니다.
6. 새 가상 컴퓨터를 배포할 **위치**를 선택합니다.
7. **VM 목록** 필드에서 관리되는 도메인에 조인할 기존 가상 컴퓨터의 이름을 지정합니다. 쉼표를 사용하여 개별 VM 이름을 구분합니다. 예를 들어 **contoso-web, contoso-api**와 같습니다.
8. **도메인 조인 사용자 이름**에서 관리되는 도메인에 VM을 조인하는 데 사용해야 하는 관리되는 도메인의 사용자 계정 이름을 지정합니다.
9. **도메인 조인 사용자 암호**에서 'domainUsername' 매개 변수에서 참조하는 도메인 사용자 계정의 암호를 지정합니다.
10. **도메인 FQDN**에서 관리되는 도메인의 DNS 도메인 이름을 지정합니다.
11. 선택 사항: 가상 컴퓨터를 추가할 사용자 지정 OU에 대한 **OU 경로**를 지정할 수 있습니다. 이 매개 변수에 대한 값을 지정하지 않으면 가상 컴퓨터가 관리되는 도메인의 기본 **AAD DC 컴퓨터** OU에 추가됩니다.
12. **위에 명시된 사용 약관에 동의함**을 클릭합니다.
13. **구매**를 클릭하여 가상 컴퓨터를 프로비전합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일에는 도메인 계정의 암호뿐만 아니라 가상 컴퓨터의 로컬 관리자 암호도 포함됩니다. 파일 공유 또는 다른 공유 위치에 이 파일을 남기지 않도록 합니다. 가상 컴퓨터 배포가 완료되면 이 파일을 삭제하는 것이 좋습니다.
>

배포가 성공적으로 완료되면 지정된 Windows 가상 컴퓨터가 관리되는 도메인에 조인됩니다.


## <a name="related-content"></a>관련 콘텐츠
* [Azure PowerShell 개요](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Azure 빠른 시작 템플릿 - 도메인에 새 VM 조인](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure 빠른 시작 템플릿 - 도메인에 기존 VM 조인](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
