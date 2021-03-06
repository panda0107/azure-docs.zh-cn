---
title: 在 Azure 安全中心内管理用户数据 | Microsoft Docs
description: 了解如何在 Azure 安全中心管理用户数据。 管理用户数据包括访问、删除或导出数据的能力。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: bf715d872fab421de30ebcb146a1981a7d008738
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585976"
---
# <a name="manage-user-data-in-azure-security-center"></a>在 Azure 安全中心内管理用户数据
本文提供了有关如何在 Azure 安全中心内管理用户数据的信息。 管理用户数据包括访问、删除或导出数据的能力。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

分配了读者、所有者、参与者或帐户管理员角色的安全中心用户可以在该工具中访问客户数据。 要了解有关帐户管理员角色详细信息，请参阅[Azure 基于角色的访问控件的内置角色](../role-based-access-control/built-in-roles.md)，以了解有关"读者"、所有者角色和参与者角色。 请参阅[Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)。

## <a name="searching-for-and-identifying-personal-data"></a>搜索并标识个人数据
安全中心用户可以通过 Azure 门户查看其个人数据。 安全中心仅存储安全联系人详细信息，例如电子邮件地址和电话号码。 有关详细信息，请参阅在[Azure 安全中心中提供安全联系人详细信息](security-center-provide-security-contact-details.md)。

在 Azure 门户中，用户可以使用安全中心的实时 VM 访问功能查看允许的 IP 配置。 有关详细信息，请参阅[使用及时管理虚拟机访问](security-center-just-in-time.md)。

在 Azure 门户中，用户可以查看安全中心提供的安全警报，包括 IP 地址和攻击者详细信息。 有关详细信息，请参阅在[Azure 安全中心中管理和响应安全警报](security-center-managing-and-responding-alerts.md)。

## <a name="classifying-personal-data"></a>对个人数据进行分类
您无需对安全中心的安全联系人功能中找到的个人数据进行分类。 保存的数据是电子邮件地址（或多个电子邮件地址）和电话号码。 [联系人数据](security-center-provide-security-contact-details.md)由安全中心进行验证。

您无需对安全中心[及时](security-center-just-in-time.md)功能保存的 IP 地址和端口号进行分类。

只有分配有管理员角色的用户可以通过在安全中心内[查看警报](security-center-managing-and-responding-alerts.md)来对个人数据进行分类。

## <a name="securing-and-controlling-access-to-personal-data"></a>保护和控制对个人数据的访问
分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以访问[安全联系人数据](security-center-provide-security-contact-details.md)。

分配了读取器、所有者、参与者或帐户管理员角色的安全中心用户可以访问其[实时](security-center-just-in-time.md)策略。

分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以查看其[警报](security-center-managing-and-responding-alerts.md)。

## <a name="updating-personal-data"></a>更新个人数据
分配有所有者、参与者或帐户管理员角色的安全中心用户可以通过 Azure 门户更新[安全联系人数据](security-center-provide-security-contact-details.md)。

分配了所有者、参与者或帐户管理员角色的安全中心用户可以更新其[实时策略](security-center-just-in-time.md)。

帐户管理员无法编辑警报事件。 [警报事件](security-center-managing-and-responding-alerts.md)被视为安全数据并且是只读的。

## <a name="deleting-personal-data"></a>删除个人数据
分配有所有者、参与者或帐户管理员角色的安全中心用户可以通过 Azure 门户删除[安全联系人数据](security-center-provide-security-contact-details.md)。

分配了所有者、参与者或帐户管理员角色的安全中心用户可以通过 Azure 门户删除[及时策略](security-center-just-in-time.md)。

安全中心用户无法删除警报事件。 出于安全原因，[警报事件](security-center-managing-and-responding-alerts.md)被视为只读数据。

## <a name="exporting-personal-data"></a>导出个人数据
分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以通过以下方式导出[安全联系人数据](security-center-provide-security-contact-details.md)：

- 从 Azure 门户复制
- 执行 Azure REST API 调用、GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

分配了帐户管理员角色的安全中心用户可以通过以下策略导出包含 IP 地址[的及时策略](security-center-just-in-time.md)：

- 从 Azure 门户复制
- 执行 Azure REST API 调用、GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

帐户管理员可以通过以下方式导出警报详细信息：

- 从 Azure 门户复制
- 执行 Azure REST API 调用、GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

有关详细信息，请参阅[获取安全警报（GET 集合）。](https://msdn.microsoft.com/library/mt704050.aspx)

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>限制在未经同意的情况下将个人数据用于分析或营销
安全中心用户可以选择通过删除其[安全联系人数据](security-center-provide-security-contact-details.md)来选择退出。

[实时数据](security-center-just-in-time.md)被视为不可识别数据，并保留 30 天。

[警报数据](security-center-managing-and-responding-alerts.md)被视为安全数据，并且保留两年。

## <a name="auditing-and-reporting"></a>审核和报告
审核安全联系人、及时和警报更新的审核日志在[Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中维护。