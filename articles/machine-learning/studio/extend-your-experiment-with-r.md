---
title: R로 실험 확장 | Microsoft Docs
description: R 스크립트 실행 모듈을 사용하여 R 언어를 통해 Azure Machine Learning Studio의 기능을 확장하는 방법.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 99c57cc095c3eb017ada68417b7b36c8773ed3d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="extend-your-experiment-with-r"></a>R을 사용하여 실험 확장
[R 스크립트 실행][execute-r-script] 모듈을 사용하여 R 언어를 통해 Azure Machine Learning Studio의 기능을 확장할 수 있습니다.

이 모듈에서는 여러 입력 데이터 집합을 허용하고 출력으로 단일 데이터 집합을 생성합니다. [R 스크립트 실행][execute-r-script] 모듈의 **R 스크립트** 매개 변수에 R 스크립트를 입력할 수 있습니다.

다음과 비슷한 코드를 사용하여 모듈의 각 입력 포트에 액세스합니다.

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>현재 설치된 모든 패키지 나열
설치된 패키지 목록을 변경할 수 있습니다. [Azure Machine Learning에서 지원하는 R 패키지](https://msdn.microsoft.com/library/azure/mt741980.aspx)에 현재 설치된 패키지의 목록이 있습니다.

[R 스크립트 실행][execute-r-script] 모듈에 다음 코드를 입력하여 설치된 패키지의 완전한 현재 목록을 가져올 수도 있습니다.

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

[R 스크립트 실행][execute-r-script] 모듈의 출력 포트에 패키지의 목록을 전송합니다.
패키지 목록을 보려면 [CSV로 변환][convert-to-csv]과 같은 변환 모듈을 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력에 연결하고, 실험을 실행한 다음, 변환 모듈의 출력을 클릭하고 **다운로드**를 선택합니다. 

!["CSV로 변환" 모듈의 출력 다운로드](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>패키지 가져오기
[R 스크립트 실행][execute-r-script] 모듈에서 다음 명령을 사용하여 아직 설치되지 않은 패키지를 가져올 수 있습니다.

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

여기에서 `my_favorite_package.zip` 파일은 패키지를 포함합니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
