---
title: 虚拟机缩放集的邻近放置组预览
description: 了解如何在 Azure 中创建和使用用于 Windows 虚拟机规模集的邻近放置组。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273622"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>预览：使用 PowerShell 创建和使用接近放置组

若要让 VM 尽可能靠近，将延迟尽可能降至最低，应将规模集部署到一个[邻近放置组](co-location.md#preview-proximity-placement-groups)中。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。

> [!IMPORTANT]
> 邻近放置组目前为公开预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 在预览期间，这些地区没有接近放置组：**日本东部**、**澳大利亚东部****和印度中部**。


## <a name="create-a-proximity-placement-group"></a>创建邻近放置组
使用 [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet 创建邻近放置组。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>列出邻近放置组

可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet 列出所有邻近放置组。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>创建规模集

通过 [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) 创建规模集时，请在邻近放置组中创建规模集，使用 `-ProximityPlacementGroup $ppg.Id` 引用邻近放置组 ID。

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) 查看放置组中的实例。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>后续步骤

也可使用 [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) 创建邻近放置组。
