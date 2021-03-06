---
title: 了解 Azure 文件同步的云分层 | Microsoft Docs
description: 了解 Azure 文件同步的云分层功能
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e8a8502b40410df221886cde2fa5f3db15bf3eed
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549175"
---
# <a name="cloud-tiering-overview"></a>云分层概述
云分层是 Azure 文件同步的一项可选功能，其中经常访问的文件在服务器本地缓存，而所有其他文件根据策略设置分层到 Azure 文件。 当文件分层时，Azure 文件同步文件系统筛选器 (StorageSync.sys) 将本地文件替换为指针或重分析点。 重分析点表示 Azure 文件中的文件 URL。 分层文件在 NTFS 中设置了“脱机”属性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性，这样第三方应用程序便能安全地识别分层文件。
 
当用户打开分层文件时，Azure 文件同步会无缝地从 Azure 文件中撤回文件数据，而无需用户知道该文件存储在 Azure 中。 
 
 > [!Important]  
 > Windows 系统卷不支持云分层。
    
 > [!Important]  
 > 要调用已分层的文件，网络带宽应至少为 1 Mbps。 如果网络带宽小于 1 Mbps，则文件可能无法下载超时错误。

## <a name="cloud-tiering-faq"></a>云分层 FAQ

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>云分层的工作原理是什么？
Azure 文件同步系统筛选器生成每个服务器终结点上命名空间的“热度地图”。 它监视一段时间内的访问情况（读取和写入操作），然后根据访问频率和新近度，向每个文件分配热度分数。 最近打开的频繁访问文件被视为“热”，而几乎无人打开且已有一段时间未获访问的文件则被视为“冷”。 如果服务器上的文件卷超过设置的卷可用空间阈值，它会将最冷的文件分层到 Azure 文件中，直到达到可用空间百分比。

在 Azure 文件同步代理 4.0 和更高版本中，还可以针对每个服务器终结点指定日期策略，以便将指定天数内未访问或修改的所有文件分层。

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>要层的文件的最低文件大小是多少？
对于代理版本 9.x 和更高版本，要层的文件的最小文件大小基于文件系统群集大小（将文件系统群集大小翻倍）。 例如，如果 NTFS 文件系统群集大小为 4KB，则生成的文件到层的最小文件大小为 8KB。 对于代理版本 8.x 和旧版本，文件到层的最小文件大小为 64KB。

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>卷可用空间分层策略的工作原理是什么？
卷可用空间是要在服务器终结点所在的卷上保留的可用空间量。 例如，如果将有一个服务器终结点的卷的卷可用空间设置为 20%，那么最近访问的文件最多占用 80% 的卷空间，而其余所有不适应此空间的文件都会被分层到 Azure 中。 卷可用空间适用于卷级别，不适用于各个目录或同步组的级别。 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>卷可用空间分层策略如何处理新服务器终结点？
如果有服务器终结点新预配并连接到 Azure 文件共享，服务器会先下拉命名空间，再下拉实际文件，直到它达到卷可用空间阈值。 此过程亦称为“快速灾难恢复”或“快速命名空间还原”。

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>如果卷上有多个服务器终结点，该如何解释卷可用空间？
如果卷上有多个服务器终结点，有效卷可用空间阈值为在该卷上的所有服务器终结点中指定的最大卷可用空间。 将根据使用模式对文件进行分层，而不考虑它们属于哪一个服务器终结点。 例如，如果卷上有两个服务器终结点 Endpoint1 和 Endpoint2，其中 Endpoint1 的卷可用空间阈值为 25%，Endpoint2 的卷可用空间阈值为 50%，那么这两个服务器终结点的卷可用空间阈值将为 50%。 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>日期分层策略如何与卷可用空间分层策略配合工作？ 
针对服务器终结点启用云分层时，可以设置卷可用空间策略。 此策略始终优先于其他任何策略，包括日期策略。 或者，您可以为该卷上的每个服务器终结点启用日期策略。 此策略管理仅在此策略描述的天数范围内访问的文件（即读取或写入）的文件保持为本地文件。 未访问的文件与指定的天数，将被分层。 

