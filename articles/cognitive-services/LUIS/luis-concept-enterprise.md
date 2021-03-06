---
title: 企业概念 - LUIS
titleSuffix: Azure Cognitive Services
description: 了解大型 LUIS 应用程序或多个应用程序（包括 LUIS 和 QnA Maker）的设计概念。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221058"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>LUIS 应用的企业策略
查看企业应用的设计策略。

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>LUIS 请求超出配额

LUIS 基于 Azure 资源的定价层，具有每月配额和每秒配额。 

如果 LUIS 应用请求速率超过了允许的[配额速率](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)，你可以：

* 将负载分散到更多具有[相同应用定义](#use-multiple-apps-with-same-app-definition)的 LUIS 应用。 这包括从[容器](luis-container-howto.md)运行 LUIS（可选）。 
* 创建[多个密钥](#assign-multiple-luis-keys-to-same-app)并将其分配给应用。 

### <a name="use-multiple-apps-with-same-app-definition"></a>使用多个具有相同应用定义的应用
导出原始 LUIS 应用，然后将该应用导入回单独的应用。 每个应用都有其自己的应用 ID。 在发布时，请为每个应用创建单独的密钥，而不是对所有应用使用相同的密钥。 均衡所有应用的负载，避免单个应用处于过载状态。 添加 [Application Insights](luis-tutorial-bot-csharp-appinsights.md) 来监视使用情况。 

若要在所有应用之间获取同样的最高意向，请确保第一意向和第二意向之间的差距足够大，不会让 LUIS 混淆，针对陈述中微小的变化在应用间给出不同的结果。 

训练这些同级应用时，请确保[使用所有数据进行训练](luis-how-to-train.md#train-with-all-data)。

将单个应用指定为主应用。 建议查看的任何陈述都应添加到主应用，然后移回所有其他应用。 这是应用的一次完整导出，或是将主应用中已标记的陈述加载到子级。 可从 [LUIS](luis-reference-regions.md) 网站或者[单个话语](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)或[批量话语](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)的创作 API 完成加载。 

计划定期审核（如每两周一次）的[终结点话语](luis-how-to-review-endpoint-utterances.md)以进行主动学习，然后重新训练和重新发布。 

### <a name="assign-multiple-luis-keys-to-same-app"></a>将多个 LUIS 密钥分配到相同的应用
如果 LUIS 应用接收到的终结点命中数超过了单个密钥的配额，请创建更多密钥并将它们分配到该 LUIS 应用。 创建流量管理器或负载均衡器，管理这些终结点密钥间的终结点查询。 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>单体应用返回错误意向
如果应用要预测多种陈述，请考虑实施[调度模型](#dispatch-tool-and-model)。 分解单体应用可让 LUIS 成功地专注于意向间的检测，而不会在父应用和子应用中的意向间产生混淆。 

计划定期（例如每两周一次）[查看终结点陈述](luis-how-to-review-endpoint-utterances.md)以进行主动学习，然后重新训练并重新发布。 

## <a name="when-you-need-to-have-more-than-500-intents"></a>需要超过 500 个意向
假设要开发一个包含超过 500 个意向的办公室助手。 如果有 200 个关于计划会议的意向、200 个关于提醒的意向、200 个关于获取同事信息的意向以及 200 个 关于发送电子邮件的意向，则对意向进行分组，每组位于一个应用中，然后创建包含每个意向的顶层应用。 使用[调度模型](#dispatch-tool-and-model)来生成顶层应用。 然后更改机器人以使用[调度模型教程](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)中所示的级联调用。 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>需要合并多个 LUIS 和 QnA Maker 应用
如果有多个 LUIS 和 QnA Maker 应用需要响应一个机器人，请使用[调度模型](#dispatch-tool-and-model)来生成顶层应用。  然后更改机器人以使用[调度模型教程](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)中所示的级联调用。 

## <a name="dispatch-tool-and-model"></a>调度工具和模型
使用 [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) 中的[调度][dispatch-tool]命令行工具，将多个 LUIS 和/或 QnA Maker 应用合并至父 LUIS 应用。 采用此方法可以得到一个父域，将所有使用者域和不同的子使用者域都包含在单独的应用中。 

![调度体系结构的概念图](./media/luis-concept-enterprise/dispatch-architecture.png)

在 LUIS 中使用应用列表中名为 `Dispatch` 的版本记录父域。 

聊天机器人接收话语，然后发送至父 LUIS 应用进行预测。 父应用中的最高预测意向决定下一个要调用的 LUIS 子应用。 聊天机器人将话语发送至子应用进行更具体的预测。

了解如何从 Bot Builder v4 [dispatcher-application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs) 创建这个调用层次结构。  

### <a name="intent-limits-in-dispatch-model"></a>调度模型中的意向限制
一个调度应用程序最多可包含 500 个调度资源，相当于 500 个意向。 

## <a name="more-information"></a>详细信息

* [机器人框架 SDK](https://github.com/Microsoft/botframework)
* [调度模型教程](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [调度 CLI](https://github.com/Microsoft/botbuilder-tools)
* 调度模型机器人示例 - [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch)、[Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>后续步骤

* 了解如何[测试批处理](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
