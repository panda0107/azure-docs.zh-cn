---
title: 在 Marketo 中配置潜在客户管理 |Azure 应用商店
description: 为 Azure 市场客户配置 Marketo 的潜在客户管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288523"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中配置潜在顾客管理

本文介绍如何设置 Marketo 来处理 Microsoft 销售潜在顾客。

1. 登录到 Marketo。
2. 选择“Design Studio”。****
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  选择“新建窗体”。****
    ![Marketo 新建窗体](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  在“新建窗体”中填写必填字段，然后选择“创建”。****
    ![Marketo 创建新窗体](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  在“字段详细信息”中，选择“完成”。****
    ![Marketo 完成窗体](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  同意并关闭。

6.  在 MarketplaceLeadBacked 选项卡上，选择“嵌入代码”。****
    ![Marketo 嵌入代码选项](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo 的“嵌入代码”将显示类似于以下示例的代码。

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. 复制“嵌入代码”中显示的值，以便可以在云合作伙伴门户的 Marketo 字段中配置“服务器 ID”、“Munchkin ID”和“窗体 ID”。************

使用下面的示例说明如何从 Marketo 的嵌入代码示例获取所需的 ID。

- Server Id = **ys12**
- Munchkin Id = **123-PQR-789**
- Form Id = **1179**
