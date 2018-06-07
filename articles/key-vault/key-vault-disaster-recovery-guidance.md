---
title: Azure Key Vault에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업 | Microsoft Docs
description: Azure Key Vault에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업에 대해 알아봅니다.
services: key-vault
documentationcenter: ''
author: adamglick
manager: mbaldwin
editor: ''
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: aglick
ms.openlocfilehash: 11c2fe5d4b84f99c3b0e303d1abeea73442f57aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault 가용성 및 중복성
Azure Key Vault에는 서비스의 개별 구성 요소가 실패해도 응용 프로그램에서 키 및 암호를 사용할 수 있도록 해주는 여러 계층의 중복성이 있습니다.

주요 자격 증명 모음의 내용은 지역 내에는 물론 동일한 지리 내에 150마일 이상 떨어진 보조 지역에도 복제됩니다. 따라서 키와 암호의 내구성이 매우 높습니다. 특정 지역 쌍에 대한 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 문서를 참조하세요.

주요 자격 증명 모음 서비스 내에서 개별 구성 요소가 실패하면 기능이 저하되지 않도록 하기 위해 해당 지역 내의 대체 구성 요소가 요청을 처리하도록 개입됩니다. 이 트리거에 어떤 조치도 취할 필요가 없습니다. 자동으로 발생하고 투명하게 보일 것입니다.

전체 Azure 지역을 사용할 수 없는 드문 경우, 해당 지역에 Azure Key Vault를 활용하는 요청이 보조 지역으로 자동 라우팅(*장애 조치*)됩니다. 기본 지역을 다시 사용할 수 있는 경우 요청은 주 지역으로 다시 라우팅(*장애 복구(failback)*)됩니다. 다시 한 번 말씀드리지만, 이 작업은 자동으로 이루어지므로 어떤 조치도 필요하지 않습니다.

알고 있어야 하는 몇 가지 주의 사항이 있습니다.

* 지역 장애 조치 시 서비스를 장애 조치하는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 중에 이루어진 요청은 장애 조치가 완료될 때까지 실패할 수 있습니다.
* 장애 조치가 완료되면 주요 자격 증명 모음은 읽기 전용 모드입니다. 이 모드에서 지원되는 요청은 다음과 같습니다.
  * 주요 자격 증명 모음 나열
  * 주요 자격 증명 모음 속성 가져오기
  * 암호 나열
  * 암호 가져오기
  * 키 나열
  * 키(키의 속성) 가져오기
  * 암호화
  * 암호 해독
  * 래핑
  * 래핑 취소
  * Verify
  * 로그인
  * Backup
* 장애 조치가 장애 복구되면 모든 요청 유형( 읽기 *및* 쓰기 요청 포함)을 사용할 수 있습니다.

