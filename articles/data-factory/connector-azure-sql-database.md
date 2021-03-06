---
title: 在 Azure SQL 数据库中复制和转换数据
description: 了解如何使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据，以及如何在 Azure SQL 数据库中转换数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 3a16a8263c80852127ca61db3c666ebf0f7f1e4c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011695"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure SQL 数据库中复制和转换数据

> [!div class="op_single_selector" title1="选择正在使用的 Azure 数据工厂版本："]
> * [版本 1](v1/data-factory-azure-sql-connector.md)
> * [当前版本](connector-azure-sql-database.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 Azure SQL 数据库复制数据，以及使用数据流转换 Azure SQL 数据库中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure SQL 数据库连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)表的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [查找活动](control-flow-lookup-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure SQL 数据库连接器支持以下功能：

- 将 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证与服务主体或 Azure 资源的托管标识配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

>[!NOTE]
>目前此连接器不支持 Azure SQL 数据库 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)。 为了解决此问题，可以通过自承载 Integration Runtime 使用[泛型 ODBC 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 按照[此指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current)完成 ODBC 驱动程序下载和连接字符串配置。

> [!IMPORTANT]
> 如果使用 Azure 数据工厂 Integration Runtime 复制数据，请将 [Azure SQL Server 防火墙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)配置为允许 Azure 服务访问服务器。
> 如果使用自承载集成运行时复制数据，请将 Azure SQL Server 防火墙配置为允许合适的 IP 范围。 此范围包括用于连接 Azure SQL 数据库的计算机的 IP。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure SQL 数据库连接器的 Azure 数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库链接服务支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | **类型**属性必须设置为**AzureSqlDatabase**。 | 是 |
| connectionString | 指定连接到**连接String**属性的 Azure SQL 数据库实例所需的信息。 <br/>还可以将密码或服务主体密钥放在 Azure Key Vault 中。 如果使用 SQL 身份验证，请从连接字符串中提取 `password` 配置。 有关详细信息，请参阅表格后面的 JSON 示例，以及[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 **SecureString**，以安全地将其存储在 Azure 数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| tenant | 指定应用程序所在的租户的信息（例如域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 | 否 |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

>[!TIP]
>如果遇到错误（错误代码为“UserErrorFailedToConnectToSqlServer”，且消息如“数据库的会话限制为 XXX 且已达到。”），请将 `Pooling=false` 添加到连接字符串中，然后重试。

### <a name="sql-authentication"></a>SQL 身份验证

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure 密钥保管库中的密码** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>服务主体身份验证

要使用基于服务主体的 Azure AD 应用程序令牌身份验证，请执行以下步骤：

1. 从 Azure 门户[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 如果尚未这样做，则在 Azure 门户上为 Azure SQL Server[预配 Azure 活动目录管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)。 Azure AD 管理员必须是 Azure AD 用户或 Azure AD 组，但不能是服务主体。 执行此步骤后，在下一步骤中便可使用 Azure AD 标识来为服务主体创建包含的数据库用户。

3. 为服务主体[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 等工具和至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到要向其/从中复制数据的数据库。 运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户或其他用户所做的那样向服务主体授予所需的权限。 运行以下代码。 有关更多选项，请参阅[本文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. 在 Azure 数据工厂中配置 Azure SQL 数据库链接服务。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服务主体身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 资源身份验证的托管标识

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可将此托管标识用于 Azure SQL 数据库身份验证。 指定的工厂可以使用此标识访问数据库数据或从/向数据库复制数据。

若要使用托管标识身份验证，请执行以下步骤：

1. 如果尚未这样做，则在 Azure 门户上为 Azure SQL Server[预配 Azure 活动目录管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)。 Azure AD 管理员可以是 Azure AD 用户，也可以是 Azure AD 组。 如果为具有托管标识的组授予管理员角色，请跳过步骤 3 和 4。 管理员拥有对数据库的完全访问权限。

2. 为 Azure 数据工厂托管标识[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 等工具和至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到要向其/从中复制数据的数据库。 运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 像通常对 SQL 用户和其他用户所做的那样向数据工厂托管标识授予所需的权限。 运行以下代码。 有关更多选项，请参阅[本文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. 在 Azure 数据工厂中配置 Azure SQL 数据库链接服务。

**示例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个节和属性的完整列表，请参阅[数据集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。 

Azure SQL 数据库数据集支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集**的类型**属性必须设置为**AzureSqlTable**。 | 是 |
| 架构 | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

#### <a name="dataset-properties-example"></a>数据集属性示例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure SQL 数据库源和接收器支持的属性列表。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL 数据库作为源

若要从 Azure SQL 数据库复制数据，复制活动的 **source** 节需要支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **AzureSqlSource**。 为了向后兼容，仍然支持“SqlSource”类型。 | 是 |
| sqlReaderQuery | 此属性使用自定义 SQL 查询来读取数据。 示例为 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否 |
| storedProcedureParameters | 存储过程的参数。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| isolationLevel | 指定 SQL 源的事务锁定行为。 允许的值是：**已读提交**（默认）、**未提交**、**可重复读取**、**可序列化**、**快照**。 有关详细信息，请参阅[此文档](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)。 | 否 |

**需要注意的要点：**

- 如果为 **AzureSqlSource** 指定 **sqlReaderQuery**，则复制活动针对 Azure SQL 数据库源运行此查询可获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数********。
- 如果不指定**sqlReaderQuery**或**sqlReader存储过程名称**，则数据集 JSON 的"结构"部分中定义的列用于构造查询。 查询 `select column1, column2 from mytable` 针对 Azure SQL 数据库运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

#### <a name="sql-query-example"></a>SQL 查询示例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>存储过程示例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>存储过程定义

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL 数据库作为接收器

> [!TIP]
> 若要详细了解支持的写入行为、配置和最佳做法，请参阅[有关将数据载入 Azure SQL 数据库的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

将数据复制到 Azure SQL 数据库时，复制活动的 **sink** 节支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **AzureSqlSink**。 为了向后兼容，仍然支持“SqlSink”类型。 | 是 |
| writeBatchSize | 每批要插入到 SQL 表中的行数。**<br/> 允许的值为 **integer**（行数）。 默认情况下，Azure 数据工厂会根据行大小动态确定适当的批大小。 | 否 |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/> 允许的值是**时间跨度**。 例如“00:30:00”（30 分钟）。 | 否 |
| preCopyScript | 将数据写入到 Azure SQL 数据库之前，指定复制活动要运行的 SQL 查询。 每次运行复制仅调用该查询一次。 使用此属性清理预加载的数据。 | 否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用**。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| tableOption | 指定是否根据源架构自动创建接收器表（如果不存在）。 当接收器指定存储过程或在复制活动中配置了暂存复制时，不支持自动表创建。 允许的值为：`none`（默认值）、`autoCreate`。 |否 |
| disableMetricsCollection | 数据工厂收集指标（如 Azure SQL 数据库 DTU），以获取复制性能优化和建议。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |

**示例 1：追加数据**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**示例 2：在复制期间调用存储过程**

请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)，了解更多详细信息。

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>有关将数据载入 Azure SQL 数据库的最佳做法

将数据复制到 Azure SQL 数据库时，可能需要不同的写入行为：

- [追加：](#append-data)我的源数据只有新记录。
- [Upsert](#upsert-data)：我的源数据同时包含插入和更新。
- [覆盖](#overwrite-the-entire-table)：我想每次重新加载整个维度表。
- [用自定义逻辑编写](#write-data-with-custom-logic)：在最终插入目标表之前，我需要额外的处理。

有关如何在 Azure 数据工厂中进行配置和最佳做法，请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 Azure SQL 数据库接收器连接器的默认行为。 Azure 数据工厂执行批量插入，以有效地在表中写入数据。 可以相应地在复制活动中配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 当您有大量要复制的数据时，请使用以下方法执行 upsert： 

- 首先，使用[数据库范围的临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database)通过复制活动批量加载所有记录。 系统不会记录对数据库范围的临时表执行的操作，因此你可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂中运行存储过程活动，以应用 [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) 或 INSERT/UPDATE 语句。 使用临时表作为源，以单个事务的形式执行所有更新或插入操作。 这样可以减少往返次数和日志操作次数。 在存储过程活动结束时，可以截断临时表，使之可以用于下一更新插入循环。

例如，在 Azure 数据工厂中，可以使用**复制活动**创建一个管道，并将其与**存储过程活动**相链接。 前者将数据从源存储复制到数据集中的 Azure SQL 数据库临时表（例如，表名为 **##UpsertTempTable** 的表）。 然后，后者调用一个存储过程，以将临时表中的源数据合并到目标表中，并清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中使用 MERGE 逻辑定义一个存储过程（如以下示例所示），以便从上述存储过程活动指向该过程。 假设目标是具有三列**的营销**表：**配置文件 ID、****状态**和**类别**。 根据 **ProfileID** 列执行更新插入。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**选项 2：** 您还可以选择[在复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 此方法在源表中运行每个批处理（由`writeBatchSize`属性控制），而不是在复制活动中使用批量插入作为默认方法。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置 **preCopyScript** 属性。 在此情况下，对于运行的每个复制活动，Azure 数据工厂会先运行脚本。 然后，运行复制来插入数据。 例如，若要使用最新数据覆盖整个表，请指定一个脚本，以先删除所有记录，然后从源批量加载新数据。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

使用自定义逻辑写入数据的步骤与[更新插入数据](#upsert-data)部分中的描述类似。 如果在将源数据大规模地最终插入目标表之前需要应用额外的处理，可执行以下两项操作之一：

- 先加载到数据库范围的临时表，然后调用存储过程。 
- 在复制过程中调用存储过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 Azure SQL 数据库时，还可以配置和调用用户指定的存储过程，并在源表的每一批上使用其他参数。 存储过程功能利用了[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

当内置复制机制无法使用时，还可使用存储过程。 例如，在将源数据最终插入目标表之前应用额外的处理。 额外处理的示例包括合并列、查找其他值以及将数据插入多个表。

以下示例演示如何使用存储过程在 Azure SQL 数据库数据库中的表内执行 upsert。 假设输入数据和接收器**市场营销**表各有三列：**配置文件 ID、****状态**和**类别**。 基于 ProfileID 列执行更新插入，并仅将其应用于名为“ProductA”的特定类别****。

1. 在数据库中，定义与**sqlWriterTableType**同名的表类型。 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在数据库中，定义与**sqlWriter存储过程名称**相同的存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中的表类型的参数名称与数据集中定义的 **tableName** 相同。

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. 在 Azure 数据工厂中，在复制活动中定义 **SQL sink** 节，如下所示：

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure SQL 数据库读取和写入表。 有关详细信息，请参阅映射数据流的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

特定于 Azure SQL 数据库的设置可在源转换的 **"源选项**"选项卡中提供。 

**输入：** 选择是将源指向表（等效```Select * from <table-name>```项）还是输入自定义 SQL 查询。

**查询**：如果在输入字段中选择"查询"，请输入源的 SQL 查询。 此设置将覆盖您在数据集中选择的任何表。 **此处不支持"按订单"** 子句，但您可以设置完整的 SELECT FROM 语句。 您还可以使用用户定义的表函数。 **从 udfGetData（） 中选择 *** 是 SQL 中的 UDF，用于返回表。 此查询将生成一个源表，可用于数据流。 使用查询也是减少用于测试或查找的行的好方法。 

* SQL 示例：```Select * from MyTable where customerId > 1000 and customerId < 2000```

**批处理大小**：输入批处理大小以将大数据块块到读取中。

**隔离级别**：映射数据流的 SQL 源的默认值为未提交。 您可以在此处将隔离级别更改为以下值之一：
* 已提交读
* 读取未提交
* 可重复的读取
* 可序列化
* 无（忽略隔离级别）

![隔离级别](media/data-flow/isolationlevel.png "隔离级别")

### <a name="sink-transformation"></a>水槽变换

特定于 Azure SQL 数据库的设置可在接收器转换的 **"设置"** 选项卡中提供。

**更新方法：** 确定数据库目标上允许的操作。 默认值是仅允许插入。 要更新、更新或删除行，需要更改行转换来标记这些操作的行。 对于更新、升级和删除，必须设置键列或列以确定要更改的行。

![键列](media/data-flow/keycolumn.png "键列")

此处选择作为键的列名称将由 ADF 用作后续更新、upsert、删除的一部分。 因此，必须选取"接收器"映射中存在的列。 如果不希望将值写入此键列，请单击"跳过写入键列"。

**表操作：** 确定在写入之前是从目标表重新创建或删除所有行。
* 无：对表不执行任何操作。
* 重新创建：该表将被删除并重新创建。 动态创建新表时是必需的。
* 截取：将删除目标表中的所有行。

**批处理大小**：控制每个存储桶中写入的行数。 较大的批处理大小可改善压缩和内存优化，但在缓存数据时存在内存异常风险。

**前 SQL 和后 SQL 脚本**：输入将在（预处理）和之后（后处理）数据写入 Sink 数据库之前执行的多行 SQL 脚本

![SQL 处理脚本的后处理脚本](media/data-flow/prepost1.png "SQL 处理脚本")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL 数据库的数据类型映射

从/向 Azure SQL 数据库复制数据时，以下映射用于从 Azure SQL 数据库数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库数据类型 | Azure 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 十进制 |十进制 |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |十进制 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |十进制 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |十进制 |
| sql_variant |对象 |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前 Azure 数据工厂支持的最大精度为 28。 如果有精度大于 28 的数据，请考虑在 SQL 查询中将其转换为字符串。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
