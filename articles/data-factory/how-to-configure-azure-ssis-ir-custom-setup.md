---
title: Azure-SSIS 런타임 사용자 지정 설정 | Microsoft Docs
description: 이 문서에서는 Azure SSIS 통합 런타임에 사용자 지정 설정 인터페이스를 사용하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: b377b5ca9d46d66fe99a8f60383076920b098a7d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33769718"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 사용자 지정 설정

Azure-SSIS Integration Runtime의 사용자 지정 설정 인터페이스를 사용하면 기본 운영 구성이나 환경을 변경하거나(예: 추가 Windows 서비스 시작) 추가 구성 요소(예: 어셈블리, 드라이버, 확장)를 Azure-SSIS IR의 각 노드에 설치할 수 있습니다. 일반적으로 Azure-SSIS IR의 프로비전 또는 재구성 중에 자체 설정 단계를 추가하는 인터페이스를 제공하빈다.

스크립트 및 관련 파일을 준비하고 Azure Storage 계정에서 Blob 컨테이너에 업로드하여 사용자 지정 설정을 구성할 수 있습니다.  Azure-SSIS IR을 프로비전 또는 다시 구성할 때 컨테이너에 대해 SAS(Shared Access Signature) URI(Uniform Resource Identifier)를 제공합니다. 그러면 각 Azure-SSIS IR 노드가 스크립트와 관련 파일을 컨테이너에서 다운로드하고 사용자 지정 설정을 상승된 권한으로 실행합니다. 사용자 지정 설정이 완료되면 각 노드가 실행의 표준 출력과 다른 로그를 컨테이너에 업로드합니다.

무료 또는 라이선스 없는 구성 요소, 유료 또는 라이선스가 있는 구성 요소를 모두 설치할 수 있습니다. ISV인 경우 [Azure-SSIS IR에 대한 유료 또는 라이선스가 있는 구성 요소를 개발하는 방법](how-to-develop-azure-ssis-ir-licensed-components.md)을 참조하세요.


## <a name="current-limitations"></a>현재 제한 사항

-   GAC(전역 어셈블리 캐시)에 어셈블리를 설치하기 위해 `gacutil.exe`을 사용하려는 경우 사용자 지정 설치의 일부로서 제공하거나 공개 미리 보기 컨테이너에 제공된 복사본을 사용해야 합니다.

-   사용자 지정 설정을 통해 Azure-SSIS IR을 VNet에 연결해야 할 경우 Azure Resource Manager VNet만 지원됩니다. 일반 VNet은 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

Azure SSIS IR을 사용자 지정하려면 다음 항목이 필요합니다.

