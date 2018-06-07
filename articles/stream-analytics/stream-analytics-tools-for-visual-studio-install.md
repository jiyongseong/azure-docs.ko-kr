---
title: Visual Studio용 Azure Stream Analytics 도구 설정
description: 이 문서에서는 설치 요구 사항 및 Visual Studio용 Azure Stream Analytics 도구를 설정하는 방법을 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio용 Azure Stream Analytics 도구 설치
Azure Stream Analytics 도구는 이제 Visual Studio 2017, 2015 및 2013을 지원합니다. 이 문서에서는 이 도구를 설치 및 제거하는 방법을 설명합니다.

도구 사용에 대한 자세한 내용은 [Visual Studio용 Stream Analytics 도구](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)를 참조하세요.

## <a name="install"></a>설치
### <a name="visual-studio-2017"></a>Visual Studio 2017
* [Visual Studio 2017(15.3 이상)](https://www.visualstudio.com/)을 다운로드합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다. 
* Stream Analytics 도구는 Visual Studio 2017에서 **Azure 개발**과 **데이터 저장 및 처리** 워크로드의 일부입니다. Visual Studio 설치의 일부로 이러한 두 워크로드 중 하나를 사용하도록 설정합니다.

다음과 같이 **데이터 저장 및 처리** 워크로드를 사용하도록 설정합니다.

![데이터 저장 및 처리 워크로드](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

다음과 같이 **Azure 개발** 워크로드를 사용하도록 설정합니다.

![Azure 개발 워크로드](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Visual Studio 2015 또는 Visual Studio 2013 업데이트 4를 설치합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다. 
* [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 .NET용 Microsoft Azure SDK 버전 2.7.1 이상을 설치합니다.
* [Azure Stream Analytics Tools for Visual Studio](http://aka.ms/asatoolsvs)를 설치합니다.



## <a name="update"></a>주 지역에서

### <a name="visual-studio-2017"></a>Visual Studio 2017
Visual Studio 알림 영역에 새 버전 미리 알림이 표시됩니다. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
설치된 Visual Studio용 Stream Analytics 도구는 새 버전을 자동으로 확인합니다. 팝업 창의 지침에 따라 최신 버전을 설치합니다. 


## <a name="uninstall"></a>제거

### <a name="visual-studio-2017"></a>Visual Studio 2017
Visual Studio 설치 관리자를 두 번 클릭하고 **수정**을 선택합니다. **데이터 저장 및 처리** 워크로드 또는 **Azure 개발** 워크로드에서 **Azure Data Lake 및 Stream Analytics 도구** 확인란을 선택 취소합니다.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
[제어판]으로 이동하고 **Microsoft Azure Data Lake 및 Visual Studio용 Stream Analytics 도구**를 제거합니다.





