---
title: 신뢰할 수 있는 컬렉션 작업 | Microsoft Docs
description: 신뢰할 수 있는 컬렉션으로 작업하는 모범 사례를 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: 2568e116fdb3f80976d49787877d2ecf68f128ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210820"
---
# <a name="working-with-reliable-collections"></a>신뢰할 수 있는 컬렉션 작업
서비스 패브릭은 신뢰할 수 있는 컬렉션을 통해 .NET 개발자에게 사용할 수 있는 상태 저장 프로그래밍 모델을 제공합니다. 즉, 서비스 패브릭은 신뢰할 수 있는 사전 및 신뢰할 수 있는 큐 클래스를 제공합니다. 이러한 클래스를 사용하는 경우 상태가 분할되고(확장성의 경우) 복제되며(가용성의 경우) 파티션 내에서 트랜잭션 처리됩니다(ACID 의미 체계의 경우). 신뢰할 수 있는 사전 개체의 일반적인 사용을 살펴보고 실제로 어떤 역할을 하는지 확인하겠습니다.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

신뢰할 수 있는 사전 개체에 대한 모든 작업(취소할 수 없는 ClearAsync 실행 제외)은 ITransaction 개체를 필요로 합니다. 이 개체는 단일 파티션 내의 신뢰할 수 있는 모든 사전 및 신뢰할 수 있는 큐 개체에 시도하려는 모든 변경 사항과 관련됩니다. 파티션의 StateManager에 있는 CreateTransaction 메서드를 호출하여 ITransaction 개체를 인식합니다.

위 코드에서 ITransaction 개체를 신뢰할 수 있는 사전의 AddAsync 메서드에 전달합니다. 내부적으로 키를 허용하는 사전 메서드는 키와 관련된 판독기/기록기 잠금을 수행합니다. 메서드가 키 값을 수정하는 경우 메서드는 키에 쓰기 잠금을 사용하고 메서드가 키 값을 읽기만 하는 경우 키에 읽기 잠금이 수행됩니다. AddAsync가 키 값을 새로 전달된 값으로 수정하므로 키의 쓰기 잠금을 수행합니다. 따라서 2개(이상)의 스레드가 동시에 동일한 키 값을 추가하려는 경우 하나의 스레드가 쓰기 잠금을 인식하고 다른 스레드는 차단됩니다. 기본적으로 메서드는 잠금을 인식하기 위해 최대 4초 동안 차단되며 4초 후에 메서드는 TimeoutException을 throw합니다. 선호하는 경우 메서드 오버로드는 명시적인 시간 제한 값을 전달할 수 있습니다.

일반적으로 위 코드에서 보여준 대로 TimeoutException을 catch하고 전체 작업을 다시 시도하여 이에 반응하는 코드를 작성합니다. 간단한 코드에서 매번 100밀리초를 전달하는 Task.Delay를 호출하려 합니다. 그러나 실제로는 대신 일종의 지수 백오프 지연을 사용하는 것이 좋습니다.

잠금이 설정되면 AddAsync는 ITransaction 개체와 관련된 내부 임시 사전에 키와 값 개체 참조를 추가합니다. 고유한 쓰기 읽기 의미 체계를 제공하여 수행됩니다. 즉, AddAsync를 호출한 후에 트랜잭션을 커밋하지 않더라도 (동일한 ITransaction 개체를 사용하는) TryGetValueAsync에 대한 이후의 호출은 값을 반환합니다. 다음으로 AddAsync는 키 및 값 개체를 바이트 배열로 직렬화하고 로컬 노드의 로그 파일에 이러한 바이트 배열을 추가합니다. 마지막으로, AddAsync가 바이트 배열을 모든 보조 복제본에 보내므로 동일한 키/값 정보를 얻습니다. 키/값 정보가 로그 파일에 작성된 경우라도 연결되어 있는 트랜잭션을 커밋할 때까지 정보는 사전의 일부로 간주되지 않습니다.

위 코드에서 CommitAsync에 대한 호출은 모든 트랜잭션의 작업을 커밋합니다. 특히, 로컬 노드에서 로그 파일에 커밋 정보를 추가하고 모든 보조 복제본에 커밋 레코드를 보냅니다. 복제본의 쿼럼(다수)이 응답하면 모든 데이터 변경 사항이 영구적인 것으로 간주됩니다. ITransaction 개체를 통해 조작된 키와 관련된 모든 잠금이 해제되어 다른 스레드/트랜잭션이 동일한 키와 해당 값을 조작할 수 있게 됩니다.

