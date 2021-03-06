---
title: Azure Cosmos DB 表 API .NET Standard SDK 和资源
description: 了解有关 Azure Cosmos DB 表 API 和 .NET Standard SDK 的所有信息，包括发布日期、停用日期和各版本之间所做的更改。
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771596"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 表 .NET Standard API：下载和发行说明
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**示例**|[Cosmos DB 表 API .NET 示例](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**快速入门**|[快速入门](create-table-dotnet.md)|
|**教程**|[教程](tutorial-develop-table-dotnet.md)|
|**当前受支持的框架**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**报告问题**|[报告问题](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2.0.0 系列的发行说明
2.0.0 系列依赖于[Microsoft.Azure.Cosmos，](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)性能改进和命名空间合并到 Cosmos DB 终结点。

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* 2.0.0 表 SDK 的初始预览，它依赖于[Microsoft.Azure.Cosmos，](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)性能改进和命名空间合并到 Cosmos DB 终结点。 公共 API 保持不变。

## <a name="release-notes-for-100-series"></a>1.0.0 系列的发行说明
1.0.0 系列依赖于[Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)。

### <a name="105"></a><a name="1.0.5"/>1.0.5
* 在表客户端配置下引入新的配置，以使用休息执行器与 Cosmos DB 表 API 进行通信

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5 预览版
* Bug 修复

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Bug 修复
* 为休息执行器配置提供 HttpClient 超时选项。

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4 预览
* Bug 修复
* 为休息执行器配置提供 HttpClient 超时选项。

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Bug 修复

### <a name="100"></a><a name="1.0.0"/>1.0.0
* 正式发布版

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* 对 CloudTableClient 的配置方式进行了更改。 它现在会在构造过程中使用 TableClientConfiguration 对象。 TableClientConfiguration 提供不同的属性来配置客户端行为，具体取决于目标终结点是 Cosmos DB 表 API 还是 Azure 存储表 API。
* 增加了对 TableQuery 的支持，可以在自定义列中按排序顺序返回结果。 只有 Cosmos DB 表终结点支持此功能。
* 增加了相关支持，可以在不同的结果类型上公开 RequestCharge。 只有 Cosmos DB 表终结点支持此功能。

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1 预览版
* 针对 Azure 存储表终结点添加了对 SAS 令牌以及 TablePermissions、ServiceProperties 和 ServiceStats 的操作的支持。 
   > [!NOTE]
   > 尚不支持以前的 Azure 存储表 SDK 中的某些功能，例如客户端加密。

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0 预览版
* 针对 Azure 存储表终结点添加了对核心 CRUD、批处理和查询操作的支持。 
   > [!NOTE]
   > 尚不支持以前的 Azure 存储表 SDK 中的某些功能，例如客户端加密。

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1 预览版
* Azure Cosmos DB 表 .NET Standard SDK 是一个跨平台 .NET 库，可高效访问 Cosmos DB 上的表数据模型。 此初始版本支持完整的表和实体 CRUD + 查询功能集，其中 API 与[用于 .NET Framework 的 Cosmos DB 表 SDK](table-sdk-dotnet.md) 类似。 
   > [!NOTE]
   >  0.9.1 预览版尚不支持 Azure 存储表终结点。

## <a name="release-and-retirement-dates"></a>发布日期和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月**** 发出通知，以便顺利转换为更高版本/受支持版本。

这个跨平台.NET标准库[微软.Azure.Cosmos.表](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)将取代.NET框架库[微软.Azure.CosmosDB.表](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。

### <a name="200-series"></a>2.0.0 系列
| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |奥客 22， 2019 |--- |

### <a name="100-series"></a>1.0.0 系列
| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019 年 9 月 13 日 |--- |
| [1.0.5 预览版](#1.0.5-preview) |奥客 20， 2019 |--- |
| [1.0.4](#1.0.4) |奥Guest 12， 2019 |--- |
| [1.0.4 预览](#1.0.4-preview) |2019 年 6 月 26 日 |--- |
| 1.0.2 预览 |2019年5月2日 |--- |
| [1.0.1](#1.0.1) |2019年4月19日 |--- |
| [1.0.0](#1.0.0) |2019 年 3 月 13 日 |--- |
| [0.11.0-preview](#0.11.0-preview) |2019 年 3 月 5 日 |--- |
| [0.10.1 预览版](#0.10.1-preview) |2019 年 1 月 22 日 |--- |
| [0.10.0 预览版](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1 预览](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅
若要了解有关 Azure Cosmos DB 表 API 的详细信息，请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)。
