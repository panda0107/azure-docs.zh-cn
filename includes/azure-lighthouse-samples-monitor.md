---
title: include 文件
description: include 文件
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986720"
---
这些示例演示如何使用 Azure Monitor 为已针对 Azure 委派资源管理而载入的订阅创建警报。

| **模板** | **说明** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | 查询管理租户中过去 1 天的活动，并[报告有关任何已添加或已删除委派的信息](../articles/lighthouse/how-to/monitor-delegation-changes.md)（或有关失败尝试的信息）。|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | 此模板创建一个 Azure 警报并连接到现有操作组。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | 基于 Kusto 查询创建多个日志警报。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | 当用户将订阅委派给管理租户时在租户中部署警报。|
