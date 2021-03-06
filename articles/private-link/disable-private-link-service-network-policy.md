---
title: '对 Azure 专用链接服务源 IP 地址禁用网络策略 '
description: 了解如何对 Azure 专用链接禁用网络策略。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452996"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>对专用链接服务源 IP 禁用网络策略

若要为专用链接服务选择源 IP 地址，子网上需要 `privateLinkServiceNetworkPolicies` 显式禁用设置。 此设置仅适用于特定专用 IP 地址，该地址已选择作为专用链接服务的源 IP 地址。 对于子网中的其他资源，访问权限基于网络安全组 (NSG) 安全规则定义进行控制。 
 
使用任何 Azure 客户端（PowerShell、CLI 或模板）时，需要通过额外的步骤来更改此属性。 可以使用 Azure 门户中的云外壳禁用策略，或者 Azure PowerShell、Azure CLI 的本地安装或使用 Azure 资源管理器模板。  
 
按照以下步骤为名为 *myVirtualNetwork* 的虚拟网络禁用专用链接服务网络策略，并在名为 *myResourceGroup* 的资源组中托管默认** 子网。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>使用模板
本部分介绍如何使用 Azure 资源管理器模板禁用子网专用终结点策略。
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用终结点](private-endpoint-overview.md)
 
