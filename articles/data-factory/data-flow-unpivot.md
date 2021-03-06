---
title: 映射数据流取消轴变换
description: Azure 数据工厂映射数据流取消透视转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930143"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure 数据工厂取消透视转换



在 ADF 映射数据流中使用 Unpivot，通过将单个记录中的多个列的值扩展到单个列中具有相同值的多个记录，将未规范化数据集转换为更规范化的版本。

![取消支点变换](media/data-flow/unpivot1.png "取消透视选项 1")

## <a name="ungroup-by"></a>分组依据

![取消支点变换](media/data-flow/unpivot5.png "取消透视选项 2")

首先，为你的透视聚合设置要用作分组依据的列。 使用列列表旁边的 + 号设置一个或多个列用于取消组合。

## <a name="unpivot-key"></a>逆透视键

![取消支点变换](media/data-flow/unpivot6.png "取消透视选项 3")

透视键是 ADF 从行透视到列的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到列。 但是，你也可以选择输入此数据集中要透视到列值的值。

## <a name="unpivoted-columns"></a>已逆透视列

![取消支点变换](media/data-flow//unpivot7.png "取消透视选项 4")

最后，选择要用于透视值的聚合以及如何在转换的新输出投影中显示列。

（可选）你可以设置一个命名模式，在其中包含要在行值中的每个新列名称中添加的前缀、中间名和后缀。

例如，通过“区域”透视“销售”只会为你提供每个销售值的新列值。 例如："25"，"50"，"1000"，...但是，如果设置了前缀值"销售"，则"销售"将预固定到这些值。

<img src="media/data-flow/unpivot3.png" width="400">

将列排列设置为“正常”，将所有透视列与其聚合值组合在一起。 将列排列设置为“横向”将在列和值之间交替。

![取消支点变换](media/data-flow//unpivot7.png "取消透视选项 5")

最终的已逆透视数据结果集显示已逆透视到单独行值的列总值。

## <a name="next-steps"></a>后续步骤

使用[数据透视转换](data-flow-pivot.md)将行透视到列。
