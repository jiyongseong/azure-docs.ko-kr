---
title: "Azure Cloud Storage에서 구조화되지 않은 데이터 검색"
description: "Azure Search를 사용하여 구조화되지 않은 데이터 검색"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>클라우드 저장소의 구조화되지 않은 데이터 검색

이 자습서에서는 Azure Blob에 저장된 데이터를 사용하는 [Azure Search](../../search/search-what-is-azure-search.md)를 사용하여 구조화되지 않은 데이터를 검색하는 방법에 대해 알아봅니다. 구조화되지 않은 데이터는 사전 정의된 방식으로 구성되어 있지 않거나 데이터 모델이 없는 데이터입니다. 예는 .txt 파일입니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹 만들기
> * 저장소 계정 만들기
> * 컨테이너 만들기
> * 컨테이너로 데이터 업로드
> * 포털을 통해 검색 서비스 만들기
> * 검색 서비스를 사용하여 컨테이너 검색

## <a name="download-the-sample"></a>샘플 다운로드

샘플 데이터 집합이 준비되어 있습니다. **[clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)을 다운로드**하고 해당 폴더에 압축을 풉니다.

이 샘플은 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)에서 가져온 텍스트 파일로 구성되어 있습니다. 예제 텍스트 파일로 사용하여 Azure를 통해 검색할 수 있습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Azure 포털](http://portal.azure.com) 에 로그인합니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

저장소 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위한 고유한 위치를 제공합니다.

현재는 두 가지 유형의 저장소 계정, **Blob**과 **범용**이 있습니다. 이 자습서에서는 **범용** 저장소 계정을 만듭니다.

범용 저장소 계정을 만드는 과정에 대해 잘 모르는 경우에는 다음 만드는 방법을 참조하세요.

1. 왼쪽 메뉴에서 **저장소 계정**을 선택하고 **추가**를 선택합니다.

2. 저장소 계정에 고유한 이름을 입력합니다. 

3. **배포 모델**에 **리소스 관리자**를 선택하고 **계정 종류** 드롭다운에서 **범용**을 선택합니다.

4. **복제** 드롭다운에서 **로컬 중복 저장소(LRS)**를 선택합니다.

5. **리소스 그룹**에서 **새로 만들기**를 선택하고 고유한 이름을 입력합니다.

6. 적합한 구독을 선택합니다.

7. 위치를 선택하고 **만들기**를 선택합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 폴더와 비슷하며 blob을 저장하는 데 사용됩니다.

이 자습서에서는 단일 컨테이너를 사용하여 clinicaltrials.gov에서 획득한 텍스트 파일을 저장합니다.

1. Azure Portal의 저장소 계정으로 이동합니다.

2. **Blob Service**에서 **Blob 찾아보기**를 선택합니다.

3. 새 컨테이너를 추가합니다.

4. 컨테이너 이름을 "data"로 지정하고 공용 액세스 수준으로 **컨테이너**를 선택합니다.

5. **확인**을 선택하여 컨테이너를 만듭니다. 

  ![구조화되지 않은 검색](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>예제 데이터 업로드

이제 컨테이너가 있으므로 예제 데이터를 업로드할 수 있습니다.

1. 컨테이너를 선택하고 **업로드**를 선택합니다.

2. 파일 필드 옆에 있는 파랑 아이콘을 선택하고 샘플 데이터를 추출한 로컬 폴더로 이동합니다.

3. 압축을 푼 파일을 모두 선택하고 **열기**를 선택합니다.

4. **업로드**를 선택하여 업로드 프로세스를 시작합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/upload.png)

업로드 프로세스는 시간이 걸릴 수 있습니다.

완료되면 데이터 컨테이너로 다시 이동하여 업로드된 텍스트 파일을 확인합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>검색 서비스 만들기

Azure Search는 개발자에게 웹, 모바일 및 엔터프라이즈 응용 프로그램에 대한 풍부한 검색 환경을 추가하기 위한 API 및 도구를 제공하는 SaaS(Search-as-a-Service) 클라우드 솔루션입니다.

검색 서비스를 만드는 과정에 대해 잘 모르는 경우에는 다음 만드는 방법을 참조하세요.

1. Azure Portal의 저장소 계정으로 이동합니다.

2. 아래로 스크롤하여 **BLOB SERVICE**에서 **Azure Search 추가**를 클릭합니다.

3. **데이터 가져오기**에서 **서비스 선택**을 선택합니다.

4. **새 검색 서비스를 만들려면 여기를 클릭**을 선택합니다.

5. **새 검색 서비스** 내에서 **URL** 필드에 검색 서비스의 고유한 이름을 입력합니다.

6. **리소스 그룹**에서 **기존 항목 사용**을 선택하고 이전에 만든 리소스 그룹을 선택합니다.

7. **가격 책정 계층**에 **무료** 계층을 선택하고 **선택**을 클릭합니다.

8. **만들기**를 선택하여 검색 서비스를 만듭니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>검색 서비스를 컨테이너에 연결

이제 검색 서비스가 있으므로 Blob 저장소에 연결할 수 있습니다. 이 섹션에서는 데이터 원본 선택, 인덱스 및 인덱서 만들기 과정을 안내합니다.

1. 저장소 계정으로 이동합니다.

2. **BLOB SERVICE**에서 **Azure Search 추가**를 클릭합니다.

3. **데이터 가져오기**에서 **Search Service**를 선택한 다음, 이전 섹션에서 만든 검색 서비스를 클릭합니다. 이렇게 하면 **새 데이터 원본**이 열립니다.

### <a name="new-data-source"></a>새 데이터 원본

  데이터 원본은 인덱싱할 데이터와 데이터 액세스 방법을 지정합니다. 데이터 원본을 동일한 검색 서비스에서 여러 번 사용할 수 있습니다.

1. 데이터 원본의 이름을 입력합니다. **추출할 데이터**에서 **콘텐츠 및 메타데이터**를 선택합니다. 데이터 원본은 인덱싱할 Blob의 부분을 지정합니다.
    
    a. 이후 자체 시나리오에서 **저장소 메타데이터만**을 선택할 수도 있습니다. 표준 Blob 속성 또는 사용자 정의 속성에 인덱싱되는 데이터를 제한하려는 경우 선택을 해야 합니다.
    
    b. 또한, **모든 메타데이터**를 선택하여 표준 Blob 속성 및 *모든* content-type 특정 메타데이터를 포함할 수도 있습니다. 

2. 사용하려는 Blob이 텍스트 파일이므로 **구문 분석 모드**를 **텍스트**로 설정합니다.
    
    a. 이후 자체 시나리오에서는 Blob의 콘텐츠에 따라 [다른 구문 분석 모드](../../search/search-howto-indexing-azure-blob-storage.md)를 선택할 수도 있습니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/datasources.png)

