---
title: Azure 스택 (클라우드 연산자)에서 사용자 지정 마켓플레이스 항목을 게시 | Microsoft Docs
description: Azure 스택 연산자 Azure 스택에 사용자 지정 마켓플레이스 항목을 게시 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 4ea23ed01e6432f24024d7e8cc07c2dfe42ac639
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605572"
---
# <a name="the-azure-stack-marketplace-overview"></a>Azure 스택 마켓플레이스 개요

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

마켓플레이스는 서비스, 응용 프로그램 및 Azure 스택에 대 한 사용자 지정 된 리소스의 컬렉션입니다. 리소스에는 네트워크, 가상 컴퓨터, 저장소 및에 포함 됩니다. 여기로 사용자와 새 리소스를 만들 새 응용 프로그램을 배포 합니다. 것으로 생각 하면 쇼핑 카탈로그 사용자 찾아볼 수도 있고 사용 하려는 항목을 선택 합니다. 마켓플레이스 항목을 사용 하려면 사용자가 항목에 대 한 액세스를 부여 하는 서비스를 구독 해야 합니다.

Azure 스택 연산자로 결정 추가할 항목을 마켓플레이스에 (게시). 데이터베이스, 응용 프로그램 서비스 등과 같은 항목을 게시할 수 있습니다. 게시에 모든 사용자에 게 표시 되도록 만듭니다. 작성 한 사용자 지정 항목을 게시할 수 있습니다. 증가 하는 항목을 게시할 수도 있습니다 [Azure 마켓플레이스 항목 목록이](azure-stack-marketplace-azure-items.md)합니다. 마켓플레이스 항목을 게시할 때 사용자가 5 분 이내 볼 수 있습니다.

> [!Caution]  
> 이미지 및 json 파일 이라고 하는 모든 갤러리 항목 아티팩트는 Azure 스택 시장에서 사용할 수 있도록 후 인증 없이 액세스할 수입니다. 사용자 지정 마켓플레이스 항목을 게시 하는 경우 자세한 문제에 대해서 참조 [만들기 마켓플레이스 항목을 게시 하 고](azure-stack-create-and-publish-marketplace-item.md)합니다.

마켓플레이스를 열려면 선택에 관리 콘솔에서 **새로**합니다.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace 항목
Azure 스택 마켓플레이스 항목 서비스, 응용 프로그램 또는 사용자가 다운로드 하 고 사용할 수 있는 리소스입니다. 모든 Azure 스택 마켓플레이스 항목을 계획 및 제안 같은 관리 항목을 포함 하 여 모든 사용자에 게 표시 됩니다. 이러한 항목에 대 한 구독 보기 하지만 사용자에 게 기능 필요 하지 않습니다.

모든 Marketplace 항목은 다음이 있습니다.

* 리소스 프로비전을 위한 Azure 리소스 관리자 템플릿
* 문자열, 아이콘 및 기타 마케팅 참고 자료와 같은 메타데이터
* 포털에서 항목을 표시 하는 서식 지정 정보

마켓플레이스에 게시 된 모든 항목에는 Azure 갤러리 패키지 (.azpkg) 형식을 사용 합니다. 마켓플레이스 항목의 일부가 아니라 Azure 스택에 배포 또는 런타임 리소스 (예: 코드, 소프트웨어 또는 가상 컴퓨터 이미지와 zip 파일)를 개별적으로 추가 합니다. 

1803 이상 버전, Azure 스택 또는 사용자 지정 이미지를 업로드 하는 경우 Azure에서 사이트 설정을 다운로드 하는 경우 스파스 파일에 이미지를 변환 합니다. 이 프로세스는 시간 추가 이미지를 추가할 때 하지만 공간을 절약 하며 해당 이미지의 배포 빨라집니다. 변환 새 이미지에만 적용 됩니다.  기존 이미지는 변경 되지 않습니다. 

## <a name="next-steps"></a>다음 단계
[마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md)  
[만들기 및 마켓플레이스 항목을 게시](azure-stack-create-and-publish-marketplace-item.md)

