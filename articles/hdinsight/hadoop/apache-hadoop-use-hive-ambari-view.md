---
title: 在 Azure HDInsight 中使用阿帕奇安巴里蜂巢视图与阿帕奇哈多普
description: 了解如何在 Web 浏览器中使用 Hive 视图提交 Hive 查询。 Hive 视图是基于 Linux 的 HDInsight 群集随附提供的 Ambari Web UI 的一部分。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: 787d88d336abcf3b0ba9b14c3d3798850b665eca
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745091"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>将 Apache Ambari Hive 视图与 HDInsight 中的 Apache Hadoop 配合使用

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 Apache Ambari Hive 视图运行 Hive 查询。 Hive 视图允许从 Web 浏览器创作、优化和运行 Hive 查询。

## <a name="prerequisites"></a>先决条件

HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

## <a name="run-a-hive-query"></a>运行 Hive 查询

1. 在 [Azure 门户](https://portal.azure.com/)中，选择群集。  有关说明，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。 群集在新门户视图中打开。

1. 从**群集仪表板**中，选择“Ambari 视图”****。 当提示进行身份验证时，请使用在创建群集时所提供的群集登录名（默认为 `admin`）帐户名称和密码。 您还可以在浏览器中导航`https://CLUSTERNAME.azurehdinsight.net/#/main/views`到群集的名称所在的`CLUSTERNAME`位置。

1. 在视图列表中，选择“Hive 视图”____。

    ![阿帕奇·安巴里选择阿帕奇蜂巢视图](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Hive 视图页面类似于下图：

    ![Hive 视图查询工作表图像](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. 将以下 HiveQL 语句从“查询”____ 选项卡粘贴到工作表中：

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    这些语句执行以下操作：

    |语句 | 说明 |
    |---|---|
    |DROP TABLE|删除表和数据文件（如果该表已存在）。|
    |CREATE EXTERNAL TABLE|在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义。 数据将保留在原始位置。|
    |行格式|演示如何设置数据格式。 在此情况下，每个日志中的字段以空格分隔。|
    |存储为文本文件位置|显示数据的存储位置，并且数据已存储为文本。|
    |SELECT|选择 t4 列包含值 [ERROR] 的所有行的计数。|

   > [!IMPORTANT]  
   > 将“数据库”____ 选择保留为“默认”____。 本文档中的示例使用 HDInsight 附带的默认数据库。

1. 若要启动查询，请选择工作表下方的“执行”****。 按钮变为橙色，文本更改为“停止”****。

1. 完成查询后，“结果”选项卡显示操作结果****。 以下文本是查询结果：

        loglevel       count
        [ERROR]        3

    可使用“日志”选项卡查看创建的作业的日志记录信息****。

   > [!TIP]  
   > 从“结果”**** 选项卡下的“操作”**** 下拉对话框下载或保存结果。

### <a name="visual-explain"></a>可视化说明

要显示查询计划的可视化效果，选择工作表下方的“可视化说明”**** 选项卡。

查询的**可视化说明**视图可帮助理解复杂查询的流。

### <a name="tez-ui"></a>Tez UI

若要显示查询的 Tez UI，请选择工作表下方的“Tez UI”**** 选项卡。

> [!IMPORTANT]  
> Tez 不用于解析所有查询。 无需使用 Tez 即可解析许多查询。

## <a name="view-job-history"></a>查看作业历史记录

“作业”____ 选项卡显示 Hive 查询的历史记录。

![Apache Hive 查看作业选项卡历史记录](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>数据库表

可使用“表”____ 选项卡处理 Hive 数据库内的表。

![Apache Hive 表选项卡的图像](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>已保存的查询

在“查询”选项卡中，可以按需要保存查询****。 保存查询后，可通过“已保存的查询”选项卡对其重复进行使用____。

![Apache Hive 视图保存的查询选项卡](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> 保存的查询存储在默认群集存储中。 可在路径 `/user/<username>/hive/scripts` 下找到保存的查询。 它们存储为纯文本 `.hql` 文件。
>
> 如果删除群集但保留存储，可使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)或 Data Lake 存储资源管理器（通过 [Azure 门户](https://portal.azure.com)）等实用工具来检索查询。

## <a name="user-defined-functions"></a>用户定义的函数

可以通过用户定义函数 (UDF) 扩展 Hive。 使用 UDF 实现 HiveQL 中不容易建模的功能或逻辑。

使用 Hive 视图顶部的“UDF”选项卡，声明并保存一组 UDF****。 可以在**查询编辑器**中使用这些 UDF。

![阿帕奇蜂巢视图 UDFs 选项卡显示](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

**插入 udfs**按钮将显示在**查询编辑器**的底部。 此条目显示蜂巢视图中定义的 UdF 的下拉列表。 选择 UDF 会将 HiveQL 语句添加到查询以启用 UDF。

例如，如果您已使用以下属性定义 UDF：

* 资源名称：myudfs

* 资源路径：/myudfs.jar

* UDF 名称：myawesomeudf

* UDF 类名：com.myudfs.Awesome

使用“插入 UDF”按钮将显示名为 myudfs 的条目，以及为该资源定义的每个 UDF 的另一下拉列表********。 本例中为 myawesomeudf****。 选择此条目会将以下各项添加到查询的开头部分：

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

然后可在查询中使用 UDF。 例如，`SELECT myawesomeudf(name) FROM people;` 。

有关如何在 HDInsight 中将 UDF 与 Hive 配合使用的详细信息，请参阅以下文章：

* [在 HDInsight 中将 Python 与 Apache Hive 和 Apache Pig 配合使用](python-udf-hdinsight.md)
* [将 Java UDF 与 HDInsight 中的 Apache Hive 配合使用](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive 设置

可以更改各种 Hive 设置，例如将 Hive 的执行引擎从 Tez（默认）更改为 MapReduce。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 中 Hive 的常规信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)