-   [Azure 구독](https://azure.microsoft.com/)

-   [Azure SQL 데이터베이스 또는 관리되는 인스턴스 서버](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Azure-SSIS IR 프로 비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure Storage 계정](https://azure.microsoft.com/services/storage/) 사용자 지정 설정을 위해 사용자 지정 설정 스크립트와 관련 파일을 Blob 컨테이너에 업로드 및 저장합니다. 사용자 지정 설정 프로세스에서는 실행 로그도 같은 Blob 컨테이너에 업로드합니다.

## <a name="instructions"></a>지침

2.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)(버전 5.4 이상)을 다운로드하여 설치합니다.

3.  사용자 지정 설정 스크립트 및 관련 파일(예: .bat, .cmd, .exe, .dll, .msi, 또는 .ps1 파일)을 준비합니다.

    1.  사용자 지정 설정의 진입점 역할을 하는 이름이 `main.cmd`인 스크립트 파일이 필요합니다.

    2.  다른 도구에서 생성한 추가 로그(예: `msiexec.exe`)를 컨테이너에 업로드하려는 경우 미리 정의된 환경 변수 `CUSTOM_SETUP_SCRIPT_LOG_DIR`을 스크립트에 로그 폴더로 지정합니다(예:  `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  [Azure Storage 탐색기](http://storageexplorer.com/)를 다운로드 및 설치하고 시작합니다.

    1.  **(로컬 및 연결)** 에서 **저장소 계정**을 마우스 오른쪽 단추로 선택하고 **Azure Storage에 연결**을 선택합니다.

       ![Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  **저장소 계정 이름과 키 사용**, **다음**을 차례로 선택합니다.

       ![저장소 계정 이름 및 키 사용](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Azure Storage 계정 이름과 키를 입력하고 **다음**을 선택하고 **연결**을 선택합니다.

       ![저장소 계정 이름 및 키 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  연결된 Azure Storage 계정에서 **Blob 컨테이너**를 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 선택한 다음, 새 컨테이너의 이름을 지정합니다.

       ![Blob 컨테이너 만들기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  새 컨테이너를 선택하고 사용자 지정 설정 스크립트와 관련 파일을 업로드합니다. 컨테이너의 다른 폴더가 아니라 컨테이너 최상위에 `main.cmd`를 업로드해야 합니다. 

       ![Blob 컨테이너에 파일 업로드](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  컨테이너를 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기**를 선택합니다.

       ![컨테이너에 대한 공유 액세스 서명 가져오기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  충분히 긴 만료 시간을 지정하고 읽기 + 쓰기 + 나열 권한이 있는 컨테이너에 대한 SAS URI를 만듭니다. Azure-SSIS IR 노드의 이미지를 다시 만들 때마다 사용자 지정 설정 스크립트 및 관련 파일을 다운로드하여 실행하기 위해 SAS URI가 필요합니다. 설정 실행 로그를 업로드하려면 쓰기 권한이 필요합니다.

       ![컨테이너에 대한 공유 액세스 서명 생성](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  컨테이너의 SAS URI를 복사하여 저장합니다.

       ![공유 액세스 서명 복사 및 저장](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  PowerShell로 Azure-SSIS IR을 프로비전 또는 다시 구성할 때 Azure-SSIS IR을 시작하기 전에 새 `SetupScriptContainerSasUri` 매개 변수에 대한 값으로 컨테이너의 SAS URI를 사용하여 `Set-AzureRmDataFactoryV2IntegrationRuntime` cmdlet을 실행합니다.  예: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  사용자 지정 설정이 Azure-SSIS IR 시시작을 마친 후 `main.cmd`의 표준 출력과 다른 실행 로그를 저장소 컨테이너의 `main.cmd.log` 폴더에서 확인할 수 있습니다.

2.  다른 사용자 지정 설정 예제를 보려면 Azure Storage Explorer를 통해 공개 미리 보기 컨테이너에 연결합니다.

    a.  **(로컬 및 연결)** 에서 **저장소 계정**을 마우스 오른쪽 단추로 클릭하고 **Azure Storage에 연결**을 선택한 다음, **연결 문자열 또는 공유 액세스 서명 URI**를 선택하고 **다음**을 선택합니다.

       ![공유 액세스 서명을 사용하여 Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    나.  **SAS URI 사용**을 선택하고 공개 미리 보기 컨테이너에 다음 SAS URI를 입력합니다. **다음**, **연결**을 차례로 선택합니다.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![컨테이너에 공유 액세스 서명 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    다. 연결된 공개 미리 보기 컨테이너를 선택하고 `CustomSetupScript` 폴더를 두 번 클릭합니다. 이 폴더에는 다음 항목이 있습니다.

       1. `Sample` 폴더: Azure-SSIS IR의 각 노드에 기본 작업을 설치하는 사용자 지정 설정을 포함합니다. 이 작업은 몇 초 동안 대기하면서 아무 것도 수행하지 않습니다. 폴더는 또한 `gacutil.exe`를 포함한 `gacutil` 폴더를 포함합니다.

       2. `UserScenarios` 폴더: 실제 사용자 시나리오에 대한 여러 사용자 지정 설정을 포함합니다.

    ![공개 미리 보기 컨테이너의 콘텐츠](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. `UserScenarios` 폴더를 두 번 클릭합니다. 이 폴더에는 다음 항목이 있습니다.

       1. `.NET FRAMEWORK 3.5` 폴더: Azure SSIS IR의 각 노드에서 사용자 지정 구성 요소에 필요할 수 있는 이전 버전의 .NET Framework를 설치하기 위한 사용자 지정 설치를 포함합니다.

       2. `BCP` 폴더: 대량 복사 프로그램(`bcp`) 등, Azure-SSIS IR의 각 노드에 SQL Server 명령줄 유틸리티를 설치하기 위한 사용자 지정 설정을 포함합니다(`MsSqlCmdLnUtils.msi`).

       3. `EXCEL` 폴더: Azure-SSIS IR의 각 노드에 오픈 소스 어셈블리(`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` 및`ExcelDataReader.dll`)를 설치하는 사용자 지정 설정을 포함합니다.

       4. `MSDTC` 폴더: Azure-SSIS IR의 각 노드에 MSDTC(Microsoft Distributed Transaction Coordinator) 인스턴스에 대한 네트워크 및 보안 구성을 수정하기 위한 사용자 지정 설치를 포함합니다.

       5. `ORACLE ENTERPRISE` 폴더: Azure-SSIS IR Enterprise Edition(비공개 미리 보기)의 각 노드에 Oracle OCI 드라이버를 설치하기 위한 사용자 지정 설정 스크립트(`main.cmd`) 및 자동 설치 구성 파일(`client.rsp`)을 포함합니다. 이 설정을 통해 Oracle Connection Manager, 원본 및 대상을 사용할 수 있습니다. 먼저 [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)에서 `winx64_12102_client.zip`을 다운로드한 다음, `main.cmd` 및 `client.rsp`와 함께 컨테이너에 업로드해야 합니다. TNS를 사용하여 Oracle에 연결할 경우, 설정 중에 Oracle 설치 폴더에 복사될 수 있게 `tnsnames.ora`도 다운로드하여 편집하고 컨테이너에 업로드해야 합니다.

       6. `ORACLE STANDARD` 폴더: Azure-SSIS IR의 각 노드에 Oracle ODP.NET 드라이버를 설치하는 사용자 지정 설정 스크립트(`main.cmd`)를 포함합니다. 이 설정을 통해 ADO.NET Connection Manager, 원본 및 대상을 사용할 수 있습니다. 먼저 [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)에서 `ODP.NET_Managed_ODAC122cR1.zip`을 다운로드한 다음, `main.cmd`와 함께 컨테이너에 업로드합니다.

       7. `SAP BW` 폴더: Azure-SSIS IR Enterprise Edition(비공개 미리 보기)의 각 노드에 SAP .NET 연결 어셈블리(`librfc32.dll`)를 설치하기 위한 사용자 지정 설정 스크립트(`main.cmd`)를 포함합니다. 이 설정을 통해 SAP BW Connection Manager, 원본 및 대상을 사용할 수 있습니다. 먼저, SAP 설치 폴더의 64비트 또는 32비트 `librfc32.dll` 버전을 컨테이너에 `main.cmd`와 함께 업로드합니다. 그러면 설정 중에 스크립트나 SAP 어셈블리를 `%windir%\SysWow64` 또는 `%windir%\System32` 폴더에 복사합니다.

       8. `STORAGE` 폴더: Azure-SSIS IR의 각 노드에 Azure PowerShell을 설치하는 사용자 지정 설정을 포함합니다. 이 설정을 통해 [PowerShell 스크립트를 실행하여 Azure Storgae 계정을 조작하는](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) SSIS 패키지를 배포 및 실행할 수 있습니다. `main.cmd`, 샘플 `AzurePowerShell.msi`(또는 최신 버전을 설치) 및 `storage.ps1` 을 컨테이너에 복사합니다. 패키지에 대해 PowerShell.dtsx를 템플릿으로 사용합니다. 패키지 템플릿은 수정 가능한 PowerShell 스크립트로 `storage.ps1`을 다운로드하는 [Azure Blob 다운로드 작업](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)과, 각 노드에서 스크립트를 실행하는 [프로세스 실행 작업](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)을 결합합니다.

       9. `TERADATA` 폴더: 사용자 지정 설정 스크립트(`main.cmd)`), 관련 파일(`install.cmd`) 및 설치 관리자 패키지(`.msi`)를 포함합니다. 이 파일은 Teradata 커넥터, TPT API 및 ODBC 드라이버를 Azure-SSIS IR Enterprise Edition(비공개 미리 보기)의 각 노드에 설치합니다. 이 설정을 통해 Teradata Connection Manager, 원본 및 대상을 사용할 수 있습니다. 먼저 [Teradata](http://partnerintelligence.teradata.com)에서 TTU(Teradata Tools and Utilities) 15.x zip 파일(예: `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`)을 다운로드한 다음, 위의 `.cmd` 및 `.msi` 파일과 함께 컨테이너에 업로드합니다.

    ![사용자 시나리오 폴더의 폴더](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. 이러한 사용자 지정 설정 샘플을 사용해 보려면 선택한 폴더의 콘텐츠를 복사하여 컨테이너에 붙여 넣습니다. PowerShell로 Azure-SSIS IR을 프로비전 또는 다시 구성할 때 새 `SetupScriptContainerSasUri` 매개 변수에 대한 값으로 컨테이너의 SAS URI를 사용하여 `Set-AzureRmDataFactoryV2IntegrationRuntime` cmdlet을 실행합니다. 

## <a name="next-steps"></a>다음 단계

-   [Azure-SSIS Integration Runtime Enterprise 버전](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Azure-SSIS Integration Runtime에 대한 유료 또는 라이선스 사용자 지정 구성 요소를 개발하는 방법](how-to-develop-azure-ssis-ir-licensed-components.md)