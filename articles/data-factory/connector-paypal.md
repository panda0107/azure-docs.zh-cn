---
title: 使用 Azure 数据工厂（预览版）从 PayPal 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 PayPal 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 2bf3452e3489c8b4c9664b2ffb58e4a001f8846b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991801"
---
# <a name="copy-data-from-paypal-using-azure-data-factory-preview"></a>使用 Azure 数据工厂（预览版）从 PayPal 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 PayPal 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!IMPORTANT]
> 此连接器目前提供预览版。 欢迎试用并提供反馈。 若要在解决方案中使用预览版连接器的依赖项，请联系 [Azure 客户支持](https://azure.microsoft.com/support/)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 PayPal 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [查找活动](control-flow-lookup-activity.md)

可以将数据从 PayPal 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 PayPal 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

PayPal 链接服务支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**PayPal** | 是 |
| host | PayPal 实例的 URL。 （即，api.sandbox.paypal.com）  | 是 |
| clientId | 与 PayPal 应用程序关联的客户端 ID。  | 是 |
| clientSecret | 与 PayPal 应用程序关联的客户端密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定在通过 TLS 连接时，是否要求服务器证书中的主机名与服务器的主机名匹配。 默认值为 true。  | 否 |
| usePeerVerification | 指定在通过 TLS 连接时是否验证服务器的标识。 默认值为 true。  | 否 |

**例子：**

```json
{
    "name": "PayPalLinkedService",
    "properties": {
        "type": "PayPal",
        "typeProperties": {
            "host" : "api.sandbox.paypal.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的节和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 PayPal 数据集支持的属性列表。

要从 PayPal 复制数据，请将数据集的 type 属性设置为“PayPalObject”****。 支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为 **：PayPalObject** | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "PayPalDataset",
    "properties": {
        "type": "PayPalObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PayPal linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 PayPal 数据源支持的属性列表。

### <a name="paypal-as-source"></a>以 PayPal 作为源

要从 PayPal 复制数据，请将复制活动中的源类型设置为“PayPalSource”****。 复制活动**源**部分支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：PayPalSource**** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Payment_Experience"`。 | 否（如果指定了数据集中的“tableName”） |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromPayPal",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PayPal input dataset name>",
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
                "type": "PayPalSource",
                "query": "SELECT * FROM Payment_Experience"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动作为源和接收器支持的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
