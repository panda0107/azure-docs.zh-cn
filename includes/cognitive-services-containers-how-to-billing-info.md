---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474113"
---
对该容器的查询在用于 `ApiKey` 的 Azure 资源的定价层计费。

Azure 认知服务容器未获得运行许可，而不连接到计量/计费终结点。 必须始终让容器可以向计费终结点传送计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

### <a name="connect-to-azure"></a>连接到 Azure

容器需要计费参数值才能运行。 这些值使容器可以连接到计费终结点。 容器约每 10 到 15 分钟报告一次使用情况。 如果容器未在允许的时间范围内连接到 Azure，容器将继续运行，但不会为查询提供服务，直到计费终结点恢复。 尝试连接按 10 到 15 分钟的相同时间间隔进行 10 次。 如果无法连接到 10 次尝试中的计费终结点，则容器将停止服务请求。

### <a name="billing-arguments"></a>计费参数

当<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">`docker run`<span class="docon docon-navigate-external x-hidden-focus"></span></a>以下所有三个选项都提供有效值时，该命令将启动该容器：

| 选项 | 描述 |
|--------|-------------|
| `ApiKey` | 用于跟踪计费信息的认知服务资源的 API 密钥。<br/>必须将此选项的值设置为 `Billing` 中指定的已预配资源的 API 密钥。 |
| `Billing` | 用于跟踪计费信息的认知服务资源的终结点。<br/>必须将此选项的值设置为已预配的 Azure 资源的终结点 URI。|
| `Eula` | 表示已接受容器的许可条款。<br/>此选项的值必须设置为 **accept**。 |
