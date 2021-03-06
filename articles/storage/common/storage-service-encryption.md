---
title: 静态数据的 Azure 存储加密
description: Azure 存储在将数据保存到云之前会自动对其进行加密，以此保护数据。 您可以依赖 Microsoft 管理的密钥来加密存储帐户中的数据，也可以使用自己的密钥管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409824"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

Azure 存储在将数据保存到云时会自动加密数据。 Azure 存储加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-azure-storage-encryption"></a>关于 Azure 存储加密

Azure 存储中的数据使用 256 位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)进行加密和透明解密，这是可用的最强的块密码之一，并且符合 FIPS 140-2 标准。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

为所有存储帐户（包括资源管理器和经典存储帐户）启用 Azure 存储加密。 无法禁用 Azure 存储加密。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。

无论性能层（标准层或高级级）、访问层（热或酷）或部署模型（Azure 资源管理器或经典）如何，存储帐户中的数据都会加密。 存档层中的所有 Blob 也进行加密。 所有 Azure 存储冗余选项都支持加密，启用异地复制后，主区域和辅助区域中的所有数据都会加密。 所有 Azure 存储资源（包括 Blob、磁盘、文件、队列和表）都会加密。 所有对象元数据也会加密。 Azure 存储加密不会产生额外的费用。

2017 年 10 月 20 日后写入 Azure 存储的每个块 Blob、追加 Blob 或页 Blob 均已加密。 在此日期之前创建的 Blob 继续由后台进程加密。 若要强制对 2017 年 10 月 20 日之前创建的 Blob 进行加密，可以重写 Blob。 若要了解如何检查 Blob 的加密状态，请参阅 [检查 Blob 的加密状态](../blobs/storage-blob-encryption-status.md)。

有关 Azure 存储加密基础的加密模块的详细信息，请参阅[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，存储帐户中的数据使用 Microsoft 管理的密钥进行加密。 您可以依赖 Microsoft 管理的密钥来加密数据，也可以使用自己的密钥管理加密。 如果你选择使用自己的密钥来管理加密，则可以采用两种做法：

- 您可以使用 Azure 密钥保管库指定*客户管理的密钥*，用于加密和解密 Blob 存储和 Azure 文件中的数据。<sup>1，2</sup>有关客户管理的密钥的详细信息，请参阅使用[Azure 密钥保管库使用客户管理的密钥来管理 Azure 存储加密](encryption-customer-managed-keys.md)。
- 可以在 Blob 存储操作中指定客户提供的密钥。** 对 Blob 存储发出读取或写入请求的客户端可以在请求中包含加密密钥，以便精细控制 Blob 数据的加密和解密方式。 有关客户提供密钥的详细信息，请参阅[在请求 Blob 存储时提供加密密钥（预览）。](encryption-customer-provided-keys.md)

下表比较了 Azure 存储加密的密钥管理选项。

|                                        |    Microsoft 管理的密钥                             |    客户管理的密钥                                                                                                                        |    客户提供的密钥                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支持的 Azure 存储服务    |    All                                                |    Blob 存储，Azure 文件<sup>1，2</sup>                                                                                                               |    Blob 存储                                                                  |
|    密钥存储                         |    Microsoft 密钥存储    |    Azure Key Vault                                                                                                                              |    Azure Key Vault 或任何其他密钥存储                                                                 |
|    密钥轮换责任         |    Microsoft                                          |    客户                                                                                                                                     |    客户                                                                      |
|    密钥使用情况                           |    Microsoft                                          |    Azure 门户、存储资源提供程序 REST API、Azure 存储管理库、PowerShell、CLI        |    Azure 存储 REST API（Blob 存储）、Azure 存储客户端库    |
|    密钥访问权限                          |    仅限 Microsoft                                     |    Microsoft、客户                                                                                                                    |    仅限客户                                                                 |

<sup>1</sup>有关创建支持使用具有队列存储的客户托管密钥的帐户的信息，请参阅[创建一个帐户，该帐户支持队列的客户托管密钥](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup>有关创建支持将客户管理的密钥与表存储一起使用的帐户的信息，请参阅[创建一个帐户，该帐户支持表的客户托管密钥](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault？](../../key-vault/key-vault-overview.md)
- [通过 Azure 门户配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)
