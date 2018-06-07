---
title: "Azure에서 OpenShift 배포 문제 해결 | Microsoft Docs"
description: "Azure에서 OpenShift 배포 문제를 해결합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2017
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Azure에서 OpenShift 배포 문제 해결

OpenShift 클러스터가 성공적으로 배포되지 않으면 다음과 같은 문제 해결 작업을 시도하여 문제의 범위를 좁히십시오. 배포 상태를 보고 다음 종료 코드 목록과 비교합니다.

- 종료 코드 3: Red Hat 등록 사용자 이름 / 암호 또는 조직 ID / 활성화 키가 잘못되었습니다.
- 종료 코드 4: Red Hat 풀 ID가 잘못되었거나 사용 가능한 자격이 없습니다.
- 종료 코드 5: Docker 씬 풀 볼륨을 프로비전할 수 없습니다.
- 종료 코드 6: OpenShift 클러스터 설치에 실패했습니다.
- 종료 코드 7: OpenShift 클러스터 설치가 성공했지만 Azure 클라우드 솔루션 공급자 구성이 실패했습니다. - 마스터 노드의 마스터 구성 문제
- 종료 코드 8: OpenShift 클러스터 설치가 성공했지만 Azure 클라우드 솔루션 공급자 구성이 실패했습니다. - 마스터 노드의 노드 구성 문제
- 종료 코드 9: OpenShift 클러스터 설치가 성공했지만 Azure 클라우드 솔루션 공급자 구성이 실패했습니다. - 인프라 또는 앱 노드의 노드 구성 문제
- 종료 코드 10: OpenShift 클러스터 설치가 성공했지만 Azure 클라우드 솔루션 공급자 구성이 실패했습니다. - 마스터 노드를 수정 중이거나 마스터를 예약 불가능으로 설정할 수 없습니다.
- 종료 코드 11: 메트릭 배포에 실패했습니다.
- 종료 코드 12: 로깅 배포에 실패했습니다.

종료 코드 7~10의 경우 OpenShift 클러스터가 설치되었지만 Azure 클라우드 솔루션 공급자 구성이 실패했습니다. 마스터 노드(OpenShift 원점) 또는 요새 노드(OpenShift 컨테이너 플랫폼)로 SSH를 실행하고 거기에서 각 클러스터 노드에 SSH를 실행하여 문제를 해결할 수 있습니다.

종료 코드 7~9의 일반적인 실패 원인은 서비스 주체가 구독 또는 리소스 그룹에 대한 적절한 권한이 없는 것입니다. 이것이 문제가 되는 경우 올바른 권한을 할당하고 모든 후속 스크립트에서 실패한 스크립트를 수동으로 다시 실행합니다.

스크립트를 다시 실행하기 전에 실패한 서비스를 다시 시작해야 합니다(예: systemctl restart atomic-openshift-node.service).

추가 문제 해결을 위해 포트 2200(원점)의 마스터 노드 또는 포트 22(컨테이너 플랫폼)의 요새 노드에 SSH를 실행합니다. root에 있어야 하고(sudo su -) 다음 디렉터리로 이동해야 합니다. /var/lib/waagent/custom-script/download

여기에서 "0" 및 "1"이라는 폴더를 볼 수 있습니다. 각 폴더에 "stderr" 및 "stdout"이라는 두 개의 파일이 있습니다. 이 파일을 살펴보고 오류가 발생한 위치를 확인합니다.
