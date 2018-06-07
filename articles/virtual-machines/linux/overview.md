---
title: Azure의 Linux VM 개요 | Microsoft Docs
description: Linux 가상 컴퓨터를 사용하여 Azure Compute, Storage 및 네트워킹 서비스를 설명합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: jeconnoc
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d2e4a014a0f7bb7f94885528abc930d2b243318b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-and-linux"></a>Azure와 Linux
Microsoft Azure는 솔루션을 호스팅하는 데 적합한 분석, Virtual Machines, 데이터베이스, 모바일, 네트워킹, 저장소 및 웹을 비롯한 나날이 다양해지는 통합 공용 클라우드 서비스입니다.  Microsoft Azure는 온-프레미스 하드웨어 없이도 확장할 수 있는 컴퓨팅 플랫폼을 제공하며, 필요한 때, 사용한 만큼 비용을 지불하면 됩니다.   Azure는 솔루션을 강화하고 클라이언트의 요구를 맞추기 위해 필요한 규모에 준비되어 있습니다.

Amazon의 AWS의 다양한 기능에 익숙한 경우 Azure과 AWS 비교 [정의 매핑 문서](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)를 검토할 수 있습니다.

## <a name="regions"></a>영역
Microsoft Azure 리소스는 전 세계 여러 지리적 지역에 걸쳐 분산됩니다.  "지역"은 동일한 지리적 지역에서 여러 데이터 센터를 나타냅니다. 현재 Azure는 일반적으로 추가로 발표된 6개 지역을 포함하여 전 세계 36개 지역에서 사용할 수 있습니다(2017년 11월 기준). 업데이트된 기존 및 새로 발표된 지역 목록을 다음 페이지에서 찾을 수 있습니다.

