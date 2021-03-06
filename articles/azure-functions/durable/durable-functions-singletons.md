---
title: Durable Functions 的单一实例 - Azure
description: 如何使用 Azure Functions 的 Durable Functions 扩展中的单一实例。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262803"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions 中的单一实例业务流程协调程序 (Azure Functions)

对于后台作业，通常需要确保一次只运行特定业务流程协调程序的一个实例。 通过在创建业务流程协调程序时将特定实例 ID 分配给该业务流程协调程序，可以确保 [Durable Functions](durable-functions-overview.md) 中的这种单一实例行为。

## <a name="singleton-example"></a>单一实例示例

以下示例显示了一个 HTTP 触发器函数，该函数创建一个单一实例后台作业业务流程。 该代码可确保只存在一个具有指定实例 ID 的实例。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关不同版本之间的差异的详细信息，请参阅[持久函数版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[Javascript](#tab/javascript)

**函数.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

---

默认情况下，实例 ID 是随机生成的 GUID。 但是，在前面的示例中，实例 ID 通过 URL 在路由数据中传递。 该代码调用 `GetStatusAsync` (C#) 或 `getStatus` (JavaScript) 检查具有指定 ID 的实例是否已在运行。 如果没有此类实例正在运行，将使用该 ID 创建一个新实例。

> [!NOTE]
> 在此示例中有潜在的争用条件。 如果 **HttpStartSingle** 的两个实例同时执行，则两个函数调用都将报告成功，但实际上只会启动一个业务流程实例。 根据你的要求，这可能会产生不良副作用。 因此，必须确保没有两个请求可以同时执行此触发器函数。

业务流程协调程序函数的实现细节实际上无关紧要。 它可以是一个启动并完成的常规业务流程协调程序函数，也可以是一个永远运行的业务流程协调程序函数（即[永久业务流程](durable-functions-eternal-orchestrations.md)）。 重点是一次只有一个实例在运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解业务流程的本机 HTTP 功能](durable-functions-http-features.md)
