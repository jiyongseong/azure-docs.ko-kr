---
title: Azure Site Recovery를 사용하여 VMware의 소스 환경을 Azure 복제로 설정 | Microsoft Docs
description: 이 아티클에서는 Azure Site Recovery를 사용하여 Azure에 VMware VM을 복제하도록 온-프레미스 환경을 설정하는 방법을 설명합니다.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b2c564e8d49e39d9cdc09d3fe168388d579de70e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Azure 복제에 대한 VMware의 원본 환경 설정

이 아티클에서는 VMware VM을 Azure에 복제하도록 원본 온-프레미스 환경을 설정하는 방법을 설명합니다. 여기에는 복제 시나리오 선택, 온-프레미스 컴퓨터를 Site Recovery 구성 서버로 설정, 온-프레미스 VM 자동 검색을 위한 단계가 포함됩니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 작업을 이미 수행한 것으로 가정합니다.
- [Azure Portal](http://portal.azure.com)에서 [리소스 설정](tutorial-prepare-azure.md)
- 자동 검색용 전용 계정을 포함하는 [온-프레미스 VMware 설정](vmware-azure-tutorial-prepare-on-premises.md)



## <a name="choose-your-protection-goals"></a>보호 목표 선택

1. Azure Portal에서 **Recovery Services** 자격 증명 모음 블레이드로 이동한 후 사용자 자격 증명 모음을 선택합니다.
2. 자격 증명 모음의 리소스 메뉴에서 **시작** > **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**로 이동합니다.

    ![목표 선택](./media/vmware-azure-set-up-source/choose-goals.png)
3. **보호 목표**에서 **Azure에**를 선택하고 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![목표 선택](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>구성 서버 설정

구성 서버를 온-프레미스 VMware VM으로 설정하고, OVF(Open Virtualization Format) 템플릿을 사용합니다. VMware VM에 설치되는 구성 요소에 대해 [자세히 알아봅니다](concepts-vmware-to-azure-architecture.md). 

1. 구성 서버 배포에 대한 [필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)에 대해 알아봅니다.
2. 배포에 대한 [용량 수치를 확인](vmware-azure-deploy-configuration-server.md#capacity-planning)합니다.
3. 구성 서버를 실행하는 온-프레미스 VMware VM을 설치하기 위한 OVF 템플릿(how-to-deploy-configuration-server.md)을 [다운로드](vmware-azure-deploy-configuration-server.md#download-the-template)하고 [가져옵니다](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware).
4. VMware VM을 켜고 Recovery Services 자격 증명 모음에 [등록](vmware-azure-deploy-configuration-server.md#register-the-configuration-server)합니다.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>자동 검색에 VMware 계정 추가

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>VMware 서버에 연결

Azure Site Recovery가 온-프레미스 환경에서 실행 중인 가상 머신을 검색할 수 있게 하려면 Site Recovery와 VMware vCenter 서버 또는 vSphere ESXi 호스트를 연결해야 합니다.

**+vCenter**를 선택하여 VMware vCenter 서버 또는 VMware vSphere ESXi 호스트를 연결하기 시작합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>일반적인 문제
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>다음 단계
Azure에서 [대상 환경 설정](./vmware-azure-set-up-target.md).
