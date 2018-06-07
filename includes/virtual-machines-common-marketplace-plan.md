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
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Marketplace 약관으로 이미지 배포

Azure Marketplace의 특정 VM 이미지를 프로그래밍 방식으로 배포하려면 동의가 필요한 추가 라이선스 및 구매 약관이 있습니다.  

이러한 이미지에서 VM을 배포하려면 이미지의 약관에 동의하고 프로그래밍 방식 배포를 사용하도록 설정해야 합니다. 이 작업은 구독에 대해 한 번만 수행하면 됩니다. 그런 다음, VM을 이미지에서 프로그래밍 방식으로 배포할 때마다 *구매 계획* 매개 변수를 지정해야 합니다.

다음 섹션에서는 수행 방법을 보여 줍니다.

* Marketplace 이미지에 추가 사용 조건이 있는지 확인 
* 프로그래밍 방식으로 약관에 동의
* 프로그래밍 방식으로 VM을 배포할 때 구매 계획 매개 변수 제공