3. **저장소 컨테이너**를 선택하여 사용 가능한 저장소 계정을 나열합니다.

4. 저장소 계정을 선택한 다음 이전에 만든 컨테이너를 선택합니다.

5. **선택**을 클릭하여 **새 데이터 원본**으로 돌아가고 **확인**을 선택하여 계속합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>인덱스 구성

  인덱스는 검색할 수 있는 데이터 원본의 필드 컬렉션입니다. 인덱스는 데이터를 어떤 방식으로 검색해야 하는지 검색 서비스가 파악하는 방식입니다.

1. **데이터 가져오기**에서 **대상 인덱스 사용자 지정**을 선택합니다.
 
2. **인덱스 이름** 필드에 인덱스의 이름을 입력합니다.

3. **metadata_storage_name**에서 **조회 가능** 특성의 확인란을 선택합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/valuestoselect.png)

4. **확인**을 클릭하여 **인덱서 만들기**를 불러 옵니다.

인덱스의 매개 변수와 이러한 인덱스에 부여하는 특성은 중요합니다. 매개 변수는 *어떤* 데이터를 저장할지 지정하고 특성은 이러한 데이터를 *어떻게* 저장할지 지정합니다.

**필드 이름** 열에는 매개 변수가 포함되어 있습니다. 다음 표에 사용 가능한 특성과 해당 설명에 대한 목록이 나와 있습니다.

