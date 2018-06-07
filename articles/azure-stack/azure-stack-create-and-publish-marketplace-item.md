---
title: 만들기 및 Azure 스택의 마켓플레이스 항목을 게시 | Microsoft Docs
description: 만들고 스택의 Azure 마켓플레이스 항목을 게시 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5e0349d6bae9295e7a0ba9f366f84753ebd838c2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Marketplace 항목 만들기 및 게시

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="create-a-marketplace-item"></a>Marketplace 항목 만들기
1. [다운로드](http://www.aka.ms/azurestackmarketplaceitem) Azure 갤러리 Packager 도구 및 샘플 Azure 스택 마켓플레이스 항목입니다.
2. 샘플 마켓플레이스 항목을 열고 이름 바꾸기는 **SimpleVMTemplate** 폴더입니다. (마켓플레이스 항목-예를 들어 동일한 이름을 사용 **Contoso.TodoList**.) 이 폴더에 포함 되어 있습니다.
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Azure 리소스 관리자 템플릿을 만들어](../azure-resource-manager/resource-group-authoring-templates.md) 또는 GitHub에서 템플릿을 선택 합니다. 마켓플레이스 항목 리소스를 만들려면이 템플릿을 사용 합니다.
4. 을 리소스를 성공적으로 배포할 수 있는지 확인 하기 위해 Microsoft Azure 스택 Api와 서식 파일을 테스트 합니다.
5. 서식 파일에는 가상 컴퓨터 이미지를 사용 하는 경우 지침에 따라 [가상 컴퓨터 이미지를 Azure 스택에 추가](azure-stack-add-vm-image.md)합니다.
6. Azure 리소스 관리자 서식 파일에 저장 된 **/Contoso.TodoList/DeploymentTemplates/** 폴더입니다.
7. 아이콘 및 마켓플레이스 항목에 대 한 텍스트를 선택 합니다. 아이콘을 추가 **아이콘** 폴더 텍스트를 추가 하 고는 **리소스** 파일에 **문자열** 폴더입니다. 아이콘에 대해 작게, 보통, 크게 및 넓게의 명명 규칙을 사용합니다. 참조 [마켓플레이스 항목 UI 참조](#reference-marketplace-item-ui) 자세한 설명에 대 한 합니다.
   
   > [!NOTE]
   > 모든 4 개의 아이콘 크기 (소형, 중형, 대형 넓은)는 마켓플레이스 항목을 올바르게 빌드하기 위한 필요 합니다.
   > 
   > 
8. 에 **manifest.json** 파일에서 변경 **이름** 마켓플레이스 항목의 이름으로 합니다. 또한 변경 **게시자** 이름이 나 회사입니다.
9. 아래 **아티팩트**, 변경 **이름** 및 **경로** 포함 하는 Azure 리소스 관리자 템플릿에 대 한 정보가 수정 합니다.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. 대체 **내 마켓플레이스 항목** 마켓플레이스 항목 표시 위치를 범주 목록이 있습니다.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. 모든 추가 편집 manifest.json 위한 참조 [참조: 마켓플레이스 항목 manifest.json](#reference-marketplace-item-manifestjson)합니다.
12. 폴더는.azpkg 파일을 패키지 하려면 명령 프롬프트를 열고 다음 명령을 실행:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > 전체 경로를 출력 패키지 존재 해야 합니다. 예를 들어 출력 경로가 C:\MarketPlaceItem\yourpackage.azpkg C:\MarketPlaceItem 폴더 존재 해야 합니다.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Marketplace 항목 게시
1. PowerShell 또는 Azure 저장소 탐색기를 사용 하 여 프로그램 마켓플레이스 항목 (.azpkg) Azure Blob 저장소에 업로드 합니다. 로컬 스택 Azure 저장소에 업로드 하거나 Azure 저장소에 업로드할 수 있습니다. (이 패키지에 대 한 임시 위치입니다.) Blob는 공개적으로 액세스할 수 있는지 확인 합니다.
2. Microsoft Azure 스택 환경에서 클라이언트 가상 컴퓨터에서 PowerShell 세션 서비스 관리자 자격 증명으로 설정 되어 있는지 확인 합니다. PowerShell에서 Azure 스택에 인증 하는 방법에 대 한 지침을 제공 [PowerShell과 함께 서식 파일을 배포](user/azure-stack-deploy-template-powershell.md)합니다.
3. 사용 하 여는 **추가 AzureRMGalleryItem** 스택에 Azure 마켓플레이스 항목을 게시 하려면 PowerShell cmdlet. 예: 
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | 매개 변수 | 설명 |
   | --- | --- |
   | 구독 ID |관리자 구독 id입니다. PowerShell을 사용 하 여 검색할 수 있습니다. 포털에서 가져올 하려는 공급자 구독으로 이동 하 고 구독 ID를 복사 |
   | GalleryItemUri |Blob 저장소에 이미 업로드 된 갤러리 패키지에 대 한 URI입니다. |
   | Apiversion |으로 설정 **2015-04-01**합니다. |
4. 포털로 이동 합니다. 이제는 연산자 또는 사용자가 포털에서 마켓플레이스 항목을 볼 수 있습니다.
   
   > [!NOTE]
   > 패키지에 나타나는 데 몇 분 정도 걸릴 수 있습니다.
   > 
   > 
5. 마켓플레이스 항목 이제 Azure 스택 마켓플레이스로 저장 되었습니다. Blob 저장소 위치에서 삭제할 수 있습니다.
6. 사용 하 여 마켓플레이스 항목을 제거할 수 있습니다는 **제거 AzureRMGalleryItem** cmdlet. 예:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > 마켓플레이스 UI 항목을 제거 하면 후 오류를 표시할 수 있습니다. 오류를 해결 하려면 **설정을** 포털에서입니다. 그런 다음 선택 **수정한 삭제** 아래 **포털 사용자 지정**합니다.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>마켓플레이스 항목 manifest.json 참조:
### <a name="identity-information"></a>ID 정보
| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| 이름 |X |문자열 |[A-Za-z0-9]+ | |
| 게시자 |X |문자열 |[A-Za-z0-9]+ | |
| 버전 |X |문자열 |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| DisplayName |X |문자열 |문자의 경우 80 자의 권장 사항 |문자의 경우 80 자 보다 긴 경우 포털 항목 이름 정상적으로 표시 되지 수도 있습니다. |
| PublisherDisplayName |X |문자열 |30 자의 권장 사항 |30 자 보다 긴 경우 포털 게시자 이름을 적절 하 게 표시 되지 수도 있습니다. |
| PublisherLegalName |X |문자열 |최대 256 자 | |
| 요약 |X |문자열 |60-100자 | |
| LongSummary |X |문자열 |140-256자 |아직에 해당 되지 않는 Azure 스택 합니다. |
| 설명 |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 5, 000 자 | |

### <a name="images"></a>이미지
마켓플레이스는 다음 아이콘을 사용합니다.

| 이름 | 너비 | 높이 | 메모 |
| --- | --- | --- | --- |
| 넓은 |255 px |115 px |항상 필요 |
| 큰 |115 px |115 px |항상 필요 |
| 중간 |90 px |90 px |항상 필요 |
| 작음 |40 픽셀 |40 픽셀 |항상 필요 |
| 스크린샷 |533 px |32 픽셀 |옵션 |

### <a name="categories"></a>범주
각 마켓플레이스 항목의 포털 UI에서 항목이 나타나는 식별 하는 범주와 태그를 지정 해야 합니다. Azure 스택에서 기존 범주 중 하나를 선택할 수 있습니다 (계산, 데이터 + 저장소 등) 하거나 새를 선택 합니다.

### <a name="links"></a>링크
각 마켓플레이스 항목 추가 내용에 대 한 다양 한 링크를 포함할 수 있습니다. 링크는 이름 및 URI 목록으로 지정됩니다.

| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| DisplayName |X |문자열 |최대 64 자 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>추가 속성
이전 메타 데이터 뿐 아니라 마켓플레이스 작성자는 다음 폼의 사용자 지정 키/값 쌍 데이터를 제공할 수 있습니다.

| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| DisplayName |X |문자열 |최대 25 자 | |
| 값 |X |문자열 |최대 30 자 | |

### <a name="html-sanitization"></a>HTML 삭제
HTML 수 있는 모든 필드에 대해 다음 요소와 특성이 허용 됩니다.

h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i

## <a name="reference-marketplace-item-ui"></a>참조: 마켓플레이스 항목 UI
아이콘 및 스택 Azure 포털에서 표시 된 대로 마켓플레이스 항목에 대 한 텍스트는 다음과 같습니다.

### <a name="create-blade"></a>블레이드 만들기
![블레이드 만들기](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace 항목 세부 정보 블레이드
![Marketplace 항목 세부 정보 블레이드](media/azure-stack-marketplace-item-ui-reference/image3.png)

