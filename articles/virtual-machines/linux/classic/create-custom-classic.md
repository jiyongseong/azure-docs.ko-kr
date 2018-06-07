---
title: Azure CLI 1.0을 사용하여 클래식 Linux VM 만들기 | Microsoft Docs
description: Azure CLI 1.0에서 클래식 배포 모델을 사용하여 Linux 가상 머신을 만드는 방법에 대해 알아보기
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 6d3f0dd0c82ad32df4d6e17058d9b1bea57c301f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 클래식 Linux VM을 만드는 방법
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 버전은 [여기](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

이 항목에서는 Azure CLI 1.0에서 클래식 배포 모델을 사용하여 Linux VM(가상 컴퓨터)을 만드는 방법을 설명합니다. Azure에서 제공되는 **이미지** 의 Linux 이미지를 사용합니다. Azure CLI 1.0 명령은 특히 다음과 같은 구성 선택 항목을 제공합니다.

* 가상 네트워크에 VM 연결
* 기존 클라우드 서비스에 VM 추가
* 기존 저장소 계정에 VM 추가
* 가용성 집합 또는 위치에 VM 추가

> [!IMPORTANT]
> VM에서 가상 네트워크를 사용하여 호스트 이름으로 VM에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 VM을 만들 때 가상 네트워크를 지정해야 합니다. VM을 만드는 경우에만 가상 네트워크에 가입하도록 VM을 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 Linux VM을 만드는 방법
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

