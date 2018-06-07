
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory는 데이터 통합 서비스입니다. 이를 통해 클라우드에서 데이터 기반 워크플로를 만들 수 있습니다. 워크플로는 하나 이상의 *파이프라인*으로 구현됩니다. 파이프라인은 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화합니다. 파이프라인은 데이터와 함께 다음 시퀀스를 수행할 수 있습니다.

1. 서로 다른 데이터 저장소에서 데이터를 수집합니다.
2. 다음과 같은 계산 서비스를 사용하여 데이터를 변환하거나 처리합니다.
    - Azure HDInsight Hadoop
    - Spark
    - Azure 데이터 레이크 분석
    - Azure 기계 학습
3. 출력 데이터를 데이터 저장소에 게시합니다.
    - 게시 대상은 BI(비즈니스 인텔리전스) 응용 프로그램에서 사용할 Azure SQL Data Warehouse일 수 있습니다. 

Data Factory를 사용하여 파이프라인을 예약할 수 있습니다.

