---
title: include 文件
description: include 文件
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c3677e7897498aa06d7bd547988ad4dc0326f39b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "67173039"
---
## <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序

如果以前未在 Azure 订阅中使用过事件网格，则可能需要注册事件网格资源提供程序。

在 Azure 门户中：

1. 选择 **订阅**。
1. 选择要用于事件网格的订阅。
1. 在“设置”下，选择“资源提供程序”。  
1. 找到 **Microsoft.EventGrid**。
1. 如果尚未注册，请选择“注册”。  

完成注册可能需要一些时间。 选择“刷新”可更新状态。  当“状态”为“已注册”后，即可继续。  