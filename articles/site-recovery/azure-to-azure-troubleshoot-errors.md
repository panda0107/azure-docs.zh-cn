---
title: 在 Azure 站点恢复中排除 Azure VM 复制故障
description: 排查复制 Azure 虚拟机进行灾难恢复时出现的错误。
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 0882eaa8b54966c7a804cf78a3928771b238e056
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884998"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>排查 Azure 到 Azure VM 复制错误

本文介绍如何排查在 Azure Site Recovery 中将 Azure 虚拟机 (VM) 从一个区域复制和恢复到另一个区域期间出现的常见错误。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](azure-to-azure-support-matrix.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 资源配额问题（错误代码 150097）

确保已启用订阅，在计划用作灾难恢复 （DR） 区域的目标区域中创建 Azure VM。 您的订阅需要足够的配额来创建必要大小的 VM。 默认情况下，Site Recovery 会选择与源 VM 大小相同的目标 VM 大小。 如果匹配的大小不可用，Site Recovery 会自动选择最接近的可用大小。

如果没有支持源 VM 配置的大小，将显示以下消息：

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>可能的原因

- 你的订阅 ID 未启用，因此无法在目标区域位置创建任何 VM。
- 你的订阅 ID 未启用或没有足够的配额，因此无法在目标区域位置创建特定大小的 VM。
- 对于目标区域位置中的订阅 ID，找不到与源 VM 网络接口卡 (NIC) 计数 (2) 匹配的适当目标 VM 大小。

### <a name="fix-the-problem"></a>解决问题

联系 [Azure 计费支持](/azure/azure-portal/supportability/resource-manager-core-quotas-request)启用订阅，以便在目标位置中创建所需大小的 VM。 然后重试失败的操作。

如果目标位置具有容量约束，请禁用对该位置的复制。 然后在订阅具有足够配额的其他位置启用复制，以创建所需大小的 VM。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根证书（错误代码 151066）

如果 VM 上未存在所有最新的受信任根证书，则启用站点恢复复制的作业可能会失败。 如果没有这些证书，VM 发出的 Site Recovery 服务调用的身份验证和授权会失败。

如果启用复制作业失败，将显示以下消息：

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>可能的原因

虚拟机上不存在用于授权和身份验证的必需受信根证书。

### <a name="fix-the-problem"></a>解决问题

#### <a name="windows"></a>Windows

对于运行 Windows 操作系统的 VM，安装最新的 Windows 更新，以便 VM 上存在所有受信任的根证书。 按照组织中典型的 Windows 更新管理或证书更新管理过程，获取 VM 上的最新根证书和更新的证书吊销列表。

- 如果处于未联网的环境中，请按照组织中的标准 Windows 更新过程获取证书。
- 如果 VM 上没有所需的证书，对 Site Recovery 服务的调用会出于安全原因失败。

要验证问题已解决，请从 VM`login.microsoftonline.com`中的浏览器转到。

有关详细信息，请参阅[配置受信任根和不允许的证书](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))。

#### <a name="linux"></a>Linux

按照 Linux 操作系统版本的分发服务器提供的指南获取最新的受信任的根证书和 VM 上的最新证书吊销列表。

由于 SUSE Linux 使用符号链接或符号链接来维护证书列表，请按照以下步骤操作：

1. 以**根**用户身份登录。 哈希符号 （`#`） 是默认命令提示符。

1. 要更改目录，请运行以下命令：

   `cd /etc/ssl/certs`

1. 检查 Symantec 根 CA 证书是否存在：

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - 如果未找到赛门铁克根 CA 证书，请运行以下命令以下载该文件。 检查是否有任何错误，对于网络故障执行建议的操作。

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. 检查 Baltimore 根 CA 证书是否存在：

   `ls Baltimore_CyberTrust_Root.pem`

   - 如果未找到巴尔的摩根 CA 证书，请运行此命令以下载证书：

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. 检查 DigiCert_Global_Root_CA 证书是否存在：

   `ls DigiCert_Global_Root_CA.pem`

    - 如果未找到DigiCert_Global_Root_CA，请运行以下命令下载证书：

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. 要更新新下载的证书的证书主题哈希，请运行重新哈希脚本：

   `c_rehash`

