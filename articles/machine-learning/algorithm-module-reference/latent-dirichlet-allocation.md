---
title: 潜移默化分配
titleSuffix: Azure Machine Learning
description: 了解如何使用"潜伏迪里切分配"模块将其他未分类的文本分组到多个类别中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109220"
---
# <a name="latent-dirichlet-allocation"></a>潜移默化分配

本文介绍如何使用 Azure 机器学习设计器（预览）中的 **"潜在迪里切分配"** 模块将其他未分类的文本分组到多个类别中。 

潜移默分配 （LDA） 通常用于自然语言处理 （NLP） 来查找类似的文本。 另一个常用术语是*主题建模*。

此模块采用一列文本，并生成以下输出：

+ 源文本，以及每个类别的分数

+ 包含每个类别提取的术语和系数的特征矩阵

+ 转换，您可以保存并重新应用于用作输入的新文本

本模块使用学子库。 有关 scikit 学习的详细信息，请参阅 #GitHub 存储库，其中包含教程和算法说明。

### <a name="more-about-latent-dirichlet-allocation-lda"></a>更多关于潜伏性迪里切分配 （LDA）

一般来说，LDA本身并不是一种分类方法，而是使用生成方法。 这意味着您不需要提供已知的类标签，然后推断模式。  相反，该算法生成一个概率模型，用于标识主题组。 可以使用概率模型对现有训练案例或作为输入提供给模型的新案例进行分类。

生成模型可能更可取，因为它避免对文本和类别之间的关系做出任何强烈的假设，并且仅将单词的分布用于数学模型主题。

