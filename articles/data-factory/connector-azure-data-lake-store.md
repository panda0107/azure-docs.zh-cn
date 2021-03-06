---
title: 将数据复制到或从 Azure 数据存储第 1 代
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Data Lake Store，或从 Data Lake Store 复制到支持的接收器存储。
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
ms.openlocfilehash: a8ba8b212a504a8f8e4e29fbd50126189998e81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065467"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到或从 Azure 数据存储第 1 代

> [!div class="op_single_selector" title1="选择正在使用的 Azure 数据工厂版本："]
> * [版本 1](v1/data-factory-azure-datalake-connector.md)
> * [当前版本](connector-azure-data-lake-store.md)

本文概述了如何将数据复制到 Azure 数据存储第 1 代。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

此 Azure 数据存储库第 1 代连接器支持以下活动：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md) 
- [映射数据流](concepts-data-flow-overview.md)
- [查找活动](control-flow-lookup-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)

具体而言，使用此连接器可以：

- 使用以下身份验证方法之一复制文件：Azure 资源的服务主体或托管标识。
- 复制文件作为或解析或生成文件与[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)。
- 复制到 Azure 数据湖存储 Gen2 时[保留 ACL。](#preserve-acls-to-data-lake-storage-gen2)

> [!IMPORTANT]
> 如果使用自托管的集成运行时复制数据，请将公司防火墙配置为允许进出站流量到`<ADLS account name>.azuredatalakestore.net`端口 443`login.microsoftonline.com/<tenant>/oauth2/token`和端口 443 上。 后者是 Azure 安全令牌服务，集成运行时需要与之通信以获取访问令牌。

## <a name="get-started"></a>入门

> [!TIP]
> 有关如何使用 Azure 数据湖存储连接器的演练，请参阅[将数据加载到 Azure 数据湖存储中](load-azure-data-lake-store.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各节提供有关用于定义特定于 Azure 数据湖存储的数据工厂实体的属性的信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Store 链接服务支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

### <a name="use-service-principal-authentication"></a>使用服务主体身份验证

若要使用服务主体身份验证，请执行以下步骤。

1. 在 Azure 活动目录中注册应用程序实体，并授予其对数据湖存储的访问权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当的权限。 请参阅[有关 Azure 数据湖存储 Gen1 中访问控件](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)中权限在数据湖存储 Gen1 中的工作方式的示例。

    - **作为源**：在**数据资源管理器** > **访问**中，至少授予所有上游文件夹（包括根）**的执行**权限，以及文件复制的**读取**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。******** 对帐户级访问控制 （IAM） 没有要求。
    - **作为接收器**：在**数据资源管理器** > **访问**中，至少对所有上游文件夹（包括根 **）授予执行**权限，以及接收器文件夹的**写入**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。******** 如果使用 Azure 集成运行时进行复制（源和接收器都在云中），则至少授予**Reader**角色，以便让数据工厂检测数据湖存储的区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 例如，如果数据湖存储位于西欧，请创建 Azure 集成运行时，位置设置为"西欧"。 将它们关联到数据湖存储链接服务中，如以下示例所示。

支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString` 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序所在的租户信息，如域名或租户 ID。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |

**例子：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>使用托管标识进行 Azure 资源身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此托管标识进行 Data Lake Store 身份验证。 此指定工厂可通过此方法访问以及向/从 Data Lake Store 复制数据。

要使用托管标识进行 Azure 资源身份验证，请按照以下步骤操作。

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授予对数据湖存储的托管标识访问权限。 请参阅[有关 Azure 数据湖存储 Gen1 中访问控件](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)中权限在数据湖存储 Gen1 中的工作方式的示例。

    - **作为源**：在**数据资源管理器** > **访问**中，至少授予所有上游文件夹（包括根）**的执行**权限，以及文件复制的**读取**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。******** 对帐户级访问控制 （IAM） 没有要求。
    - **作为接收器**：在**数据资源管理器** > **访问**中，至少对所有上游文件夹（包括根 **）授予执行**权限，以及接收器文件夹的**写入**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。******** 如果使用 Azure 集成运行时进行复制（源和接收器都在云中），则至少授予**Reader**角色，以便让数据工厂检测数据湖存储的区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 将它们关联到数据湖存储链接服务中，如以下示例所示。

在 Azure 数据工厂中，除了链接服务中的常规 Data Lake Store 信息以外，不需要指定任何属性。

**例子：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的数据集中的设置下`location`，Azure 数据湖存储 Gen1 支持以下属性：

| properties   | 描述                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集`location`下的类型属性必须设置为**AzureDataLakeStoreLocation**。 | 是      |
| folderPath | 文件夹的路径。 如果要使用通配符筛选文件夹，请跳过此设置并在活动源设置中指定。 | 否       |
| fileName   | 给定 folderPath 下的文件名。 如果要使用通配符筛选文件，请跳过此设置并在活动源设置中指定。 | 否       |

**例子：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure Data Lake Store 源和接收器支持的属性列表。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作为源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的副本源中的设置下`storeSettings`，Azure 数据湖存储 Gen1 支持以下属性：

| properties                 | 描述                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下`storeSettings`的类型属性必须设置为**AzureDataLakeStoreRead设置**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值**为 true（** 默认值）和**false**。 | 否                                            |
| wildcardFolderPath       | 带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 给定的 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 如果数据集中未指定 `fileName`，则为“是” |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store 作为接收器

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的副本接收器中的设置下`storeSettings`，Azure 数据湖存储 Gen1 支持以下属性：

| properties                 | 描述                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的类型属性必须设置为**AzureDataLakeStoreWrite设置**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| 到期日期时间 | 指定写入文件的过期时间。 时间以"2020-03-01T08：00：00：00Z"的格式应用于 UTC 时间。 默认情况下，它是 NULL，这意味着写入的文件永远不会过期。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

**例子：**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选结果（检索**粗体**文件）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**文件3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**文件3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>复制操作的行为示例

本部分介绍将 `recursive` 和 `copyBehavior` 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 = File2 = File3 = File4 = File5 内容合并到一个文件中，并带有一个自动生成的文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>未选取包含 File3、File4 和 File5 的子文件夹1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>未选取包含 File3、File4 和 File5 的子文件夹1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>未选取包含 File3、File4 和 File5 的子文件夹1。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>将 ACL 保留到数据存储库第 2 代

>[!TIP]
>要将数据从 Azure 数据存储 Gen1 复制到 Gen2，请参阅[将数据从 Azure 数据存储第 1 代复制到 Azure 数据工厂](load-azure-data-lake-storage-gen2-from-gen1.md)的第 2 代数据，了解演练和最佳实践。

如果要在从数据存储库第 1 代升级到数据存储第 1 代时复制访问控制列表 （ACL） 以及数据文件，请参阅[保留来自数据存储湖存储 Gen1 的 ACL。](copy-activity-preserve-metadata.md#preserve-acls)

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure 数据存储第 1 代（JSON、Avro、分隔文本或 Parquet 格式）读取和写入文件。 有关详细信息，请参阅映射数据流功能中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

在源转换中，可以从 Azure 数据湖存储 Gen1 中的容器、文件夹或单个文件进行读取。 "**源"选项**选项卡允许您管理文件读取的方式。 

![源选项](media/data-flow/sourceOptions1.png "源选项")

**通配符路径：** 使用通配符模式将指示 ADF 在单个源转换中循环访问每个匹配的文件夹和文件。 这是处理单个流中的多个文件的有效方法。 使用将鼠标悬停在现有通配符模式上时出现的 + 符号添加多个通配符匹配模式。

从源容器中选择一系列与模式匹配的文件。 只能在数据集中指定容器。 因此，通配符路径还必须包括根文件夹中的文件夹路径。

通配符示例：

* ```*```表示任意一组字符
* ```**```表示递归目录嵌套
* ```?```替换一个字符
* ```[]```匹配括号中更多字符之一

* ```/data/sales/**/*.csv```获取 /数据/销售下的所有 csv 文件
* ```/data/sales/20??/**/```获取 20 世纪的所有文件
* ```/data/sales/*/*/*.csv```获取 csv 文件下 /数据/销售两个级别
* ```/data/sales/2004/*/12/[XY]1?.csv```在 2004 年 12 月获取所有 csv 文件，以 X 或 Y 开头，以两位数字开头

**分区根路径：** 如果文件源中的文件夹已采用```key=value```格式（例如，year_2019）进行分区，则可以将该分区文件夹树的顶层分配给数据流数据流中的列名称。

首先，设置通配符以包括分区文件夹的所有路径以及您希望读取的叶文件。

![分区源文件设置](media/data-flow/partfile2.png "分区文件设置")

使用分区根路径设置定义文件夹结构的顶层。 当您通过数据预览查看数据内容时，您将看到 ADF 将添加在每个文件夹级别中找到的已解析分区。

![分区根路径](media/data-flow/partfile1.png "分区根路径预览")

**文件列表：** 这是一个文件集。 创建包含要处理的相对路径文件的列表的文本文件。 指向此文本文件。

**要存储文件名的列：** 将源文件的名称存储在数据中的列中。 在此处输入新的列名以存储文件名字符串。

**完成后：** 选择在数据流运行后对源文件不执行任何操作、删除源文件或移动源文件。 移动的路径是相对的。

要将源文件移到处理后的另一个位置，首先选择文件操作的"移动"。 然后，设置"from"目录。 如果您没有为路径使用任何通配符，则"from"设置将与源文件夹位于同一文件夹。

如果您有具有通配符的源路径，则语法如下所示：

```/data/sales/20??/**/*.csv```

您可以将"从"指定为

```/data/sales```

和"到"作为

```/backup/priorSales```

在这种情况下，在 /data/sales 下获取的所有文件将移动到 /备份/优先销售。

> [!NOTE]
> 仅当从管道运行（管道调试或执行运行）启动数据流时，文件操作才会运行，该流使用管道中的执行数据流活动。 文件操作*不在*数据流调试模式下运行。

**按上次修改进行筛选：** 您可以通过指定上次修改文件的日期范围来筛选处理的文件。 所有日期时间均以 UTC 表示。 

### <a name="sink-properties"></a>接收器属性

在接收器转换中，可以写入 Azure 数据湖存储 Gen1 中的容器或文件夹。 通过 **"设置"** 选项卡，您可以管理文件的写入方式。

![接收器选项](media/data-flow/file-sink-settings.png "接收器选项")

**清除文件夹：** 确定在写入数据之前是否清除目标文件夹。

**文件名选项：** 确定目标文件在目标文件夹中的命名方式。 文件名选项为：
   * **默认值**：允许 Spark 根据 PART 默认值命名文件。
   * **模式**：输入一种模式，以枚举每个分区的输出文件。 例如，**贷款\n\csv**将创建贷款1.csv，贷款2.csv，等等。
   * **每个分区**：每个分区输入一个文件名。
   * **作为列中的数据**：将输出文件设置为列的值。 路径相对于数据集容器，而不是目标文件夹。 如果数据集中包含文件夹路径，则该路径将被覆盖。
   * **输出到单个文件**：将分区的输出文件合并到单个命名文件中。 路径与数据集文件夹相关。 请注意，te 合并操作可能会根据节点大小失败。 不建议对大型数据集使用此选项。

**报价所有：** 确定是否将报价中的所有值括起来

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要了解有关属性的详细信息，请查看 [Delete 活动](delete-activity.md)

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍按原样支持以下模型，以实现向后兼容性。 建议你以后使用前面部分中提到的新模型，ADF 创作 UI 已经切换到生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为**AzureDataLakeStoreFile**。 |是 |
| folderPath | Data Lake Store 中的文件夹的路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 <br/><br/>例如：根文件夹/子文件夹/。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>用于`^`转义，如果实际文件名有通配符或此转义字符里面。<br/><br/>当未为输出数据集指定文件名并在活动接收器中未指定**保留层次结构**时，复制活动将自动生成具有以下模式的文件名："*数据"。活动运行 ID GUID]。[GUID 如果扁平层次结构]。[格式（如果已配置）。"[压缩如果配置]*"，例如，"数据.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果使用表名而不是查询从表格源复制，则名称模式为 *"[表名]"。格式*。[压缩（如果配置）"，"* 例如，"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 如果要在基于文件的商店（二进制副本）之间复制文件，请跳过输入数据集和输出数据集定义中的格式部分。<br/><br/>如果想要分析或生成具有特定格式的文件，则下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 将**格式**下**的类型**属性设置为这些值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format) [、JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)[、Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)[、Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和[Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型是 GZip、Deflate、BZip2 和**ZipDeflate**。 **GZip** **Deflate** **BZip2**<br/>支持的级别**是最佳**和**最快的**。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>要复制具有特定名称的单个文件，请指定文件夹部分的**文件夹 Path**和具有文件名**的文件名**。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。 

**例子：**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>旧复制活动源模型

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 必须`type`将复制活动源的属性设置为**AzureDataLakeStoreSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当`recursive`设置为 true 并且接收器是基于文件的存储时，不会在接收器上复制或创建空文件夹或子文件夹。 允许的值**为 true（** 默认值）和**false**。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>旧复制活动接收器模型

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 复制`type`活动接收器的属性必须设置为**AzureDataLakeStoreSink**。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，会自动生成文件名。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动作为源和接收器支持的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
