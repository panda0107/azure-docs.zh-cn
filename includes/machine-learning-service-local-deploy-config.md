---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477266"
---
`deploymentconfig.json` 文档中的条目对应于 [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) 的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 计算目标。 对于本地目标，值必须是 `local`。 |
| `port` | `port` | 用于公开服务的 HTTP 终结点的本地端口。 |

此 JSON 是用于 CLI 的部署配置示例：

```json
{
    "computeType": "local",
    "port": 32267
}
```
