---
title: Azure Linux VM에서 VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결 | Microsoft Docs
description: Linux VM이 예기치 않게 다시 부팅되는 문제 해결 방법입니다.
keywords: ssh 연결 거부, ssh 오류, azure ssh, SSH 연결 실패
services: virtual-machines-linux
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 29aecf748ddda280e6f42be0bb3c986d90a3d7f9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364423"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결

Azure VM(Virtual Machine)에 같은 저장소 계정의 VHD가 여러 개 연결되어 있으면 개별 저장소 계정의 확장성 목표가 초과되어 VM이 예기치 않게 다시 부팅될 수 있습니다. 저장소 계정의 확장성 목표를 초과하는 급증 현상이 있는 저장소 계정의 분 메트릭(**TotalRequests**/**TotalIngress**/**TotalEgress**)을 확인합니다. 저장소 계정에 대해 제한이 발생했는지 확인하는 데 도움이 필요하면 [메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError)을 참조하세요.

일반적으로 Virtual Machine에서 수행하는 VHD의 개별 입력 또는 출력 작업은 기본 페이지 Blob에서 **페이지 가져오기** 또는 **페이지 배치** 작업으로 변환됩니다. 따라서 환경에 대한 예상 IOPS를 사용하여 응용 프로그램의 특정 동작을 기준으로 단일 저장소 계정에 포함할 수 있는 VHD의 수를 조정할 수 있습니다. 단일 저장소 계정에 40개 이하의 디스크를 보유하는 것이 좋습니다. 저장소 계정의 확장성 목표, 특히 사용 중인 저장소 계정 유형에 대한 총 요청 속도 및 총 대역폭에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../../storage/common/storage-scalability-targets.md)를 참조하세요.

저장소 계정의 확장성 목표가 초과되는 경우에는 VHD를 여러 저장소 계정에 배치하여 개별 계정마다 작업량을 줄여야 합니다.
