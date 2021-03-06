---
title: 将 Apache Beeline 与 Apache Hive 配合使用 - Azure HDInsight
description: 了解如何使用 Beeline 客户端通过 Hadoop on HDInsight 运行 Hive 查询。 Beeline 是用于通过 JDBC 操作 HiveServer2 的实用工具。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 4f2b192765aab4c7cf18c62988ae2f6080b4b17c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436923"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>将 Apache Beeline 客户端与 Apache Hive 配合使用

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上运行 Apache Hive 查询。

Beeline 是一个 Hive 客户端，包含在 HDInsight 群集的头节点上。 若要在本地安装 Beeline，请参阅下面的[安装 Beeline 客户端](#install-beeline-client)。 Beeline 使用 JDBC 连接到 HiveServer2，后者是 HDInsight 群集上托管的一项服务。 还可以使用 Beeline 通过 Internet 远程访问 Hive on HDInsight。 以下示例提供了用于从 Beeline 连接到 HDInsight 的最常见连接字符串。

## <a name="types-of-connections"></a>连接类型

### <a name="from-an-ssh-session"></a>从 SSH 会话

如果从 SSH 会话连接到群集头节点，则可随后连接到端口 `headnodehost` 上的 `10001` 地址：

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>通过 Azure 虚拟网络

通过 Azure 虚拟网络从客户端连接到 HDInsight 时，必须提供群集头节点的完全限定域名 (FQDN)。 由于直接与群集节点建立此连接，因此此连接使用端口 `10001`：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

将 `<headnode-FQDN>` 替换为群集头节点的完全限定域名。 若要查找头节点的完全限定域名，请使用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文档中的信息。

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>使用 Kerberos 向 HDInsight 企业安全包 （ESP） 群集

当从客户端连接到在群集同一区域的计算机上连接到 Azure 活动目录 （AAD）-DS） 的企业安全包 （ESP） 群集时，还必须指定具有访问群集`<AAD-Domain>``<username>`权限的域用户帐户的域名和名称：

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

将 `<username>` 替换为域中有权访问群集的帐户的名称。 将 `<AAD-DOMAIN>` 替换为群集加入到的 Azure Active Directory (AAD) 的名称。 对于 `<AAD-DOMAIN>` 值，请使用大写字符串，否则会找不到凭据。 如果需要，请查看 `/etc/krb5.conf` 中是否有领域名。

要从安巴里找到 JDBC URL：

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`，其中 `CLUSTERNAME` 是群集的名称。 确保蜂巢服务器 2 正在运行。

1. 使用剪贴板复制 HiveServer2 JDBC URL。

---

### <a name="over-public-or-private-endpoints"></a>通过公共或专用终结点

使用公共终结点或专用终结点连接到群集时，必须提供群集登录帐户名称（默认`admin`）和密码。 例如，使用 Beeline 从客户端系统连接到 `clustername.azurehdinsight.net` 地址。 此连接通过端口`443`进行，并使用 TLS/SSL 进行加密。

将 `clustername` 替换为 HDInsight 群集的名称。 将 `admin` 替换为群集的群集登录帐户。 对于 ESP 群集，请使用完整的 UPN（例如user@domain.com， 将 `password` 替换为群集登录帐户的密码。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

或对于专用终结点：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

专用终结点指向一个基本的负载均衡器，后者只能从在同一区域中进行对等互连的 VNET 访问。 有关详细信息，请参阅[对全局 VNet 对等互连和负载均衡器的约束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 在使用 beeline 之前，可以将 `curl` 命令与 `-v` 选项配合使用，以便排查公共或专用终结点的任何连接问题。

---

### <a name="use-beeline-with-apache-spark"></a>将 Beeline 与 Apache Spark 配合使用

Apache Spark 提供自己的 HiveServer2 实现（有时称为 Spark Thrift 服务器）。 此服务使用 Spark SQL 而不是 Hive 来解析查询，并且可以根据查询改善性能。

#### <a name="through-public-or-private-endpoints"></a>通过公共或专用终结点

使用的连接字符串略有不同。 而不是包含`httpPath=/hive2`它`httpPath/sparkhive2`。 将 `clustername` 替换为 HDInsight 群集的名称。 将 `admin` 替换为群集的群集登录帐户。 对于 ESP 群集，请使用完整的 UPN（例如user@domain.com， 将 `password` 替换为群集登录帐户的密码。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

或对于专用终结点：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

专用终结点指向一个基本的负载均衡器，后者只能从在同一区域中进行对等互连的 VNET 访问。 有关详细信息，请参阅[对全局 VNet 对等互连和负载均衡器的约束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 在使用 beeline 之前，可以将 `curl` 命令与 `-v` 选项配合使用，以便排查公共或专用终结点的任何连接问题。

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>使用 Apache Spark 从群集头或 Azure 虚拟网络中

当直接从群集头节点或者从 HDInsight 群集所在的 Azure 虚拟网络中的资源进行连接时，应当为 Spark Thrift 服务器使用端口 `10002` 而非 `10001`。 以下示例演示如何直接连接到头节点：

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>先决条件示例

* HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* 请记下群集主存储的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 例如，对于 Azure 存储，此值为 `wasb://`；对于Azure Data Lake Storage Gen2，此值为 `abfs://`；对于 Azure Data Lake Storage Gen1，此值为 `adl://`。 如果为 Azure 存储启用了安全传输，则`wasbs://`URI 为 。 有关详细信息，请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

* 选项 1：SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 本文档中的大多数步骤假定您使用的是从 SSH 会话到群集的 Beeline。

* 选项 2：本地蜂线客户端。

## <a name="run-a-hive-query"></a>运行 Hive 查询

此示例的基础是通过 SSH 连接使用 Beeline 客户端。

1. 使用以下代码打开到群集的 SSH 连接。 将 `sshuser` 替换为群集的 SSH 用户，并将 `CLUSTERNAME` 替换为群集的名称。 出现提示时，输入 SSH 用户帐户的密码。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 输入以下命令，通过 Beeline 客户端从打开的 SSH 会话连接到 HiveServer2：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 命令以 `!` 字符开头，例如，`!help` 会显示帮助。 但是，`!` 对于某些命令可以省略。 例如，`help` 也是有效的。

    有 `!sql`，用于执行 HiveQL 语句。 但是，由于 HiveQL 非常流行，因此可以省略前面的 `!sql`。 以下两个语句等效：

    ```hiveql
    !sql show tables;
    show tables;
    ```

    在新群集上，只会列出一个表：**hivesampletable**。

4. 使用以下命令显示 hivesampletable 的架构：

    ```hiveql
    describe hivesampletable;
    ```

    此命令返回以下信息：

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    此信息描述表中的列。

5. 输入以下语句，使用 HDInsight 群集提供的示例数据创建名为**log4jLogs**的表：（根据需要根据[URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)进行修订。

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    这些语句执行以下操作：

    |语句 |说明 |
    |---|---|
    |DROP TABLE|如果表存在，则将其删除。|
    |CREATE EXTERNAL TABLE|在蜂巢中创建**外部**表。 外部表只会在 Hive 中存储表定义。 数据将保留在原始位置。|
    |行格式|数据的格式设置方式。 在此情况下，每个日志中的字段以空格分隔。|
    |存储为文本文件位置|数据存储的位置和文件格式。|
    |SELECT|选择列**t4**包含值 **[ERROR]** 的所有行的计数。 此查询返回值 **3**，因为有三行包含此值。|
    |INPUT__FILE__NAME喜欢".log"|Hive 尝试将架构应用于目录中的所有文件。 在这种情况下，目录包含与架构不匹配的文件。 为防止结果中包含垃圾数据，此语句指示 Hive 应当仅返回以 .log 结尾的文件中的数据。|

   > [!NOTE]  
   > 如果希望通过外部源更新基础数据，应使用外部表。 例如，自动化数据上传进程或 MapReduce 操作。
   >
   > 删除外部表**不会**删除数据，只会删除表定义。

    此命令的输出类似于以下文本：

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. 退出蜜蜂线：

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>运行 HiveQL 文件

这是上一示例的继续。 使用以下步骤创建文件，并使用 Beeline 运行该文件。

1. 使用以下命令创建一个名为 **query.hql** 的文件：

    ```bash
    nano query.hql
    ```

1. 将以下文本用作文件的内容。 此查询创建名为 **errorLogs** 的新“内部”表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    这些语句执行以下操作：

    |语句 |说明 |
    |---|---|
    |创建表（如果不存在）|如果表不存在，则创建该表。 由于不使用**外部**关键字，因此此语句将创建一个内部表。 内部表存储在 Hive 数据仓库中，由 Hive 全权管理。|
    |存储为 ORC|以优化的行纵栏式 (ORC) 格式存储数据。 ORC 格式是高度优化且有效的 Hive 数据存储格式。|
    |插入覆盖...选择|从包含 **[ERROR]** 的 **log4jLogs** 表中选择行，然后将数据插入 **errorLogs** 表中。|

    > [!NOTE]  
    > 与外部表不同，删除内部表会同时删除基础数据。

1. 要保存文件，请使用**Ctrl**+**X**，然后输入**Y，** 最后**输入**。

1. 使用以下命令以通过 Beeline 运行该文件：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 参数将启动 Beeline，并运行 `query.hql` 文件中的语句。 查询完成后，会出现 `jdbc:hive2://headnodehost:10001/>` 提示符。 还可以使用 `-f` 参数运行文件，该参数在查询完成后会退出 Beeline。

1. 若要验证是否已创建 **errorLogs** 表，请使用以下语句从 **errorLogs** 返回所有行：

    ```hiveql
    SELECT * from errorLogs;
    ```

    应返回三行数据，所有数据都包含列 t4 中的 **[ERROR]：**

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>安装 beeline 客户端

尽管 HDInsight 群集的头节点上包含 Beeline，但你可能需要将其安装在本地计算机上。  用于在本地计算机上安装 Beeline 的步骤基于[用于 Linux 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/install-win10)。

1. 更新包列表。 在 bash shell 中输入以下命令：

    ```bash
    sudo apt-get update
    ```

1. 安装 Java（如果未安装）。 可以使用 `which java` 命令进行检查。

    1. 如果未安装 java 包，请输入以下命令：

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. 打开 bashrc 文件（通常位于 */.bashrc`nano ~/.bashrc`中）： 。

    1. 修改 bashrc 文件。 在该文件的末尾添加以下行：

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        依次按 **Ctrl+X**、**Y**、Enter。

1. 下载 Hadoop 和 Beeline 存档，输入以下命令：

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 解压缩这些存档，输入以下命令：

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. 进一步修改 bashrc 文件。 你需要确定存档解压缩到的路径。 如果使用[适用于 Linux 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/install-win10)，并严格按步骤操作，则路径为 `/mnt/c/Users/user/`，其中 `user` 是你的用户名。

    1. 打开文件 `nano ~/.bashrc`

    1. 用适当的路径修改下面的命令，并将其输入到 bashrc 文件的末尾：

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 依次按 **Ctrl+X**、**Y**、Enter。

1. 关闭并重新打开 bash 会话。

1. 测试连接。 使用上面的[公共或专用终结点](#over-public-or-private-endpoints)的连接格式。

## <a name="next-steps"></a>后续步骤

* 有关 HDInsight 中的 Hive 的更多常规信息，请参阅[将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

* 若要深入了解使用 Hadoop on HDInsight 的其他方法，请参阅[将 MapReduce 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-mapreduce.md)
