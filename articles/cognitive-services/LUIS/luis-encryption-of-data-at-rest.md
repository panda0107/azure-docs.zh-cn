---
title: 静态数据的语言理解服务加密
titleSuffix: Azure Cognitive Services
description: 语言理解服务对静态数据进行加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372333"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>静态数据的语言理解服务加密

语言理解服务在数据保存到云时会自动加密数据。 语言理解服务加密可保护您的数据，并帮助您履行组织安全和合规性承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)兼容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密对数据进行加密和解密。 加密和解密是透明的，这意味着加密和访问是为您管理的。 默认情况下，您的数据是安全的，您无需修改代码或应用程序就可以利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，您的订阅使用 Microsoft 管理的加密密钥。 还有一个选项可以用自己的密钥管理订阅。 客户管理的密钥 （CMK） 提供了创建、旋转、禁用和撤销访问控制的更大灵活性。 还可以审核用于保护数据的加密密钥。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥与 Azure Key Vault

还有一个选项可以用自己的密钥管理订阅。 客户管理的密钥 （CMK）也称为"自带密钥 （BYOK"），为创建、旋转、禁用和撤销访问控制提供了更大的灵活性。 还可以审核用于保护数据的加密密钥。

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和同一 Azure 活动目录 （Azure AD） 租户中，但它们可以位于不同的订阅中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>客户管理的语言理解密钥

要请求使用客户管理的密钥，请填写并提交 [LUIS 服务客户管理密钥请求表](https://aka.ms/cogsvc-cmk)。 大约需要 3-5 个工作日才能回复您的请求状态。 根据需求，您可能会被放置在队列中，并在可用空间时获得批准。 批准将 CMK 与 LUIS 一起使用后，您需要从 Azure 门户创建新的语言理解资源，并选择 E0 作为定价层。 除 CMK 外，新的 SKU 的功能与已可用的 F0 SKU 相同。 用户将无法从 F0 升级到新的 E0 SKU。

E0 资源仅适用于创作服务，E0 层最初仅在美国西部区域受支持。

![LUIS 订阅映像](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>区域可用性

客户管理的密钥目前**在美国西部**区域可用。

### <a name="limitations"></a>限制

将 E0 层与现有/以前创建的应用程序一起使用时存在一些限制：

* 将阻止迁移到 E0 资源。 用户只能将其应用迁移到 F0 资源。 将现有资源迁移到 F0 后，可以在 E0 层中创建新资源。 [在此处了解有关迁移的更多详细信息](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)。  
* 将阻止将应用程序移动到或从 E0 资源移动到或从 E0 资源移动到。 此限制的解决方法是导出现有应用程序，并将其导入为 E0 资源。
* 不支持必应拼写检查功能。
* 如果应用程序为 E0，则禁用日志记录最终用户流量。
* E0 层中的应用程序不支持 Azure Bot 服务中的语音准备功能。 此功能可通过不支持 CMK 的 Azure 机器人服务获得。
* 来自门户的语音启动功能需要 Azure Blob 存储。 有关详细信息，请参阅[自带存储](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

新的认知服务资源始终使用 Microsoft 管理的密钥进行加密。 在创建资源时无法启用客户管理的密钥。 客户管理的密钥存储在 Azure 密钥保管库中，并且必须预配密钥保管库，这些访问策略向与认知服务资源关联的托管标识授予密钥权限。 只有在使用 CMK 的定价层创建资源后，托管标识才可用。

要了解如何将客户管理的密钥与 Azure 密钥保管库用于认知服务加密，请参阅：

- [使用密钥保管库配置客户管理的密钥，以便从 Azure 门户进行认知服务加密](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户托管密钥还将启用系统分配的托管标识，这是 Azure AD 的一项功能。 启用系统分配的托管标识后，此资源将注册到 Azure 活动目录。 注册后，托管标识将有权访问客户托管密钥设置期间选择的密钥保管库。 您可以了解有关[托管标识的更多信息](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，将删除对密钥保管库的访问，并且将不再访问使用客户密钥加密的任何数据。 依赖于此数据的任何功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户托管密钥时，托管标识将自动在封面下分配。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与资源关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”****。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

要启用客户管理的密钥，必须使用 Azure 密钥保管库来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性********。

认知服务加密仅支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的“Key Vault 密钥”。****

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 旋转密钥时，必须更新认知服务资源才能使用新的密钥 URI。 要了解如何更新资源以在 Azure 门户中使用密钥的新版本，请参阅[使用 Azure 门户在"配置认知服务的客户管理密钥"](../Encryption/cognitive-services-encryption-keys-portal.md)中**更新密钥版本的**部分。

旋转密钥不会触发资源中数据的重新加密。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问可有效地阻止对认知服务资源中的所有数据的访问，因为认知服务无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

* [LUIS 服务客户管理的关键请求表](https://aka.ms/cogsvc-cmk)
* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
