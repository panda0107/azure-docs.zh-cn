---
title: 句子配对和对齐 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 在执行训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译每次学习一个句子的翻译，并通过读取另一个句子来获取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370140"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>并行文档中的句子配对和对齐

在训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译会报告它能够配对为每个数据集中“已对齐句子”的句子数。

## <a name="pairing-and-alignment-process"></a>配对和对齐过程

自定义翻译每次学习一个句子的翻译。 它从源中读取某个句子，然后从目标中读取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。 此过程可让自定义翻译将一个句子中的单词和短语映射到此句子的翻译中的对应单词和短语。 对齐的目的是尽量确保基于相互翻译的句子训练系统。

## <a name="pre-aligned-documents"></a>预先对齐的文档

如果你知道自己可以提供并行文档，则可以通过提供预先对齐的文本文件来替代句子对齐。 可将两个文档中的所有句子提取到该文本文件，按每行一个句子的方式进行组织，然后使用 `.align` 扩展上传。 `.align` 扩展告知自定义翻译应该跳过句子对齐。

为获得最佳结果，请尽量确保在文件中每行放置一个句子。不要在句子中插入换行符，否则会导致对齐结果不佳。

## <a name="suggested-minimum-number-of-sentences"></a>建议的最小句子数

要成功培训，下表显示了每种文档类型所需的最小句子数。此限制是一个安全网，以确保您的并行句子包含足够的唯一词汇，以成功训练翻译模型。 一般准则是有更多的域内并行句子，人类翻译质量应产生更高质量的模型。

| 文档类型   | 建议的最小刑期数 | 最大句子计数 |
|------------|--------------------------------------------|--------------------------------|
| 培训   | 10,000                                     | 没有上限                 |
| 优化     | 500                                      | 2,500       |
| 正在测试    | 500                                      | 2,500  |
| 字典 | 0                                          | 没有上限                 |

> [!NOTE]
> - 如果不符合培训的最低刑期数，培训将不会开始，并且将失败。 
> - 调整和测试是可选的。 如果不提供它们，系统将从"培训"中删除适当的百分比，用于验证和测试。 
> - 可以仅使用字典数据来训练模型。 请参阅[什么是字典](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)。

## <a name="next-steps"></a>后续步骤

- 了解如何在自定义翻译中使用[字典](what-is-dictionary.md)。
