---
title: Azure 安全中心的安全建议
description: 本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245299"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 安全中心的安全建议 
本主题说明如何查看和了解 Azure 安全中心内的建议，以帮助你保护 Azure 资源。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  本文档不是一份分步指南。
>

## <a name="what-are-security-recommendations"></a>安全建议是什么？

建议是为了保护资源而要采取的措施。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何删除这些安全漏洞的建议。

每项建议都提供：

- 建议的简短说明。
- 为实施建议而要执行的补救步骤。 <!-- In some cases, Quick Fix remediation is available. -->
- 哪些资源需要你对其执行建议的操作。
- **安全分数影响**，这是如果您实施此建议，您的安全分数将上升的金额。

## <a name="monitor-recommendations"></a>监测建议<a name="monitor-recommendations"></a>

安全中心将分析资源的安全状态，以识别潜在的漏洞。 “概述”**** 下的“建议”**** 磁贴显示了安全中心列出的建议总数。

![安全中心概述](./media/security-center-recommendations/asc-overview.png)

1. 选择“概述”**** 下的“建议”**** 磁贴。 这会打开“建议”**** 列表。

      ![查看建议](./media/security-center-recommendations/view-recommendations.png)

    可筛选建议。 要筛选建议，请选择“建议”边栏选项卡上的“筛选器”。******** 此时会打开“筛选器”**** 边栏选项卡，选择要查看严重性和状态值。

   * **建议**：建议。
   * **安全分数影响**：安全中心使用您的安全建议生成的分数，并应用高级算法来确定每个建议有多重要。 有关详细信息，请参阅[安全分数计算](security-center-secure-score.md#secure-score-calculation)。
   * **资源**：列出了此建议适用的资源。
   * **状态 BARS**： 描述该特定建议的严重性：
       * **高（红色）：** 存在有意义的资源（如应用程序、VM 或网络安全组）的漏洞，需要注意。
       * **中等（橙色）：** 存在漏洞，需要非关键或额外步骤来消除它或完成一个过程。
       * **低（蓝色）：** 存在一个漏洞，应该解决，但不需要立即注意。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。） 
       * **正常（绿色）**：
       * **不可用（灰色）**：

1. 若要查看每个建议的详细信息，请单击该建议。

    ![建议详细信息](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> 有关 Azure 资源，请参阅[经典和资源管理器部署模型](../azure-classic-rm.md)。
 
## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍安全中心的安全建议。 要了解如何修复建议：

* [修正建议](security-center-remediate-recommendations.md)– 了解如何为 Azure 订阅和资源组配置安全策略。
