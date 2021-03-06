---
title: 从 VHD 创建快照以创建多个相同的托管磁盘 - PowerShell 示例
description: Azure PowerShell 脚本示例 - 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: de89ff77d09124449b64f664c60c72f870319b93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75613799"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>使用 PowerShell 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘

此脚本基于 VHD 文件在同一或不同订阅中的存储帐户中创建快照。 可以使用此脚本将专用（非通用/系统准备的）VHD 导入到某个快照中，然后使用该快照在短时间内创建多个相同的托管磁盘。 另外，还可以使用它将数据 VHD 导入到某个快照中，然后使用该快照在短时间内创建多个托管磁盘。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>后续步骤

[基于快照创建托管磁盘](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[通过将托管磁盘附加为 OS 磁盘创建虚拟机](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
