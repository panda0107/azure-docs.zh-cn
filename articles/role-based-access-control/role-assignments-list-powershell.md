---
title: 使用 Azure RBAC 和 Azure PowerShell 列出角色分配
description: 了解如何使用 Azure 基于角色的访问控制 (RBAC) 和 Azure PowerShell 来确定用户、组、服务主体和托管标识有权访问的资源内容。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931154"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>使用 Azure RBAC 和 Azure PowerShell 列出角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍如何使用 Azure PowerShell 列出角色分配。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> 如果您的组织将管理功能外包给使用[Azure 委派资源的](../lighthouse/concepts/azure-delegated-resource-management.md)服务提供商，则该服务提供商授权的角色分配将不会在此处显示。

## <a name="prerequisites"></a>先决条件

- [Azure 云外壳](/azure/cloud-shell/overview)或[Azure PowerShell](/powershell/azure/install-az-ps)中的 PowerShell

## <a name="list-role-assignments-for-the-current-subscription"></a>列出当前订阅的角色分配

若要获取当前订阅中所有角色分配的列表（包括从根和管理组继承的角色分配），最简单的方法是使用不带任何参数的 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>列出订阅的角色分配

若要列出订阅范围内的所有角色分配，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。 若要获取订阅 ID，可以在 Azure 门户中的“订阅”**** 边栏选项卡上找到它，也可以使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>为用户列出角色分配

若要列出分配给指定用户的所有角色，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

若要列出分配给指定用户的所有角色和分配给该用户所属组的所有角色，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>列出资源组的角色分配

若要列出资源组范围内的所有角色分配，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理组的角色分配

若要列出管理组范围内的所有角色分配，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。 若要获取管理组 ID，可以在 Azure 门户中的“管理组”**** 边栏选项卡上找到它，也可以使用 [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>列出经典服务管理员和共同管理员的角色分配

若要列出经典订阅管理员和共同管理员的角色分配，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出托管标识的角色分配

1. 获取系统分配的或用户分配的托管标识的对象 ID。 

    若要获取用户分配的托管标识的对象 ID，可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. 若要列出角色分配，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 Azure PowerShell 添加或删除角色分配](role-assignments-powershell.md)
