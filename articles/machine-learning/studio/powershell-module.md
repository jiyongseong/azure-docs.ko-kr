---
title: Machine Learning용 PowerShell 모듈 | Microsoft Docs
description: Azure Machine Learning용 PowerShell 모듈은 공개 미리 보기 모드로 사용할 수 있습니다. PowerShell을 사용하여 작업 영역, 실험, 웹 서비스 등을 만들고 관리합니다.
keywords: 실험, 선형 회귀, 기계 학습 알고리즘, 기계 학습 자습서, 예측 모델링 기술, 데이터 과학 실험
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: c9553e372f4d1cb5c60935fae5a7af61806ea6d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Microsoft Azure Machine Learning용 PowerShell 모듈
Azure Machine Learning용 PowerShell 모듈은 Windows PowerShell을 사용하여 작업 영역, 실험, 데이터 집합, 기존 웹 서비스 등을 관리할 수 있는 강력한 도구입니다.

[https://aka.ms/amlps](https://aka.ms/amlps)에서 설명서를 보고 전체 소스 코드와 함께 모듈을 다운로드할 수 있습니다. 

> [!NOTE]
> Azure Machine Learning PowerShell 모듈은 현재 미리 보기 모드입니다. 모듈은 이 미리 보기 기간 중에 계속 개선되고 확장됩니다. 뉴스 및 정보는 [Cortana Intelligence 및 Machine Learning 블로그](https://blogs.technet.microsoft.com/machinelearning/)를 확인하세요.

## <a name="what-is-the-machine-learning-powershell-module"></a>Machine Learning PowerShell 모듈이란?
Machine Learning PowerShell 모듈은 Windows PowerShell에서 Azure Machine Learning 작업 영역, 실험, 데이터 집합, 기존 웹 서비스 및 기존 웹 서비스 끝점을 완벽하게 관리할 수 있는 .NET 기반 DLL 모듈입니다. 

모듈과 함께 명확하게 구분된 [C# API 계층](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)을 포함하는 전체 소스 코드를 다운로드할 수 있습니다. 고유한 .NET 프로젝트에서 이 DLL을 참조하고 .NET 코드를 통해 Azure Machine Learning을 관리할 수 있습니다. 또한 DLL은 자주 사용하는 클라이언트에서 직접 활용할 수 있는 기본 REST API에 따라 달라집니다.

## <a name="what-can-i-do-with-the-powershell-module"></a>PowerShell 모듈을 사용해서 무엇을 할 수 있나요?
다음은 이 PowerShell 모듈로 수행할 수 있는 일부 작업입니다. 다음을 포함한 여러 기능은 [전체 설명서](https://aka.ms/amlps) 를 확인합니다.

* 관리 인증서를 사용한 새 작업 영역 프로비전([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* 실험 그래프를 나타내는 JSON 파일을 내보내기 및 가져오기([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 및 [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* 실험 실행([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* 예측 실험에서 웹 서비스 만들기([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* 게시된 웹 서비스에 끝점 만들기([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* RRS 또는 BES 웹 서비스 끝점 호출([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 및 [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

기존 실험을 실행하기 위해 PowerShell을 사용하는 간단한 예는 다음과 같습니다.

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

일반적으로 요청된 작업을 자동화하도록 PowerShell 모듈을 사용하는 방법에 대한 자세한 사용 사례는 [PowerShell을 사용하여 한 실험에서 여러 Machine Learning 모델 및 웹 서비스 끝점 만들기](create-models-and-endpoints-with-powershell.md)문서를 참조하세요.

## <a name="how-do-i-get-started"></a>어떻게 시작하나요?
Machine Learning PowerShell을 시작하려면 GitHub에서 [릴리스 패키지](https://github.com/hning86/azuremlps/releases)를 다운로드하고 [설치에 대한 지침](https://github.com/hning86/azuremlps/blob/master/README.md)을 따릅니다. 지침은 다운로드/압축 해제된 DLL의 차단을 해제한 다음 PowerShell 환경으로 가져오는 방법을 설명합니다. 대부분의 cmdlet에는 작업 영역 ID, 작업 영역 권한 부여 토큰 및 작업 영역이 위치한 Azure 지역을 제공해야 합니다. 값을 제공하는 가장 간단한 방법은 기본 config.json 파일을 통하는 것입니다. 지침은 또한 이 파일을 구성하는 방법을 설명합니다. 

원하는 경우 Visual Studio를 사용하여 git 트리 복제하고 코드를 수정하여 로컬에 컴파일할 수 있습니다.

## <a name="next-steps"></a>다음 단계
PowerShell 모듈에 대한 전체 설명서를 [https://aka.ms/amlps](https://aka.ms/amlps)에서 찾을 수 있습니다. 

실제 시나리오에서 모듈을 사용하는 방법의 확장된 예제는 자세한 사용 사례인 [PowerShell을 사용하여 한 실험에서 여러 Machine Learning 모델 및 웹 서비스 끝점을 만들기](create-models-and-endpoints-with-powershell.md)를 확인하세요.
