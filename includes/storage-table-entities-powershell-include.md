---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67172625"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>管理表实体

至此，你已创建了一个表，接下来让我们了解如何管理表中的实体或行。 

实体可以具有多达 255 个属性，包括三个系统属性：**分区键**、**行键**和**时间戳**。 您负责插入和更新**分区键**和**RowKey**的值。 服务器管理时间**戳**的值，无法修改。 **PartitionKey** 和 **RowKey** 共同唯一标识表中的每个实体。

* **PartitionKey**：确定实体存储在其中的分区。
* **RowKey**：唯一标识分区内的实体。

最多可为一个实体定义 252 个自定义属性。 

### <a name="add-table-entities"></a>添加表实体

使用**Add-AzTableRow**将实体添加到表中。 这些示例使用具有值`partition1`和`partition2`的分区键，行键等于状态缩写。 每个实体的属性为`username`和`userid`。 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>查询表实体

可以使用**Get-AzTableRow**命令查询表中的实体。

> [!NOTE]
> cmdlet**获取 Azure 存储 TableRowAll、****获取 Azure 存储 TableBy 分区密钥**、**获取 Azure 存储 TableByColumn 名称**和获取 Azure 存储**TableBy 自定义筛选器**将被弃用，并将在将来的版本更新中删除。

#### <a name="retrieve-all-entities"></a>检索所有实体

```powershell
Get-AzTableRow -table $cloudTable | ft
```

此命令得到的结果类似于下表所示：

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>检索特定分区的实体

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

结果类似于下表所示：

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>在特定列中检索具有特定值的实体

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

此查询将检索一条记录。

|字段|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>使用自定义筛选器检索实体 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

此查询将检索一条记录。

|字段|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>更新实体 

更新实体有三个步骤。 第一步，检索要更改的实体。 第二步，进行更改。 第三，使用**Update-AzTableRow**提交更改。

将 username = 'Jessie' 的实体更改为 username = 'Jessie2'。 此示例还介绍了使用 .NET 类型创建自定义筛选器的另一种方法。

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

结果将显示 Jessie2 记录。

|字段|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>删除表实体

可以删除表中的一个实体或所有实体。

#### <a name="deleting-one-entity"></a>删除一个实体

要删除单个实体，请获取对该实体的引用，并将其传送到**删除-AzTableRow**中。

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>删除表中的所有实体

若要删除表中的所有实体，请检索所有实体，并将结果通过管道传递到删除 cmdlet。 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
