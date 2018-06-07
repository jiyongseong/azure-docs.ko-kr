---
title: "포함 파일"
description: "포함 파일"
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
## <a name="terminology"></a>용어

Azure의 Marketplace 이미지에는 다음과 같은 특성이 있습니다.

* **Publisher** - 이미지를 만든 조직입니다. 예: Canonical, MicrosoftWindowsServer
* **Offer** - 게시자가 만든 관련 이미지 그룹의 이름입니다. 예: Ubuntu Server, WindowsServer
* **SKU** - 제공의 인스턴스(예: 배포의 주 릴리스)입니다. 예: 16.04-LTS, 2016-Datacenter
* **Version** - 이미지 SKU의 버전 번호입니다. 

프로그래밍 방식으로 VM을 배포할 때 Marketplace 이미지를 식별하기 위해서, 이러한 값을 개별적인 매개변수로 제공하거나 일부 도구는 이미지 *URN*을 허용합니다. URN은 다음 값을 콜론(:) 문자로 구분해서 조합합니다. *Publisher*:*Offer*:*Sku*:*Version* URN에서 버전 번호를 "latest"로 바꿀 수 있으며, 그러면 이미지의 최신 버전이 선택됩니다. 

이미지 게시자가 추가 라이선스 및 구매 약관을 제공하는 경우에는, 해당 약관에 동의하고 프로그래밍 방식 배포를 사용하도록 설정해야 합니다. VM을 프로그래밍 방식으로 배포하는 경우에는 *구매 계획* 매개 변수도 제공해야 합니다. [Marketplace 약관으로 이미지 배포](#deploy-an-image-with-marketplace-terms)를 참조하세요.