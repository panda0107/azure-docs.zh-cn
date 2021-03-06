---
title: 使用 Azure CLI 在开发人员测试实验室中创建和管理虚拟机
description: 了解如何通过 Azure 开发测试实验室，使用 Azure CLI 创建和管理虚拟机
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167049"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 通过开发测试实验室创建和管理虚拟机
此快速入门将指导您完成实验室中的开发计算机的创建、启动、连接、更新和清理。 

开始之前：

* 如果尚未创建实验室，[此处](devtest-lab-create-lab.md)提供相关说明。

* [安装 Azure CLI](/cli/azure/install-azure-cli)。 若要开始，请运行 az login，创建与 Azure 的连接。 

## <a name="create-and-verify-the-virtual-machine"></a>创建并验证虚拟机 
在执行 DevTest Labs 相关命令之前，请使用以下`az account set`命令设置相应的 Azure 上下文：

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

创建虚拟机的命令是： `az lab vm create`。 实验室、实验室名称和虚拟机名称的资源组都是必需的。 其余参数会根据虚拟机的类型而变化。

以下命令从 Azure 市场位置创建基于 Windows 的映像。 映像的名称与使用 Azure 门户创建虚拟机时看到的名称相同。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

以下命令基于实验室中可用的自定义映像创建虚拟机：

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**图像类型**参数已由**库**更改为**自定义**。 映像的名称与在 Azure 门户中创建虚拟机时看到的内容匹配。

以下命令从具有 ssh 身份验证的市场映像创建 VM：

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

还可以通过将**映像类型**参数设置为**公式**，根据公式创建虚拟机。 如果需要为虚拟机选择特定的虚拟网络，请使用**vnet 名称**和**子网**参数。 有关详细信息，请参阅创建[的 az 实验室 vm。](/cli/azure/lab/vm#az-lab-vm-create)

## <a name="verify-that-the-vm-is-available"></a>验证 VM 是否可用。
在启动`az lab vm show`并连接到 VM 之前，使用 命令验证 VM 是否可用。 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>启动并连接到虚拟机
以下示例命令启动 VM：

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

连接到 VM：[SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 或[远程桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虚拟机
以下示例命令将工件应用于 VM：

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>列出实验室中可用的项目

要列出实验室中 VM 中可用的项目，运行以下命令。

**云壳 - PowerShell**： 请注意在 $$expand （ 即 '$expand） 之前使用回勾 （\`） ：

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**云壳 - Bash**： 注意在命令\\的 $ 前面使用斜杠 （ ） 字符。 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

示例输出： 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>停止和删除虚拟机    
以下示例命令将停止 VM。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

删除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>后续步骤
请参阅以下内容[：Azure 开发人员测试实验室的 Azure CLI 文档](/cli/azure/lab?view=azure-cli-latest)。 
