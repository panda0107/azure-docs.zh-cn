---
title: Azure HPC 缓存数据引入 - msrsync
description: 如何使用 msrsync 将数据移动到 Azure HPC 缓存中的 Blob 存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168424"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC 缓存数据引入 - msrsync 方法

本文详细介绍了使用实用程序``msrsync``将数据复制到 Azure Blob 存储容器，以便与 Azure HPC 缓存一起使用。

要了解有关将数据移动到 Azure HPC 缓存的 Blob 存储的详细信息，请阅读[将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)。

该工具``msrsync``可用于将数据移动到 Azure HPC 缓存的后端存储目标。 此工具旨在通过运行多个并行 ``rsync`` 进程来优化带宽的使用。 可从 GitHub 获取此工具：https://github.com/jbd/msrsync。

``msrsync`` 将源目录分解成独立的“桶”，然后针对每个桶运行单个 ``rsync`` 进程。

使用四核 VM 进行的初步测试表明，使用 64 个进程时效率最高。 使用 ``msrsync`` 选项 ``-p`` 将进程数设置为 64。

请注意，``msrsync`` 只能与本地卷相互写入。 源和目标必须作为用于发出命令的工作站上的本地装载进行访问。

按照以下说明使用 Azure ``msrsync`` HPC 缓存填充 Azure Blob 存储：

1. 安装``msrsync``及其先决条件（``rsync``和 Python 2.6 或更高版本）
1. 确定要复制的文件和目录总数。

   例如，将实用程序``prime.py``与参数```prime.py --directory /path/to/some/directory```一起使用（可通过下载<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>可用）。

   如果不使用``prime.py``，则可以使用 GNU``find``工具计算项目数，如下所示：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 将项数除以 64 即可得出每个进程的项数。 运行该命令时，在 ``-f`` 选项中使用此数字可以设置桶的大小。

1. 发出复制``msrsync``文件的命令：

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令旨在将 64 个进程中的 11，000 个文件从 /test/源存储库移动到 /mnt/hpccache/存储库：

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
