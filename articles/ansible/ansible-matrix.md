---
title: Azure용 Ansible 모듈 및 버전 매트릭스
description: Azure용 Ansible 모듈 및 버전 매트릭스
ms.service: ansible
keywords: Ansible, 역할, 매트릭스, 버전, Azure, DevOps
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 03/25/2018
ms.topic: article
ms.openlocfilehash: 011cb173ffdecc7a22c2e470209719ccaf6bda58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 모듈 및 버전 매트릭스

## <a name="ansible-modules-for-azure"></a>Azure용 Ansible 모듈
Ansible은 원격 호스트에서 직접 또는 플레이북을 통해 실행할 수 있는 여러 모듈과 함께 제공됩니다.
이 문서에서는 가상 머신, 네트워킹 및 컨테이너 서비스와 같은 Azure 클라우드 리소스를 프로비전할 수 있는 Azure용 Ansible 모듈을 나열합니다. Ansible의 공식 릴리스 또는 Microsoft에서 게시한 다음 플레이북에서 이러한 모듈을 가져올 수 있습니다.

| Azure용 Ansible 모듈                   |  Ansible 2.4 |  Ansible 2.5 |  Playbook 역할 [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Compute**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | 예          | 예                         | 예                                 | 
| azure_rm_availabilityset_facts              | 예          | 예                         | 예                                 | 
| azure_rm_deployment                         | 예          | 예                         | 예                                 | 
| azure_rm_virtualmachine_scaleset_facts      | 예          | 예                         | 예                                 | 
| azure_rm_virtualmachineimage_facts          | 예          | 예                         | 예                                 | 
| azure_rm_resourcegroup                      | 예          | 예                         | 예                                 | 
| azure_rm_resourcegroup_facts                | 예          | 예                         | 예                                 | 
| azure_rm_virtualmachine                     | 예          | 예                         | 예                                 | 
| azure_rm_virtualmachine_extension           | 예          | 예                         | 예                                 | 
| azure_rm_virtualmachine_scaleset            | 예          | 예                         | 예                                 | 
| azure_rm_image                              |              | 예                         | 예                                 | 
| **네트워킹**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | 예          | 예                         | 예                                 | 
| azure_rm_virtualnetwork_facts               | 예          | 예                         | 예                                 | 
| azure_rm_subnet                             | 예          | 예                         | 예                                 | 
| azure_rm_networkinterface                   | 예          | 예                         | 예                                 | 
| azure_rm_networkinterface_facts             | 예          | 예                         | 예                                 | 
| azure_rm_publicipaddress                    | 예          | 예                         | 예                                 | 
| azure_rm_publicipaddress_facts              | 예          | 예                         | 예                                 | 
| azure_rm_dnsrecordset                       | 예          | 예                         | 예                                 | 
| azure_rm_dnsrecordset_facts                 | 예          | 예                         | 예                                 | 
| azure_rm_dnszone                            | 예          | 예                         | 예                                 | 
| azure_rm_dnszone_facts                      | 예          | 예                         | 예                                 | 
| azure_rm_loadbalancer                       | 예          | 예                         | 예                                 | 
| azure_rm_loadbalancer_facts                 | 예          | 예                         | 예                                 | 
| azure_rm_appgw                              | -            | -                           | 예                                 | 
| azure_rm_appgwroute                         | -            | -                           | 예                                 | 
| azure_rm_appgwroute                         | -            | -                           | 예                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | 예                                 |
| azure_rm_appgwroutetable                    | -            | -                           | 예                                 |
| azure_rm_securitygroup                      | 예          | 예                         | 예                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | 예                                 | 
| **Storage**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | 예          | 예                         | 예                                 | 
| azure_rm_storageaccount_facts               | 예          | 예                         | 예                                 | 
| azure_rm_storageblob                        | 예          | 예                         | 예                                 | 
| azure_rm_managed_disk                       | 예          | 예                         | 예                                 | 
| azure_rm_managed_disk_facts                 | 예          | 예                         | 예                                 | 
| **컨테이너**                    |           |                          |                                  | 
| azure_rm_acs                                | 예          | 예                         | 예                                 | 
| azure_rm_containerinstance                  | -            | 예                         | 예                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | 예                                 | 
| azure_rm_containerregistry                  | -            | 예                         | 예                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | 예                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | 예                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | 예                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | 예                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | 예                                 | 
| **Azure 기능**                    |           |                          |                                  | 
| azure_rm_functionapp                        | 예          | 예                         | 예                                 | 
| azure_rm_functionapp_facts                  | 예          | 예                         | 예                                 | 
| **데이터베이스**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | 예                         | 예                                 | 
| azure_rm_sqlserver_facts                    | -            | 예                         | 예                                 | 
| azure_rm_sqldatabase                        | -            | 예                         | 예                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | 예                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | 예                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | 예                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | 예                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | 예                                 | 
| azure_rm_mysqlserver                        | -            | 예                         | 예                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | 예                                 | 
| azure_rm_mysqldatabase                      | -            | 예                         | 예                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | 예                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | 예                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | 예                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | 예                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | 예                                 | 
| azure_rm_postgresqlserver                   | -            | 예                         | 예                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | 예                                 | 
| azure_rm_postgresqldatabase                 | -            | 예                         | 예                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | 예                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | 예                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | 예                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | 예                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | 예                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | 예                         | 예                                 |
| azure_rm_keyvault_facts                     | -            | -                           | 예                                 |
| azure_rm_keyvaultkey                        | -            | 예                         | 예                                 |
| azure_rm_keyvaultsecret                     | -            | 예                         | 예                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Azure에 대한 플레이북 역할 소개
[azure_preview_module 플레이북 역할](https://galaxy.ansible.com/Azure/azure_preview_modules/)은 가장 완벽한 역할로써 최신 Azure 모듈을 모두 포함합니다. 공식 Ansible 릴리스보다 업데이트 및 버그 수정이 더 적절하게 수행됩니다. Azure 리소스를 프로비전할 목적으로 Ansible를 사용하는 경우 azure_preview_module 역할을 설치하는 것이 좋습니다.

azure_preview_module 플레이북 역할은 3주마다 릴리스됩니다.

## <a name="next-steps"></a>다음 단계
플레이북 역할과 관련된 자세한 내용은 [다시 사용할 수 있는 플레이북 만들기](http://docs.ansible.com/ansible/latest/playbooks_reuse.html)를 참조하세요. 
