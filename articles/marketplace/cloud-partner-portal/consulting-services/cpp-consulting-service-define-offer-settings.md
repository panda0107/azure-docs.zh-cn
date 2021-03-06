---
title: 定义咨询服务产品/服务的报价设置 |Azure 应用商店
description: 在 Azure 应用商店的云合作伙伴门户中定义 Azure 或 Dynamics 365 咨询服务产品中的产品/服务设置。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 6a3c168d0bd13e7c335841ac4016f18464cd50d5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985110"
---
# <a name="offer-settings-tab"></a>“产品/服务设置”选项卡

>[!Important]
>从 2020 年 4 月 13 日起，我们将开始将您的咨询服务优惠的管理转移到合作伙伴中心。 迁移后，您将在合作伙伴中心创建和管理您的优惠。 按照[咨询服务创建概述](https://aka.ms/AzureCreateConsultingService)中的说明来管理迁移的优惠。

在“新建产品/服务”**** 屏幕中，第一个步骤是创建产品/服务标识。 套餐标识由三个部分组成：“套餐 ID”、“发布者 ID”和“名称”。************ 以下各部分将介绍其中的每个组成部分。

![创建新的咨询服务套餐 -“套餐设置”选项卡](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>优惠 ID*

此标识符是首次提交套餐时创建的唯一名称。 它只能包含小写的字母数字字符、短划线或下划线。 “套餐 ID”将显示在 URL 中，并会影响搜索引擎的结果****。 例如，yourcompanyname_exampleservice**。

如示例中所示，“套餐 ID”将追加到发布者 ID 的后面，以创建唯一的标识符****。 此唯一标识符将公开为可预订服务的永久链接，并由搜索引擎编制索引。

>[!Note]
>套餐上线后，无法更新其标识符。


### <a name="publisher-id"></a>发布者 ID*

此标识符与你的帐户相关。 使用组织帐户登录后，“发布者 ID”将显示在下拉菜单中****。


### <a name="name"></a>名称*

此字符串显示为 AppSource 或 Azure 市场中的套餐名称。 “名称”框被限制为最多包含 50 个字符****。 审阅者可能需要编辑你的标题以在套餐名称后追加持续时间和套餐类型。

以下示例演示了如何组合套餐名称。 

![创建新的咨询服务套餐](media/cppsampleconsultingoffer.png)

套餐名称由四个部分组成：

-   **持续时间：** 在编辑器的 **"店面详细信息"** 选项卡上定义。 可使用小时、天或周作为单位来表示持续时间。
-   **服务类型：** 在编辑器的 **"店面详细信息"** 选项卡上定义。 服务类型包括 `Assessment`、`Briefing`、`Implementation`、`Proof of concept` 和 `Workshop`。
-   **介词：** 由审阅者插入。
-   **名称：** 在 **"产品/服务设置"页上定义**。

>[!Note]
>“名称”框被限制为最多包含 50 个字符****。 审阅者可能需要编辑你的标题以在套餐名称后追加持续时间和套餐类型。

以下列表提供了几个适当的套餐名称：

-   专业服务基础知识：1 小时简报
-   云迁移平台：1 小时简报
-   PowerApps 和 Microsoft Flow：1 天研讨会
-   Azure 机器学习：3-Wk PoC
-   实体与虚拟零售解决方案：1 小时简报
-   自带数据：1Wk研讨会
-   云分析：3 天研讨会
-   Power BI 培训：3 天研讨会
-   销售管理解决方案：1 周实施
-   CRM 快速入门：为期 1 天的研讨会
-   Dynamics 365 for Sales：2 天评估

填写“套餐设置”选项卡后，保存提交****。 现在，套餐名称显示在编辑器的上方，并且可以在“所有套餐”中找到它****。

## <a name="next-steps"></a>后续步骤

现在，你可以[输入店面详细信息并确定是要在 Azure 市场中还是要在 AppSource 上发布](./cpp-consulting-service-storefront-details.md)。
