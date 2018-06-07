---
title: "Azure Data Lake Tools: Visual Studio Code를 사용한 U-SQL 로컬 실행 및 로컬 디버그 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code를 사용하여 로컬 실행 및 로컬 디버그하는 방법을 알아봅니다."
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview storage file,upload to storage path
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: d109e4d57f4ad5ab2be73805ba41bf9ed362cccb
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2017
---
# <a name="u-sql-local-run-and-local-debug-for-windows-with-visual-studio-code"></a>Visual Studio Code로 Windows에 대한 U-SQL 로컬 실행 및 로컬 디버그
이 문서에서는 로컬 개발 컴퓨터에서 U-SQL 작업을 실행하여 초기 코딩 단계를 빠르게 진행하거나 Visual Studio Code에서 로컬로 코드를 디버그하는 방법을 알아봅니다. Azure Data Lake Tools for Visual Studio Code에 대한 지침을 보려면 [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)을 참조하세요. 


## <a name="set-up-the-u-sql-local-run-environment"></a>U-SQL 로컬 실행 환경 설정

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 연 다음 **ADL: Download LocalRun Dependency**를 입력하여 패키지를 다운로드합니다.  

   ![ADL LocalRun Dependency 패키지 다운로드](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. **출력** 패널에 표시된 경로에서 종속성 패키지를 찾은 다음 BuildTools 및 Win10SDK 10240을 설치합니다. 다음은 예제 경로입니다.  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![종속성 패키지 찾기](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 **BuildTools**를 설치하려면 LocalRunDependency 폴더에서 visualcppbuildtools_full.exe를 클릭한 다음 마법사의 지시를 따릅니다.   

    ![BuildTools 설치](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 **Win10SDK 10240**을 설치하려면 LocalRunDependency/Win10SDK_10.0.10240_2 폴더에서 sdksetup.exe를 클릭한 후 마법사의 지시를 따릅니다.  

    ![Win10SDK 10240 설치](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. 환경 변수를 설정합니다. **SCOPE_CPP_SDK** 환경 변수를 설정합니다.  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>로컬 실행 서비스 시작 및 로컬 계정에 U-SQL 작업 제출 
첫 번째 사용자에 대해 [U-SQL 로컬 실행 환경을 설정](#set-up-the-u-sql-local-run-environment)하지 않은 경우 **ADL: Download Local Run Dependency**를 사용하여 로컬 실행 패키지를 다운로드합니다.

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 연 다음 **ADL: Start Local Run Service**를 입력합니다.   
2. **Accept**를 선택하여 처음으로 Microsoft 소프트웨어 사용 조건에 동의합니다. 

   ![Microsoft 소프트웨어 사용 조건에 동의](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. cmd 콘솔이 열립니다. 처음 사용하는 경우 **3**을 입력한 다음 데이터 입력 및 출력을 위한 로컬 폴더 경로를 찾아야 합니다. 다른 옵션은 기본값을 사용할 수 있습니다. 

   ![Data Lake Tools for Visual Studio Code가 cmd 로컬 실행](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Ctrl+Shift+P를 선택하여 명령 팔레트를 열고 **ADL: Submit Job**을 입력한 다음 **Local**을 선택하여 작업을 로컬 계정에 제출합니다.

   ![Data Lake Tools for Visual Studio Code 로컬 선택](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. 작업을 제출하면 제출 세부 정보를 볼 수 있습니다. 제출 세부 정보를 보려면 **Output** 창에서 **jobUrl**을 선택합니다. cmd 콘솔에서 작업 제출 상태를 볼 수도 있습니다. 작업 세부 정보에 대해 더 알고 싶은 경우 cmd 콘솔에 **7**을 입력합니다.

   ![Data Lake Tools for Visual Studio Code 로컬 실행 출력](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code 로컬 실행 cmd 상태](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>U-SQL 작업에 대한 로컬 디버그 시작  
첫 번째 사용자:

1. [U-SQL 로컬 실행 환경을 설정](#set-up-the-u-sql-local-run-environment)하지 않은 경우 **ADL: Download Local Run Dependency**를 사용하여 로컬 실행 패키지를 다운로드합니다.
2. .NET Core SDK 2.0을 설치하지 않은 경우 메시지 상자에 제시된 대로 설치합니다.
 
  ![Dotnet 설치 미리 알림](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Visual Studio Code용 C#을 설치하지 않은 경우 메시지 상자에 제시된 대로 설치합니다. **설치**를 클릭하여 계속 진행한 후 VSCode를 다시 시작합니다.

    ![C# 설치 미리 알림](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

로컬 디버그를 수행하려면 아래 단계를 따릅니다.
  
1. Ctrl+Shift+P를 선택하여 명령 팔레트를 연 다음 **ADL: Start Local Run Service**를 입력합니다. cmd 콘솔이 열립니다. **DataRoot**가 설정되어 있는지 확인합니다.
2. C# 코드 숨김에서 중단점을 설정합니다.
3. 스크립트 편집기로 돌아가서 마우스 오른쪽 단추를 클릭하고 **ADL: 로컬 디버그**를 선택합니다.
    
   ![Data Lake Tools for Visual Studio Code 로컬 디버그 결과](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>다음 단계
* [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)
* [VSCode에서 Python, R 및 CSharp를 사용하여 Azure Data Lake Analytics용 U-SQL 개발](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Azure Data Lake Analytics 작업용 U-SQL 어셈블리 개발](data-lake-analytics-u-sql-develop-assemblies.md)
* [PowerShell을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-powershell.md)
* [Azure 포털을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-portal.md)
* [U-SQL 응용 프로그램 개발에 Visual Studio용 데이터 레이크 도구 사용하기](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics(U-SQL) 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)