### <a name="field-attributes"></a>필드 특성
| 특성 | 설명 |
| --- | --- |
| *키* |문서 조회에 사용하는 각 문서의 고유 ID를 제공하는 문자열입니다. 모든 인덱스에는 하나의 키가 있어야 합니다. 필드 한 개만 키가 될 수 있으며, 이 필드 형식을 Edm.String으로 설정해야 합니다. |
| *조회 가능* |검색 결과에서 필드를 반환할 수 있는지 여부를 지정합니다. |
| *필터링 가능* |필드를 필터 쿼리에 사용하도록 허용합니다. |
| *정렬 가능* |쿼리에서 이 필드를 사용하여 검색 결과를 정렬할 수 있습니다. |
| *패싯 가능* |사용자 자기 주도형 필터링을 위해 필드를 패싯 탐색 구조에 사용할 수 있습니다. 일반적으로 여러 문서(예를 들어 단일 브랜드 또는 서비스 범주에 속하는 여러 문서)를 그룹화하는 데 사용할 수 있는 반복되는 값을 포함한 필드는 패싯으로 가장 적합합니다. |
| *검색 가능* |전체 텍스트 검색 가능 필드로 표시합니다. |


### <a name="create-an-indexer"></a>인덱서 만들기
    
  인덱서는 데이터 원본을 검색 인덱스와 연결하고 데이터를 다시 인덱싱하는 일정을 제공합니다.

1. **이름** 필드에 이름을 입력하고 **확인**을 선택합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/exindexer.png)

2. **데이터 가져오기**로 돌아온 후 **확인**을 선택하여 연결 과정을 완료합니다.

이제 Blob을 검색 서비스에 성공적으로 연결했습니다. 포털에서 인덱스가 채워지는 것을 보여주는 데 몇 분이 소요됩니다. 그러나 검색 서비스는 곧바로 검색을 시작할 수 있도록 즉시 인덱싱을 시작합니다.

## <a name="search-your-text-files"></a>텍스트 파일 검색

파일을 검색하려면 새로 만든 검색 서비스의 인덱스 내에서 검색 탐색기를 엽니다.

검색 단계는 검색 탐색기를 찾는 위치를 보여 주고 몇 가지 예제 쿼리를 제공합니다.

1. 모든 리소스로 이동하여 새로 만든 검색 서비스를 찾습니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/exampleurl.png)

3. 인덱스를 선택하여 엽니다. 

  ![구조화되지 않은 검색](media/storage-unstructured-search/overview.png)

4. **검색 탐색기**를 선택하여 데이터에 대한 라이브 쿼리를 할 수 있는 검색 탐색기를 엽니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/indexespane.png)

5. 쿼리 문자열 필드가 비어 있는 상태에서 **검색**을 선택합니다. 빈 쿼리는 Blob에서 *모든* 데이터를 반환합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>전체 텍스트 검색 

**쿼리 문자열** 필드에 "Myopia"를 입력하고 **검색**을 선택합니다. 파일 내용에 대한 검색을 개시하고 "Myopia"라는 단어가 포함된 하위 집합을 반환합니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>시스템 속성 검색

또한, `$select` 매개 변수를 사용하여 시스템 속성으로 검색하는 쿼리를 만들 수도 있습니다. `$select=metadata_storage_name`을 쿼리 문자열에 입력하고 Enter 키를 누르면 특정 필드만 반환합니다.
    
쿼리 문자열은 직접 URL을 수정하므로 공백은 허용되지 않습니다. 여러 필드를 검색하려면 쉼표를 사용하세요(예: `$select=metadata_storage_name,metadata_storage_path`).
    
`$select` 매개 변수는 인덱스 정의 시 조회 가능으로 표시된 필드에만 사용할 수 있습니다.

  ![구조화되지 않은 검색](media/storage-unstructured-search/metadatasearchunstructured.png) 

이제 이 자습서를 완료했으며 구조화되지 않은 데이터의 검색 가능한 집합이 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Search에서 다음 방법 등을 사용하여 구조화되지 않은 데이터를 검색하는 방법을 배웠습니다.

> [!div class="checklist"]
> * 리소스 그룹 만들기
> * 저장소 계정 만들기
> * 컨테이너 만들기
> * 컨테이너로 데이터 업로드
> * 검색 서비스 만들기
> * 검색 서비스를 사용하여 컨테이너 검색

이 링크를 따라 Azure Search로 문서를 인덱싱하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Search로 Azure Blob Storage에서 문서 인덱싱](../../search/search-howto-indexing-azure-blob-storage.md)