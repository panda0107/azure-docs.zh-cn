---
title: 为 Azure Cosmos DB 帐户配置客户管理的密钥
description: 了解如何使用 Azure Key Vault 为 Azure Cosmos DB 帐户配置客户管理的密钥
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 6e2a90b8f81b9b945905ee98beb1686c54a62e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063754"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>使用 Azure Key Vault 为 Azure Cosmos 帐户配置客户管理的密钥

> [!NOTE]
> 目前，必须请求访问权限才能使用此功能。 为此，请联系[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)。

存储在 Azure Cosmos 帐户中的数据会自动无缝加密，使用 Microsoft 管理的**密钥（服务管理密钥**）。 或者，您可以选择使用您管理的密钥（**客户管理的密钥**）添加第二层加密。

![围绕客户数据的加密层](./media/how-to-setup-cmk/cmk-intro.png)

必须在[Azure 密钥保管库中](../key-vault/key-vault-overview.md)存储客户管理的密钥，并为使用客户管理的密钥启用的每个 Azure Cosmos 帐户提供密钥。 此密钥用于加密该帐户中存储的所有数据。

> [!NOTE]
> 目前，客户管理的密钥仅适用于新的 Azure Cosmos 帐户。 应在帐户创建期间配置它们。

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>注册 Azure 订阅的 Azure 宇宙数据库资源提供程序

1. 登录到 Azure[门户](https://portal.azure.com/)，转到 Azure 订阅，并在 **"设置"** 选项卡下选择**资源提供程序**：

   ![左侧菜单中的“资源提供程序”项](./media/how-to-setup-cmk/portal-rp.png)

1. 搜索 **Microsoft.DocumentDB** 资源提供程序。 确认该资源提供程序是否标记为已注册。 如果不是，请选择该资源提供程序，然后选择“注册”：****

   ![注册 Microsoft.DocumentDB 资源提供程序](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure Key Vault 实例

对 Azure Cosmos DB 使用客户管理的密钥需要在你打算用来托管加密密钥的 Azure Key Vault 实例上设置两个属性。 这些属性包括“软删除”和“不清除”。******** 默认情况下未启用这些属性。 可以使用 PowerShell 或 Azure CLI 启用它们。

若要了解如何在现有 Azure Key Vault 实例上启用这些属性，请参阅以下文章之一中的“启用软删除”和“启用清除保护”部分：

- [如何使用电源Shell软删除](../key-vault/key-vault-soft-delete-powershell.md)
- [如何通过 Azure CLI 使用软删除](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>将访问策略添加到 Azure Key Vault 实例

1. 在 Azure 门户中，转到你打算用来托管加密密钥的 Azure Key Vault 实例。 在左侧菜单中选择“访问策略”：****

   ![左侧菜单中的“访问策略”](./media/how-to-setup-cmk/portal-akv-ap.png)

1. 选择 **= 添加访问策略**。

1. 在 **"键权限**下拉菜单"下，**选择"获取**"、"**取消包装密钥**"和 **"包装密钥**"权限：

   ![选择适当的权限](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. 在“选择主体”下，选择“未选择任何项”。******** 然后，搜索**Azure Cosmos DB**主体并选择它（为了更容易查找，还可以按主体 ID 搜索：`a232010e-820c-4083-83bb-3ace5fc29d0b`除主 ID 所在的`57506a73-e302-42a9-b869-6f12d9ec29e9`Azure 政府区域外的任何 Azure 区域。 最后，**选择底部的"选择**"。 如果**Azure Cosmos DB**主体不在列表中，则可能需要重新注册**Microsoft.DocumentDB**资源提供程序，如[注册本文的资源提供程序](#register-resource-provider)部分所述。

   ![选择 Azure Cosmos DB 主体](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 选择 **"添加**"以添加新的访问策略。

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中生成密钥

1. 在 Azure 门户中，转到你打算用来托管加密密钥的 Azure Key Vault 实例。 然后在左侧菜单中选择“密钥”：****

   ![左侧菜单中的“密钥”项](./media/how-to-setup-cmk/portal-akv-keys.png)

1. 选择 **"生成/导入**"，为新密钥提供名称，然后选择 RSA 密钥大小。 建议至少 3072 提供最佳安全性。 然后选择 **"创建**"

   ![新建密钥](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 创建密钥后，选择新创建的密钥，然后选择其当前版本。

1. 复制密钥**的密钥标识符**，最后一个前斜杠后部件除外：

   ![复制密钥的密钥标识符](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>创建新的 Azure Cosmos 帐户

### <a name="using-the-azure-portal"></a>使用 Azure 门户

从 Azure 门户创建新的 Azure Cosmos DB 帐户时，在**加密**步骤中选择**客户管理的密钥**。 在 **"密钥 URI"** 字段中，粘贴从上一步骤复制的 Azure 密钥保管库密钥的 URI/键标识符：

![在 Azure 门户中设置 CMK 参数](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用 PowerShell 创建新的 Azure Cosmos DB 帐户时：

- 传递之前在**属性对象**中的**keyVaultKeyUri**属性下复制的 Azure 密钥保管库密钥的 URI。

- 使用 **2019-12-12** 作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `Location` 参数，才能使用客户管理的密钥成功创建帐户。

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

通过 Azure 资源管理器模板创建新的 Azure Cosmos 帐户时：

- 在 **properties** 对象中的 **keyVaultKeyUri** 属性下，传递前面复制的 Azure Key Vault 密钥 URI。

- 使用 **2019-12-12** 作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `Location` 参数，才能使用客户管理的密钥成功创建帐户。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

使用以下 PowerShell 脚本部署模板：

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

通过 Azure CLI 创建新的 Azure Cosmos 帐户时，请传递之前在 **--key-uri**参数下复制的 Azure 密钥保管库密钥的 URI。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>使用客户管理的密钥是否收取额外费用？

是的。 为了考虑使用客户管理的密钥管理数据加密和解密所需的额外计算负载，针对 Azure Cosmos 帐户执行的所有操作都使用[请求单元](./request-units.md)增加 25%。

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些数据是使用客户管理的密钥加密的？

存储在 Azure Cosmos 帐户中的所有数据都使用客户管理的密钥进行加密，但以下元数据除外：

- Azure Cosmos DB [帐户、数据库和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名称

- [存储过程](./stored-procedures-triggers-udfs.md)的名称

- [索引策略](./index-policy.md)中声明的属性路径

- 容器[分区键](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>现有的 Azure Cosmos 帐户是否支持客户管理的密钥？

此功能当前仅适用于新帐户。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>你们是否有计划支持比帐户级密钥更精细的粒度？

当前未，但正在考虑容器级密钥。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>客户管理的密钥如何影响备份？

Azure Cosmos DB [定期自动备份](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)帐户中存储的数据。 此操作会备份已加密的数据。 若要使用还原的备份，需要提供备份时使用的加密密钥。 这意味着未进行吊销，并且仍将启用备份时使用的密钥版本。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何吊销加密密钥？

可以通过禁用密钥的最新版本来吊销密钥：

![禁用密钥的版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，若要从 Azure Key Vault 实例中吊销所有密钥，可以删除授予 Azure Cosmos DB 主体的访问策略：

![删除 Azure Cosmos DB 主体的访问策略](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>吊销客户管理的密钥后可执行哪些操作？

吊销加密密钥后，删除帐户是唯一可执行的操作。

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 宇宙 DB 中数据加密](./database-encryption-at-rest.md)的更多详细信息。
- 获取[对 Cosmos DB 中数据的安全访问](secure-access-to-data.md)概述。
