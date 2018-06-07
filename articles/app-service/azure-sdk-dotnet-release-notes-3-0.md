---
title: Azure SDK for .NET 3.0 릴리스 정보 | Microsoft 문서
description: Azure SDK for .NET 3.0 릴리스 정보
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: 207aa137b25e44baf73e7f481ebc8b6362dfa245
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Azure SDK for .NET 3.0 릴리스 정보

이 항목에는 Azure SDK for .NET 버전 3.0에 대한 릴리스 정보가 포함되어 있습니다.

## <a name="azure-sdk-for-net-30-release-summary"></a>Azure SDK for .NET 3.0 릴리스 요약

릴리스 날짜: 03/07/2017
 
Azure SDK 3.0의 새로운 변경 내용은 이번 릴리스에 도입되지 않았습니다. 기존 클라우드 서비스 프로젝트에서 이 SDK를 활용하는 데 필요한 업그레이드 프로세스도 없습니다. 업그레이드 프로세스를 요구하지 않고 Azure SDK 3.0의 사용을 허용하려면 Azure SDK 3.0은 Azure SDK 2.9와 같은 디렉터리에 설치합니다. 대부분의 구성 요소는 2.9에서 주 버전을 변경하지 않았지만 대신 빌드 번호로 업데이트했습니다.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- Visual Studio 2017에서는 이 Azure SDK for .NET 릴리스가 Azure 워크로드에 구축되었습니다. Azure 개발을 수행하는 데 필요한 모든 도구는 앞으로 Visual Studio 2017에 포함될 예정입니다. Visual Studio 2015의 경우 해당 SDK는 계속 WebPI를 통해 사용할 수 있습니다. 이제 Visual Studio 2017이 릴리스되었으므로 Visual Studio 2013용 Azure SDK for .NET 릴리스는 중단되었습니다.

### <a name="azure-diagnostics"></a>Azure 진단

- 이 동작은 키가 Cloud Services 진단 저장소 연결 문자열에 대한 토큰으로 교체되어 부분 연결 문자열만 저장하는 방식으로 변경되었습니다. 이제 실제 저장소 키가 액세스를 제어할 수 있도록 사용자 프로필 폴더에 저장됩니다. Visual Studio는 로컬 디버깅 및 게시 프로세스에 대한 사용자 프로필 폴더에서 저장소 키를 읽습니다. 
- 위에 설명된 변경에 대한 응답으로, Visual Studio Online 팀은 사용자가 연속 통합 및 배포에서 Azure에 게시할 때 진단 확장을 설정하기 위한 저장소 키를 지정할 수 있도록 Azure Cloud Services 배포 작업 템플릿을 개선했습니다.
- 작업 환경 간 구성 문제를 해결하는 데 도움을 주기 위해 Azure 진단(WAD)에 대한 보안 연결 문자열 및 토큰화를 저장할 수 있게 했습니다.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 가상 머신

- 이제 Visual Studio에서는 OS 제품군 5(Windows Server 2016) 가상 머신에 Cloud Services를 배포하도록 지원합니다. 기존 클라우드 서비스의 경우, 새 OS 제품군을 대상으로 설정을 변경할 수 있습니다. 새 클라우드 서비스를 만들 때 .NET 4.6 이상을 사용하여 서비스를 만들려면 선택하는 경우 기본적으로 서비스는 OS 제품군 5를 사용하게 됩니다.  자세한 내용은 [게스트 OS 제품군 지원 테이블](../cloud-services/cloud-services-guestos-update-matrix.md)을 참조하세요.

### <a name="known-issues"></a>알려진 문제

- Azure .NET SDK 3.0에는 Visual Studio 2015와의 병렬 구성에서 Visual Studio 2017을 제거할 경우 문제가 소개되었습니다.  Visual Studio 2015용 Azure SDK가 설치되어 있는 경우 Visual Studio 2017을 제거하면 Microsoft Azure Storage 에뮬레이터 및 Microsoft Azure Compute 에뮬레이터가 제거됩니다.  이로 인해 Visual Studio 2015에서 새 클라우드 서비스 프로젝트를 만들고 디버그할 때 오류가 발생합니다. 이 문제를 수정하려면 웹 플랫폼 설치 관리자에서 Azure SDK를 다시 설치합니다.  이 문제는 Visual Studio 2017 업데이트에서 해결된 예정입니다.  에서도 확인할 수 있습니다.

 
### <a name="azure-in-role-cache"></a>Azure In-Role Cache 

- Azure In-Role Cache에 대한 지원은 2016년 11월 30일에 종료되었습니다. 자세한 내용을 보려면 [여기](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)를 클릭하세요.