云分层使用最后一个访问时间来确定应分层哪些文件。 云分层筛选器驱动程序 （storagesync.sys） 跟踪上次访问时间，并在云分层热存储中记录信息。 您可以使用本地 PowerShell cmdlet 查看热存储。

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Get-StorageSyncHeatStoreInformation '<LocalServerEndpointPath>'
```

> [!IMPORTANT]
> 上次访问的时间戳不是 NTFS 跟踪的属性，因此在文件资源管理器中默认不可见。 不要在文件上使用上次修改的时间戳来检查日期策略是否按预期方式工作。 此时间戳仅跟踪写入，不跟踪读取。 使用显示的 cmdlet 获取此评估的最后访问时间戳。

> [!WARNING]
> 不要打开 NTFS 功能，以跟踪文件和文件夹的最后访问时间戳。 默认情况下，此功能处于关闭状态，因为它对性能影响很大。 Azure 文件同步将自动且非常高效地跟踪上次访问时间，并且不会利用此 NTFS 功能。

请记住，卷可用空间策略始终优先，当卷上没有足够的可用空间来保留日期策略描述的尽可能多的文件时，Azure 文件同步将继续分层最冷的文件，直到满足卷可用空间百分比。

例如，假设基于日期的分层策略指定保留天数为 60 天，卷可用空间策略指定可用空间百分比为 20%。 如果在应用该日期策略之后，卷上的可用空间小于 20%，则卷可用空间策略将会激活，并替代日期策略。 这会导致更多的文件分层，使服务器上保留的数据量可从 60 天数据减少为 45 天数据。 相反，此策略会强制将超出时间范围的文件分层，即使尚未达到可用空间的阈值 – 因此，即使卷是空的，也会将 61 天前的文件分层。

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>我该如何确定适当的卷可用空间量？
应在本地保留的数据量由以下几个因素决定：带宽、数据集的访问模式和预算。 如果连接带宽低，建议在本地保留更多数据，以确保用户滞后时间最短。 反之，可以给定时间段内的变动率为依据。 例如，如果确定 1TB 数据集每月大约有 10% 发生变化或获主动访问，建议在本地保留 100GB，以免频繁召回文件。 如果卷为 2TB，建议在本地保留 5%（或 100GB）；也就是说，剩余 95% 是卷可用空间百分比。 不过，建议添加缓冲区，以将变动率更高的时间段考虑在内；也就是说，从较低的卷可用空间百分比入手，以后再根据需要进行调整。 

在本地保留更多数据意味着降低流出费用，因为从 Azure 召回的文件变少；但还必须维护更大的本地存储，这需要自己支付费用。 部署 Azure 文件同步实例后，可以查看存储帐户的出入口，大致衡量卷可用空间设置是否适合使用。 假设存储帐户仅包含 Azure 文件同步云终结点（即同步共享），那么高流出意味着要从云中召回许多文件，应考虑增加本地缓存。

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>我添加了新的服务器终结点。 要在多长时间后，我的文件会在此服务器上分层？
在 Azure 文件同步代理的版本 4.0 及以上版本中，一旦文件上载到 Azure 文件共享，将在下一次分层会话运行时根据策略对其进行分层，每小时一次。 在早期的代理上，分层最长可能需要在 24 小时后发生。

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>如何判断文件是否已分层？
有多种查看文件是否已被分层到 Azure 文件共享的方法：
    
   *  **查看文件上的文件属性。**
     右键单击文件，转到“详细信息”****，再向下滚动到“属性”**** 属性。 分层的文件具有以下属性集：     
        
        | 属性字母 | 特性 | 定义 |
        |:----------------:|-----------|------------|
        | A | 存档 | 指示备份软件应备份此文件。 无论文件被分层还是完全存储在磁盘上，始终都会设置此属性。 |
        | P | 稀疏文件 | 指示该文件为稀疏文件。 稀疏文件是 NTFS 提供的专用化文件类型，用于在占用空间流上的文件几乎为空时提高使用效率。 Azure 文件同步将使用稀疏文件，因为文件会被完全分层或部分召回。 在完全分层文件中，文件流存储在云中。 在部分召回的文件中，文件的一部分已在磁盘上。 如果文件被完全召回到磁盘，Azure 文件同步会将其从稀疏文件转换为常规文件。 此属性仅在 Windows Server 2016 及更旧版上设置。|
        | M | 在数据访问时召回 | 指示文件的数据未完全存在于本地存储中。 读取该文件将导致至少从服务器终结点连接到的 Azure 文件共享获取某些文件内容。 此属性仅在 Windows Server 2019 上设置。 |
        | L | 重分析点 | 指示该文件包含重分析点。 重分析点是供文件系统筛选器使用的特殊指针。 Azure 文件同步使用重分析点来定义 Azure 文件同步文件系统筛选器 (StorageSync.sys) 存储文件的云位置。 这样即可支持无缝访问。 用户无需知道是否使用了 Azure 文件同步或如何获取在 Azure 文件共享中的文件访问权限。 如果文件被完全召回，则 Azure 文件同步将从此文件中删除重分析点。 |
        | O | Offline | 指示文件的部分或全部内容未存储在磁盘上。 如果文件被完全召回，Azure 文件同步将删除此属性。 |

        ![文件的“属性”对话框（“详细信息”选项卡被选中）](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        你可以通过向文件资源管理器的表显示添加“属性”**** 字段，查看文件夹中所有文件的属性。 若要执行此操作，请右键单击现有的列（例如“大小”****），选择“更多”****，然后从下拉列表中选择“属性”****。
        
   * **用于`fsutil`检查文件中的重新分析点。**
       如前面的选项中所述，分层的文件始终具有重分析点集。 重分析指针是 Azure 文件同步文件系统筛选器 (StorageSync.sys) 的特殊指针。 若要查看文件是否包含重分析点，你可以在提升的命令提示符或 PowerShell 窗口中运行 `fsutil` 实用程序：
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        如果文件包含重分析点，应能看到“重分析标记值: 0x8000001e”****。 此十六进制值是 Azure 文件同步拥有的重解析点值。输出还包含表示 Azure 文件共享上文件的路径的重新分析数据。

        > [!WARNING]  
        > 同时，`fsutil reparsepoint` 实用程序命令还包含删除重分析点的功能。 除非 Azure 文件同步工程团队要求，否则请不要执行此命令。 运行此命令可能会导致数据丢失。 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>我要使用的文件已分层。 如何将文件召回到磁盘以供本地使用？
将文件召回到磁盘的最简单方法是打开此文件。 Azure 文件同步文件系统筛选器 (StorageSync.sys) 将会从 Azure 文件共享中无缝下载此文件，你无需执行任何操作。 对于可被部分读取的文件类型（如多媒体文件或 .zip 文件），打开文件后不会下载整个文件。

此外，你也可以使用 PowerShell 来强制召回文件。 在需要同时召回多个文件（例如，一个文件夹内的所有文件）的情况下，可能更适合使用此选项。 打开已安装 Azure 文件同步的服务器节点的 PowerShell 会话，然后运行以下 PowerShell 命令：
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
可选参数：
* `-Order CloudTieringPolicy`将首先撤回最近修改的文件。  
* `-ThreadCount`确定可以并行调用的文件数。
* `-PerFileRetryCount`确定当前被阻止的文件的调用尝试频率。
* `-PerFileRetryDelaySeconds`确定重试调用尝试之间的时间（以秒为单位），应始终与前面的参数结合使用。

> [!Note]  
> 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>使用 Azure 文件共享后，为什么文件的占用空间属性与大小属性不一致？**** 
Windows 文件资源管理器公开了两个属性来表示文件的大小：即“大小”**** 和“占用空间”****。 这些属性的含义略有不同。 “大小”**** 表示文件的完整大小。 “占用空间”**** 表示存储在磁盘上的文件流的大小。 这些属性的值可能因各种原因而异，例如压缩、使用数据重复数据消除或使用 Azure 文件同步进行云分层。如果将文件分层到 Azure 文件共享，则磁盘上的大小为零，因为文件流存储在 Azure 文件共享中，而不是存储在磁盘上。 另外，文件也可能会被部分分层（或部分召回）。 在部分分层文件中，该文件的一部分位于磁盘上。 应用程序（例如，多媒体播放器或压缩工具）对文件进行了部分读取时可能会发生此情况。 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>如何强制对文件或目录进行分层？
启用云分层功能后，它会根据上次访问和修改时间自动分层文件，以实现云终结点上指定的卷可用空间百分比， 但有时你可能需要手动强制分层文件。 如果你要保存在长时间内计划不再次使用的大型文件，并且想要将卷上现有可用空间用于其他文件和文件夹，则可使用此方法。 可使用以下 PowerShell 命令强制分层：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>为什么我的分层文件在 Windows 资源管理器中不显示缩略图或预览？
对于分层文件，缩略图和预览在服务器终结点上不可见。 由于 Windows 中的缩略图缓存功能有意跳过使用脱机属性读取文件，因此应对此行为进行预期。 启用云分层后，读取分层文件将导致下载（调用）。

此行为不特定于 Azure 文件同步，Windows 资源管理器将显示具有脱机属性集的任何文件的"灰色 X"。 通过 SMB 访问文件时，您将看到 X 图标。 有关此行为的详细说明，请参阅[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
