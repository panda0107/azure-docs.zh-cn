---
title: 配置 Windows 虚拟桌面负载平衡 - Azure
description: 如何为 Windows 虚拟桌面环境配置负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128307"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>配置 Windows 虚拟桌面负载均衡方法

通过配置主机池的负载平衡方法，您可以调整 Windows 虚拟桌面环境以更好地满足您的需求。

>[!NOTE]
> 这不适用于持久桌面主机池，因为用户始终具有 1：1 映射到主机池中的会话主机。

## <a name="configure-breadth-first-load-balancing"></a>配置广度优先负载平衡

宽度优先负载平衡是新的非持久性主机池的默认配置。 "最先"负载平衡将在主机池中的所有可用会话主机上分发新的用户会话。 配置广度优先负载平衡时，可以设置主机池中每个会话主机的最大会话限制。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

要将主机池配置为在不调整最大会话限制的情况下执行广度优先负载平衡，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

要配置主机池以执行广度优先负载平衡并使用新的最大会话限制，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>配置深度优先负载平衡

深度优先负载平衡将新用户会话分发到连接次数最多但未达到其最大会话限制阈值的可用会话主机。 配置深度优先负载平衡时，**必须在**主机池中设置每个会话主机的最大会话限制。

要配置主机池以执行深度优先负载平衡，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
