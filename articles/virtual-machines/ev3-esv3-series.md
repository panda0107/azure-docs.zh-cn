---
title: Ev3 系列和 Esv3 系列 - Azure 虚拟机
description: Ev3 和 Esv3 系列 VM 的规格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164366"
---
# <a name="ev3-and-esv3-series"></a>Ev3 和 Esv3 系列

Ev3 和 Esv3 系列采用英特尔® Xeon ® 8171M 2.1 GHz（天湖）或英特尔® Xeon® E5-2673 v4 2.3 GHz （Broadwell） 处理器，具有超线程配置，为大多数通用工作负载提供了更好的价值主张，并带来了 Ev3与大多数其他云的通用 VM 保持一致。  在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存也得到了扩展（从 7 GiB/vCPU 到 8 GiB/vCPU）。 Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

## <a name="ev3-series"></a>Ev3 系列

Ev3 系列实例基于英特尔® Xeon ® 8171M 2.1 GHz（天湖）或英特尔®至强® E5-2673 v4 2.3 GHz（布罗德韦尔）处理器和英特尔涡轮增压技术 2.0 的功能。 Ev3 系列实例适用于内存密集型企业应用程序。

数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 ESv3 大小。 ESv3 系列大小的定价和计费标准与 Ev3 系列相同。

Ev3 系列 VM 的功能英特尔®超线程技术。

ACU：160 - 190

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：受支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大网卡数/网络带宽等级 |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1，2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup>受限型核心尺寸可用。

<sup>2</sup> 实例对于专用于单个客户的硬件独立。

## <a name="esv3-series"></a>Esv3 系列

Esv3 系列实例基于英特尔® Xeon ® 8171M 2.1 GHz（天湖）或英特尔® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）处理器、英特尔涡轮增压技术 2.0 以及使用高级存储的功能。 Esv3 系列实例是内存密集型企业应用程序的理想选择。

Esv3 系列 VM 的功能英特尔®超线程技术。

ACU：160-190

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：受支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存和临时存储吞吐量：IOPS/MBps（GiB 中的缓存大小） | 最大未缓存磁盘吞吐量：IOPS/MBps | 最大 NIC/预期网络带宽 （Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup>受限型核心尺寸可用。

<sup>2</sup> 实例对于专用于单个客户的硬件独立。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
