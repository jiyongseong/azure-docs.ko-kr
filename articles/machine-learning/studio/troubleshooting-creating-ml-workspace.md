---
title: '문제 해결: Machine Learning 작업 영역 만들기 및 연결 | Microsoft Docs'
description: Azure Machine Learning 작업 영역 만들기 및 연결과 관련된 일반적인 문제에 대한 해결 방법
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5c265b14a88e993512811de365f1ba51f7ba6028
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>문제 해결 가이드: Machine Learning 작업 영역 만들기 및 연결
이 가이드에서는 Azure Machine Learning 작업 영역을 설정할 때 자주 발생하는 몇 가지 문제에 대한 해결 방법을 제공합니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>작업 영역 소유자
Machine Learning Studio에서 작업 영역을 열려면 작업 영역을 만드는 데 사용한 Microsoft 계정으로 로그인하거나 작업 공간에 조인하라는 소유자의 초대를 받아야 합니다. Azure Portal에서 액세스를 구성하는 기능을 포함하여 작업 영역을 관리할 수 있습니다.

작업 영역을 관리하는 방법에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 관리]를 참조하세요.

[Azure Machine Learning 작업 영역 관리]: manage-workspace.md

## <a name="allowed-regions"></a>허용되는 지역
Machine Learning은 현재 제한된 지역에서 사용할 수 있습니다. 구독에 이들 지역 중 한 곳이 포함되어 있지 않으면 “허용되는 지역에 구독이 없습니다.”라는 오류 메시지가 나타납니다.

구독에 영역을 추가하도록 요청하려면 Azure Portal에서 Microsoft 지원 요청을 만들고 문제 유형으로 **청구**를 선택한 다음 프롬프트에 따라 요청을 제출합니다.

## <a name="storage-account"></a>Storage 계정
Machine Learning 서비스에서 데이터를 저장하려면 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나, 새 저장소 계정을 만들 수 있는 할당량이 있는 경우 새 Machine Learning 작업 영역을 만들 때 새 저장소 계정을 만들 수 있습니다.

새 Machine Learning 작업 영역이 만들어진 후에 작업 영역을 만드는 데 사용한 Microsoft 계정을 사용하여 Machine Learning Studio에 로그인할 수 있습니다. "작업 영역을 찾을 수 없음"(다음 스크린샷과 유사) 오류 메시지가 나타나는 경우 다음 단계를 사용하여 브라우저 쿠키를 삭제하세요.

![작업 영역을 찾을 수 없음][screen3]

**브라우저 쿠키를 삭제하려면**

1. Internet Explorer를 사용하는 경우 오른쪽 위에 있는 **도구** 단추를 클릭하고 **인터넷 옵션**을 선택합니다.  

![인터넷 옵션][screen4]

2. **일반** 탭에서 **삭제…**를 클릭합니다.

![일반 탭][screen5]

3. **검색 기록 삭제** 대화 상자에서 **쿠키 및 웹 사이트 데이터**가 선택되었는지 확인하고 **삭제**를 클릭합니다.

![쿠키 삭제][screen6]

쿠키를 삭제한 후 브라우저를 다시 시작하고 [Microsoft Azure Machine Learning](https://studio.azureml.net) 페이지로 이동합니다. 사용자 이름과 암호를 묻는 메시지가 표시되면 작업 영역을 만드는 데 사용한 동일한 Microsoft 계정을 입력합니다.

## <a name="comments"></a>설명

Microsoft는 Machine Learning 환경을 가능한 한 원활하게 만들기 위해 노력하고 있습니다. 의견이나 문제가 있을 경우 더 나은 서비스를 제공할 수 있도록 [Azure Machine Learning 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)에 게시해 주세요.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
