---
title: Azure VM에서 SAP 솔루션 시작 | Microsoft Docs
description: Microsoft Azure의 VM(가상 머신)에서 실행되는 SAP 솔루션에 대한 자세한 정보
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54bb2857c18e273fe98f2a590068d73f80d2fad1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>SAP 워크로드 시나리오 호스팅 및 실행에 Azure 사용
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8
[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b
[deploy-template-cli]:../../../resource-group-template-deploy.md
[deploy-template-portal]:../../../resource-group-template-deploy.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c


[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056
[ha-guide]:sap-high-availability-guide.md
[ha-guide-get-started]:sap-high-availability-guide-start.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP 제품 가용성 매트릭스)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

SAP 준비 클라우드 파트너로 Microsoft Azure를 선택하여 중요 업무용 SAP 워크로드 및 시나리오를 확장 가능하고 규정을 준수하는 엔터프라이즈 입증 플랫폼에서 안정적으로 실행할 수 있습니다.  Azure의 확장성, 유연성 및 비용 절감을 활용하세요. Microsoft와 SAP 사이의 확장된 파트너 관계 덕분에 Azure에서 개발/테스트 및 프로덕션 시나리오 전체에서 SAP 응용 프로그램을 실행할 수 있으며 완전한 지원을 받을 수 있습니다. SAP NetWeaver에서 SAP S4/HANA, SAP BI로, Linux에서 Windows로, SAP HANA에서 SQL로, 모두 가능합니다.

Azure에 다양한 DBMS가 있는 SAP NetWeaver 시나리오 호스팅 외에, Azure의 SAP BI와 같은 다른 다양한 SAP 워크로드 시나리오를 호스팅할 수 있습니다. "Azure Virtual Machines의 SAP NetWeaver” 섹션에서 Azure 네이티브 Virtual Machines에 SAP NetWeaver 배포하기에 대한 문서를 찾을 수 있습니다.

Azure에는 SAP HANA를 활용하는 SAP 워크로드를 처리하기 위해 CPU와 메모리 리소스를 계속해서 늘리고 있는 네이티브 Azure Virtual Machine 제안이 있습니다. 이 영역에 대한 자세한 내용은 “Azure Virtual Machines의 SAP HANA” 섹션 아래의 문서를 검색하세요.

SAP HANA용 Azure의 고유한 특성은 Azure를 경쟁에서 차별화하는 고유한 요소입니다. SAP HANA와 관련된 메모리 및 CPU 리소스 요구량이 높은 SAP 시나리오를 호스팅하기 위해 Azure는 S/4HANA 또는 다른 SAP HANA 워크로드를 위해 최대 20TB(60TB 스케일 아웃)의 메모리가 필요한 SAP HANA 배포를 실행할 용도로 고객 전용 베어 메탈 하드웨어 사용을 제안합니다. 이 고유한 Azure의 SAP HANA Azure 솔루션(큰 인스턴스)을 통해 SAP 응용 프로그램 레이어 또는 워크로드 미들웨어 레이어를 네이티브 Azure Virtual Machines에 호스팅하면서 전용 베어 메탈 하드웨어에서 SAP HANA를 실행할 수 있습니다. 이 솔루션은 “Azure의 SAP HANA(큰 인스턴스)” 섹션의 여러 문서에 설명되어 있습니다.   

Azure의 SAP 워크로드 호스팅 시나리오에서는 Azure Activity Directory를 사용한 다양한 SAP 구성 요소 및 SAP SaaS 또는 PaaS 제품에 대한 ID 통합 및 Single-Sign-On 요구 사항도 발생할 수 있습니다. Azure Active Directory(AAD) 및 SAP 엔터티를 사용한 이러한 통합 및 Single-Sign-On 시나리오 목록은 "AAD SAP ID 통합 및 Single-Sign-On” 섹션에 설명 및 문서화되어 있습니다.


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA

### <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA의 개요 및 아키텍처
제목: Azure(큰 인스턴스)에서 SAP HANA의 개요 및 아키텍처

요약: 이 아키텍처 및 기술적 배포 가이드는 Azure(큰 인스턴스)에서 새 SAP HANA에 SAP를 배포하는 데 도움이 되는 정보를 제공합니다. SAP 솔루션의 특정 설치를 설명하는 포괄적인 가이드라기 보다는 초기 배포 및 진행 중인 작업에 대한 유용한 정보를 제공합니다. SAP HANA 설치와 관련된 SAP 설명서(또는 해당 영역을 다루는 여러 SAP 지원 참고 사항) 대신 이 문서를 사용하지 마세요. 이 가이드는 개요를 제공하고 Azure(큰 인스턴스)에서 SAP HANA를 설치하는 방법에 대한 추가 정보를 제공합니다.

업데이트 날짜: 2017년 10월

[이 가이드는 여기서 확인할 수 있습니다.](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결
제목: Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결

요약: 사용자와 Microsoft 엔터프라이즈 계정 팀 간에 Azure(큰 인스턴스)의 SAP HANA 구매가 완료된 후에 적절한 연결을 위해 다양한 네트워크 구성이 필요합니다.  이 문서에서는 다음 정보와 함께 공유되어야 하는 정보를 간단히 설명합니다. 또한 수집해야 하는 정보와 실행해야 하는 구성 스크립트에 대해서도 설명합니다.

업데이트 날짜: 2017년 10월

[이 가이드는 여기서 확인할 수 있습니다.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="install-sap-hana-in-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 설치
제목: Azure(큰 인스턴스)에서 SAP HANA 설치

요약: 이 문서에서는 Azure 큰 인스턴스에서 SAP HANA를 설치하기 위한 설치 절차를 설명합니다.

업데이트: 2017년 7월

[이 가이드는 여기서 확인할 수 있습니다.](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구
제목: Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구

요약: Azure(큰 인스턴스) 서버에서 업무상 중요한 SAP HANA를 실행할 때 HA(고가용성) 및 DR(재해 복구)은 중요한 측면입니다. SAP, 시스템 통합업체 및/또는 Microsoft와 협의하여 적합한 HA/DR 전략을 설계하고 구현하는 것이 중요합니다. 사용자 환경별로 RPO(복구 지점 목표) 및 RTO(복구 시간 목표)와 같은 중요한 사항으로 고려해야 합니다.  이 문서에서는 기본 수준의 HA 및 DR을 사용하도록 설정하기 위한 옵션에 대해 설명합니다.

업데이트 날짜: 2017년 10월

[이 문서는 여기에서 찾을 수 있습니다.](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="troubleshooting-and-monitoring-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA 문제 해결 및 모니터링
제목: Azure(큰 인스턴스)의 SAP HANA 문제 해결 및 모니터링

요약: 이 가이드에서는 Azure 환경에서 SAP HANA의 모니터링 설정 시 유용한 정보와 추가적인 문제 해결 정보를 제공합니다.

업데이트 날짜: 2017년 10월

[이 문서는 여기에서 찾을 수 있습니다.](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA

### <a name="getting-started-with-sap-hana-on-azure"></a>Azure에서 SAP HANA 시작
제목: Azure VM에서 SAP HANA 수동 설치에 대한 빠른 시작 가이드

요약: 이 빠른 시작 가이드를 사용하면 SAP NetWeaver 7.5 및 SAP HANA SP12를 수동으로 설치하여 Azure VM에서 단일 인스턴스 SAP HANA 시스템을 설정할 수 있습니다. 이 가이드는 독자가 JSON 템플릿 사용 옵션을 포함하여 Azure Portal 또는 Powershell/CLI를 통한 가상 머신이나 가상 네트워크 배포 방법과 같은 Azure IaaS 기본 사항에 대해 잘 알고 있다는 것을 전제로 합니다. 또한 독자가 SAP HANA, SAP NetWeaver 및 온-프레미스 설치 방법에 익숙하고

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.](hana-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="s4hana-sap-cal-deployment-on-azure"></a>Azure에서 S/4HANA SAP CAL 배포
제목: Azure에서 SAP S/4HANA 또는 BW/4HANA 배포

요약: 이 가이드는 SAP 클라우드 어플라이언스 라이브러리를 사용하여 Azure에서 SAP S/4HANA의 배포를 시연하는 데 도움이 됩니다. SAP 클라우드 어플라이언스 라이브러리는 Azure에서 SAP 응용 프로그램을 배포할 수 있게 하는 SAP에 의한 서비스입니다. 이 가이드에서는 배포를 단계별로 설명합니다.

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.](cal-s4h.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="high-availability-of-sap-hana-in-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA 고가용성
제목: Azure Virtual Machines의 SAP HANA 고가용성

요약: 이 가이드는 자동 장애 조치를 사용하여 HANA 시스템 복제를 수용하기 위해 SUSE 12 OS 및 SAP HANA의 고가용성 구성을 설명합니다. 이 가이드는 SUSE 및 Azure Virtual Machines에 해당됩니다. 이 가이드는 Red Hat이나 완전 또는 사설 클라우드나 다른 비 Azure 공용 클라우드 배포에 아직 적용되지 않습니다.

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.](sap-hana-high-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="sap-hana-backup-overview-on-azure-vms"></a>Azure VM의 SAP HANA 백업 개요
제목: Azure Virtual Machines의 SAP HANA Backup 가이드

요약: 이 가이드에서는 Azure Virtual Machines에서 SAP HANA를 실행하는 백업 가능성에 대한 기본 정보를 제공합니다.

업데이트: 2017년 3월

[이 가이드는 여기서 확인할 수 있습니다.](sap-hana-backup-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="sap-hana-file-level-backup-on-azure-vms"></a>Azure VM의 SAP HANA 파일 수준 백업
제목: 저장소 스냅숏에 기반한 SAP HANA 백업

요약: 이 가이드에서는 Azure Virtual Machines에서 SAP HANA를 실행하는 경우 Azure VM에서 스냅숏 기반 백업을 사용하는 방법에 대한 정보를 제공합니다.

업데이트: 2017년 3월

[이 가이드는 여기서 확인할 수 있습니다.](sap-hana-backup-file-level.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="sap-hana-snapshot-based-backups-on-azure-vms"></a>Azure VM의 SAP HANA 스냅숏 기반 백업
제목: 파일 수준의 SAP HANA Azure Backup

요약: 이 가이드에서는 Azure Virtual Machines에서 SAP HANA를 실행하는 SAP HANA 파일 수준 백업을 사용하는 방법에 대한 정보를 제공합니다.

업데이트: 2017년 3월

[이 가이드는 여기서 확인할 수 있습니다.](sap-hana-backup-storage-snapshots.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure Virtual Machines에서 배포된 SAP NetWeaver

### <a name="deploy-sap-ides-system-on-windows-and-sql-server-through-sap-cal-on-azure"></a>Azure에서 SAP CAL을 통해 Windows 및 SQL Server의 SAP IDES 시스템 배포
제목: Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트

요약: 이 문서에서는 SAP 클라우드 어플라이언스 라이브러리를 사용하여 Azure에서 Windows 및 SQL Server에 기반한 SAP IDES 시스템을 배포하는 방법을 설명합니다. SAP 클라우드 어플라이언스 라이브러리는 Azure에서 SAP 제품의 배포를 허용하는 SAP 서비스입니다. 이 문서는 SAP IDES 시스템의 배포 과정을 단계별로 진행합니다. IDES 시스템은 Microsoft Azure에서 SAP 클라우드 어플라이언스를 통해 배포할 수 있는 몇 가지 다른 여러 응용 프로그램의 예시일 뿐입니다.

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.](cal-ides-erp6-erp7-sp3-sql.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Azure의 SUSE Linux NetWeaver에 대한 빠른 시작 가이드
제목: Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트

요약: 이 문서에서는 Microsoft Azure SUSE Linux VM(가상 머신)에서 SAP NetWeaver를 실행할 때 고려해야 할 다양한 항목을 설명합니다. SAP NetWeaver는 Azure의 SUSE Linux VM에서 공식적으로 지원됩니다. Linux 버전, SAP 커널 버전 및 기타 세부 정보와 관련된 모든 세부 정보는 SAP 정보 1928533, "Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형"에서 찾을 수 있습니다.

업데이트 날짜: 2016년 9월

[이 가이드는 여기서 확인할 수 있습니다.](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>계획 및 구현
제목: SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현

요약: 이 문서는 Azure Virtual Machines에서 SAP NetWeaver를 실행하는 것을 고려하는 경우 시작하기 위한 가이드입니다. 이 계획 및 구현 가이드를 통해 기존 또는 계획된 SAP NetWeaver 기반 시스템을 Azure Virtual Machines 환경에 배포할 수 있는지 여부를 평가할 수 있습니다. 여러 SAP NetWeaver 배포 시나리오를 다루며 Azure에 고유한 SAP 구성을 포함합니다. 문서는 SAP/Azure 측면에서 하이브리드 SAP 환경을 실행하는 데 필요한 모든 구성 정보를 나열하고 설명합니다. IaaS에서 SAP NetWeaver 기반 시스템의 고가용성을 보장하기 위해 수행할 수 있는 측정값에 대해서도 다룹니다.

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.][planning-guide]

### <a name="high-availability-configurations-of-sap-netweaver-in-azure-vms"></a>Azure VM에서 SAP NetWeaver의 고가용성 구성
제목: SAP NetWeaver에 대한 Azure Virtual Machines 고가용성

요약: 이 문서에서는 Azure Resource Manager 배포 모델을 사용하여 Azure에서 고가용성 SAP 시스템을 배포하기 위해 사용할 수 있는 단계를 설명합니다. 다음과 같은 주요 작업을 진행하게 됩니다. 이 문서에서는 단일 실패 지점 구성 요소(예: ASCS(ABAP(고급 비즈니스 응용 프로그램 프로그래밍) SAP 중앙 서비스)/SCS(SAP 중앙 서비스) 및 DBMS(데이터베이스 관리 시스템)) 및 중복 구성 요소(예: SAP 응용 프로그램 서버)를 Azure VM에서 실행할 때 보호하는 방법에 대해 설명합니다. 이 문서에서는 Azure의 Windows Server 장애 조치(failover) 클러스터링 클러스터 및 SUSE Linux Enterprise Server Cluster Framework에서 고가용성 SAP 시스템을 설치하고 구성하는 단계별 예제를 보여줍니다.

업데이트 날짜: 2017년 10월

[이 가이드는 여기서 확인할 수 있습니다.][ha-guide-get-started]

### <a name="realizing-multi-sid-deployments-of-sap-netweaver-in-azure-vms"></a>Azure VM에서 SAP NetWeaver의 다중 SID 배포 인식
제목: SAP NetWeaver 다중 SID 구성 만들기

요약: 이 문서는 Azure VM에서 SAP NetWeaver의 고가용성 문서에 대한 추가본입니다. 2016년 9월에 도입된 Azure의 새로운 기능으로 인해 몇몇 Azure VM에서 여러 SAP NetWeaver ASCS/SCS 인스턴스를 배포할 수 있습니다. 이러한 구성을 사용하여 항상 사용 가능한 SAP NetWeaver 구성을 실현하기 위해 배포하는 데 필요한 VM 수를 줄일 수 있습니다. 이 가이드에서는 이러한 다중 SID 구성을 설치하는 방법을 설명합니다.

업데이트한 날짜: 2016년 12월

[이 가이드는 여기서 확인할 수 있습니다.](high-availability-multi-sid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>Azure VM에서 SAP NetWeaver 배포
제목: SAP NetWeaver에 대한 Azure Virtual Machines 배포

요약: 이 문서에서는 Azure의 가상 머신에 SAP NetWeaver 소프트웨어를 배포하기 위한 절차 지침을 제공합니다. 이 문서에서는 SAP용 Azure 모니터링 확장에 대한 권장 문제 해결 방법을 비롯한 SAP용 Azure 모니터링 확장 사용을 중점적으로 세 가지 특정 배포 시나리오에 중점을 둡니다. 이 문서에서는 계획 및 구현 가이드를 읽은 것을 가정합니다.

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.][deployment-guide]

### <a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>DBMS 배포 가이드
제목: SAP NetWeaver에 대한 Azure Virtual Machines DBMS 배포

요약: 이 문서에서는 SAP와 함께 실행해야 하는 DBMS 시스템에 대한 계획 및 구현 고려 사항을 살펴봅니다. 첫 번째 부분에서는 일반적인 고려 사항이 나열되고 표시됩니다. 문서의 다음 부분은 SAP에서 지원되는 Azure의 여러 DBMS 배포와 관련되어 있습니다. 제공되는 DBMS는 SQL Server, SAP ASE 및 Oracle입니다. 해당 특정 부분에서 해당 DBMS와 함께 Azure에서 SAP 시스템을 실행 중인 경우 고려해야 하는 고려 사항에 대해 설명합니다. Azure의 각 DMS에서 지원되는 백업 및 고가용성 방법과 같은 주제가 SAP 응용 프로그램과 함께 사용하기 위해 표시됩니다.

업데이트: 2017년 6월

[이 가이드는 여기서 확인할 수 있습니다.][dbms-guide]

### <a name="using-azure-site-recovery-for-sap-workload"></a>SAP 워크로드를 위한 Azure Site Recovery 사용
제목: SAP NetWeaver - Azure Site Recovery를 사용하여 재해 복구 솔루션 빌드

요약: 이 문서에서는 재해 복구 시나리오를 처리하는 데 Azure Site Recovery 서비스를 사용할 수 있는 방법을 설명합니다. Azure Site Recovery Services를 사용하여 온-프레미스 SAP 지형의 재해 복구 위치로 Azure를 사용하는 사례입니다. 문서에 설명된 다른 시나리오는 A2A(Azure 간) 재해 복구 사례이며, Azure Site Recovery를 사용하여 어떻게 관리하는지를 설명합니다.  

업데이트 날짜: 2017년 8월

[이 가이드는 여기서 확인할 수 있습니다.](http://aka.ms/asr-sap)