(일반적으로 throw된 예외로 인해)CommitAsync를 호출하지 않으면 ITransaction 개체는 삭제됩니다. 커밋되지 않은 ITransaction 개체를 삭제하는 경우 서비스 패브릭은 중단 정보를 로컬 노드의 로그 파일로 추가하고 아무 것도 보조 복제본 중 하나에 전송되지 않아야 합니다. 그런 다음 트랜잭션을 통해 조작된 키와 관련된 모든 잠금을 해제합니다.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>일반적인 실수 및 방지하는 방법
신뢰할 수 있는 컬렉션이 내부적으로 작업하는 방법을 이해했으므로 이 중에서 몇 가지 일반적인 오용을 살펴보겠습니다. 아래 코드를 확인합니다.

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

일반 .NET 디렉터리로 작업할 경우 키/값을 사전에 추가한 다음 속성(예: LastLogin)의 값을 변경할 수 있습니다. 그러나 이 코드는 신뢰할 수 있는 사전으로 제대로 작동하지 않습니다. 이전 토론에서 AddAsync에 대한 호출이 바이트 배열에 키/값 개체를 직렬화한 다음 배열을 로컬 파일에 저장하고 보조 복제본에 보내기도 합니다. 나중에 속성을 변경하면 이는 메모리에서 이 속성의 값을 변경하고 복제본에 전송되는 로컬 파일 또는 데이터에 영향을 주지 않습니다. 프로세스가 충돌하는 경우 메모리의 내용이 삭제됩니다. 새 프로세스가 시작되거나 다른 복제본이 기본 복제본이 되는 경우 이전 속성 값을 사용할 수 있습니다.

위에 표시된 종류의 실수를 쉽게 저지를 수 있습니다. 그리고 프로세스가 다운되는 경우/때 실수에 대해 살펴봅니다. 코드를 작성하는 올바른 방법은 다음 두 줄을 반전하는 것입니다.


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

일반적인 실수를 보여주는 또 다른 예는 다음과 같습니다.

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

다시 일반 .NET 사전으로 위의 코드가 제대로 작동하며 이는 일반적인 패턴입니다. 개발자는 키를 사용하여 값을 조회합니다. 값이 있는 경우 개발자는 속성의 값을 변경합니다. 그러나 신뢰할 수 있는 컬렉션으로 이 코드는 **개체를 신뢰할 수 있는 컬렉션에 제공하면 수정해서는 안됩니다**에서 설명한 바와 같이 동일한 문제를 보여줍니다.

신뢰할 수 있는 컬렉션에서 값을 업데이트하는 올바른 방법은 기존 값에 대한 참조를 가져오고 변경할 수 없는 이 참조에서 참조하는 개체를 고려하는 것입니다. 그런 다음 원래 개체의 정확한 복사본인 새 개체를 만듭니다. 이제 이 새 개체의 상태를 수정하고 새 개체를 컬렉션에 작성하여 바이트 배열로 직렬화되며 이는 로컬 파일에 추가되고 복제본에 전송됩니다. 변경 내용을 커밋한 후에 메모리내 개체인 로컬 파일 및 모든 복제본은 정확히 동일한 상태입니다. 모두 정상입니다.

아래 코드는 신뢰할 수 있는 컬렉션에서 값을 업데이트하는 올바른 방법을 보여줍니다.

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>변경할 수 없는 데이터 형식을 정의하여 프로그래머 오류 방지
이상적으로는 변경할 수 없다고 간주되는 개체의 상태를 변경하는 코드를 실수로 생성한 경우 컴파일러가 오류를 보고할 수 있어야 합니다. 하지만 C# 컴파일러에는 이 작업을 수행하는 기능이 없습니다. 따라서 잠재적인 프로그래머 버그를 방지하려면 신뢰할 수 있는 컬렉션을 사용하여 사용할 형식을 변경할 수 없는 형식으로 정의하는 것이 좋습니다. 즉, 핵심 값 형식(예: 숫자[Int32, UInt64 등], DateTime, Guid, TimeSpan 등)을 사용해야 합니다. 물론 문자열도 사용할 수 있습니다. 컬렉션 속성을 직렬화 및 역직렬화하면 성능이 떨어질 수 있기 때문에 해당 속성을 방지하는 것이 좋습니다. 그러나 컬렉션 속성을 사용하려는 경우 .NET의 변경 불가능 컬렉션 라이브러리([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/))를 사용하는 것이 좋습니다. 이 라이브러리는 http://nuget.org에서 다운로드할 수 있습니다. 또한 클래스를 봉인하고 가능한 경우 읽기 전용 필드를 만드는 것이 좋습니다.

