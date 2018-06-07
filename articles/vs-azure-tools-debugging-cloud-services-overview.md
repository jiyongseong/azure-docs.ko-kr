---
title: Azure Cloud Services 디버깅 옵션 | Microsoft Docs
description: Azure Cloud Services 디버깅
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 3d559bea8043ebcde9ffc655b617f711bfe0ea7f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Azure 클라우드 서비스를 디버그하는 다양한 방법을 알아봅니다.
이 문서에서는 Azure 클라우드 서비스를 디버그하는 다양한 방법에 대한 링크를 제공합니다. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Visual Studio에서 Azure 클라우드 서비스 디버깅
Azure 계산 에뮬레이터를 사용하여 로컬 컴퓨터에서 클라우드 서비스 디버그하면 시간과 돈을 절약할 수 있습니다. 배포하기 전에 로컬로 서비스를 디버깅하면, 계산 시간이 소요되지 않고 안정성과 성능을 향상할 수 있습니다. 그러나, Azure에서 클라우드 서비스를 실행하는 경우, 일부 오류가 발생할 수 있습니다. Azure에서 클라우드 서비스를 실행할 때만 발생하는 오류는 서비스를 게시할 때 원격 디버깅을 사용하도록 설정한 다음 역할 인스턴스에 디버거를 연결하여 디버그할 수 있습니다. 자세한 내용은 [로컬 컴퓨터에서 클라우드 서비스 디버그](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)를 참조하세요.

## <a name="using-intellitrace"></a>IntelliTrace 사용 
Visual Studio를 사용하여 .NET Framework 4.5를 대상으로 하는 역할을 작성하는 경우, Visual Studio에서 Azure 클라우드 서비스를 배포할 때 IntelliTrace를 사용할 수 있습니다. IntelliTrace는 Azure에서 실행할 때처럼 응용 프로그램을 디버그하는 Visual Studio와 사용 가능한 로그를 제공합니다. 자세한 내용은 [IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅](http://go.microsoft.com/fwlink/p/?LinkId=623016)을 참조하십시오.

## <a name="remote-debugging"></a>원격 디버깅 
Visual Studio에서 클라우드 서비스를 배포할 때 클라우드 서비스를 원격 디버깅할 수 있습니다. 배포에 원격 디버깅 사용을 선택하면 원격 디버깅 서비스는 각 역할 인스턴스에 실행되는 가상 머신에 설치됩니다. `msvsmon.exe` 같은 서비스는 성능에 영향을 주지 않거나 추가 비용이 발생하지 않습니다. 자세한 내용은 [Azure에서 클라우드 서비스 디버그](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Visual Studio에서 Azure 클라우드 서비스 또는 VM 디버깅](./vs-azure-tools-debug-cloud-services-virtual-machines.md) - Azure Cloud Services를 디버그하는 방법을 자세히 알아봅니다.