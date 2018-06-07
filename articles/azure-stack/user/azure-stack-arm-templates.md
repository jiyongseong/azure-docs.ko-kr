---
title: Azure 스택에서 Azure 리소스 관리자 템플릿을 사용 하 여 | Microsoft Docs
description: 리소스를 제공 하려면 Azure 스택에서 Azure 리소스 관리자 템플릿을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Azure Stack에서 Azure Resource Manager 템플릿 사용

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 리소스 관리자 템플릿을 배포 하 고 단일, 통합 작업에서 응용 프로그램에 대 한 모든 리소스를 프로 비전에 사용할 수 있습니다. 리소스 그룹에 리소스를 변경 하려면 서식 파일을 다시 배포할 수 있습니다.

이러한 템플릿은 Microsoft Azure Stack 포털, PowerShell, 명령줄 및 Visual Studio에서 배포할 수 있습니다.

다음 빠른 시작 템플릿은에서 사용할 수 있는 [GitHub](http://aka.ms/azurestackgithub)합니다.

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>SharePoint Server (비 고가용성 배포)를 배포 합니다.

PowerShell DSC 확장을 사용 하 여 다음 리소스를 포함 하는 SharePoint Server 2013 팜을 만들려면:

* 가상 네트워크
* 저장소 계정 3개
* 외부 부하 분산 장치 2개
* 단일 도메인이 포함 된 새 포리스트의 도메인 컨트롤러로 구성 하나의 가상 컴퓨터 (VM)
* SQL Server 2014 독립 실행형 서버로 하나의 VM이 구성됨
* 하나의 컴퓨터 SharePoint Server 2013 팜을로 구성 된 하나의 VM

## <a name="deploy-ad-non-high-availability-deployment"></a>AD (비-높은-가용성-배포의 경우)를 배포 합니다.

다음 리소스를 포함 하는 AD 도메인 컨트롤러 서버를 만들려면 PowerShell DSC 확장을 사용 합니다.

* 가상 네트워크
* 저장소 계정 1개
* 외부 부하 분산 장치 1개
* 단일 도메인이 포함 된 새 포리스트의 도메인 컨트롤러로 구성 하나의 가상 컴퓨터 (VM)

## <a name="deploy-adsql-non-high-availability-deployment"></a>AD/SQL (비-높은-가용성-배포의 경우)를 배포 합니다.

다음 리소스를 포함 하는 SQL Server 2014 독립 실행형 서버를 만들려면 PowerShell DSC 확장을 사용 합니다.

* 가상 네트워크
* 두 저장소 계정
* 외부 부하 분산 장치 1개
* 단일 도메인이 포함 된 새 포리스트의 도메인 컨트롤러로 구성 하나의 가상 컴퓨터 (VM)
* SQL Server 2014 독립 실행형 서버로 하나의 VM이 구성됨

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

PowerShell DSC 확장을 사용하여 기존 가상 머신 로컬 구성 관리자(LCM)를 구성하고 Azure Automation 계정 DSC 끌어오기 서버에 등록합니다.

## <a name="create-a-virtual-machine-from-a-user-image"></a>사용자 이미지에서 가상 머신 만들기

사용자 지정 사용자 이미지에서 가상 머신을 만듭니다. 이 템플릿은 가상 네트워크(DNS 사용), 공용 IP 주소 및 네트워크 인터페이스도 배포합니다.

## <a name="basic-virtual-machine"></a>기본 가상 컴퓨터

DNS) (포함 된 가상 네트워크, 공용 IP 주소 및 네트워크 인터페이스를 포함 하는 Windows VM을 배포 합니다.

## <a name="cancel-a-running-template-deployment"></a>실행 중인 템플릿 배포 취소

실행 중인 템플릿 배포를 취소 하려면 사용 하 여는 `Stop-AzureRmResourceGroupDeployment` PowerShell cmdlet.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 템플릿 배포](azure-stack-deploy-template-portal.md)
* [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)
