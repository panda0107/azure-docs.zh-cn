---
title: 高可用性
description: 了解 Azure SQL 数据库服务的高可用性功能和特性
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: 1c4ed77112e8c06db1946d756239e02cb187f3ef
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618502"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库

Azure SQL 数据库中的高可用性体系结构的目标是保证数据库在至少 99.99% 的时间内可正常运行（若要详细了解不同层级的特定 SLA，请参阅 [Azure SQL 数据库的 SLA](https://azure.microsoft.com/support/legal/sla/sql-database/)），无需让用户担心维护操作所造成的影响和服务中断。 Azure 会自动处理关键的维护任务（例如修补、备份、Windows 和 SQL 升级），以及底层硬件、软件或网络故障等计划外的事件。  修补或故障转移底层 SQL 实例时，如果在应用中[使用重试逻辑](sql-database-develop-overview.md#resiliency)，则停机不会产生明显影响。 即使出现最严重的问题，Azure SQL 数据库也能快速恢复，确保数据始终可用。

高可用性解决方案旨在确保提交的数据永远不会由于故障而丢失，维护操作不会影响工作负荷，且数据库不会成为软件体系结构中的单一故障点。 在升级或维护数据库期间，维护或停机时段都不需要停止工作负荷。 

Azure SQL 数据库中使用两种高可用性体系结构模型：

- 基于计算和存储隔离的标准可用性模型。  该模型依赖于远程存储层的高可用性和可靠性。 此体系结构面向可以容忍在维护活动期间出现一定程度的性能下降的预算导向型业务应用程序。
- 基于数据库引擎进程群集的高级可用性模型。 该模型依赖于以下事实：始终存在可用数据库引擎节点的仲裁。 此体系结构面向具有较高 IO 性能和较高事务处理率的任务关键型应用程序，保证维护活动期间尽量减小对工作负荷性能造成的影响。

Azure SQL 数据库在最新稳定版本的 SQL Server 数据库引擎和 Windows OS 上运行，大多数用户不会察觉到正在持续执行升级。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>“基本”、“标准”和“常规用途”服务层级可用性

这些服务层级利用标准的可用性体系结构。 下图显示了具有隔离的计算和存储层的四个不同节点。

![计算和存储隔离](media/sql-database-high-availability/general-purpose-service-tier.png)

标准可用性模型包括两个层：

- 无状态计算层：运行 `sqlservr.exe` 进程，仅包含暂时性的缓存数据，例如在附加的 SSD 上的 TempDB、模型数据库，内存中的计划缓存、缓冲池和列存储池。 此无状态节点由 Azure Service Fabric 运行。Service Fabric 初始化 `sqlservr.exe`、控制节点运行状况，并根据需要执行到另一节点的故障转移。
- 有状态数据层：包含存储在 Azure Blob 存储中的数据库文件 (.mdf/.ldf)。 Azure Blob 存储具有内置的数据可用性和冗余功能。 它可以保证即使 SQL Server 进程崩溃，日志文件中的每条记录或者数据文件中的页面也仍会得到保留。

每当升级数据库引擎或操作系统，或者检测到故障时，Azure Service Fabric 会将无状态 SQL Server 进程移到具有足够可用容量的另一个无状态计算节点。 Azure Blob 存储中的数据不受移动操作的影响，数据/日志文件将附加到新初始化的 SQL Server 进程。 此过程保证 99.99% 的可用性，但在过渡期间，繁重工作负荷的性能可能会有一定程度的下降，因为新的 SQL Server 实例是使用冷缓存启动的。

## <a name="premium-and-business-critical-service-tier-availability"></a>“高级”或“业务关键”服务层级可用性

高级和业务关键型服务层级利用高级可用性模型，该模型与单个节点上的计算资源（SQL Server 数据库引擎进程）和存储（本地附加的 SSD）相集成。 实现高可用性的方式是将计算和存储资源复制到其他节点，从而建立由三到四个节点组成的群集。 

![数据库引擎节点群集](media/sql-database-high-availability/business-critical-service-tier.png)

底层数据库文件 (.mdf/.ldf) 放在附加的 SSD 存储中，以便为工作负荷提供延迟极低的 IO。 高可用性是使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术来实现的。 群集包含可供读/写客户工作负荷访问的单个主要副本（SQL Server 进程），最多包含三个次要副本（计算和存储），这些副本包含数据的副本。 主要节点不断地将更改按顺序推送到辅助节点，在提交每个事务之前，它可确保数据已至少同步到一个次要副本。 此过程可以保证当主要节点出于任何原因而崩溃时，始终可以故障转移到某个完全同步的节点。 故障转移由 Azure Service Fabric 启动。 次要副本变成新的主要节点后，会创建另一个次要副本，以确保群集中有足够的节点（仲裁集）。 故障转移后，SQL 连接会自动重定向到新的主要节点。

作为一项额外的优势，高级可用性模型提供用于将只读 SQL 连接重定向到某个次要副本的功能。 此功能称为[读取横向扩展](sql-database-read-scale-out.md)。它为从主副本卸载只读操作（如分析工作负载）提供 100% 的额外计算容量，无需额外付费。

## <a name="hyperscale-service-tier-availability"></a>“超大规模”服务层级可用性

“超大规模”服务层级体系结构在[分布式函数体系结构](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)中进行了介绍。 

![“超大规模”函数体系结构](./media/sql-database-hyperscale/hyperscale-architecture.png)

“超大规模”中的可用性模型包括四个层：

- 无状态计算层：运行 `sqlservr.exe` 进程，仅包含暂时性的缓存数据，例如在附加的 SSD 的上非覆盖性 RBPEX 缓存、TempDB、模型数据库等，在内存中的计划缓存、缓冲池和列存储池。 此无状态层包括主要计算副本，并且可以包括许多能够用作故障转移目标的次要计算副本。
- 由页服务器组成的无状态存储层。 此层是在计算副本上运行的 `sqlservr.exe` 进程的分布式存储引擎。 每个页面服务器仅包含暂时性的缓存数据，例如附加的 SSD 上的覆盖性 RBPEX 缓存、在内存中缓存的数据页。 每个页服务器在主动-主动配置中都有一个配对的页服务器，用于提供负载均衡、冗余性和高可用性。
- 一个有状态事务日志存储层，包含运行日志服务进程的计算节点、事务日志登陆区域，以及事务日志长期存储。 登陆区域和长期存储使用 Azure 存储，后者提供事务日志所需的可用性和[冗余性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，确保已提交事务的数据持久性。
- 有状态数据存储层，包含的数据库文件 (.mdf/.ndf) 存储在 Azure 存储中并通过页服务器进行更新。 此层使用 Azure 存储的数据可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)功能。 它保证保存数据文件中的每个页面，即使“超大规模”体系结构的其他层中的进程崩溃或计算节点故障，也是如此。

所有“超大规模”层中的计算节点都运行在 Azure Service Fabric 上，后者控制每个节点的运行状况，并在必要时将数据故障转移到可用的健康节点。

若要详细了解超大规模中的高可用性，请参阅[超大规模中的数据库高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)。

## <a name="zone-redundant-configuration"></a>区域冗余配置

默认情况下，高级可用性模型的节点群集在同一数据中心中创建。 引入 Azure[可用性区域](../availability-zones/az-overview.md)后，SQL 数据库可以将业务关键型数据库的不同副本放置到同一区域中的不同可用性区域。 若要消除单一故障点，还要将控件环跨区域地复制为三个网关环 (GW)。 到特定网关环的路由受 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) (ATM) 控制。 由于高级或业务关键型服务层中的区域冗余配置不会创建额外的数据库冗余，因此可以不花费额外费用启用它。 通过选择区域冗余配置，可以使高级数据库或业务关键型数据库能够抵御更大的故障集，包括灾难性的数据中心中断，而无需对应用程序逻辑进行任何更改。 还可以将所有现有“高级”或“业务关键”数据库或池转换到区域冗余配置。

