---
title: 使用 PowerShell 管理 Azure CDN | Microsoft Docs
description: 了解如何使用 Azure PowerShell cmdlet 来管理 Azure CDN。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: magattus
ms.openlocfilehash: 476779eff41cb9ce1c0a9c79430813ce9a39e91f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74286659"
---
# <a name="manage-azure-cdn-with-powershell"></a>使用 PowerShell 管理 Azure CDN
PowerShell 提供了一种最灵活的方法来管理 Azure CDN 配置文件和终结点。  可以使用 PowerShell 以交互方式或通过编写脚本来自动执行管理任务。  本教程演示了使用 PowerShell 可以完成的几个最常见的任务来管理 Azure CDN 配置文件和终结点。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 PowerShell 管理 Azure CDN 配置文件和终结点，必须已安装 Azure PowerShell 模块。  若要了解如何安装 Azure PowerShell 以及使用 `Connect-AzAccount` cmdlet 连接到 Azure，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

> [!IMPORTANT]
> 执行 Azure PowerShell cmdlet 之前，必须使用 `Connect-AzAccount` 登录。
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>列出 Azure CDN cmdlet
可以使用 `Get-Command`cmdlet 列出所有 Azure CDN cmdlet。

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Confirm-AzCdnEndpointProbeURL                      1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomain                          1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomainHttps                     1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomainHttps                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEdgeNode                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointResourceUsage                     1.4.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileResourceUsage                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                             1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSupportedOptimizationType          1.4.0      Az.Cdn
Cmdlet          Get-AzCdnSubscriptionResourceUsage                 1.4.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryPolicy                            1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRule                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleAction                        1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleCondition                     1.4.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          New-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                       1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                               1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                                1.4.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                                1.4.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                                 1.4.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                             1.4.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                     1.4.0      Az.Cdn
```

## <a name="getting-help"></a>获取帮助
通过使用 `Get-Help`cmdlet 可以获取这些 cmdlet 的帮助 。  `Get-Help` 提供了用法和语法，并显示示例（可选）。

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>列出现有的 Azure CDN 配置文件
不带任何参数的 `Get-AzCdnProfile` cmdlet 会检索所有现有的 CDN 配置文件。

```powershell
Get-AzCdnProfile
```

此输出可以输送到 cmdlet 用于枚举。

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

还可以通过指定配置文件名称和资源组来返回单个配置文件。

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> 可能会有多个同名的 CDN 配置文件，只要它们处于不同的资源组即可。  省略 `ResourceGroupName` 参数将返回具有匹配名称的所有配置文件。
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>列出现有的 CDN 终结点
`Get-AzCdnEndpoint` 可以检索配置文件上的单个终结点或所有终结点。  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>创建 CDN 配置文件和终结点
`New-AzCdnProfile` 和 `New-AzCdnEndpoint` 用于创建 CDN 配置文件和终结点。 支持以下 SKU：
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>检查终结点名称可用性
`Get-AzCdnEndpointNameAvailability` 会返回一个对象，指示终结点名称是否可用。

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>添加自定义域
`New-AzCdnCustomDomain` 会将自定义域名添加到现有的终结点。

> [!IMPORTANT]
> 必须按[如何将自定义域映射到内容分发网络 (CDN) 终结点](cdn-map-content-to-custom-domain.md)中所述，通过 DNS 提供商设置 CNAME。  在使用 `Test-AzCdnCustomDomain` 修改终结点之前可以测试映射 。
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>修改终结点
`Set-AzCdnEndpoint` 会修改现有的终结点。

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>清除/预加载 CDN 资产
`Unpublish-AzCdnEndpointContent` 会清除缓存的资产，而 `Publish-AzCdnEndpointContent` 会在受支持的终结点上预加载资产。

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>启动/停止 CDN 终结点
`Start-AzCdnEndpoint` 和 `Stop-AzCdnEndpoint` 可用于启动和停止单个终结点或一组终结点。

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="creating-standard-rules-engine-policy-and-applying-to-an-existing-cdn-endpoint"></a>创建标准规则引擎策略并应用于现有 CDN 终结点
`New-AzCdnDeliveryRule``New=AzCdnDeliveryRuleCondition`，`New-AzCdnDeliveryRuleAction`并可用于从 Microsoft 配置文件在 Azure CDN 上配置 Azure CDN 标准规则引擎。 

```powershell
# Create a new http to https redirect rule
$Condition=New-AzCdnDeliveryRuleCondition -MatchVariable RequestProtocol -Operator Equal -MatchValue HTTP
$Action=New-AzCdnDeliveryRuleAction -RedirectType Found -DestinationProtocol HTTPS
$HttpToHttpsRedirectRule=New-AzCdnDeliveryRule -Name "HttpToHttpsRedirectRule" -Order 2 -Condition $Condition -Action $Action

# Create a path based Response header modification rule. 
$Cond1=New-AzCdnDeliveryRuleCondition -MatchVariable UrlPath -Operator BeginsWith -MatchValue "/images/"
$Action1=New-AzCdnDeliveryRuleAction -HeaderActionType ModifyResponseHeader -Action Overwrite -HeaderName "Access-Control-Allow-Origin" -Value "*"
$PathBasedCacheOverrideRule=New-AzCdnDeliveryRule -Name "PathBasedCacheOverride" -Order 1 -Condition $Cond1 -Action $action1

# Create a delivery policy with above deliveryRules.
$Policy = New-AzCdnDeliveryPolicy -Description "DeliveryPolicy" -Rule $HttpToHttpsRedirectRule,$UrlRewriteRule

# Update existing endpoint with created delivery policy
$ep = Get-AzCdnEndpoint -EndpointName cdndocdemo -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
$ep.DeliveryPolicy = $Policy
Set-AzCdnEndpoint -CdnEndpoint $ep
```

## <a name="deleting-cdn-resources"></a>删除 CDN 资源
`Remove-AzCdnProfile` 和 `Remove-AzCdnEndpoint` 可用于删除配置文件和终结点。

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>后续步骤
了解如何通过 [.NET](cdn-app-dev-net.md) 或 [Node.js](cdn-app-dev-node.md) 实现 Azure CDN 的自动化。

要了解有关 CDN 功能的信息，请参阅 [CDN 概述](cdn-overview.md)。

