<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>테이블 엔터티 관리

이제 테이블이 준비되었으므로 테이블에서 엔터티 또는 행을 관리하는 방법을 살펴보겠습니다. 

엔터티는 3개의 시스템 속성 즉, **PartitionKey**, **RowKey** 및 **Timestamp**를 포함하여 최대 255개의 속성을 포함할 수 있습니다. **PartitionKey** 및 **RowKey**의 값을 삽입 및 업데이트하는 작업은 사용자가 해야 합니다. 서버는 수정할 수 없는 **Timestamp**값을 관리합니다. **PartitionKey** 및 **RowKey**가 함께 테이블 내의 모든 엔터티를 고유하게 식별합니다.

* **PartitionKey**: 엔터티가 저장되는 파티션을 결정합니다.
* **RowKey**: 파티션 내에서 엔터티를 고유하게 식별합니다.

엔터티에 대한 사용자 지정 속성을 최대 252개 정의할 수 있습니다. 

### <a name="add-table-entities"></a>테이블 엔터티 추가

**Add-StorageTableRow**를 사용하여 테이블에 엔터티를 추가합니다. 이 예제에서는 값이 "partition1" 및 "partition2"인 파티션 키와 주 약어와 같은 행 키를 사용합니다. 각 엔터티의 속성은 username과 userid입니다. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>테이블 엔터티 쿼리

테이블의 엔터티를 쿼리하는 데에는 여러 가지 다른 방법이 있습니다.

#### <a name="retrieve-all-entities"></a>모든 엔터티 검색

모든 엔터티를 검색하려면 **Get-AzureStorageTableRowAll**을 사용합니다.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

이 명령은 다음 표와 비슷한 결과를 생성합니다.

| userId | 사용자 이름 | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>특정 파티션에 대한 엔터티 검색

특정 파티션의 모든 항목을 검색하려면 **Get-AzureStorageTableRowByPartitionKey**를 사용합니다.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
결과는 다음 표와 비슷합니다.

| userId | 사용자 이름 | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>특정 열의 특정 값에 대한 엔터티 검색

특정 열의 값이 특정 값과 동일한 엔터티를 검색하려면 **Get-AzureStorageTableRowByColumnName**을 사용합니다.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

이 쿼리는 하나의 레코드를 검색합니다.

|필드|값|
|----|----|
| userId | 1 |
| 사용자 이름 | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>사용자 지정 필터를 사용하여 엔터티 검색 

사용자 지정 필터를 사용하여 엔터티를 검색하려면 **Get-AzureStorageTableRowByCustomFilter**를 사용합니다.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

이 쿼리는 하나의 레코드를 검색합니다.

|필드|값|
|----|----|
| userId | 1 |
| 사용자 이름 | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>엔터티 업데이트 

엔터티를 업데이트하는 세 가지 단계가 있습니다. 먼저, 변경할 엔터티를 검색합니다. 둘째, 엔터티를 변경합니다. 셋째, **Update-AzureStorageTableRow**를 사용하여 변경 내용을 커밋합니다.

엔터티를 username = 'Jessie'로 업데이트하면 username = 'Jessie2'가 됩니다. 또한 이 예제에서는 .NET 형식을 사용하여 사용자 지정 필터를 만드는 또 다른 방법을 보여 줍니다. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

결과에서 Jessie2 레코드를 보여 줍니다.

|필드|값|
|----|----|
| userId | 2 |
| 사용자 이름 | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>테이블 엔터티 삭제

테이블에서 하나의 엔터티 또는 모든 엔터티를 삭제할 수 있습니다.

#### <a name="deleting-one-entity"></a>하나의 엔터티 삭제

엔터티를 하나만 삭제하려면 해당 엔터티에 대한 참조를 가져와서 **Remove-AzureStorageTableRow**에 파이프합니다.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>테이블의 모든 엔터티 삭제 

테이블의 모든 엔터티를 삭제하려면 해당 엔터티를 검색하고 결과를 Remove cmdlet으로 파이프합니다. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```