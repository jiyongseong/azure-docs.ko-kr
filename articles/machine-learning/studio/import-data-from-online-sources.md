---
title: 온라인 데이터 원본에서 Machine Learning Studio로 데이터 가져오기 | Microsoft Docs
description: 다양한 온라인 원본에서 Azure Machine Learning Studio로 학습 데이터를 가져오는 방법.
keywords: 데이터 가져오기, 데이터 형식, 데이터 유형, 데이터 원본, 학습 데이터
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 5882f79b6479f71cfd1df503f55703e6177c072b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>데이터 가져오기 모듈을 사용하여 다양한 온라인 데이터 원본에서 Azure Machine Learning Studio로 데이터 가져오기
이 문서에서는 다양한 원본에서 온라인 데이터를 가져오는 기능에 대한 지원 및 이러한 원본의 데이터를 Azure Machine Learning 실험으로 이동하는 데 필요한 정보를 설명합니다.

> [!NOTE]
> 이 문서에서는 [데이터 가져오기][import-data] 모듈에 대한 일반 정보를 제공합니다. 액세스할 수 있는 데이터 형식, 형식, 매개변수 및 일반 질문의 응답에 대한 자세한 내용은 [데이터 가져오기][import-data] 모듈의 모듈 참조 항목을 참조하세요.
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>소개
[Azure Machine Learning Studio](https://studio.azureml.net/Home)에서 [데이터 가져오기][import-data] 모듈을 사용하여 실험을 실행하는 동안 여러 온라인 데이터 원본 중 하나의 데이터에 액세스할 수 있습니다.

* HTTP을 사용하는 웹 URL
* HiveQL을 사용하는 Hadoop
* Azure Blob 저장소
* Azure 테이블
* Azure SQL 데이터베이스 또는 Azure VM의 SQL Server
* 온-프레미스 SQL Server 데이터베이스
* 데이터 피드 공급자, OData 현재
* Azure Cosmos DB

Studio 실험에서 온라인 데이터 원본에 액세스하려면 [데이터 가져오기][import-data] 모듈을 추가하고 **데이터 원본**을 선택한 다음 데이터에 액세스하는 데 필요한 매개 변수를 제공합니다. 지원되는 온라인 데이터 원본을 아래 표에 항목별로 나타냈습니다. 또한 이 테이블은 지원되는 파일 형식 및 데이터에 액세스하는 데 사용되는 매개 변수도 요약합니다.

실험이 실행 중인 동안 이 학습 데이터에 액세스하므로 데이터는 해당 실험에서만 사용할 수 있습니다. 비교에 의해 데이터 집합 모듈에 저장된 데이터를 작업 영역의 아무 실험에나 사용할 수 있습니다.

> [!IMPORTANT]
> 현재 [데이터 가져오기][import-data] 및 [데이터 내보내기][export-data] 모듈은 클래식 배포 모델을 사용하여 만든 Azure Storage의 데이터를 읽고 쓸 수 있습니다. 다시 말해, 핫 저장소 액세스 계층 또는 쿨 저장소 액세스 계층을 제공하는 새로운 Azure Blob Storage 계정 유형은 아직 지원되지 않습니다. 
> 
> 일반적으로 이 서비스 옵션이 제공되기 전에 만들었을 수 있는 모든 Azure 저장소 계정은 영향을 받지 않습니다. 
> 새 계정을 만들려면 배포 모델에 대해 **클래식**을 선택하거나 Resource Manager를 사용하고 **계정 종류**에는 **Blob Storage** 대신 **범용**을 선택합니다. 
> 
> 자세한 내용은 [Azure Blob Storage: 핫 및 쿨 저장소 계층](../../storage/blobs/storage-blob-storage-tiers.md)을 참조하세요.
> 
> 

## <a name="supported-online-data-sources"></a>지원되는 온라인 데이터 원본
Azure Machine Learning **데이터 가져오기** 모듈은 다음과 같은 데이터 원본을 지원합니다.

| 데이터 원본 | 설명 | 매개 변수 |
| --- | --- | --- |
| HTTP를 통한 웹 URL |HTTP를 사용하는 어떤 웹 URL에서도 데이터를 쉼표로 구분된 값(CSV), 탭으로 구분된 값(TSV), 특성-관계 파일 형식(ARFF) 및 지원 벡터 컴퓨터(SVM-light) 형식으로 읽습니다. |<b>URL</b>: 사이트 URL과 파일 이름을 포함한 파일의 전체 이름을 확장자와 함께 지정합니다. <br/><br/><b>데이터 형식</b>: 지원되는 데이터 형식: CSV, TSV, ARFF 또는 SVM-light 중 하나를 지정합니다. 데이터에 머리글 행이 있는 경우 해당 행을 사용하여 열 이름을 할당합니다. |
| Hadoop/HDFS |Hadoop의 분산 저장소에서 데이터를 읽습니다. SQL과 유사한 쿼리 언어인 HiveQL을 사용하여 원하는 데이터를 지정합니다. 또한 HiveQL을 사용하여 데이터를 Machine Learning Studio에 추가하기 전에 데이터를 집계하고 데이터 필터링을 수행할 수도 있습니다. |<b>Hive 데이터베이스 쿼리</b>: 데이터 생성에 사용되는 Hive 쿼리를 지정합니다.<br/><br/><b>HCatalog 서버 URI </b>: *&lt;클러스터 이름&gt;.azurehdinsight.net.* 형식을 사용하여 클러스터의 이름을 지정합니다.<br/><br/><b>Hadoop 사용자 계정 이름</b>: 클러스터 프로비전에 사용되는 Hadoop 사용자 계정 이름을 지정합니다.<br/><br/><b>Hadoop 사용자 계정 암호</b>: 클러스터를 프로비전할 때 사용되는 자격 증명을 지정합니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](../../hdinsight/hdinsight-provision-clusters.md)를 참조하세요.<br/><br/><b>출력 데이터의 위치</b>: 데이터를 Hadoop 분산 파일 시스템(HDFS) 또는 Azure 중 어디에 저장할지 지정합니다. <br/><ul>출력 데이터를 HDFS에 저장하는 경우 HDFS 서버 URI를 지정합니다. (HHDInsight 클러스터 이름을 TTPS:// 접두사 없이 사용해야 합니다.) <br/><br/>Azure에 출력 데이터를 저장하는 경우 Azure Storage 계정 이름, Storage 액세스 키 및 Storage 컨테이너 이름을 지정해야 합니다.</ul> |
| SQL 데이터베이스 |Azure 가상 머신에서 실행되는 SQL Server 데이터베이스 또는 Azure SQL 데이터베이스에 저장된 데이터를 읽습니다. |<b>데이터베이스 서버 이름</b>: 데이터베이스가 실행되는 서버의 이름을 지정합니다.<br/><ul>Azure SQL Database인 경우 생성된 서비스 이름을 입력합니다. 일반적인 양식은 *&lt;generated_identifier&gt;.database.windows.net*입니다. <br/><br/>Azure Virtual Machines에서 호스트되는 SQL Server인 경우 *tcp:&lt;Virtual Machine DNS 이름&gt;, 1433*을 입력합니다.</ul><br/><b>데이터베이스 이름</b>: 서버의 데이터베이스 이름을 지정합니다. <br/><br/><b>서버 사용자 계정 이름</b>: 데이터베이스에 대한 액세스 권한이 있는 계정의 사용자 이름을 지정합니다. <br/><br/><b>서버 사용자 계정 암호</b>: 사용자 계정에 대한 암호를 지정합니다.<br/><br/><b>데이터베이스 쿼리</b>: 읽을 데이터를 설명하는 SQL 문을 입력합니다. |
| 온-프레미스 SQL 데이터베이스 |온-프레미스 SQL 데이터베이스에 저장된 데이터를 읽습니다. |<b>데이터 게이트웨이</b>: SQL Server 데이터베이스에 액세스할 수 있는 컴퓨터에 설치된 데이터 관리 게이트웨이의 이름을 지정합니다. 게이트웨이 설정에 대한 자세한 내용은 [온-프레미스 SQL Server 데이터베이스의 데이터를 사용하여 Azure Machine Learning을 통해 고급 분석 수행](use-data-from-an-on-premises-sql-server.md)을 참조하세요.<br/><br/><b>데이터베이스 서버 이름</b>: 데이터베이스가 실행되는 서버의 이름을 지정합니다.<br/><br/><b>데이터베이스 이름</b>: 서버의 데이터베이스 이름을 지정합니다. <br/><br/><b>서버 사용자 계정 이름</b>: 데이터베이스에 대한 액세스 권한이 있는 계정의 사용자 이름을 지정합니다. <br/><br/><b>사용자 이름 및 암호</b>: <b>값 입력</b>을 클릭하여 데이터베이스 자격 증명을 입력합니다. 온-프레미스 SQL Server가 구성된 방식에 따라 Windows 통합 인증 또는 SQL Server 인증을 사용할 수 있습니다.<br/><br/><b>데이터베이스 쿼리</b>: 읽을 데이터를 설명하는 SQL 문을 입력합니다. |
| Azure 테이블 |Azure Storage의 Table service에서 데이터를 읽습니다.<br/><br/>대량의 데이터를 가끔 읽는 경우 Azure 테이블 서비스를 사용해 보세요. 이는 유연하고, 비관계형(NoSQL)이고, 확장성이 매우 뛰어나고, 저렴하고, 가용성이 높은 저장소 솔루션을 제공합니다. |**데이터 가져오기**의 옵션은 공용 정보에 액세스하는지 로그인 자격 증명이 필요한 개인 저장소 계정에 액세스하는지에 따라 달라집니다. 이는 각각 자체의 매개 변수 집합을 가지고 있는 "PublicOrSAS" 또는 "Account"의 값을 가질 수 있는 <b>인증 유형</b>에 의해 결정됩니다. <br/><br/><b>공용 또는 공유 액세스 서명(SAS) URI</b>: 매개 변수는 다음과 같습니다.<br/><br/><ul><b>테이블 URI</b>: 테이블에 대한 공용 또는 SAS URL을 지정합니다.<br/><br/><b>속성 이름에 대해 검색할 행 지정</b>: 지정된 행 수를 검색하려면 값이 <i>TopN</i>이고 테이블의 모든 행을 가려오려면 값이 <i>ScanAll</i>입니다. <br/><br/>데이터가 같은 유형이고 예측이 가능한 경우 *TopN*을 선택하고 N에 해당하는 숫자를 입력하는 것이 좋습니다. 테이블이 큰 경우 이렇게 하면 읽기 시간이 단축될 수 있습니다.<br/><br/>데이터가 테이블의 깊이와 위치에 따라 달라지는 속성 집합으로 구조화되어 있는 경우 *ScanAll* 옵션을 선택하여 모든 행을 검색합니다. 이렇게 하면 결과로 얻는 속성과 메타데이터 변환의 무결성이 확보됩니다.<br/><br/></ul><b>개인 Storage 계정</b>: 매개 변수는 다음과 같습니다. <br/><br/><ul><b>계정 이름</b>: 읽을 테이블을 포함하는 계정의 이름을 지정합니다.<br/><br/><b>계정 키</b>: 계정과 연결된 저장소 키를 지정합니다.<br/><br/><b>테이블 이름</b>: 읽을 데이터를 포함하는 테이블의 이름을 지정합니다.<br/><br/><b>속성 이름에 대해 검색할 행</b>: 지정된 행 수를 검색하려면 값이 <i>TopN</i>이고 테이블의 모든 행을 가려오려면 값이 <i>ScanAll</i>입니다.<br/><br/>데이터가 같은 유형이고 예측이 가능한 경우 *TopN*을 선택하고 N에 해당하는 숫자를 입력하는 것이 좋습니다. 테이블이 큰 경우 이렇게 하면 읽기 시간이 단축될 수 있습니다.<br/><br/>데이터가 테이블의 깊이와 위치에 따라 달라지는 속성 집합으로 구조화되어 있는 경우 *ScanAll* 옵션을 선택하여 모든 행을 검색합니다. 이렇게 하면 결과로 얻는 속성과 메타데이터 변환의 무결성이 확보됩니다.<br/><br/> |
| Azure Blob Storage |Azure Storage의 Blob service에 저장된 이미지, 구조화되지 않은 텍스트 또는 이진 데이터 등의 데이터를 읽습니다.<br/><br/>또한 Blob service를 사용하여 데이터를 공용으로 표시하거나 응용 프로그램 데이터를 개인적으로 저장할 수 있습니다. HTTP 또는 HTTPS 연결을 사용하여 어디서나 데이터에 액세스할 수 있습니다. |**데이터 가져오기** 모듈의 옵션은 공용 정보에 액세스하는지 로그인 자격 증명이 필요한 개인 저장소 계정에 액세스하는지에 따라 달라집니다. 이것은 <b>인증 유형</b>에 따라 결정되며 "PublicOrSAS" 또는 "Account" 중 하나의 값을 가질 수 있습니다.<br/><br/><b>공용 또는 공유 액세스 서명(SAS) URI</b>: 매개 변수는 다음과 같습니다.<br/><br/><ul><b>URI</b>: Storage BLOB에 대한 공용 또는 SAS URL을 지정합니다.<br/><br/><b>파일 형식</b>: Blob service의 데이터 형식을 지정합니다. 지원되는 형식은 CSV, TSV 및 ARFF입니다.<br/><br/></ul><b>개인 Storage 계정</b>: 매개 변수는 다음과 같습니다. <br/><br/><ul><b>계정 이름</b>: 읽으려는 BLOB을 포함하는 계정의 이름을 지정합니다.<br/><br/><b>계정 키</b>: 계정과 연결된 저장소 키를 지정합니다.<br/><br/><b>컨테이너, 디렉터리 또는 BLOB 경로</b>: 읽을 데이터를 포함하는 BLOB의 이름을 지정합니다.<br/><br/><b>Blob 파일 형식</b>: Blob service의 데이터 형식을 지정합니다. 지원되는 데이터 형식은 CSV, TSV, ARFF, 지정된 인코딩의 CSV 및 Excel입니다. <br/><br/><ul>형식이 CSV 또는 TSV인 경우 파일에 머리글 행이 포함되는지 여부를 나타내야 합니다.<br/><br/>Excel 옵션을 사용하여 Excel 통합 문서에서 데이터를 읽을 수 있습니다. <i>Excel 데이터 형식</i> 옵션에서는 데이터가 Excel 워크시트 범위에 있는지 아니면 Excel 테이블에 있는지 여하를 나타냅니다. <i>Excel 시트 또는 포함된 테이블</i> 옵션에서는 읽을 시트 또는 테이블의 이름을 지정합니다.</ul><br/> |
| 데이터 피드 공급자 |지원되는 피드 공급자에서 데이터를 읽습니다. 현재 ODP(Open Data Protocol) 형식만 지원됩니다. |<b>데이터 콘텐츠 형식</b>: OData 형식을 지정합니다.<br/><br/><b>원본 URL</b>: 데이터 피드에 대한 전체 URL을 지정합니다. <br/>예를 들어 다음 URL은 Northwind 샘플 데이터베이스에서 읽습니다. http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>다음 단계

[데이터 가져오기 및 데이터 내보내기 모듈을 사용하는 Azure ML 웹 서비스 배포](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