아래의 UserInfo 형식은 앞에서 설명한 권장 사항을 활용하는 변경할 수 없는 형식을 정의하는 방법을 보여줍니다.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

ItemId 형식은 다음과 같이 변경할 수 없는 형식이기도 합니다.

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>스키마 버전 관리(업그레이드)
내부적으로 신뢰할 수 있는 컬렉션은 .NET에 있는 DataContractSerializer를 사용하여 개체를 직렬화합니다. 직렬화된 개체는 기본 복제본의 로컬 디스크에 유지되고 보조 복제본에도 전송됩니다. 서비스가 완성되면 서비스에 필요한 데이터(스키마)의 종류를 변경할 가능성이 높습니다. 데이터의 버전 관리에 주의를 기울여서 접근해야 합니다. 무엇보다도 언제든 오래된 데이터를 역직렬화할 수 있어야 합니다. 즉, 역직렬화 코드는 이전 버전과 제한 없이 호환되어야 합니다. 서비스 코드의 버전 333은 5년 전에 서비스 코드의 버전 1이 신뢰할 수 있는 컬렉션에 배치한 데이터에서 작동할 수 있어야 합니다.

또한 서비스 코드는 한 번에 하나의 업그레이드 도메인을 업그레이드합니다. 따라서 업그레이드하는 동안 다른 두 버전의 서비스 코드를 동시에 실행합니다. 이전 버전의 서비스 코드가 새 스키마를 처리할 수 없을 수 있으므로 서비스 코드의 새 버전이 새 스키마를 사용하지 않도록 해야 합니다. 가능하면 서비스의 각 버전이 1버전에서 앞으로의 버전과 호환되도록 디자인해야 합니다. 즉, 서비스 코드의 V1이 명시적으로 처리하지 않는 스키마 요소를 무시할 수 있어야 합니다. 그러나 명시적으로 알지 못하는 모든 데이터를 저장하고 사전 키 또는 값을 업데이트하는 경우 다시 작성할 수 있어야 합니다.

> [!WARNING]
> 키의 스키마를 수정할 수 있는 반면 키의 해시 코드 및 같음 알고리즘이 안정적인지 확인해야 합니다. 이러한 알고리즘 중 하나의 작동 방법을 변경하면 신뢰할 수 있는 사전 내에서 키를 다시 검색할 수 없습니다.
>
>

또는 일반적으로 참조되는 요소를 2단계 업그레이드로 수행할 수 있습니다. 2단계 업그레이드로 V1에서 V2로 서비스를 업그레이드합니다. V2는 새 스키마 변경을 처리하는 방법을 알지만 이 코드가 실행하지 않는 코드를 포함합니다. V2 코드가 V1 데이터를 읽는 경우 해당 위치에서 작동하고 V1 데이터를 작성합니다. 그런 다음 모든 업그레이드 도메인에서 업그레이드가 완료되면 어떤 이유로든 실행 중인 V2 인스턴스에 업그레이드가 완료되었음을 알릴 수 있습니다. (이를 신호하는 한 가지 방법은 구성 업그레이드를 롤아웃하는 것이며 이는 2단계 업그레이드를 사용합니다.) 이제 V2 인스턴스는 V1 데이터를 읽고 V2 데이터에 변환하며 해당 위치에서 작업하고 V2 데이터로 작성할 수 있습니다. 다른 인스턴스가 V2 데이터를 읽을 경우 변환할 필요가 없이 해당 위치에서 작동하고 V2 데이터를 작성합니다.

## <a name="next-steps"></a>다음 단계
이후에 호환 가능한 데이터 계약 만들기에 대해 알아보려면 [이후 호환 가능한 데이터 계약](https://msdn.microsoft.com/library/ms731083.aspx)을 참조하세요.

데이터 계약을 버전 관리하는 모범 사례를 알아보려면 [데이터 계약 버전 관리](https://msdn.microsoft.com/library/ms731138.aspx)를 참조하세요.

내결함성 데이터 계약의 버전을 구현하는 방법을 알아보려면 [버전 내결함성 직렬화 콜백](https://msdn.microsoft.com/library/ms733734.aspx)을 참조하세요.

여러 버전에 상호 운용할 수 있는 데이터 구조를 제공하는 방법을 알아보려면 [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)를 참조하세요.
