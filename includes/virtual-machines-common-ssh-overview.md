---
title: 포함 파일
description: 포함 파일
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 919582745d166cf8b3f3937f9bac4fc0dc1fe64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요

SSH는 안전하지 않은 연결에서 안전하게 로그인할 수 있도록 하는 암호화된 연결 프로토콜입니다. Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH 자체에서 암호화된 연결을 제공하지만 SSH 연결과 함께 암호를 하더라도 VM은 여전히 무차별 암호 대입 공격이나 암호 추측에 취약합니다. SSH를 사용하여 VM에 연결하는 데 있어 더 안전하고 선호하는 방법은 이러한 공개-개인 키 쌍, 즉 SSH 키를 사용하는 것입니다. 

* *공개 키*는 public-key 암호화를 사용하려는 Linux VM 또는 다른 서비스에 배치됩니다.

* *개인 키*는 사용자가 SSH 연결을 수행할 때 자신의 ID를 확인하도록 Linux VM에 제출하는 키입니다. 이 개인 키는 보호해야 하는 한편, 공유하면 안됩니다.

조직의 보안 정책에 따라 여러 Azure VM 및 서비스에 액세스하는 단일 공개-개인 키 쌍을 다시 사용할 수 있습니다. 각 VM 또는 서비스에 액세스하기 위해 별도의 키 쌍이 반드시 필요한 것은 아닙니다. 

다른 사람과 공개 키를 공유할 수 있지만 사용자(또는 로컬 보안 인프라)만이 개인 키를 소유합니다.