1. 要检查主题哈希值是否为证书创建了符号链接，请运行以下命令：

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. 使用文件名_b204d74a.0_创建文件_VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_的副本：

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. 使用文件名_653b494a.0_创建文件_Baltimore_CyberTrust_Root.pem_的副本：

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. 使用文件名_3513523f.0_创建文件_DigiCert_Global_Root_CA.pem_的副本：

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. 检查文件是否存在：

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

要使站点恢复复制正常工作，需要 VM 与特定 URL 的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。 尽管我们继续支持通过 URL 进行出站访问，但不再支持使用允许的 IP 范围列表。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>问题 1：未能向 Site Recovery 注册 Azure 虚拟机 (151195)

#### <a name="possible-causes"></a>可能的原因

- 由于域名系统 （DNS） 解析失败，无法建立与站点恢复终结点的连接。
- 当您在虚拟机上出现故障，但无法从灾难恢复 （DR） 区域到达 DNS 服务器时，此问题在重新保护期间更常见。

#### <a name="fix-the-problem"></a>解决问题

如果使用自定义 DNS，请确保 DNS 服务器可从灾难恢复区域访问。

要检查 VM 是否使用自定义 DNS 设置，请进行以下检查：

1. 打开**虚拟机**并选择 VM。
1. 导航到 VM**设置**并选择 **"网络**"。
1. 在**虚拟网络/子网**中，选择链接以打开虚拟网络的资源页面。
1. 转到 **"设置"** 并选择**DNS 服务器**。

尝试从虚拟机访问 DNS 服务器。 如果 DNS 服务器无法访问，则通过在 DNS 服务器上失败或在 DR 网络和 DNS 之间创建站点行来访问它。

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com 错误。":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>问题 2：Site Recovery 配置失败 (151196)

#### <a name="possible-cause"></a>可能的原因

无法连接到 Office 365 身份验证和标识 IP4 终结点。

#### <a name="fix-the-problem"></a>解决问题

