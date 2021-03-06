---
title: Azure IoT 边缘模块提供发布概述 |Azure 应用商店
description: 在 Azure 市场中发布 IoT Edge 模块套餐的过程概述。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 20c76cec82944568c1b16694bef2838626b90b03
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983341"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge 模块套餐发布概述

>[!Important]
>从 2020 年 4 月 13 日起，我们将开始将 IoT Edge 模块产品的管理转移到合作伙伴中心。 迁移后，您将在合作伙伴中心创建和管理您的优惠。 按照[创建 IoT Edge 模块产品/服务](https://aka.ms/AzureCreateIoT)中的说明进行操作，以管理迁移的优惠。

<table> <tr> <td>本部分介绍如何将新的 Azure IoT Edge 模块产品/服务发布到 Microsoft <a href="https://azuremarketplace.microsoft.com">Azure 市场</a>。 IoT Edge 模块是可在 IoT Edge 设备上运行的与 Docker 兼容的容器。 Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务或自定义解决方案代码。 </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>发布过程

概括而言，发布 IoT Edge 模块套餐的步骤包括：

1. 创建套餐<br> 提供有关套餐的详细信息。 此信息包括：套餐说明、营销材料、支持信息和资产规格。

2. 创建业务资产和技术资产<br> 为关联的解决方案（托管在 Azure 容器注册表中的 IoT Edge 模块映像）创建业务资产（法律文档和营销材料）和技术资产。

3. 创建 SKU<br> 创建与套餐关联的 SKU。 必须为要发布的每个映像指定唯一的 SKU。

4. 认证并发布套餐 <br>完成产品/服务和技术资产以后，即可发布该产品/服务。 提交后会启动发布过程。 在此过程中，将会测试、验证并认证该解决方案，然后在 Azure 市场中将其“上线”。

## <a name="parts-of-an-offer"></a>套餐的组成部分

以下文章介绍了 IoT Edge 模块套餐的关键组成部分。

- [先决条件](./cpp-prerequisites.md) <br>此文列出了在创建或发布 IoT Edge 模块套餐之前所要满足的技术和业务要求。
- [准备 IoT Edge 模块技术资产](./cpp-create-technical-assets.md) <br>此文介绍了如何准备 IoT Edge 模块的技术资产。 在 Azure 市场中发布 IoT Edge 模块之前，这些资产必须满足所有所需技术条件。
- [创建 IoT Edge 模块产品/服务](./cpp-create-offer.md) <br>此文列出了使用[云合作伙伴门户](https://cloudpartner.azure.com)创建新 IoT Edge 模块套餐项所要执行的步骤。
- [发布 IoT Edge 模块套餐](./cpp-publish-offer.md)<br> 此文介绍了如何提交套餐，以便在 Azure 市场中发布。

## <a name="next-steps"></a>后续步骤

查看将 IoT Edge 模块发布到 Microsoft Azure 市场所要满足的[技术和业务要求](./cpp-prerequisites.md)。
