---
title: 자습서 - Azure에서 Windows VM을 위한 Azure Security Center 사용 | Microsoft Docs
description: 이 자습서에서는 Azure에서 Windows 가상 머신을 보호하고 안전하게 하는 데 유용한 Azure Security Center 기능에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b573208107b07b47b471d9c5247b362ef144099e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-use-azure-security-center-to-monitor-windows-virtual-machines"></a>자습서: Azure Security Center를 사용하여 Windows 가상 머신 모니터링

Azure Security Center를 사용하면 Azure 리소스 보안 사례에 대한 가시성을 얻을 수 있습니다. Security Center는 통합 보안 모니터링 기능을 제공합니다. 다른 방법으로는 눈에 띄지 않을 수 있는 위협을 검색할 수 있습니다. 이 자습서에서는 Azure Security Center에 대해 알아보고 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 데이터 수집 설정
> * 보안 정책 설정
> * 구성 상태 문제 보기 및 수정
> * 검색된 위협 검토

## <a name="security-center-overview"></a>Security Center 개요

Security Center는 잠재적인 VM(가상 컴퓨터) 구성 문제 및 대상이 되는 보안 위협을 식별합니다. 여기에는 네트워크 보안 그룹이 누락된 VM, 암호화되지 않은 디스크 및 RDP(원격 데스크톱 프로토콜) 무차별 암호 대입 공격이 포함될 수 있습니다. 이 정보는 Azure Security Center 대시보드에서 읽기 쉬운 그래프로 표시됩니다.

Security Center 대시보드에 액세스하려면 Azure Portal의 메뉴에서 **Security Center**를 선택합니다. 이 대시보드에서 Azure 환경의 보안 상태를 보고, 현재 권장되는 구성의 수를 찾고, 현재의 위협 경고 상태를 볼 수 있습니다. 각 상위 수준의 차트를 펼쳐 세부 정보를 표시할 수 있습니다.

![Security Center 대시보드](./media/tutorial-azure-security/asc-dash.png)

Security Center는 데이터 검색 외에도 검색된 문제에 대한 권장 사항을 제공합니다. 예를 들어 연결된 네트워크 보안 그룹 없이 VM을 배포한 경우 Security Center에서 수행할 수 있는 재구성 단계와 함께 권장 사항을 표시합니다. Security Center의 컨텍스트를 벗어나지 않으면서 자동화된 재구성을 얻을 수 있습니다.  