+ 本文讨论了这一理论，可作为 PDF 下载：[潜伏的迪里切分配：布莱、Ng 和约旦](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ 本模块中的实现基于 LDA[的 scikit 学习库](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py)。

有关详细信息，请参阅[技术说明](#technical-notes)部分。

## <a name="how-to-configure-latent-dirichlet-allocation"></a>如何配置延迟迪里奇特分配

此模块需要包含文本列（原始或预处理）的数据集。

1. 将 **"潜伏的迪里切分配**"模块添加到管道中。

2. 作为模块的输入，提供包含一个或多个文本列的数据集。

3. 对于**目标列**，选择要分析的一个或多个包含文本的列。

    您可以选择多个列，但它们必须是字符串数据类型的列。

    通常，由于 LDA 从文本创建大型要素矩阵，因此通常会分析单个文本列。

4. 对于**要建模的主题数**，键入介于 1 和 1000 之间的整数，指示要从输入文本派生的类别或主题数。

    默认情况下，将创建 5 个主题。

5. 对于**N-gram，** 指定哈希期间生成的 N-gram 的最大长度。

    默认值为 2，这意味着生成大拉姆和单格。

6. 选择 **"规范化**"选项以将输出值转换为概率。 因此，输出和要素数据集中的值将转换如下：而不是将转换后的值表示为整数：

    + 数据集中的值将表示为 概率。 `P(topic|document)`

    + 要素主题矩阵中的值将表示为`P(word|topic)`概率。

    > [!NOTE] 
    > 在 Azure 机器学习设计器（预览）中，由于我们基于的库学 scikit 学习，不再支持版本 0.19 中的非规范化*doc_topic_distr*输出，因此，在此模块中，**规范化**参数只能应用于**要素主题矩阵**输出，**转换数据集**输出始终规范化。

7. 选择选项"**显示所有选项**"，然后将其设置为 TRUE（如果要查看，然后设置其他高级参数）。

    这些参数特定于 LDA 的 scikit 学习实现。 有一些关于LDA的辅导在scikit学习，以及官方的[scikit学习文件](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html)。

    + **Rho 参数**. 提供主题分布稀疏性的先前概率。 对应于 sklearn 的`topic_word_prior`参数。 如果预期单词的分布是平的，则可以使用值 1;也就是说，所有单词都是假定相等的。 如果您认为大多数单词以稀疏方式显示，则可以将其设置为低得多的值。

    + **阿尔法参数**. 为每个文档主题权重的稀疏性指定先前的概率。  对应于 sklearn 的`doc_topic_prior`参数。

    + **估计文件数量**。 键入一个数字，表示要处理的文档（行）数的最佳估计值。 这允许模块分配足够大大小的哈希表。  对应于学中的`total_samples`参数。

    + **批处理的大小**。 键入一个数字，指示发送到 LDA 模型的每批文本中要包含多少行。 对应于学中的`batch_size`参数。

    + **学习更新计划中使用的迭代的初始值**。 指定降低在线学习早期迭代学习速率的起始值。 对应于学中的`learning_offset`参数。

    + **在更新期间应用于迭代的电源**。 指示应用于迭代计数的功率级别，以便在联机更新期间控制学习速率。 对应于学中的`learning_decay`参数。

    + **通过数据的次数**。 指定算法在数据中循环的最大次数。 对应于学中的`max_iter`参数。

8. 如果要在初始传递中创建 n-gram 列表，请在对文本进行分类之前选择"**生成 ngram 字典**"或 **"在 LDA 之前生成 ngram 词典**"选项。

    如果事先创建初始字典，则稍后可以在查看模型时使用字典。 能够将结果映射到文本而不是数字索引通常更容易解释。 但是，保存字典将需要更长的时间，并使用额外的存储。

9. 对于**ngram 字典的最大大小**，键入可在 n-gram 字典中创建的行总数。

    此选项可用于控制字典的大小。 但是，如果输入中的 ngram 数超过此大小，则可能发生冲突。

10. 提交管道。 LDA 模块使用贝叶斯定理来确定哪些主题可能与单个单词相关联。 单词不只与任何主题或组相关联;相反，每个 n-gram 都有与任何发现的类关联的学习概率。

## <a name="results"></a>结果

模块有两个输出：

+ **转换后的数据集**：包含输入文本和指定数量的已发现类别，以及每个类别的每个文本示例的分数。

+ **要素主题矩阵**：最左侧的列包含提取的文本要素，并且每个类别都有一列，其中包含该类别中该要素的分数。


### <a name="lda-transformation"></a>LDA 转换

此模块还输出将 LDA 应用于数据集的*LDA 转换*。

您可以通过在模块右侧窗格中的 **"输出_logs"** 选项卡下注册数据集来保存此转换，并将其重新用于其他数据集。 如果您已对大型语料库进行了培训，并希望重用系数或类别，这可能很有用。

### <a name="refining-an-lda-model-or-results"></a>优化 LDA 模型或结果

通常，您不能创建满足所有需求的单个 LDA 模型，即使为一个任务设计的模型也可能需要多次迭代来提高准确性。 我们建议您尝试所有这些方法来改进模型：

+ 更改模型参数
+ 使用可视化来了解结果
+ 获取主题专家的反馈，以确定生成的主题是否有用。

定性措施也可用于评估结果。 要评估主题建模结果，请考虑：

+ 准确性 - 类似项目真的相似吗？
+ 多样性 - 当业务问题需要时，模型是否可以区分类似的项目？
+ 可伸缩性 - 它是在广泛的文本类别上工作，还是仅在狭窄的目标域上工作？

通过使用自然语言处理来清理、总结和简化或分类文本，通常可以提高基于 LDA 的模型的准确性。 例如，Azure 机器学习中都支持以下技术可以提高分类准确性：

+ 停止词删除

+ 大小写规范化

+ lemmat 化或词干

+ 命名实体识别

有关详细信息，请参阅[预处理文本](preprocess-text.md)。

在设计器中，还可以使用 R 或 Python 库进行文本处理：[执行 R 脚本](execute-r-script.md)、执行 Python[脚本](execute-python-script.md)



## <a name="technical-notes"></a>技术说明

本部分包含实现详情、使用技巧和常见问题解答。

### <a name="implementation-details"></a>实现详细信息

默认情况下，转换数据集和要素主题矩阵的输出分布将归化为概率。

+ 转换后的数据集规范化为给定文档的主题的条件概率。 在这种情况下，每行的总和等于 1。

+ 特征主题矩阵被规范化为给定主题的单词的条件概率。 在这种情况下，每列的总和等于 1。

> [!TIP]
> 有时，模块可能会返回一个空主题，这通常是由算法的伪随机初始化引起的。  如果发生这种情况，您可以尝试更改相关参数，例如 N-gram 字典的最大大小或用于特征哈希的位数。

### <a name="lda-and-topic-modeling"></a>LDA 和主题建模

潜移默分配 （LDA） 通常用于*基于内容的主题建模*，这基本上意味着从非机密文本学习类别。 在基于内容的主题建模中，主题是单词的分发。

例如，假设您提供了包含许多产品的客户评论。 许多客户随着时间的推移提交的评审文本将包含许多术语，其中一些术语用于多个主题。

LDA 流程标识**的主题**可能表示单个产品 A 的审核，或者表示一组产品审核。 对于 LDA，主题本身只是一组单词随时间分布的概率。

术语很少是任何一种产品独有的，但可以指其他产品，也可以指适用于所有产品的一般术语（"伟大"、"糟糕"）。 其他术语可能是噪音词。  然而，重要的是要理解，LDA方法并不意味着捕获宇宙中的所有单词，或理解单词是如何相关的，除了共发生的概率。 它只能对目标域中使用的单词进行分组。

计算术语索引后，使用基于距离的相似性度量对单个行的文本进行比较，以确定两段文本是否彼此相似。  例如，您可能会发现产品有多个名称具有高度相关。 或者，您可能会发现，强烈的负面术语通常与特定产品相关联。 您可以使用相似性度量来标识相关术语并创建建议。

###  <a name="module-parameters"></a>模块参数

|“属性”|类型|范围|可选|默认|描述|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|目标列|列选择||必选|StringFeature|目标列名称或索引|  
|要建模的主题数|Integer|[1;1000]|必选|5|根据 N 个主题对文档分发进行建模|  
|N 元语法|Integer|[1;10]|必选|2|哈希期间生成的 N 克顺序|  
|规范|Boolean|是或否|必选|true|将输出规范化为概率。  转换后的数据集为 P（主题&#124;文档），要素主题矩阵为 P（单词&#124;主题）|  
|显示所有选项|Boolean|是或否|必选|False|提供特定于 scikit 学习在线 LDA 的其他参数|  
|Rho 参数|Float|[0.00001;1.0]|选中"**显示所有选项**"复选框时应用|0.01|主题字事先分发|  
|阿尔法参数|Float|[0.00001;1.0]|选中"**显示所有选项**"复选框时应用|0.01|文档主题提前分发|  
|估计文件数量|Integer|[1;int.MaxValue]|选中"**显示所有选项**"复选框时应用|1000|估计的文档数（对应于total_samples参数）|  
|批次的大小|Integer|[1;1024]|选中"**显示所有选项**"复选框时应用|32|批次的大小|  
|学习速率更新计划中使用的迭代的初始值|Integer|{0;int.最大价值*|选中"**显示所有选项**"复选框时应用|0|早期迭代降低学习速率的初始值。 对应于learning_offset参数|  
|在更新期间应用于迭代的电源|Float|[0.0;1.0]|选中"**显示所有选项**"复选框时应用|0.5|应用于迭代计数以控制学习速率的功率。 对应于learning_decay参数 |  
|训练迭代数|Integer|[1;1024]|选中"**显示所有选项**"复选框时应用|25|训练迭代数|  
|生成 ngram 词典|Boolean|是或否|未选择"**显示所有选项**"复选框时*not*应用|True|在计算 LDA 之前构建 ngram 词典。 可用于模型检查和解释|  
|ngram 字典的最大大小|Integer|[1;int.MaxValue]|当**选项"生成 ngram"字典**为 true 时应用|20000|ngram 字典的最大大小。 如果输入中的令牌数超过此大小，则可能发生冲突|  
|用于特征哈希的位数|Integer|[1;31]|当*未*选择 **"显示所有选项**"复选框，并且**ngram 的生成字典**为 False 时应用|12|用于特征哈希的位数| 
|在 LDA 之前构建 ngram 词典|Boolean|是或否|选中"**显示所有选项**"复选框时应用|True|在 LDA 之前构建 ngram 词典。 可用于模型检查和解释|  
|字典中的最大 ngram 数|Integer|[1;int.MaxValue]|当选中"**显示所有选项**"复选框，并且 **"生成 ngram"词典**的选项为 True 时应用|20000|字典的最大大小。 如果输入中的令牌数超过此大小，则可能发生冲突|  
|哈希位数|Integer|[1;31]|当选中"**显示所有选项**"复选框，并且 **"生成 ngram"选项**为 False 时应用|12|在特征哈希期间要使用的位数|   


## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。   
有关特定于模块的错误列表，请参阅[设计器的异常和错误代码](designer-error-codes.md)。