Azure 站点恢复需要访问 Office 365 IP 范围进行身份验证。
如果使用 Azure 网络安全组 （NSG） 规则/防火墙代理来控制 VM 上的出站网络连接，请确保使用基于[Azure 活动目录 （AAD） 服务标记的](/azure/virtual-network/security-overview#service-tags)NSG 规则允许访问 AAD。 我们不再支持基于 IP 地址的 NSG 规则。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>问题 3：Site Recovery 配置失败 (151197)

#### <a name="possible-cause"></a>可能的原因

无法建立到 Azure 站点恢复服务终结点的连接。

#### <a name="fix-the-problem"></a>解决问题

如果使用 Azure 网络安全组 （NSG） 规则/防火墙代理来控制 VM 上的出站网络连接，请确保使用服务标记。 我们不再支持使用通过 NSG 的 IP 地址允许列表进行 Azure 站点恢复。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>问题 4：当网络流量通过本地代理服务器 （151072） 时，Azure 到 Azure 复制失败

#### <a name="possible-cause"></a>可能的原因

自定义代理设置无效，移动服务代理未从 Internet 资源管理器 （IE） 自动检测代理设置。

#### <a name="fix-the-problem"></a>解决问题

1. 移动服务代理在 Windows 和 Linux 上检测来自`/etc/environment`IE 的代理设置。
1. 如果您希望只为移动服务设置代理，则可以在_ProxyInfo.conf_中提供位于：

   - **Linux**：`/usr/local/InMage/config/`
   - **窗口**：`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _代理信息.conf_应具有以下_INI_格式的代理设置。

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> 移动服务代理仅支持**未经身份验证的代理**。

### <a name="more-information"></a>详细信息

要指定[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或所需的[IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，请按照[Azure 到 Azure 复制中的"关于网络"中的](azure-to-azure-about-networking.md)指南进行操作。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在计算机中找不到磁盘（错误代码 150039）

必须初始化附加到 VM 的新磁盘。 如果未找到磁盘，将显示以下消息：

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>可能的原因

- 新的数据磁盘已附加到 VM，但未初始化。
- VM 中的数据磁盘无法正确报告磁盘附加到 VM 的逻辑单位编号 （LUN） 值。

### <a name="fix-the-problem"></a>解决问题

确保数据磁盘已初始化，然后重试该操作。

- **窗口**：[附加和初始化新磁盘](/azure/virtual-machines/windows/attach-managed-disk-portal)。
- **Linux**：[在Linux中初始化一个新的数据磁盘](/azure/virtual-machines/linux/add-disk)。

如果问题持续出现，请联系支持人员。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>一个或多个磁盘可用于保护（错误代码 153039）

### <a name="possible-causes"></a>可能的原因

- 最近在保护后将一个或多个磁盘添加到虚拟机。
- 在保护虚拟机之后初始化了一个或多个磁盘。

### <a name="fix-the-problem"></a>解决问题

若要使 VM 的复制状态再次恢复正常，可以选择保护磁盘或消除警告。

#### <a name="to-protect-the-disks"></a>保护磁盘

1. 转到**复制项目** > _VM 名称_ > **磁盘**。
1. 选择未受保护的磁盘，然后选择“启用复制”：****

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="在 VM 磁盘上启用复制。":::

#### <a name="to-dismiss-the-warning"></a>消除警告

1. 转到**复制项** > _VM 名称_。
1. 选择“概述”部分选择警告，然后选择“确定”。********

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="关闭新磁盘警告。":::

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>已完成从保管库删除虚拟机的操作，但出现错误信息（错误代码 150225）

当站点恢复保护虚拟机时，它会在源虚拟机上创建链接。 去除保护或禁用复制时，Site Recovery 会在完成清理作业的过程中删除这些链接。 如果虚拟机存在资源锁，清理作业将会完成但会显示一些信息。 该信息指出，虚拟机已从恢复服务保管库中删除，但某些过期链接无法从源计算机中清理。

如果你今后不打算再次保护此虚拟机，可以忽略此警告。 但是，如果以后必须保护此虚拟机，请按照本节中的步骤清理链接。

> [!WARNING]
> 如果不执行清理：
>
> - 通过恢复服务保管库启用复制时，不会列出虚拟机。
> - 如果尝试使用**虚拟机** > **设置** > **灾难恢复**来保护 VM，则操作将失败，**因为 VM 上存在陈旧的资源链接，因此无法启用复制**。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 在执行以下步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响它。

1. 删除 VM 或 VM 资源组的锁。 例如，在下图中，必须删除命名`MoveDemo`VM 上的资源锁：

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="从 VM 中删除锁。":::

1. 下载用于[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)的脚本。
1. 运行脚本，_清理-陈旧-配置-Azure-VM.ps1_。 将**订阅 ID、VM****资源组**和**VM 名称**作为参数提供。
1. 如果系统提示您使用 Azure 凭据，请提供它们。 然后验证该脚本是否正常运行，而不会出现任何失败。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>由于 VM 上存在过时的资源链接，无法启用复制（错误代码 150226）

### <a name="possible-causes"></a>可能的原因

虚拟机上存在以前的 Site Recovery 保护中使用的过时配置。

如果你使用 Site Recovery 为 Azure VM 启用了复制，然后执行了以下操作，则 Azure VM 上可能会出现过时的配置：

- 禁用了复制，但源 VM 存在资源锁。
- 在未显式禁用 VM 上的复制的情况下删除了 Site Recovery 保管库。
- 在未显式禁用 VM 上的复制的情况下删除了包含 Site Recovery 保管库的资源组。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 在执行以下步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响它。

1. 删除 VM 或 VM 资源组的锁。 例如，在下图中，必须删除命名`MoveDemo`VM 上的资源锁：

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="从 VM 中删除锁。":::

1. 下载用于[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)的脚本。
1. 运行脚本，_清理-陈旧-配置-Azure-VM.ps1_。 将**订阅 ID、VM****资源组**和**VM 名称**作为参数提供。
1. 如果系统提示您使用 Azure 凭据，请提供它们。 然后验证该脚本是否正常运行，而不会出现任何失败。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>无法在启用复制作业中查看所选内容的 Azure VM 或资源组

### <a name="issue-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>问题 1：资源组和源虚拟机位于不同位置

Site Recovery 当前要求源区域资源组和虚拟机应位于同一位置。 否则，当您尝试应用保护时，您将无法找到虚拟机或资源组。

一种解决方法是，从 VM 而不是从恢复服务保管库启用复制。 转到**源 VM** > **属性** > **灾难恢复**并启用复制。

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>问题 2：资源组不是所选订阅的一部分

如果资源组不是所选订阅的一部分，则在保护时可能无法找到资源组。 确保该资源组属于正在使用的订阅。

### <a name="issue-3-stale-configuration"></a>问题 3：陈旧配置

如果 Azure VM 上存在陈旧的站点恢复配置，则可能无法看到要启用复制的 VM。 如果你使用 Site Recovery 为 Azure VM 启用了复制，然后执行了以下操作，则可能会出现这种情况：

- 在未显式禁用 VM 上的复制的情况下删除了 Site Recovery 保管库。
- 在未显式禁用 VM 上的复制的情况下删除了包含 Site Recovery 保管库的资源组。
- 禁用了复制，但源 VM 存在资源锁。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 在使用本节中提到的脚本`AzureRM.Resources`之前，请确保更新模块。 在执行以下步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响它。

1. 删除 VM 或 VM 资源组中的锁（如果有）。 例如，在下图中，必须删除命名`MoveDemo`VM 上的资源锁：

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="从 VM 中删除锁。":::

1. 下载用于[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)的脚本。
1. 运行脚本，_清理-陈旧-配置-Azure-VM.ps1_。 将**订阅 ID、VM****资源组**和**VM 名称**作为参数提供。
1. 如果系统提示您使用 Azure 凭据，请提供它们。 然后验证该脚本是否正常运行，而不会出现任何失败。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>无法选择虚拟机进行保护

### <a name="possible-cause"></a>可能的原因

虚拟机安装的扩展处于失败或无响应状态

### <a name="fix-the-problem"></a>解决问题

转到**虚拟机** > **设置** > **扩展，** 并检查任何处于失败状态的扩展。 卸载所有失败的扩展，然后重试保护虚拟机。

## <a name="the-vms-provisioning-state-isnt-valid-error-code-150019"></a>VM 的预配状态无效（错误代码 150019）

若要在 VM 上启用复制，预配状态必须是“成功”。**** 遵循以下步骤检查预配状态：

1. 在 Azure 门户中，从“所有服务”中选择“资源浏览器”。********
1. 展开“订阅”**** 列表并选择你的订阅。
1. 展开 **ResourceGroups** 列表并选择 VM 的资源组。
1. 展开“资源”**** 列表并选择你的 VM。
1. 检查右侧实例视图中的**预配状态**字段。

### <a name="fix-the-problem"></a>解决问题

- 如果**预配状态****失败**，请与支持人员联系以进行故障排除。
- 如果**预配状态****正在更新**，则可能正在部署另一个扩展。 检查 VM 上是否有任何正在进行的操作，等待它们完成，然后重试失败的站点恢复作业以启用复制。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>无法选择目标 VM（网络选择选项卡不可用）

### <a name="issue-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>问题 1：您的 VM 已连接到已映射到目标网络的网络

如果源 VM 是虚拟网络的一部分，并且来自同一虚拟网络的另一个 VM 已与目标资源组中的网络映射，则默认情况下网络选择下拉列表框不可用（显示为灰色）。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="网络选择列表不可用。":::

### <a name="issue-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>问题 2：您以前使用站点恢复保护 VM，然后禁用复制

禁用 VM 复制不会删除网络映射。 必须从保护 VM 的恢复服务保管库中删除映射。 转到**恢复服务保管库** > **站点恢复基础结构** > **网络映射**。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="删除网络映射。":::

在灾难恢复设置期间配置的目标网络可以在初始设置后以及 VM 保护后更改：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="修改网络映射。":::

更改网络映射会影响使用同一网络映射的所有受保护 VM。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ 或卷影影复制服务错误（错误代码 151025）

发生此错误时，将显示以下消息：

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>可能的原因

- 禁用了 COM+ 系统应用程序服务。
- 卷影影复制服务已禁用。

### <a name="fix-the-problem"></a>解决问题

将 COM+ 系统应用程序和卷影影复制服务设置为自动或手动启动模式。

1. 在 Windows 中打开“服务”控制台。
1. 确保 COM+ 系统应用程序和卷卷卷卷复制服务未设置为 **"已禁用**"，将其设置为 **"启动类型**"。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="检查 COM 的启动类型以及系统应用程序和卷卷卷复制服务。":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不支持的托管磁盘大小（错误代码 150172）

发生此错误时，将显示以下消息：

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>可能的原因

磁盘小于支持的大小 (1024 MB)。

### <a name="fix-the-problem"></a>解决问题

确保磁盘大小在支持的大小范围内，然后重试该操作。

## <a name="protection-wasnt-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a>未启用保护，因为 GRUB 配置包括设备名称而不是 UUID（错误代码 151126）

### <a name="possible-causes"></a>可能的原因

Linux 大统一引导加载程序 （GRUB） 配置文件 _（/boot/grub/menu.lst，_ _/boot/grub/grub.cfg，_ _/引导/grub2/grub.cfg，_ 或 _/etc/默认/grub）_ 可能指定实际的设备名称，而不是通用唯一标识符`root` `resume` （UUID） 值的 和 参数。 Site Recovery 需要 UUID，因为设备名称可能会更改。 重启后，VM 在故障转移时可能不会使用相同的名称，从而导致出现问题。

以下示例是来自 GRUB 文件的行，其中显示设备名称而不是所需的 UUID：

- 文件 _/引导/grub2/grub.cfg_：

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 文件： _/引导/grub/菜单.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>解决问题

将每个设备名称替换为相应的 UUID：

1. 通过执行命令`blkid <device name>`查找设备的 UUID。 例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 将设备名称替换为其 UUID，格式`root=UUID=<UUID>`和`resume=UUID=<UUID>`。 例如，更换后，_来自 /boot/grub/menu.lst_的行如下所示：

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重试保护。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>由于 GRUB 配置中所述的设备不存在，启用保护失败（错误代码 151124）

### <a name="possible-cause"></a>可能的原因

GRUB配置文件 _（/boot/grub/menu.lst_， _/引导/grub/grub.cfg，_ _/引导/grub2/grub.cfg，_ 或 _/etc/默认/grub）_`rd.lvm.lv`可能包含`rd_LVM_LV`参数或 。 这些参数指定了在启动时要发现的逻辑卷管理器 (LVM) 设备。 如果这些 LVM 设备不存在，则受保护的系统本身不会启动，而是停滞在启动过程。 故障转移 VM 上也会出现相同的问题。 以下是几个示例：

- 文件： _/引导/grub2/grub.cfg_在 RHEL7：

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- 文件： RHEL7 上 _/etc/默认/grub：_

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- 文件： _/引导/grub/菜单.lst_在 RHEL6：

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

在每个示例中，GRUB 必须检测具有名称`root`和`swap`卷组的`rootvg`两个 LVM 设备。

### <a name="fix-the-problem"></a>解决问题

如果 LVM 设备不存在，请创建该设备，或者从 GRUB 配置文件中删除该设备对应的参数。 然后重试启用保护。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>站点恢复移动服务更新已完成警告（错误代码 151083）

站点恢复移动性服务具有许多组件，其中一个组件称为筛选器驱动程序。 筛选器驱动程序只有在系统重启期间才会加载到系统内存中。 每当移动服务更新包含筛选器驱动程序更改时，计算机都会更新，但仍会看到一条警告，指出某些修补程序需要重新启动。 之所以出现该警告，是因为仅当已加载新的筛选器驱动程序（只会在重启期间发生）时，筛选器驱动程序修复措施才会生效。

> [!NOTE]
> 这只是一条警告。 即使在新的代理更新之后，现有复制仍继续工作。 可以选择每当需要获得新筛选器驱动程序的优势时才重启，但如果不重启，旧的筛选器驱动程序也能保持正常工作。
>
> 除了筛选器驱动程序之外，移动服务更新中任何其他增强和修复的好处都生效，无需重新启动。

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>由于副本托管磁盘已存在，但目标资源组中不包含预期的标记，因此无法启用保护（错误代码 150161）

### <a name="possible-cause"></a>可能的原因

如果虚拟机以前受到保护，并且禁用复制时，副本磁盘未被删除，则可能会出现此问题。

### <a name="fix-the-problem"></a>解决问题

删除错误消息中指出的副本磁盘，然后重试失败的保护作业。

## <a name="next-steps"></a>后续步骤

[将 Azure VM 复制到另一个 Azure 区域](azure-to-azure-how-to-enable-replication.md)