![권장 사항](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>데이터 수집 설정

VM 보안 구성을 확인하려면 먼저 Security Center 데이터 수집을 설정해야 합니다. 여기에는 데이터 수집을 설정하고 수집된 데이터를 저장할 Azure 저장소 계정을 만드는 작업이 포함됩니다. 

1. Azure Security Center 대시보드에서 **보안 정책**을 클릭한 다음 구독을 선택합니다. 
2. **데이터 수집**에서 **설정**을 선택합니다.
3. 저장소 계정을 만들려면 **저장소 계정 선택**을 선택합니다. 그런 다음 **확인**을 선택합니다.
4. **보안 정책** 블레이드에서 **저장**을 선택합니다. 

그러면 Security Center 데이터 수집 에이전트가 모든 VM에 설치되고 데이터 수집이 시작됩니다. 

## <a name="set-up-a-security-policy"></a>보안 정책 설정

보안 정책은 Security Center에서 데이터를 수집하고 권장할 항목을 정의하는 데 사용됩니다. 서로 다른 보안 정책을 Azure 리소스 집합마다 개별적으로 적용할 수 있습니다. 기본적으로 Azure 리소스가 모든 정책 항목에 대해 평가되지만, 모든 Azure 리소스 또는 리소스 그룹에 대해 개별 정책 항목을 해제할 수 있습니다. Azure Security Center 보안 정책에 대한 자세한 내용은 [Azure Security Center에서 보안 정책 설정](../../security-center/security-center-policies.md)을 참조하세요. 

모든 Azure 리소스에 대한 보안 정책을 설정하려면

1. Security Center 대시보드에서 **보안 정책**을 클릭한 다음 구독을 선택합니다.
2. **방지 정책**을 선택합니다.
3. 모든 Azure 리소스에 적용하려는 정책 항목을 설정하거나 해제합니다.
4. 설정 선택 작업을 완료했으면 **확인**을 선택합니다.
5. **보안 정책** 블레이드에서 **저장**을 선택합니다. 

특정 리소스 그룹에 대한 정책을 설정하려면

1. Security Center 대시보드에서 **보안 정책**을 선택한 다음 리소스 그룹을 선택합니다.
2. **방지 정책**을 선택합니다.
3. 리소스 그룹에 적용하려는 정책 항목을 설정하거나 해제합니다.
4. **상속**에서 **고유**를 선택합니다.
5. 설정 선택 작업을 완료했으면 **확인**을 선택합니다.
6. **보안 정책** 블레이드에서 **저장**을 선택합니다.  

또한 이 페이지에서 특정 리소스 그룹에 대한 데이터 수집도 해제할 수 있습니다.

다음 예제에서는 *myResoureGroup*이라는 리소스 그룹에 대한 고유 정책을 만들었습니다. 이 정책에서는 디스크 암호화 및 웹 응용 프로그램 방화벽 권장 사항이 모두 해제되어 있습니다.

![고유 정책](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>VM 구성 상태 보기

데이터 수집을 설정하고 보안 정책을 설정하면 Security Center에서 경고 및 권장 사항을 제공하기 시작합니다. VM이 배포되면 데이터 수집 에이전트가 설치됩니다. 그러면 Security Center가 새 VM에 대한 데이터로 채워집니다. VM 구성 상태에 대한 자세한 내용은 [Security Center에서 VM 보호](../../security-center/security-center-virtual-machine-recommendations.md)를 참조하세요. 

데이터가 수집되면 각 VM 및 관련 Azure 리소스에 대한 리소스 상태가 집계됩니다. 이 정보는 읽기 쉬운 차트로 표시됩니다. 

리소스 상태를 보려면

1.  Security Center 대시보드의 **리소스 보안 상태**에서 **Compute**를 선택합니다. 
2.  **Compute** 블레이드에서 **가상 머신**를 선택합니다. 이 보기는 모든 VM의 구성 상태에 대한 요약을 제공합니다.

![상태 계산](./media/tutorial-azure-security/compute-health.png)

VM에 대한 모든 권장 사항을 보려면 해당 VM을 선택합니다. 권장 사항 및 재구성에 대해서는 이 자습서의 다음 섹션에서 자세히 설명합니다.

## <a name="remediate-configuration-issues"></a>구성 문제 해결

Azure Security Center가 구성 데이터로 채워지기 시작하면 설정한 보안 정책에 따라 권장 사항이 만들어집니다. 예를 들어 연결된 네트워크 보안 그룹 없이 VM을 설정한 경우 네트워크 보안 그룹을 만들도록 제안하는 권장 사항이 만들어집니다. 

모든 권장 사항 목록을 보려면: 

1. Security Center 대시보드에서 **권장 사항**을 선택합니다.
2. 특정 권장 사항을 선택합니다. 해당 권장 사항이 적용되는 모든 리소스에 대한 목록이 표시됩니다.
3. 권장 사항을 적용하려면 특정 리소스를 선택합니다. 
4. 재구성 단계의 지침을 따릅니다. 

대부분의 경우 Security Center에서는 Security Center의 컨텍스트를 벗어나지 않으면서 권장 사항을 처리하기 위해 취할 수 있는 실행 가능한 단계를 제공합니다. 다음 예제에서는 Security Center에서 무제한 인바운드 규칙이 있는 네트워크 보안 그룹을 검색합니다. 권장 사항 페이지에서 **인바운드 규칙 편집** 단추를 선택할 수 있습니다. 규칙을 수정하는 데 필요한 UI가 나타납니다. 

![권장 사항](./media/tutorial-azure-security/remediation.png)

권장 사항이 해결되면 해결되었다고 표시됩니다. 

## <a name="view-detected-threats"></a>검색된 위협 보기

Security Center에서는 리소스 구성 권장 사항 외에도 위협 검색 경고를 표시합니다. 보안 경고 기능은 각 VM, Azure 네트워킹 로그 및 연결된 파트너 솔루션에서 수집된 데이터를 집계하여 Azure 리소스에 대한 보안 위협을 검색합니다. Security Center 위협 검색 기능에 대한 자세한 내용은 [Azure Security Center 검색 기능](../../security-center/security-center-detection-capabilities.md)을 참조하세요.

보안 경고 기능을 사용하려면 Azure Security Center 가격 책정 계층을 *체험*에서 *표준*으로 높여야 합니다. 이 가격 책정 계층으로 이동하면 30일 **평가판**을 사용할 수 있습니다. 

가격 책정 계층을 변경하려면:  

1. Azure Security Center 대시보드에서 **보안 정책**을 클릭한 다음 구독을 선택합니다.
2. **가격 책정 계층**을 선택합니다.
3. 새 계층을 선택한 다음 **선택**을 클릭합니다.
4. **보안 정책** 블레이드에서 **저장**을 선택합니다. 

가격 책정 계층을 변경하면 보안 위협이 검색될 때 보안 경고 그래프가 채워지기 시작합니다.

![보안 경고](./media/tutorial-azure-security/security-alerts.png)

경고를 선택하여 정보를 표시합니다. 예를 들어 위협, 검색 시간, 모든 위협 시도 및 권장되는 재구성에 대한 설명을 볼 수 있습니다. 다음 예제에서는 RDP 무차별 암호 대입 공격이 검색되었으며, 실패한 RDP 시도 횟수는 294회입니다. 권장되는 해결 방법이 제공됩니다.

![RDP 공격](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Security Center를 설정한 다음 Security Center에서 VM을 검토했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터 수집 설정
> * 보안 정책 설정
> * 구성 상태 문제 보기 및 수정
> * 검색된 위협 검토

Visual Studio Team Services 및 IIS를 실행하는 Windows VM을 사용하여 CI/CD 파이프라인을 만드는 방법에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [Visual Studio Team Services CI/CD 파이프라인](./tutorial-vsts-iis-cicd.md)
