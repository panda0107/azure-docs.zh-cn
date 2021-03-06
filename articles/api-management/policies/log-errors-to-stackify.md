---
title: 示例 API 管理策略 - 将错误发送到堆栈以进行日志记录
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何添加错误日志记录策略，以便将错误发送到 Stackify 进行日志记录。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442446"
---
# <a name="send-errors-to-stackify-for-logging"></a>将错误发送到 Stackify 进行日志记录

本文介绍 Azure API 管理策略示例，该示例演示如何添加错误日志记录策略，以便将错误发送到 Stackify 进行日志记录。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到 **on-error** 块中。

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

