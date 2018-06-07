---
title: Azure 앱 서비스에서 Azure 스택 업데이트 | Microsoft Docs
description: Azure 스택 Azure 앱 서비스를 업데이트 하기 위한 세부 지침
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.openlocfilehash: c822f25e25953b8709f481e51d6a63e6a912a60a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="update-azure-app-service-on-azure-stack"></a>Azure 스택에 Azure 앱 서비스를 업데이트 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

> [!IMPORTANT]
> Azure 스택 통합 시스템 1804 업데이트를 적용 하거나 Azure 앱 서비스 1.2를 배포 하기 전에 최신 Azure 스택 개발 키트를 배포 합니다.
>
>

이 문서의 지침을 따르면 업그레이드할 수 있습니다는 [앱 서비스 리소스 공급자](azure-stack-app-service-overview.md) 인터넷에 연결 된 Azure 스택 환경에 배포 합니다.

> [!IMPORTANT]
> 업그레이드를 실행 하기 전에 이미 완료 되었는지 확인는 [Azure 스택 리소스 공급자에 있는 Azure 앱 서비스의 배포](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>앱 서비스 리소스 공급자 설치 관리자 실행

이 과정에서 업그레이드가 합니다.

* 앱 서비스의 이전 배포를 검색 합니다.
* 모든 업데이트 패키지와 새 버전의 모든 OS 라이브러리를 배포 준비
* 저장소에 업로드
* 모든 앱 서비스 역할을 업그레이드 (컨트롤러, 관리, 프런트엔드, 게시자 및 작업자 역할)
* App Service 확장 집합 정의 업데이트
* 앱 서비스 리소스 공급자 매니페스트를 업데이트 합니다.

> [!IMPORTANT]
> 앱 서비스 설치 관리자는 Azure 스택 관리자 Azure 리소스 관리자 끝점에 도달할 수 있는 컴퓨터에서 실행 되어야 합니다.
>
>

Azure 스택 앱 서비스의 배포를 업그레이드 하려면 다음이 단계를 따르십시오.

1. 다운로드는 [앱 서비스 설치 관리자](https://aka.ms/appsvcupdate2installer)

2. Appservice.exe 관리자 권한으로 실행

    ![앱 서비스 설치 관리자][1]

3. 클릭 **앱 서비스 배포 또는 최신 버전으로 업그레이드 합니다.**

4. 검토 하 고 Microsoft 소프트웨어 사용 조건에 동의 하 고 클릭 **다음**합니다.

5. 검토 및 제 3 자 사용 조건에 동의 하 고 클릭 **다음**합니다.

6. 다음 사항을 확인 Azure 스택에서 Azure 리소스 관리자 끝점 및 Active Directory 테 넌 트 정보가 올바른지 합니다. Azure 스택 개발 키트 배포 하는 동안 기본 설정을 사용한 경우 여기에서 기본 값을 수락할 수 있습니다. 그러나 Azure 스택 배포 옵션 사용자 지정한 경우 값을 반영 하기 위해이 창에서 편집 해야 합니다. 예를 들어, 도메인 접미사를 사용 하는 경우 *mycloud.com*, Azure 스택에서 Azure 리소스 관리자 끝점을 변경 해야 *management.region.mycloud.com*합니다. 사용자의 정보를 확인 한 후 클릭 **다음**합니다.

    ![Azure 스택 클라우드 정보][2]

7. 다음 페이지:

   1. 클릭는 **연결** 단추 옆에 **Azure 스택 구독** 상자입니다.
        * Azure Active Directory (Azure AD)를 사용 하 여 Azure AD 관리자 계정 및 Azure 스택을 배포할 때 사용자가 제공한 암호를 입력 합니다. 클릭 **로그인**합니다.
        * Active Directory Federation Services (AD FS)를 사용 하 여 관리자 계정을 제공 합니다. 예를 들어 *cloudadmin@azurestack.local*입니다. 암호를 입력 하 고 클릭 **로그인**합니다.
   2. 에 **Azure 스택 구독** 상자는 **공급자 구독 기본**합니다.
   3. 에 **Azure 스택 위치** 상자에 배포 하는 영역에 해당 하는 위치를 선택 합니다. 예를 들어 선택 **로컬** 경우 Azure 스택 개발 키트를 배포 합니다.
   4. 기존 앱 서비스 배포를 검색 하는 경우 다음 리소스 그룹 및 저장소 계정은 채울 되며 회색으로 표시 합니다.
   5. 클릭 **다음** 업그레이드 요약을 검토 합니다.

    ![앱 서비스 설치 되어 있음][3]

8. 요약 페이지:
   1. 선택 사항을 확인 합니다. 변경 하려면 사용 된 **이전** 이전 페이지를 방문 하는 단추입니다.
   2. 구성이 올바른 경우 확인란을 선택 합니다.
   3. 업그레이드를 시작 하려면 **다음**합니다.

       ![앱 서비스 업그레이드 요약][4]

9. 업그레이드 진행률 페이지:
    1. 업그레이드 진행률을 추적 합니다. Azure 스택 앱 서비스 업그레이드 기간에 달라 배포할 역할 인스턴스 수에 따라 달라 집니다.
    2. 업그레이드가 완료 된 후 클릭 **종료**합니다.

        ![앱 서비스 업그레이드 진행률][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>다음 단계

또한을 수행 하려면 다른 [플랫폼 서비스 (PaaS) 서비스로](azure-stack-tools-paas-services.md)합니다.

* [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)