* [Azure 지역](https://azure.microsoft.com/regions/)

## <a name="availability"></a>가용성
Azure는 모든 디스크에 프리미엄 저장소를 사용하여 VM을 배포하는 경우 업계 최고의 99.9% 단일 인스턴스 가상 머신 Service Level Agreement(서비스 수준 약정)를 발표했습니다.  배포에서 표준 99.95% VM 서비스 수준 약정을 충족하려면 가용성 집합 내부에서 워크로드를 실행하는 VM을 둘 이상 계속 배포해야 합니다. 가용성 집합을 사용하면 VM이 Azure 데이터 센터에서 여러 오류 도메인 간에 분산될 뿐만 아니라 다양한 유지 관리 창이 있는 호스트에 배포됩니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

## <a name="managed-disks"></a>Managed Disks

Managed Disks는 백그라운드에서 Azure Storage 계정 만들기 및 관리 작업을 처리하기 때문에 저장소 계정의 확장성 제한에 걱정할 필요가 없습니다. 디스크 크기와 성능 계층(표준 또는 프리미엄)을 지정하면, Azure가 디스크를 만들고 관리합니다. 디스크를 추가하거나 VM을 확장하고 축소하면 사용 중인 저장소에 대해 걱정할 필요가 없습니다. 새 VM을 만드는 경우 [Azure CLI 2.0을 사용](quick-create-cli.md)하거나 Azure Portal을 사용하여 관리되는 OS 및 데이터 디스크로 VM을 만듭니다. 관리되지 않는 디스크가 있는 VM이 있는 경우 [Managed Disks로 지원되도록 VM을 변환](convert-unmanaged-to-managed-disks.md)할 수 있습니다.

또한 Azure 지역당 하나의 저장소 계정에서 사용자 지정 이미지를 관리하고 동일한 구독에서 수백 개의 VM을 만드는 데 사용할 수도 있습니다. 관리 디스크에 대한 자세한 내용은 [Managed Disks 개요](../linux/managed-disks-overview.md)를 참조하세요.

## <a name="azure-virtual-machines--instances"></a>Azure Virtual Machines 및 인스턴스
Microsoft Azure는 많은 파트너가 제공하고 유지 관리하는 다양하고 인기 있는 Linux 배포를 지원합니다.  Azure Marketplace에서 Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD 등과 같은 배포를 찾을 수 있습니다. Microsoft에서는 다양한 Linux 커뮤니티와 적극적으로 작업하여 [Azure 인증 Linux 배포판](endorsed-distros.md) 목록에 다양한 옵션을 추가합니다.

선택한 기본 Linux 배포가 현재 갤러리에 없는 경우 [Azure에서 Linux VHD 만들기 및 업로드](create-upload-generic.md)에 따라 "직접 Linux" VM을 가져올 수 있습니다.

Azure 가상 컴퓨터를 사용하면 다양한 컴퓨팅 솔루션을 민첩하게 배포할 수 있습니다. 거의 모든 운영 체제(Windows, Linux 또는 증가하는 파트너 목록 중 하나에서 사용자 지정으로 만든 운영 체제)에서 거의 모든 워크로드 및 언어를 배포할 수 있습니다. 그래도 원하는 내용이 표시되지 않나요?  걱정하지 마세요. 온-프레미스에서 고유한 이미지를 가져올 수 있습니다.

## <a name="vm-sizes"></a>VM 크기
사용할 VM의 [크기](sizes.md)는 실행하려는 워크로드를 기준으로 결정됩니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 저장소 용량 등의 요소를 결정합니다. Azure는 다양한 크기를 제공하여 다양한 유형의 사용을 지원합니다.

Azure는 VM의 크기와 운영 체제에 기반한 [시간당 가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)을 청구합니다. 부분 시간의 경우 Azure는 사용 시간(분)에 대해서만 청구합니다. 저장소는 가격이 책정되며 개별적으로 청구됩니다.

## <a name="automation"></a>Automation
적절한 DevOps 문화권을 얻으려면 모든 인프라가 코드여야 합니다.  모든 인프라가 코드로 되어 있으면 쉽게 다시 만들 수 있습니다(Phoenix 서버).  Azure는 Ansible, Chef, SaltStack 및 Puppet과 같은 모든 주요 자동화 도구와 함께 작동합니다.  또한 Azure는 자체 자동화 도구도 제공합니다.

* [Azure 템플릿](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure는 Azure를 지원하는 대부분의 Linux 배포판에서 [cloud-init](http://cloud-init.io/)에 대한 지원을 롤아웃하고 있습니다.  현재 Canonical의 Ubuntu VM은 기본적으로 사용하도록 설정된 cloud-init와 함께 배포됩니다.  Red Hats RHEL, CentOS 및 Fedora는 cloud-init를 지원하지만 RedHat에서 유지 관리하는 Azure 이미지에는 현재 cloud-init가 설치되어 있지 않습니다.  RedHat 제품군 OS에서 cloud-init를 사용하려면 cloud-init가 설치된 사용자 지정 이미지를 만들어야 합니다.

* [Azure Linux VM에서 cloud-init 사용](using-cloud-init.md)

## <a name="quotas"></a>할당량
각 Azure 구독에는 프로젝트에 대해 많은 수의 VM을 배포하는 데 영향을 줄 수 있는 기본 할당량 한도가 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다.  제한 증가를 요구하는 지원 티켓을 제출하면 할당량 제한을 빠르고 쉽게 늘릴 수 있습니다.  할당량 제한에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 구독 서비스 제한](../../azure-subscription-service-limits.md)

## <a name="partners"></a>파트너
Microsoft는 파트너와 긴밀히 협력하여 사용 가능한 이미지가 업데이트되고 Azure 런타임에 대해 최적화되도록 합니다.  Azure 파트너에 대한 자세한 내용은 다음 링크를 참조하세요.

* Azure의 Linux - [보증 배포판](endorsed-distros.md)
* SUSE- [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Azure Marketplace - CoreOS(Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - Azure Container Service with Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Azure에서 Linux 시작
Azure 사용을 시작하려면 Azure 계정, 설치된 Azure CLI, SSH 공용 및 개인 키 쌍이 필요합니다.

### <a name="sign-up-for-an-account"></a>계정 등록
Azure 클라우드를 사용하는 첫 번째 단계는 Azure 계정을 등록하는 것입니다.  시작하려면 [Azure 계정 등록](https://azure.microsoft.com/pricing/free-trial/) 페이지로 이동합니다.

### <a name="install-the-cli"></a>CLI 설치
새 Azure 계정에 사용하여 웹 기반 관리 패널인 Azure Portal에서 즉시 시작할 수 있습니다.  명령줄을 통해 Azure 클라우드를 관리하려면 `azure-cli`를 설치합니다.  Mac 또는 Linux 워크스테이션에 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치합니다.

### <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기
이제 Azure 계정, Azure 웹 포털 및 Azure CLI가 있습니다.  다음 단계는 암호를 사용하지 않고 Linux에 대한 SSH에 사용되는 SSH 키 쌍을 만드는 것입니다.  [Linux 및 Mac에서 SSH 키를 만들어](mac-create-ssh-keys.md) 암호 없는 로그인 및 향상된 보안을 사용하도록 설정합니다.

### <a name="create-a-vm-using-the-cli"></a>CLI를 사용하여 VM 만들기
CLI에서 Linux VM을 만들 경우 작업 중인 터미널을 빠져 나가지 않고 VM을 빠르게 배포할 수 있습니다.  웹 포털에서 지정할 수 있는 모든 사항은 명령줄 플래그 또는 스위치를 통해 사용할 수 있습니다.  

* [CLI를 사용하여 Linux VM 만들기](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>포털에서 VM 만들기
Azure 웹 포털에서 Linux VM을 만들 경우 배포를 진행하기 위한 다양한 옵션을 쉽게 가리키고 클릭할 수 있습니다.  명령줄 플래그 또는 스위치를 사용하는 대신, 다양한 옵션 및 설정으로 이루어진 멋진 웹 레이아웃을 볼 수 있습니다.  명령줄 인터페이스를 통해 사용 가능한 모든 사항은 포털에서도 사용할 수 있습니다.

* [포털을 사용하여 Linux VM 만들기](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>SSH를 사용하여 암호 없이 로그인
VM은 현재 Azure에서 실행되고 있으며 로그인할 수 있습니다.  암호를 사용하여 SSH를 통해 로그인하는 것은 안전하지 않으며 시간이 많이 소요됩니다.  SSH 키를 사용하는 것이 가장 안전하고 로그인하는 데 가장 빠른 방법입니다.  포털 또는 CLI를 통해 Linux VM을 만들 경우 두 가지 인증 중에서 선택해야 합니다.  SSH에 대한 암호를 선택하면 Azure에서 암호를 통한 로그인을 허용하도록 VM이 구성됩니다.  SSH 공개 키를 사용하기로 선택한 경우 Azure에서 SSH 키를 통한 로그인만 허용하도록 VM을 구성하고 암호 로그인은 사용할 수 없게 설정합니다. SSH 키 로그인만 허용하여 Linux VM을 보호하려면 포털 또는 CLI에서 VM을 만드는 동안 SSH 공개 키 옵션을 사용합니다.

## <a name="related-azure-components"></a>관련 Azure 구성 요소
## <a name="storage"></a>Storage
* [Microsoft Azure Storage 소개](../../storage/common/storage-introduction.md)
* [azure-cli를 사용하여 Linux VM에 디스크 추가](add-disk.md)
* [Azure Portal에서 Linux VM에 데이터 디스크를 연결하는 방법](attach-disk-portal.md)

## <a name="networking"></a>네트워킹
* [Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure에서 Linux VM에 포트 열기](nsg-quickstart.md)
* [Azure Portal에서 정규화된 도메인 이름 만들기](portal-create-fqdn.md)

## <a name="containers"></a>컨테이너
* [Azure Virtual Machines 및 컨테이너](containers.md)
* [Azure 컨테이너 서비스 소개](../../container-service/container-service-intro.md)
* [Azure 컨테이너 서비스 클러스터 배포](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>다음 단계
이제 Azure의 Linux를 대략적으로 이해하게 되었을 것입니다.  다음 단계로는 좀 더 깊이 들어가서 몇 개의 VM을 만들어 보겠습니다.

* [Azure CLI를 통해 일반 작업을 위한 샘플 스크립트 확장 목록 탐색](cli-samples.md)
