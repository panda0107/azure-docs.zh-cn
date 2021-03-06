---
title: Azure Cosmos DB 中的一致性级别
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4e3d29471064616039bf946bb2762c15ce67bf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530251"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性级别

依赖于复制实现高可用性和/或低延迟的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商业上可用的分布式数据库要求开发人员在两种极端一致性模型之间进行选择：*强*一致性和*最终*一致性。 可线性化或非常一致性模型是数据可编程性的黄金标准。 但其导致的延迟代价较高（稳定状态下）且会降低可用性（遇到故障时）。 另一方面，最终一致性可提供更高的可用性和性能，但会加大应用程序的编程难度。 

Azure Cosmos DB 通过某种选择范围来实现数据一致性，而不会走两种极端。 尽管非常一致性和最终一致性处于该范围的两个极端，但在一致性的整个范围中，还有很多一致性选项。 开发人员可以使用这些选项在高可用性和性能方面做出精确的选择和细致的取舍。 

借助 Azure Cosmos DB，开发人员可以在一致性范围内从五个明确定义的一致性模型中进行选择。 从最强到最弱，这些模型包括“非常”、“有限过期”、“会话”、“一致前缀”和“最终”一致性。********** 这些模型具有明确的定义且非常直观，可用于特定的真实场景。 每个模型[在可用性与性能方面各有利弊](consistency-levels-tradeoffs.md)，并有 SLA 作为保障。 下图以范围区间形式显示了不同的一致性级别。

![范围形式的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性级别与区域无关，无论在哪个区域为读取和写入操作提供服务、与 Azure Cosmos 帐户关联的区域数量是多少，或者帐户是配置了单个还是多个写入区域，都可以保证所有操作获得这种一致性。

## <a name="scope-of-the-read-consistency"></a>读取一致性的范围

读取一致性适用于分区键范围或逻辑分区内的单个读取操作。 读取操作可能由远程客户端或存储过程发出。

## <a name="configure-the-default-consistency-level"></a>配置默认一致性级别

随时都可在 Azure Cosmos DB 帐户中配置默认的一致性级别。 在帐户中配置的默认一致性级别适用于该帐户下的所有 Azure Cosmos 数据库和容器。 针对某个容器或数据库发出的所有读取和查询默认使用指定的一致性级别。 有关详细信息，请参阅如何[配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)。

## <a name="guarantees-associated-with-consistency-levels"></a>与一致性级别关联的保证

Azure Cosmos DB 提供的综合 SLA 可保证 100% 的读取请求满足所选任何一致性级别的一致性保证。 如果满足与一致性级别关联的所有一致性保证，则读取请求满足一致性 SLA。 [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 存储库中提供了 Azure Cosmos DB 中使用 TLA+ 规范语言精确定义的五个一致性级别。

下面描述了五个一致性级别的语义：

- **强**：强一致性提供了线性可数保证。 可线性化是指并发处理请求。 保证读取操作返回项的最新提交版本。 客户端永远不会看到未提交或不完整的写入。 始终保证用户读取最新确认的写入。

  下图说明了与音符的强烈一致性。 将数据写入"美国西部 2"区域后，当您从其他区域读取数据时，您将获得最新值：

  ![video](media/consistency-levels/strong-consistency.gif)

- **有边界的过时：** 读取保证遵守一致前缀保证。 读取可能滞后于项目（即"更新"）或 *"T"* 时间间隔的 *"K"* 版本（即"更新"）的写入。 换言之，如果选择有限过期，则可以通过两种方式配置“过期”： 

  * 项的版本数 （*K*）
  * 读取可能滞后于写入的时间间隔 （*T*） 

  有限过期提供全局整体顺序，但在“过期窗口”中除外。 过期窗口内部和外部的区域中提供单调读取保证。 非常一致性的语义与有限过期提供的语义相同。 过期窗口等于零。 有限过期也称为“延时可线性化”。 当客户端在接受写入的区域中执行读取操作时，有限过期一致性提供的保证与非常一致性的保证相同。

  全球分布式应用程序经常选择有界过时，这些应用程序期望低写入延迟，但需要全局订单总保证。 边界过时非常适合具有群组协作和共享、股票代码、发布-订阅/排队等功能的应用程序。下图说明了与音符的有限过时一致性。 将数据写入"美国西部 2"区域后，"东美国 2"和"澳大利亚东部"区域根据配置的最大延迟时间或最大操作读取写入值：

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **会话**：在单个客户端会话中，保证读取遵循一致前缀（假设单个"写入器"会话）、单调读取、单调写入、读取写入和写入-跟随读取保证。 执行写入操作的会话之外的客户端将看到最终一致性。

  会话一致性是单个区域和全球分布式应用程序广泛使用的一致性级别。 它提供了与最终一致性相当的写入延迟、可用性和读取吞吐量，但也提供了一致性保证，以满足编写的应用程序在用户上下文中运行的需求。 下图说明了会话与音符的一致性。 "西 US 2"区域和"美国东部 2"区域使用相同的会话（会话 A），因此它们同时读取数据。 "澳大利亚东部"区域使用"会话 B"，因此，它稍后接收数据，但顺序与写入相同。

  ![video](media/consistency-levels/session-consistency.gif)

- **一致前缀**：返回的更新包含所有更新的一些前缀，没有间隙。 一致的前缀一致性级别保证读取永远不会看到顺序外写入。

  如果按 `A, B, C` 顺序执行写入，则客户端会看到 `A`、`A,B` 或 `A,B,C`，但不会看到 `A,C` 或 `B,A,C` 这样的混乱顺序。 一致前缀提供与最终一致性类似的写入延迟、可用性和读取吞吐量，但也提供满足订单很重要的方案所需的顺序保证。 下图说明了一致性前缀与音符的一致性。 在所有区域中，读取永远不会看到顺序顺序写入：

  ![video](media/consistency-levels/consistent-prefix.gif)

- **最终**：没有读取的订购保证。 如果缺少任何进一步的写入，则副本最终会收敛。  
最终的一致性是最弱的一致性形式，因为客户端可能会读取比之前读取的值更旧的值。 如果应用程序不需要任何订购保证，则最终一致性是理想的。 示例包括转推、赞或非线程注释的计数。 下图说明了与音符的最终一致性。

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>其他阅读材料

若要详细了解一致性的概念，请阅读以下文章：

- [Azure Cosmos DB 提供的五个一致性级别的高级 TLA+ 规范](https://github.com/Azure/azure-cosmos-tla)
- [复制数据一致性解释通过棒球（视频）由道格特里](https://www.youtube.com/watch?v=gluIh8zd26I)
- [复制的数据一致性解释通过棒球（白皮书）由道格特里](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [对弱一致性复制数据的会话保证](https://dl.acm.org/citation.cfm?id=383631)
- [现代分布式数据库系统设计中的一致性平衡方案：CAP 只是冰山一角](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [概率边界过时 （PBS） 用于实际部分仲裁](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [最终一致性 - 再探](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Cosmos DB 中的一致性级别，请阅读以下文章：

* [为你的应用程序选择适当的一致性级别](consistency-levels-choosing.md)
* [跨 Azure Cosmos DB API 的一致性级别](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [替代默认一致性级别](how-to-manage-consistency.md#override-the-default-consistency-level)

