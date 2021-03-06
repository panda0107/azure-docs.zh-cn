---
title: 了解 Azure 应用配置键值存储
description: 了解配置数据如何在 Azure 应用配置中存储。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523598"
---
# <a name="keys-and-values"></a>键和值

Azure 应用配置将配置数据存储为键值对。 键值对是开发人员使用的应用程序设置的简单和灵活的表示形式。

## <a name="keys"></a>密钥

键用作键值对的标识符，用于存储和检索相应的值。 使用字符分隔符（如 `/` 或 `:`）将键组织到分层命名空间中是一种常见做法。 使用最适合您的应用程序的约定。 应用配置将密钥视为一个整体。 不会解析键以弄清楚其名字是如何构成的，也不会强制执行任何规则。

下面是结构化到层次结构中的两个键名称示例：

* 基于组件服务

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* 基于部署区域

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

在应用程序框架中使用配置数据可能规定键值的特定命名方案。 例如，Java 的 Spring Cloud 框架`Environment`定义为 Spring 应用程序提供设置的资源。  这些变量由包含*应用程序名称*和*配置文件*的变量参数化。 Spring Cloud 相关配置数据的键通常以这两个元素开头，由一个分隔符分开。

存储在应用配置中的密钥是区分大小写的、基于 unicode 的字符串。 “app1”和“App1”键在应用程序配置存储区中是有所区分的****。 在应用程序中使用配置设置时，请记住这一点，因为有些框架处理配置键时不区分大小写。 我们不建议使用 case 来区分键。

您可以在键名中使用除`*`和`,`之外的任何单码字符。 `\`  如果需要包含这些保留字符之一，请使用`\{Reserved Character}`转义它。 

键值对的组合大小限制为 10 KB。 此限制包括键中的所有字符、其值以及所有相关的可选属性。 在此限制范围内，可以为密钥设置许多层次结构级别。

### <a name="design-key-namespaces"></a>设计键命名空间

命名用于配置数据的密钥有两种通用方法：平面或分层。 从应用程序使用情况的角度来看，这些方法非常相似，但分层命名提供了许多优点：

* 更易于阅读。 分层键名称中的分隔符充当句子中的空格。 它们还在单词之间提供自然的分隔。
* 更易于管理。 密钥名称层次结构表示配置数据的逻辑组。
* 更易于使用。 更简单的方法是编写一个查询，该查询在层次结构中模式匹配密钥并仅检索部分配置数据。 此外，许多较新的编程框架对分层配置数据具有本机支持，因此应用程序可以使用特定的配置集。

可以通过多种方式按层次结构组织应用配置中的密钥。 将此类键视为 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)。 每个分层键是由一个或多个组件组成的资源“路径”，由分隔符联接在一起**。 根据应用程序、编程语言或框架的需要选择要用作分隔符的字符。 在应用配置中为不同的键使用多个分隔符。

### <a name="label-keys"></a>标签键

应用配置中的键值可以选择具有“标签”属性。 标签用于区分具有相同键的键值。 具有标签*A*和*B*的键*应用 1*在应用配置存储中形成两个单独的密钥。 默认情况下，键值没有标签。 要显式引用没有标签的键值，请使用`\0`（URL 编码为`%00`）。

标签提供了一种方便的方式来创建键的变体。 标签的常见用途是为同一个键指定多个环境：

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>对键值进行版本调整

应用配置不会自动对键值进行版本。 使用标签作为创建键值的多个版本的方法。 例如，可以在标签中输入应用程序版本号或 Git 提交 ID，以标识与特定软件版本关联的键值。

可以在标签中使用任何 unicode 字符，但 `*`、`,` 和 `\` 除外。 这些是保留字符。 若要包含保留字符，必须使用 `\{Reserved Character}` 将其转义。

### <a name="query-key-values"></a>查询键值

每个键值通过其键以及可以为 `null` 的标签进行唯一标识。 可以通过指定模式来查询应用程序配置存储区中的键值。 应用程序配置存储区会返回与模式及其对应的值和属性匹配的所有键值。 在 REST API 调用应用配置时使用以下键模式：

| 键 | |
|---|---|
| 省略 `key` 或 `key=*` | 匹配所有密钥 |
| `key=abc` | 完全匹配键名称 abc**** |
| `key=abc*` | 匹配以 abc 开头的密钥名称**** |
| `key=abc,xyz` | 匹配键名称**abc**或**xyz**。 限于五个 CSV |

还可以包含以下标签模式：

| Label | |
|---|---|
| 省略 `label` 或 `label=*` | 匹配任何标签，包括 `null` |
| `label=%00` | 匹配 `null` 标签 |
| `label=1.0.0` | 完全匹配标签 1.0.0**** |
| `label=1.0.*` | 匹配以 1.0. 开头的标签**** |
| `label=%00,1.0.0` | 匹配标签 `null` 或 **1.0.0**，限制为五个 CSV |

## <a name="values"></a>值

分配给键的值也是 unicode 字符串。 可以将所有 unicode 字符用于值。 有一个与每个值相关联的可选用户定义的内容类型。 使用此属性可以存储有关帮助应用程序正确处理该值的信息。

存储在应用配置存储中的配置数据在静态和传输过程中进行加密。 密钥未在静态时加密。 应用配置不是 Azure Key Vault 的替代解决方案。 不要在其中存储应用程序机密。

## <a name="next-steps"></a>后续步骤

* [时间点快照](./concept-point-time-snapshot.md)  
* [特色管理](./concept-feature-management.md)  
