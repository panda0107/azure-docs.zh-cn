---
title: 适用于 Windows 的 Azure 磁盘加密
description: 使用虚拟机扩展将 Azure 磁盘加密部署到 Windows 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066869"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>适用于 Windows 的 Azure 磁盘加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概述

Azure 磁盘加密利用 BitLocker 在运行 Windows 的 Azure 虚拟机上提供完全磁盘加密。  此解决方案与 Azure Key Vault 集成，以管理 Key Vault 订阅中的磁盘加密密钥和机密。 

## <a name="prerequisites"></a>先决条件

有关先决条件的完整列表，请参阅 Windows [VM 的 Azure 磁盘加密](../windows/disk-encryption-overview.md)，具体介绍以下部分：

- [支持的 VM 和操作系统](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络要求](../windows/disk-encryption-overview.md#networking-requirements)
- [组策略要求](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>扩展架构

Azure 磁盘加密 （ADE） 有两个版本的扩展架构：
- v2.2 - 不使用 Azure 活动目录 （AAD） 属性的较新的建议架构。
- v1.1 - 需要 Azure 活动目录 （AAD） 属性的旧架构。 

要选择目标架构，`typeHandlerVersion`必须将属性设置为等于要使用的架构版本。

### <a name="schema-v22-no-aad-recommended"></a>架构 v2.2： 无 AAD（推荐）

v2.2 架构建议用于所有新 VM，并且不需要 Azure Active Directory 属性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>架构 v1.1：使用 AAD 

1.1 架构需要 `aadClientID` 和 `aadClientSecret` 或 `AADClientCertificate`，建议不要用于新 VM。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

使用 `AADClientCertificate`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>属性值

| “属性” | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| 发布者 | Microsoft.Azure.Security | 字符串 |
| type | AzureDiskEncryption | 字符串 |
| typeHandlerVersion | 2.2, 1.1 | 字符串 |
| （1.1 架构）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| （1.1 架构）AADClientSecret | password | 字符串 |
| （1.1 架构）AADClientCertificate | thumbprint | 字符串 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 字符串 | 
| （可选 - 默认 RSA-OAEP ）密钥加密算法 | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 字符串 |
| KeyVaultURL | url | 字符串 |
| KeyVaultResourceId | url | 字符串 |
| （可选）密钥加密键URL | url | 字符串 |
| （可选）凯库沃资源Id | url | 字符串 |
| （可选）序列版本 | uniqueidentifier | 字符串 |
| VolumeType | OS, Data, All | 字符串 |

## <a name="template-deployment"></a>模板部署

有关基于架构 v2.2 的模板部署示例，请参阅 Azure 快速入门模板[201-加密运行-窗口-vm-无 aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)。

有关基于架构 v1.1 的模板部署示例，请参阅 Azure 快速入门模板[201 加密运行窗口 vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)。

>[!NOTE]
> 此外，`VolumeType`如果参数设置为 All，则数据磁盘仅在正确格式化时才会加密。 

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关故障排除，请参阅 [Azure 磁盘加密故障排除指南](../windows/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支持

如果本文中的任何一点都需要更多帮助，则可以在[MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/community/)上联系 Azure 专家。 

或者，你也可以提出 Azure 支持事件。 转到[Azure 支持](https://azure.microsoft.com/support/options/)并选择"获取支持"。 有关使用 Azure 支持的信息，请阅读[Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

* 有关扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](features-windows.md)。
* 有关 Windows 的 Azure 磁盘加密的详细信息，请参阅[Windows 虚拟机](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)。