由于区域冗余数据库在不同的数据中心中具有副本，它们之间具有一定距离，因此增加的网络延迟可能会增加提交时间，从而影响某些 OLTP 工作负载的性能。 始终可以通过禁用区域冗余设置返回到单个区域配置。 此过程是类似于常规服务层升级的在线操作。 在此进程结束时，该数据库或池将从区域冗余环迁移到单个区域环，反之亦然。

> [!IMPORTANT]
> 区域冗余数据库和弹性池目前仅在选定区域的高级和业务关键型服务层中支持。 使用"业务关键型"层时，区域冗余配置仅在选择 Gen5 计算硬件时可用。 有关支持区域冗余数据库的区域的最新信息，请参阅[按区域划分的服务支持](../availability-zones/az-overview.md#services-support-by-region)。  
> 此功能在托管实例中不可用。

下图演示了高可用性体系结构的区域冗余版本：

![高可用性体系结构区域冗余](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速的数据库恢复 (ADR)

[加速的数据库恢复 (ADR)](sql-database-accelerated-database-recovery.md) 是一项新的 SQL 数据库引擎功能，极大地提高数据库可用性（尤其是存在长期运行的事务时）。 ADR 目前可用于单个数据库、弹性池和 Azure SQL 数据仓库。

## <a name="testing-application-fault-resiliency"></a>测试应用程序的故障复原能力

高可用性是 Azure SQL 数据库平台的基本功能，其运作对数据库应用程序透明。 不过，我们认识到，你可能需要先测试在计划内或计划外事件期间启动的自动故障转移操作对应用程序的具体影响，然后才会将其部署到生产环境。 可以调用一个特殊的 API 来重启数据库或弹性池，从而触发故障转移。 在区域冗余数据库或弹性池中，API 调用将导致将客户端连接重定向到可用性区域中的新主数据库，而可用性区域不同于旧主数据库的可用性区域。 因此，除了测试故障转移如何影响现有数据库会话外，还可以验证故障转移是否由于网络延迟的变化而更改端到端性能。 由于重启操作会干扰系统，其数量过多可能会对平台造成压力，因此每个数据库或弹性池每 30 分钟只能进行一次故障转移调用。 

可以使用 REST API 或 PowerShell 启动故障转移。 有关 REST API 的信息，请参阅[数据库故障转移](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover)和[弹性池故障转移](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover)。 有关 PowerShell 的信息，请参阅 [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) 和 [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)。 也可使用 [az rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) 命令从 Azure CLI 进行 REST API 调用。

> [!IMPORTANT]
> 目前未为“超大规模”服务层级和托管实例提供故障转移命令。

## <a name="conclusion"></a>结束语

Azure SQL 数据库提供与 Azure 平台深度集成的内置高可用性解决方案。 它依赖于使用 Service Fabric 来执行故障检测和恢复，依赖于 Azure Blob 存储来实现数据保护，并依赖于可用性区域来提高容错能力。 此外，Azure SQL 数据库利用 SQL Server 的 Always On 可用性组技术来执行复制和故障转移。 将这些技术相结合，应用程序可完全实现混合存储模型的优势并支持最严格的 SLA。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 可用性区域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](sql-database-business-continuity.md)
