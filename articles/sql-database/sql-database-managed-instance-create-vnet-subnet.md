---
title: 为托管实例创建虚拟网络
description: 本文介绍如何创建可在其中部署 Azure SQL 数据库托管实例的虚拟网络。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 0ce88f9a61b8aa7c2588a6e077d694afa6fb8631
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878844"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例创建虚拟网络

本文说明如何创建可在其中部署 Azure SQL 数据库托管实例的有效虚拟网络和子网。

Azure SQL 数据库托管实例必须部署在 Azure [虚拟网络](../virtual-network/virtual-networks-overview.md)中。 此部署支持以下方案：

- 保护专用 IP 地址
- 直接从本地网络连接到托管实例
- 将托管实例连接到链接服务器或其他本地数据存储
- 将托管实例连接到 Azure 资源  

> [!NOTE]
> 在部署第一个实例之前，应[确定托管实例的子网大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 在资源放入子网后，无法调整子网大小。
>
> 如果打算使用现有的虚拟网络，则需要修改该网络的配置，以适应托管实例。 有关详细信息，请参阅[根据托管实例修改现有的虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)。
>
> 创建托管实例后，不支持将托管实例或 VNet 移到另一个资源组或订阅。  不支持将托管实例移动到另一个子网。
>

## <a name="create-a-virtual-network"></a>创建虚拟网络

创建和配置虚拟网络的最简单方法是使用 Azure 资源管理器部署模板。

1. 登录到 Azure 门户。

2. 选择“部署到 Azure”按钮：****

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   单击此按钮会打开一个窗体，在其中可以配置用于部署托管实例的网络环境。

   > [!Note]
   > 此 Azure 资源管理器模板将部署包含两个子网的虚拟网络。 一个名为 **ManagedInstances** 的子网预留给托管实例并且已预配置了路由表。 名为 **Default** 的另一个子网用于应当访问托管实例的其他资源（例如 Azure 虚拟机）。

3. 配置网络环境。 在以下窗体上，可以配置网络环境的参数：

   ![用于配置 Azure 网络的资源管理器模板](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   可以更改虚拟网络和子网的名称，并调整与网络资源关联的 IP 范围。 选择“购买”按钮后，此窗体将创建并配置环境。**** 如果不需要两个子网，可以删除默认子网。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例？](sql-database-managed-instance.md)
- 了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[修改托管实例的现有虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)
- 有关如何创建虚拟网络、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
