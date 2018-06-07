---
title: Azure VM에 연결된 디스크 문제 해결 | Microsoft Docs
description: Azure Blob 저장소는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 개체 데이터를 저장하도록 설계되었습니다. 응용 프로그램은 Blob 저장소의 개체를 PowerShell 또는 Azure CLI에서 액세스하거나, Azure Storage 클라이언트 라이브러리 또는 REST를 통해 코드에서 액세스할 수 있습니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: ae309efb95e2f633effcfb5723d8377f5e94d406
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779740"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure VM에 연결된 디스크 문제 해결 

Azure VM(Virtual Machines)은 OS 디스크 및 연결된 모든 데이터 디스크에 VHD(가상 하드 디스크)를 사용합니다. VHD는 하나 이상의 Azure Storage 계정에 페이지 Blob으로 저장됩니다. 이 문서에서는 VHD에서 발생할 수 있는 일반적인 문제 해결 방법을 설명합니다. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>VM의 저장소 리소스 삭제 오류 문제 해결

경우에 따라 연결된 VHD가 Resource Manager 배포의 VM에 들어 있을 때 저장소 리소스를 삭제하면 오류가 발생할 수 있습니다. 이 문제를 해결하기 위한 도움말은 다음 문서 중 하나를 참조하세요. 

  * Linux VM: [Resource Manager 배포의 저장소 삭제 오류](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows VM: [Resource Manager 배포의 저장소 삭제 오류](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결

많은 VHD가 연결된 VM이 예기치 않게 재부팅되는 경우 다음 문서 중 하나를 참조하세요.

  * Linux VM: [VHD가 연결된 VM이 예기치 않게 다시 부팅되는 경우](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows VM: [VHD가 연결된 VM이 예기치 않게 다시 부팅되는 경우](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
