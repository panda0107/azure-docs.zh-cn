---
title: 规划 Azure HDInsight 的虚拟网络
description: 了解如何规划 Azure 虚拟网络部署，以将 HDInsight 连接到其他云资源或数据中心内的资源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 30664d533215cb49fa6f436ec4cf88fa319c3300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272261"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>规划 Azure HDInsight 的虚拟网络

本文提供有关将[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)（VNet） 与 Azure HDInsight 一起使用的背景信息。 其中介绍了在为 HDInsight 群集实施虚拟网络之前必须做出的设计和实施决策。 规划阶段完成后，可以继续[为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。 有关正确配置网络安全组 （NSG） 和用户定义的路由所需的 HDInsight 管理 IP 地址的详细信息，请参阅[HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

使用 Azure 虚拟网络支持以下方案：

* 直接从本地网络连接到 HDInsight。
* 将 HDInsight 连接到 Azure 虚拟网络中的数据存储。
* 直接访问 Apache Hadoop 服务，这些服务在互联网上无法公开提供。 例如，Apache Kafka API 或 Apache HBase Java API。

> [!IMPORTANT]
> 在 VNET 中创建 HDInsight 群集时会创建多个网络资源，例如 NIC 和负载均衡器。 请**勿**删除这些网络资源，因为群集需要它们才能在 VNET 中正常运行。
>
> 2019 年 2 月 28 日以后，在 VNET 中创建的新 HDInsight 群集的网络资源（例如 NIC、LB 等）会在同一 HDInsight 群集资源组中进行预配。 以前，这些资源在 VNET 资源组中预配。 当前运行的群集以及那些在没有 VNET 的情况下创建的群集没有任何更改。

## <a name="planning"></a>规划

计划在虚拟网络中安装 HDInsight 时，必须回答以下问题：

* 是否需要将 HDInsight 安装到现有的虚拟网络？ 或者是否正在创建新的网络？

    如果您使用的是现有虚拟网络，则可能需要修改网络配置，然后才能安装 HDInsight。 有关详细信息，请参阅[将 HDInsight 添加到现有虚拟网络](#existingvnet)一节。

* 是否要将包含 HDInsight 的虚拟网络连接到其他虚拟网络或你的本地网络？

    若要轻松地跨网络使用资源，可能需要创建自定义 DNS 并配置 DNS 转发。 有关详细信息，请参阅[连接多个网络](#multinet)一节。

* 是否想要将入站或出站流量限制/重定向到 HDInsight？

    HDInsight 与 Azure 数据中心中特定 IP 地址之间的通信必须不受限制。 此外，还必须设置几个防火墙允许端口以进行客户端通信。 有关详细信息，请参阅[控制网络流量](#networktraffic)一节。

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>将 HDInsight 添加到现有虚拟网络

使用本部分中的步骤，了解如何将 HDInsight 添加到现有 Azure 虚拟网络。

> [!NOTE]  
> 无法将现有 HDInsight 群集添加到虚拟网络中。

1. 对虚拟网络使用经典模式还是资源管理器部署模式？

    HDInsight 3.4 及更高版本要求使用资源管理器虚拟网络。 早期版本的 HDInsight 要求使用经典虚拟网络。

    如果你的现有网络是经典虚拟网络，则必须创建资源管理器虚拟网络，然后连接这两者。 [将经典 VNet 连接到新 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    加入后，资源管理器网络中安装的 HDInsight 就可以与经典网络中的资源进行交互了。

2. 是否使用网络安全组、用户定义路由或虚拟网络设备来限制流量进出虚拟网络？

    作为托管服务，HDInsight 需要无限制访问 Azure 数据中心中的若干个 IP 地址。 若要允许与这些 IP 地址进行通信，请更新任何现有网络安全组或用户定义的路由。

    HDInsight 托管多个服务，这些服务使用不同的端口。 不要阻止到这些端口的流量。 有关虚拟设备防火墙的允许端口列表，请参阅“安全”一节。

    若要查找你现有的安全配置，请使用以下 Azure PowerShell 或 Azure CLI 命令：

    * 网络安全组

        将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        有关详细信息，请参阅[排查网络安全组问题](../virtual-network/diagnose-network-traffic-filter-problem.md)一文。

        > [!IMPORTANT]  
        > 已根据规则优先级按顺序应用网络安全组规则。 应用与流量模式匹配的第一个规则，不会对该流量应用其他规则。 从最高权限到最低权限排序规则。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

    * 用户定义路由

        将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        有关详细信息，请参阅[排查路由问题](../virtual-network/diagnose-network-routing-problem.md)文档。

3. 创建一个 HDInsight 群集，并在配置过程中选择 Azure 虚拟网络。 使用以下文档中的步骤了解群集创建过程：

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md)（使用 Azure 门户创建 HDInsight）
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)（使用 Azure PowerShell 创建 HDInsight）
    * [使用 Azure 经典 CLI 创建 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [使用 Azure 资源管理器模板创建 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 向虚拟网络添加 HDInsight 是一项可选的配置步骤。 请确保在配置群集时选择虚拟网络。

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>连接多个网络

多网络配置的最大挑战是网络之间的名称解析。

Azure 为安装在虚拟网络中的 Azure 服务提供名称解析。 此内置名称解析允许 HDInsight 使用完全限定的域名 (FQDN) 连接到以下资源：

* Internet 上的任何可用资源。 例如，microsoft.com、windowsupdate.com。

* 位于同一 Azure 虚拟网络中的任何资源（通过使用资源的内部 DNS 名称____）。 例如，在使用默认的名称解析时，下面是分配给 HDInsight 工作器节点的内部 DNS 名称示例：

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    这两个节点通过使用内部 DNS 名称可以直接彼此通信，以及与 HDInsight 中的其他节点进行通信。

默认名称解析不____ 允许 HDInsight 解析加入到虚拟网络的网络中的资源名称。 例如，将本地网络加入虚拟网络很常见。 仅使用默认名称解析，HDInsight 无法按名称访问本地网络中的资源。 事实正好相反，本地网络中的资源无法按名称访问虚拟网络中的资源。

> [!WARNING]  
> 必须创建自定义 DNS 服务器并配置虚拟网络以在创建 HDInsight 群集前使用它。

若要启用虚拟网络和已加入网络中的资源之间的名称解析，必须执行以下操作：

1. 在你计划安装 HDInsight 的 Azure 虚拟网络中创建自定义 DNS 服务器。

2. 配置虚拟网络以使用自定义 DNS 服务器。

3. 查找 Azure 为你的虚拟网络分配的 DNS 后缀。 该值类似于 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`。 有关查找 DNS 后缀的信息，请参阅[示例：自定义 DNS](hdinsight-create-virtual-network.md#example-dns) 一节。

4. 配置 DNS 服务器之间的转发。 配置具体取决于远程网络的类型。

   * 如果远程网络是本地网络，请按如下所示配置 DNS：

     * __自定义 DNS__（虚拟网络中）：

         * 将虚拟网络 DNS 后缀的请求转发到 Azure 递归解析程序 (168.63.129.16)。 Azure 处理虚拟网络中资源的请求

         * 将其他所有请求转发到本地 DNS 服务器。 本地 DNS 处理所有其他名称解析请求，甚至是 Internet 资源（如 microsoft.com）的请求。

     * __本地 DNS__： 将虚拟网络 DNS 后缀的请求转发到自定义 DNS 服务器。 然后，自定义 DNS 服务器转发给 Azure 递归解析程序。

       此配置将包含虚拟网络 DNS 后缀的完全限定的域名请求路由至自定义 DNS 服务器。 其他所有请求（即使是公共 Internet 地址） 都由本地 DNS 服务器处理。

   * 如果远程网络是其他 Azure 虚拟网络，请按如下所示配置 DNS：

     * __自定义 DNS__（在每个虚拟网络中）：

         * 虚拟网络 DNS 后缀的请求将转发到自定义 DNS 服务器。 每个虚拟网络中的 DNS 负责解析其网络中的资源。

         * 将所有其他请求转发到 Azure 递归解析程序。 递归解析器负责解析本地和 Internet 资源。

       每个网络的 DNS 服务器根据 DNS 后缀将请求转发到另一个服务器。 使用 Azure 递归解析程序解析其他请求。

     有关每个配置的示例，请参阅[示例：自定义 DNS](hdinsight-create-virtual-network.md#example-dns) 一节。

有关详细信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文档。

## <a name="directly-connect-to-apache-hadoop-services"></a>直接连接到 Apache Hadoop 服务

可以通过 `https://CLUSTERNAME.azurehdinsight.net` 连接到该群集。 此地址使用公共 IP，如果已使用 NSG 来限制来自 Internet 的传入流量，则可能无法访问此地址。 此外，在 VNet 中部署群集时，可以使用专用终结点 `https://CLUSTERNAME-int.azurehdinsight.net` 访问它。 此终结点可解析为 VNet 中的专用 IP，以进行群集访问。

若要通过虚拟网络连接到 Apache Ambari 以及其他网页，请使用以下步骤：

1. 若要发现 HDInsight 群集节点的内部完全限定的域名 (FQDN)，请使用以下其中一种方法：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    在返回的节点列表中，查找头节点的 FQDN，并使用这些 FQDN 连接到 Ambari 和其他 Web 服务。 例如，使用 `http://<headnode-fqdn>:8080` 访问 Ambari。

    > [!IMPORTANT]  
    > 在头节点上托管的一些服务一次只能在一个节点上处于活动状态。 如果尝试在一个头节点上访问服务并且它返回 404 错误，请切换到其他头节点。

2. 若要确定服务可用的节点和端口，请参阅 [HDInsight 的 Hadoop 服务所用的端口](./hdinsight-hadoop-port-settings-for-services.md)一文。

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a>控制网络流量

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>控制 HDInsight 群集的入站和出站流量的技术

可以使用以下方法控制 Azure 虚拟网络中的网络流量：

* 网络安全组****(NSG) 允许你筛选往返于网络的入站和出站流量。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

* **网络虚拟设备** (NVA) 只能用于出站流量。 NVA 可复制设备（如防火墙和路由器）的功能。 有关详细信息，请参阅[网络设备](https://azure.microsoft.com/solutions/network-appliances)文档。

作为托管服务，HDInsight 需要对 HDInsight 运行状况和管理服务具有不受限制的访问权限，以处理从 VNET 传入和传出的流量。 使用 NSG 时，必须确保这些服务仍然可以与 HDInsight 群集进行通信。

![在 Azure 自定义 VNET 中创建的 HDInsight 实体的关系图](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>使用网络安全组的 HDInsight

如果计划使用**网络安全组**来控制网络流量，请在安装 HDInsight 之前执行以下操作：

1. 标识你计划用于 HDInsight 的 Azure 区域。

2. 标识 HDInsight 为您的地区所需的服务标记。 有关详细信息，请参阅[Azure HDInsight 的网络安全组 （NSG） 服务标记](hdinsight-service-tags.md)。

3. 为计划将 HDInsight 安装到其中的子网创建或修改网络安全组。

    * __网络安全组__： 允许 IP 地址端口 443 上的入站________ 流量。 这将确保 HDInsight 管理服务可以从虚拟网络外部访问群集。

有关网络安全组的详细信息，请参阅[网络安全组概述](../virtual-network/security-overview.md)。

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制 HDInsight 群集的出站流量

有关控制 HDInsight 群集的出站流量的详细信息，请参阅[配置 Azure HDInsight 群集的出站网络流量限制](hdinsight-restrict-outbound-traffic.md)。

#### <a name="forced-tunneling-to-on-premises"></a>到本地的强制隧道

强制隧道是用户定义的路由配置，其中来自子网的所有流量都强制发往特定网络或位置，例如你的本地网络。 HDInsight__不支持__强制隧道将流量隧道到本地网络。

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a>需要的 IP 地址

如果使用网络安全组或用户定义的路由来控制流量，请参阅[HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

## <a name="required-ports"></a><a id="hdinsight-ports"></a>所需的端口

如果计划使用**防火墙**并在特定端口上从外部访问群集，则需要允许你的方案所需的那些端口上的流量。 默认情况下，只要允许上一部分中介绍的 Azure 管理流量在端口 443 上到达群集，则不需要特地将端口列入白名单。

对于特定服务的端口列表，请参阅 [HDInsight 上的 Apache Hadoop 服务所用的端口](hdinsight-hadoop-port-settings-for-services.md)文档。

有关虚拟设备的防火墙规则的详细信息，请参阅[虚拟设备方案](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文档。

## <a name="load-balancing"></a>负载均衡

创建 HDInsight 群集时，也会创建一个负载均衡器。 此负载均衡器的类型处于[基本 SKU 级别](../load-balancer/concepts-limitations.md#skus)，具有一定的约束。 这些约束中的一个是：如果两个虚拟网络位于不同的区域，则无法连接到基本负载均衡器。 有关详细信息，请参阅[虚拟网络常见问题解答：对全局 VNet 对等互连的约束](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

## <a name="transport-layer-security"></a>传输层安全性

通过公共群集终结点`https://<clustername>.azurehdinsight.net`与群集的连接通过群集网关节点进行接近。 这些连接使用称为 TLS 的协议进行保护。 在网关上强制实施更高版本的 TLS 可提高这些连接的安全性。 有关为什么要使用较新版本 TLS 的详细信息，请参阅[解决 TLS 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)。

默认情况下，Azure HDInsight 群集接受公共 HTTPS 终结点上的 TLS 1.2 连接，以及旧版本，以便向后兼容。 在群集创建期间，可以使用 Azure 门户或资源管理器模板控制网关节点上支持的最小 TLS 版本。 对于门户，在群集创建期间从 **"安全 + 网络**"选项卡中选择 TLS 版本。 对于部署时资源管理器模板，请使用 **"最小支持 TlsVersion"** 属性。 有关示例模板，请参阅[HDInsight 最小 TLS 1.2 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此属性支持三个值："1.0"、"1.1"和"1.2"，分别对应于 TLS 1.0°、TLS 1.1+ 和 TLS 1.2+。

> [!IMPORTANT]
> 从 2020 年 6 月 30 日开始，Azure HDInsight 将对所有 HTTPS 连接强制实施 TLS 1.2 或更高版本。 我们建议你确保所有客户端都已准备好处理 TLS 1.2 或更高版本。 有关详细信息，请参阅[Azure HDInsight TLS 1.2 强制](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)。

## <a name="next-steps"></a>后续步骤

* 有关演示如何创建 Azure 虚拟网络的代码示例和操作示例，请参阅[为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。
* 有关将 HDInsight 配置为连接到本地网络的端到端示例，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 有关在 Azure 虚拟网络中配置 Apache HBase 群集，请参阅[在 Azure 虚拟网络中的 HDInsight 上创建 Apache HBase 群集](hbase/apache-hbase-provision-vnet.md)。
* 要了解如何配置 Apache HBase 异地复制，请参阅[在 Azure 虚拟网络中设置 Apache HBase 群集复制](hbase/apache-hbase-replication.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。
* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
