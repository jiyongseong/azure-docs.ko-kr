---
title: Azure 저장소 및 Visual Studio 연결 서비스 시작(WebJob 프로젝트)
description: Visual Studio 연결된 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio Azure WebJobs 프로젝트에서 Azure 테이블 저장소 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 78ef7d98d9e11ba9b2c52bfc88d6ecb050cafa84
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Azure Storage 시작(Azure WebJob 프로젝트)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>개요
이 문서에서는 Azure 테이블 저장소 서비스에서 Azure WebJobs SDK 버전 1.x를 사용하는 방법을 보여 주는 C# 코드 샘플을 제공합니다. 코드 샘플에서는 [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 버전 1.x를 사용합니다.

Azure 테이블 저장소 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.  자세한 내용은 [.NET을 사용하여 Azure 테이블 저장소 시작](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) 을 참조하세요.

일부 코드 조각에서는 **Table** 특성이 수동으로 호출된 함수, 즉 트리거 특성 중 하나를 사용하지 않고 호출된 함수에서 사용됩니다.

## <a name="how-to-add-entities-to-a-table"></a>테이블에 엔터티를 추가하는 방법
테이블에 엔터티를 추가하려면 **ICollector<T>** 또는 **IAsyncCollector<T>** 매개 변수와 함께 **Table** 특성을 사용합니다(여기서 **T**는 추가하려는 엔터티의 스키마를 지정). 특성 생성자는 테이블의 이름을 지정하는 문자열 매개 변수를 사용합니다.

다음 코드 샘플은 **Person** 엔터티를 *Ingress*라는 테이블에 추가합니다.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

일반적으로 **ICollector**에서 사용되는 유형은 **TableEntity**에서 파생되거나 **ITableEntity**를 구현하지만 반드시 그런 것은 아닙니다. 다음 **Person** 클래스 중 하나는 이전 **Ingress** 메서드에 표시된 코드와 함께 작동합니다.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Azure 저장소 API로 직접 작업하려는 경우 메서드 서명에 **CloudStorageAccount** 매개 변수를 추가할 수 있습니다.

## <a name="real-time-monitoring"></a>실시간 모니터링
데이터 수신 함수는 많은 양의 데이터를 처리하는 경우가 많기 때문에 WebJobs SDK 대시보드에서는 실시간 모니터링 데이터를 제공합니다. **호출 로그** 섹션에 함수가 여전히 실행 중인지 표시됩니다.

![수신 함수 실행](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**호출 세부 정보** 페이지에는 실행 중인 함수의 진행률(작성된 엔터티 수)이 보고되고 실행을 중단할 수 있는 기능을 제공합니다.

![수신 함수 실행](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

함수가 완료되면 **호출 세부 정보** 페이지에 작성된 행 수가 보고됩니다.

![수신 함수 완료](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>테이블에서 여러 엔터티를 읽는 방법
테이블을 읽으려면 **IQueryable<T>** 매개 변수와 함께 **Table** 특성을 사용합니다. 여기서 **T** 유형은 **TableEntity**에서 파생되거나 **ITableEntity**를 구현합니다.

다음 코드 샘플은 **Ingress** 테이블에서 모든 행을 읽고 기록합니다.

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>테이블에서 단일 엔터티를 읽는 방법
단일 테이블 엔터티에 바인딩할 때 파티션 키 및 행 키를 지정할 수 있는 추가 매개 변수 두 개가 포함된 **Table** 특성 생성자가 있습니다.

다음 코드 샘플은 큐 메시지에서 받은 파티션 키 및 행 키를 기반으로 **Person** 엔터티에 대한 테이블 행을 읽습니다.  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


이 예제의 **Person** 클래스는 **ITableEntity**를 구현할 필요가 없습니다.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>.NET 저장소 API를 직접 사용하여 테이블로 작업하는 방법
**CloudTable** 개체에서 **Table** 특성을 사용하여 테이블 작업을 보다 유연하게 수행할 수 있습니다.

다음 코드 샘플은 **CloudTable** 개체를 사용하여 *Ingress* 테이블에 단일 엔터티를 추가합니다.

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

**CloudTable** 개체를 사용하는 방법에 대한 자세한 내용은 [.NET을 사용하여 Azure 테이블 저장소 시작](../storage/storage-dotnet-how-to-use-tables.md)을 참조하세요.

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>큐 방법 문서에서 다루는 관련 항목
큐 메시지에 의해 트리거되는 테이블을 처리하는 방법 또는 테이블 처리에 특정하지 않은 WebJobs SDK 시나리오에 대한 자세한 내용은 [Azure 큐 저장소 및 Visual Studio 연결된 서비스(WebJob 프로젝트) 시작](../storage/vs-storage-webjobs-getting-started-queues.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 테이블 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 샘플을 제공했습니다. Azure Webjob 및 Webjob SDK를 사용하는 방법에 대한 자세한 내용은 [Azure WebJobs 설명서 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.

