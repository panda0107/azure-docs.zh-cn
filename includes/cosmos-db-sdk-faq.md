---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67172701"
---
**1.如何向客户通知即将停用的 SDK？**

Microsoft 会在即将停用的 SDK 的支持结束之前提前 12 个月进行通知，以便协助平稳地转换到支持的 SDK。 此外，会通过各种通信渠道（Azure 管理门户、开发人员中心、博客文章以及与分配的服务管理员的直接通信）来通知客户。

**2. 客户能否在 12 个月内使用"即将"停用的 Azure Cosmos DB SDK 创作应用程序？** 

可以，客户在 12 个月宽限期内具有完全访问权限，可以使用“即将”停用的 Azure Cosmos DB SDK 创作、部署和修改应用程序。 在 12 个月宽限期内，建议客户根据相应情况迁移到支持的较新版本 Azure Cosmos DB SDK。

**3. 客户能否在 12 个月通知期后使用已停用的 Azure Cosmos DB SDK 创作和修改应用程序？**

在 12 个月通知期之后，SDK 将停用。 Azure Cosmos DB 平台不允许使用已停用 SDK 的应用程序对 Azure Cosmos DB 进行任何访问。 此外，Microsoft 不会对已停用的 SDK 提供客户支持。

**4. 使用不支持的 Azure Cosmos DB SDK 版本的客户正在运行的应用程序会发生什么情况？**

任何使用已停用 SDK 版本连接 Azure Cosmos DB 服务的尝试都会被拒绝。 

**5. 新功能是否会应用于所有未停用的 SDK？**

新特性和功能只添加到新版本。 如果使用的是未停用的旧版本 SDK，则对 Azure Cosmos DB 进行的请求仍会与以前一样正常工作，但是你无法访问任何新功能。  

**6. 如果我无法在截止日期之前更新我的应用程序，我该怎么办？**

我们建议尽早升级到最新 SDK。 SDK 标记为要停用之后，会有 12 个月来更新应用程序。 如果由于任何原因而无法在此时间范围内完成应用程序更新，请在截止日期之前与 [Cosmos DB 团队](mailto:askcosmosdb@microsoft.com)联系并请求其帮助。

