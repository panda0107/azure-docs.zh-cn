---
title: Azure Active Directory 门户中“标记为风险用户”的用户的安全报告 | Microsoft Docs
description: 了解 Azure Active Directory 门户中“标记为风险用户”的用户的安全报告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014448"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Azure 门户中标记为存在风险的用户的报表

Azure Active Directory (Azure AD) 可以检测到与用户帐户相关的可疑操作。 对于每个检测到的操作，都会创建一个称为[风险检测](concept-risk-events.md)的记录。

可以从 [Azure 门户](https://portal.azure.com)中通过选择“Azure Active Directory”**** 边栏选项卡并导航到“安全性”**** 部分来访问安全报告。 

检测到的风险检测用于计算：

- **风险登录** - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 

- **已标记为存在风险的用户** - 风险用户是指可能已泄露的用户帐户。 

要了解如何配置触发这些风险检测的策略，请参阅[如何配置用户风险策略](../identity-protection/howto-user-risk-policy.md)。 

![有风险的登录](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>访问风险用户报表需要什么 Azure AD 许可证？  

所有版本的 Azure Active Directory 都提供标记为存在风险的用户的报表。 但是，各版本的报表粒度级别有所不同： 

- 在“Azure Active Directory 免费版和基本版”中，获取一个列表，其中包含标记为存在风险的用户。**** 

- 此外 **，Azure 活动目录高级 1**版允许您检查已检测到每个报表的某些基本风险检测。 

- **Azure Active Directory 高级版**为您提供了有关所有潜在风险检测的最详细信息，还使您能够配置自动响应配置的风险级别的安全策略。


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Azure AD 免费版和基本版的风险用户报告

Azure AD 免费版和基本版中“标记为风险用户”报告提供可能已泄露的用户帐户列表。 

![有风险的登录](./media/concept-user-at-risk/03.png)

选择一个用户即可获得登录信息。 对于存在风险的用户，可查看其登录历史记录，并根据需要重置密码。

此对话框提供的选项用于：

- 下载报告
- 搜索用户

    ![有风险的登录](./media/concept-user-at-risk/16.png)

若要获得更详细的信息，需提供高级许可证。

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Azure Active Directory Premium 版的风险用户报告

Azure AD Premium 版中“标记为风险用户”的用户的报告提供：

- 可能已泄露的用户帐户列表 

- 有关已检测到[的风险检测类型的](concept-risk-events.md)聚合信息

- 一个用于下载报表的选项

- 一个用于配置[用户风险补救策略](../identity-protection/howto-user-risk-policy.md)的选项  

![有风险的登录](./media/concept-user-at-risk/71.png)

选择用户时，可获取此用户的详细报表视图，以便：

- 打开“所有的登录”视图

- 重置用户密码

- 清除所有事件

- 调查用户报告的风险检测。 

![有风险的登录](./media/concept-user-at-risk/324.png)

要调查风险检测，请从列表中选择一个来打开此风险检测**的"详细信息"** 边栏选项卡。 在 **"详细信息"** 边栏选项卡上，您可以选择手动关闭风险检测或重新激活手动关闭的风险检测。 

![有风险的登录](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>后续步骤

- [如何配置用户风险策略](../identity-protection/howto-user-risk-policy.md)
- [如何配置风险补救策略](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory 标识保护](../active-directory-identityprotection.md)

