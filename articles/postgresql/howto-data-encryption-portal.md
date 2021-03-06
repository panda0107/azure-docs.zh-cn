---
title: 数据加密 - Azure 门户 - 用于后格雷SQL的 Azure 数据库 - 单个服务器
description: 了解如何使用 Azure 门户为 PostgreSQL 单一服务器设置和管理 Azure 数据库的数据加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 847e3c612a200743fa08cf939c9995ebb6f3dbfc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520328"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>通过使用 Azure 门户，为后格雷SQL 单个服务器的 Azure 数据库的数据加密

了解如何使用 Azure 门户为 PostgreSQL 单一服务器的 Azure 数据库设置和管理数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 在 Azure 密钥保管库中，创建用于客户管理的密钥的密钥保管库和密钥。
* 密钥保管库必须具有以下属性才能用作客户管理的密钥：
  * [软删除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 密钥必须具有以下属性才能用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 能够执行获取、包装键和解包键操作

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在密钥保管库中，选择**访问策略** > **添加访问策略**。

   ![突出显示了密钥保管库的屏幕截图，并突出显示了访问策略和添加访问策略](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 选择 **"密钥权限**"，然后选择 **"获取**、**换行**、**解包**"和 **"主体**"，这是 PostgreSQL 服务器的名称。 如果在现有主体列表中找不到服务器主体，则需要注册它。 首次尝试设置数据加密时，系统会提示您注册服务器主体，但该加密失败。  

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 选择“保存”。 

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>为后格雷SQL单一服务器设置 Azure 数据库的数据加密

1. 在 PostgreSQL 的 Azure 数据库中，选择**数据加密**以设置客户管理的密钥。

   ![Azure 数据库的屏幕截图，用于 PostgreSQL，并突出显示数据加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以选择密钥保管库和密钥对，也可以输入密钥标识符。

   ![用于 PostgreSQL 的 Azure 数据库屏幕截图，突出显示了数据加密选项](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 选择“保存”。 

4. 为确保所有文件（包括临时文件）完全加密，请重新启动服务器。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>对还原服务器或副本服务器使用数据加密

在使用存储在密钥保管库中的客户托管密钥对 PostgreSQL 单个服务器的 Azure 数据库进行加密后，对新创建的服务器副本也进行加密。 您可以通过本地或异地还原操作或通过副本（本地/跨区域）操作制作此新副本。 因此，对于加密的 PostgreSQL 服务器，可以使用以下步骤创建加密还原的服务器。

1. 在服务器上，选择 **"概述** > **还原**"。

   ![Azure 数据库的屏幕截图，用于 PostgreSQL，突出显示了概述和还原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或者，对于启用复制的服务器，在 **"设置"** 标题下，选择 **"复制**"。

   ![Azure 数据库的屏幕截图，用于 PostgreSQL，并突出显示复制](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 还原操作完成后，创建的新服务器将使用主服务器的密钥进行加密。 但是，服务器上的功能和选项被禁用，并且无法访问服务器。 这可以防止任何数据操作，因为新服务器的标识尚未被授予访问密钥保管库的权限。

   ![用于 PostgreSQL 的 Azure 数据库屏幕截图，突出显示不可访问状态](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 要使服务器可访问，请重新验证还原服务器上的密钥。 选择**数据加密** > **重新验证密钥**。

   > [!NOTE]
   > 第一次重新验证的尝试将失败，因为需要授予新服务器的服务主体对密钥保管库的访问权限。 要生成服务主体，请选择 **"重新验证密钥**"，该键将显示错误，但会生成服务主体。 之后，请参阅[本文前面的步骤](#set-the-right-permissions-for-key-operations)。

   ![后格雷SQL Azure 数据库的屏幕截图，重新验证步骤突出显示](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必须授予密钥保管库对新服务器的访问权限。

4. 注册服务主体后，再次重新验证密钥，服务器将恢复其正常功能。

   ![用于 PostgreSQL 的 Azure 数据库屏幕截图，显示还原的功能](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>后续步骤

 要了解有关数据加密的详细信息，请参阅[Azure 数据库，用于 PostgreSQL 单一服务器数据加密，并使用客户管理的密钥](concepts-data-encryption-postgresql.md)。
