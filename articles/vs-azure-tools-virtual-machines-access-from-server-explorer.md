---
title: 서버 탐색기에서 Azure Virtual Machines 액세스 | Microsoft Docs
description: Visure Studio에서 Azure 서버 탐색기 (VM) 만들기 및 관리하기를 보는 방법에 대한 개요를 가져옵니다.
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: a19f33c4dd2654538c5718d2cd7dbe5d018e4de1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>서버 탐색기에서 Azure Virtual Machines 액세스

Azure에서 호스팅되는 가상 머신이 있는 경우 서버 탐색기에서 액세스할 수 있습니다. 처음에 Azure 구독에 로그인해야 모바일 서비스를 볼 수 있습니다. 로그인하려면 서버 탐색기에서 Azure 노드에 대한 바로 가기 메뉴를 열고 **Microsoft Azure에 연결**을 선택할 수 있습니다.

1. 클라우드 탐색기에서 가상 머신을 선택한 다음 F4 키를 선택하여 속성 창을 표시합니다.

    다음의 테이블은 사용 가능하지만 모두 읽기 전용인 속성을 보여줍니다. 변경하려면 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)을 사용합니다.

   | 자산 | 설명 |
   | --- | --- |
   | DNS 이름 |가상 머신의 인터넷 주소 URL입니다. |
   | Environment |가상 머신에 대한 이 속성의 값은 항상 프로덕션입니다. |
   | Name |가상 머신의 이름입니다. |
   | 크기 |사용 가능한 디스크 공간과 메모리의 양이 반영된 가상 머신의 크기입니다. 자세한 내용은 [Virtual Machine 크기](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs)를 참조하세요. |
   | 상태 |값은 시작, 시작됨, 중지, 중지됨 및 상태 검색을 포함합니다. 상태 검색이 나타나면 현재 상태는 알 수 없습니다. 이 속성의 값은 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 사용된 값과 다릅니다. |
   | 구독 ID |Azure 계정에 대한 구독 ID 구독에 대한 속성을 확인하여 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 대한 이 정보를 표시할 수 있습니다. |
2. 끝점 노드를 선택한 다음 **속성** 창을 확인합니다.
3. 다음 테이블은 사용 가능하지만 읽기 전용인 끝점 속성을 설명합니다. 가상 머신에 대해 끝점을 추가하거나 편집하려면 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)을 사용합니다. 

   | 자산 | 설명 |
   | --- | --- |
   | Name |끝점에 대한 식별자입니다. |
   | 개인 포트 |응용 프로그램의 내부 네트워크 액세스 용 포트입니다. |
   | 프로토콜 |이 끝점에 대한 전송 계층이 사용하는 프로토콜로, TCP 또는 UDP입니다. |
   | 공용 포트 |응용 프로그램에 대한 공용 액세스에 사용되는 포트입니다. |
