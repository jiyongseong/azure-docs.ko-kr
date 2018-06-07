---
title: Azure에서 Ansible 사용
description: Ansible을 사용하여 클라우드 프로비전, 구성 관리 및 응용 프로그램 배포를 자동화하는 방법을 소개합니다.
ms.service: ansible
keywords: Ansible, Azure, DevOps, 개요, 클라우드 프로비전, 구성 관리, 응용 프로그램 배포, Ansible 모듈, Ansible 플레이북
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="ansible-with-azure"></a>Azure에서 Ansible 사용

[Ansible](http://www.ansible.com)은 클라우드 프로비전, 구성 관리 및 응용 프로그램 배포를 자동화하는 오픈 소스 제품입니다. Ansible을 사용하면 가상 머신, 컨테이너 및 네트워크를 프로비전하고 클라우드 인프라를 완성할 수 있습니다. 또한 Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다.

이 문서에서는 Azure에서 Ansible을 사용할 때의 이점에 대한 기본적인 개요를 제공합니다.

## <a name="ansible-playbooks"></a>Ansible 플레이북

[Ansible 플레이북](http://docs.ansible.com/ansible/latest/playbooks.html)은 Ansible의 구성, 배포 및 오케스트레이션 언어입니다. 원격 시스템에서 적용할 정책 또는 일단의 일반 IT 프로세스 단계를 설명할 수 있습니다. 이 경우 플레이북을 만들 때 구성 또는 프로세스의 모델을 정의하는 YAML을 사용하면 됩니다.

## <a name="ansible-modules"></a>Ansible 모듈

Ansible에는 원격 호스트에서 직접 실행하거나 [플레이북](http://docs.ansible.com/ansible/latest/playbooks.html)을 통해 실행할 수 있는 [Ansible 모듈](http://docs.ansible.com/ansible/latest/modules_by_category.html) 제품군이 포함되어 있습니다. 또한 사용자는 자신의 모듈을 만들 수도 있습니다. 모듈은 서비스, 패키지 또는 파일과 같은 시스템 리소스를 제어하거나 시스템 명령을 실행하는 데 사용할 수 있습니다.

Azure 서비스와 상호 작용하기 위해 Ansible에는 Azure에서 인프라를 쉽게 만들고 오케스트레이션할 수 있는 도구를 제공하는 [Ansible 클라우드 모듈](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) 제품군이 포함되어 있습니다. 

## <a name="migrate-existing-workload-to-azure"></a>기존 작업을 Azure로 마이그레이션

인프라를 정의하기 위해 Ansible을 사용하면, 응용 프로그램의 플레이북을 적용하여 필요에 따라 Azure에서 사용자 환경의 크기를 자동으로 조정하도록 할 수 있습니다. 

## <a name="automate-cloud-native-application-in-azure"></a>Azure에서 클라우드 네이티브 응용 프로그램 자동화

Ansible을 사용하면 Azure 마이크로 서비스(예: [Azure Functions](https://azure.microsoft.com//services/functions/)와 [Azure의 Kuurenetes](https://azure.microsoft.com/services/container-service/kubernetes/))를 사용하여 Azure에서 클라우드 네이티브 응용 프로그램을 자동화할 수 있습니다.  

## <a name="manage-deployments-with-dynamic-inventory"></a>동적 인벤토리를 사용하여 배포 관리
Ansible은 [동적 인벤토리](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) 기능을 통해 Azure 리소스에서 인벤토리를 끌어오는 기능을 제공합니다. 그런 다음, 기존 Azure 배포에 태그를 지정하고, Ansible을 통해 이러한 태그가 지정된 배포를 관리할 수 있습니다.

## <a name="additional-azure-marketplace-options"></a>추가 Azure Marketplace 옵션
Red Hat의 [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) Azure Marketplace 이미지는 조직에서 IT 자동화의 크기를 조정하고 실제, 가상 및 클라우드 인프라에서 복잡한 배포를 관리하는 데 도움이 됩니다. Ansible Tower에는 오늘날의 기업에 필요한 가시성, 제어, 보안 및 효율성의 추가 수준을 제공하는 기능이 포함되어 있습니다. Ansible Tower는 Azure 및 SSH 키와 같은 자격 증명을 암호화하므로 자격 증명이 노출될 위험 없이 경험이 부족한 직원에게 작업을 위임할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Ansible 구성](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Linux VM 만들기](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)