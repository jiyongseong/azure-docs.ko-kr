---
title: 'Azure Analysis Services 자습서 단원 1: 새 테이블 형식 모델 프로젝트 만들기 | Microsoft Docs'
description: 새 Azure Analysis Services 자습서 프로젝트를 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 73764e7595d5b0bbc55ee9242e86ba19d5f80254
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-tabular-model-project"></a>테이블 형식 모델 프로젝트 만들기

이 단원에서는 SSDT(SQL Server Data Tools)가 있는 Visual Studio를 사용하여 1400 호환성 수준에서 새 테이블 형식 모델 프로젝트를 만듭니다. 새 프로젝트가 만들어지면 데이터를 추가하고 모델을 작성할 수 있습니다. 이 단원에서는 Visual Studio의 테이블 형식 모델 작성 환경에 대해 간략히 소개합니다.  
  
이 단원을 완료하기 위한 예상 시간: **10분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델 작성 자습서의 첫 번째 단원입니다. 이 단원을 완료하기 위해 마련해야 하는 몇 가지 필수 구성 요소가 있습니다. 자세한 내용은 [Azure Analysis Services - Adventure Works 자습서](../tutorials/aas-adventure-works-tutorial.md)를 참조하세요.  
  
## <a name="create-a-new-tabular-model-project"></a>새 테이블 형식 모델 프로젝트 만들기  
  
#### <a name="to-create-a-new-tabular-model-project"></a>새 테이블 형식 모델 프로젝트를 만들려면  
  
1.  Visual Studio의 **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 차례로 클릭합니다.  
  
2.  **새 프로젝트** 대화 상자에서 **설치됨** > **Business Intelligence** > **Analysis Services**를 확장한 후 **Analysis Services 테이블 형식 프로젝트**를 클릭합니다.  
  
3.  **이름**에 **AW Internet Sales**를 입력한 후 프로젝트 파일의 위치를 지정합니다.  
  
    기본적으로 **솔루션 이름**은 프로젝트 이름과 같게 되지만 다른 솔루션 이름을 입력할 수 있습니다.  
  
4.  **확인**을 클릭합니다.  
  
5.  **테이블 형식 모델 디자이너** 대화 상자에서 **통합된 작업 영역**을 선택합니다.  
  
    작업 영역은 모델을 작성하는 동안 프로젝트와 동일한 이름을 가진 테이블 형식 모델 데이터베이스를 호스팅합니다. 통합된 작업 영역은 Visual Studio에서 기본 제공 인스턴스를 사용하므로 모델 작성만을 위해 별도의 Analysis Services 서버 인스턴스를 설치할 필요가 없음을 의미합니다.
      
6.  **호환성 수준**에서 **SQL Server 2017 / Azure Analysis Services(1400)**를 선택합니다.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    호환성 수준 목록 상자에 SQL Server 2017 / Azure Analysis Services(1400)가 표시되지 않으면 최신 버전의 SQL Server 데이터 도구를 사용하지 않는 것입니다. 최신 버전을 설치하려면 [SQL Server 데이터 도구 설치](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 참조하세요.  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>SSDT 테이블 형식 작성 환경 이해  
이제 새 테이블 형식 모델 프로젝트를 만들었으므로 Visual Studio에서 테이블 형식 모델 작성 환경에 대해 살펴보겠습니다.  
  
프로젝트가 만들어지면 Visual Studio에서 해당 프로젝트가 열립니다. 오른쪽의 **테이블 형식 모델 탐색기**에 모델에 있는 개체의 트리 보기가 표시됩니다. 데이터를 아직 가져오지 않았으므로 폴더는 비어 있습니다. 메뉴 모음처럼 작업을 수행하기 위해 개체 폴더를 마우스 오른쪽 단추로 클릭할 수 있습니다. 이 자습서를 단계별로 실행하는 동안 테이블 형식 모델 탐색기를 사용하여 모델 프로젝트의 다양한 개체로 이동합니다.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

**솔루션 탐색기** 탭을 클릭합니다. 여기에 **Model.bim** 파일이 표시됩니다. 왼쪽(Model.bim 탭이 있는 빈 창)에 디자이너 창이 표시되지 않으면 **솔루션 탐색기**의 **AW Internet Sales Project** 아래에서 **Model.bim** 파일을 두 번 클릭합니다. Model.bim 파일은 모델 프로젝트에 대한 메타데이터를 포함합니다. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
**Model.bim**을 클릭합니다. **속성** 창에 모델 속성이 표시되며 가장 중요한 속성은 **DirectQuery 모드**입니다. 이 속성은 모델이 메모리 내 모드(Off) 또는 DirectQuery 모드(On)로 배포되는지를 지정합니다. 이 자습서의 경우 메모리 내 모드로 모델을 작성 및 배포합니다.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
모델 프로젝트를 만들 때 **도구** 메뉴 > **옵션** 대화 상자에 지정할 수 있는 데이터 모델링 설정에 따라 특정 모델 속성이 자동으로 설정됩니다. 데이터 Backup, 작업 영역 보존 및 작업 영역 서버 속성은 작업 영역 데이터베이스가 백업되고 메모리 내 보존되며 빌드되는 방식과 위치를 지정합니다. 필요한 경우 나중에 이러한 설정을 변경할 수 있지만 지금은 이러한 속성을 그대로 둡니다.  

**솔루션 탐색기**에서 **AW Internet Sales**(프로젝트)를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. **AW Internet Sales 속성 페이지** 대화 상자가 나타납니다. 이러한 속성 중 일부는 모델을 배포할 때 나중에 설정합니다.  
  
SSDT를 설치한 경우 여러 가지 새로운 메뉴 항목이 Visual Studio 환경에 추가되었습니다. **모델** 메뉴를 클릭합니다. 여기에서 데이터를 가져오고, 작업 영역 데이터를 새로 고치며, Excel에서 모델을 찾아보고, 큐브 뷰 및 역할을 만들며, 모델 뷰를 선택하고 계산 옵션을 설정합니다. **테이블** 메뉴를 클릭합니다. 여기에서 관계를 만들고 관리할 수 있으며 날짜 테이블 설정을 지정하고 파티션을 만들며 테이블 속성을 편집할 수 있습니다. **열** 메뉴를 클릭하면 테이블에서 열을 추가 및 삭제하고 열을 고정하며 정렬 순서를 지정할 수 있습니다. 또한 SSDT는 모음에 일부 단추도 추가합니다. 가장 유용한 기능은 선택한 열에 대한 표준 집계 측정값을 만드는 자동 합계 기능입니다. 다른 도구 모음 단추는 자주 사용되는 기능과 명령에 대한 빠른 액세스를 제공합니다.  
  
테이블 형식 모델 작성과 관련된 다양한 기능에 대한 위치와 대화 상자 중 일부를 살펴봅니다. 일부 항목은 아직 활성화되지 않았지만 테이블 형식 모델 작성 환경에 대한 유용한 정보를 얻을 수 있습니다.  
  

## <a name="whats-next"></a>다음 작업
[단원 2: 데이터 가져오기](../tutorials/aas-lesson-2-get-data.md)

  
  
  
