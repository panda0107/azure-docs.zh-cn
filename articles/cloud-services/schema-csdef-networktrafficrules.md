---
title: Azure 云服务定义网络流量规则架构 |微软文档
description: 了解网络流量规则，它限制可以访问角色内部终结点的角色。 它与服务定义文件中的角色结合。
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534722"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure 云服务定义 NetworkTrafficRules 架构
`NetworkTrafficRules` 节点是服务定义文件中的一个可选元素，用于指定角色如何彼此进行通信。 它限制哪些角色可以访问特定角色的内部终结点。 `NetworkTrafficRules` 不是一个独立的元素；它在服务定义文件中与两个或更多角色结合使用。

服务定义文件的默认扩展名为 .csdef。

> [!NOTE]
>  仅当使用 Azure SDK 1.3 版或更高版本时，才可以使用 `NetworkTrafficRules` 节点。

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>网络流量规则的基本服务定义架构
包含网络流量定义的服务定义文件的基本格式如下所示。

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>架构元素
服务定义文件的 `NetworkTrafficRules` 节点包含以下元素，本主题的后续部分做了详细说明：

[NetworkTrafficRules 元素](#NetworkTrafficRules)

[仅允许流量到元素](#OnlyAllowTrafficTo)

[Destinations 元素](#Destinations)

[RoleEndpoint 元素](#RoleEndpoint)

AllowAllTraffic 元素

[WhenSource 元素](#WhenSource)

[FromRole 元素](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>网络流量规则元素
`NetworkTrafficRules` 元素指定哪些角色可以与另一角色上的哪个终结点进行通信。 服务可以包含一个 `NetworkTrafficRules` 定义。

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo 元素
`OnlyAllowTrafficTo` 元素描述目标终结点的集合以及可以与它们进行通信的角色。 可以指定多个 `OnlyAllowTrafficTo` 节点。

##  <a name="destinations-element"></a><a name="Destinations"></a>目标元素
`Destinations` 元素描述可以与之进行通信的 RoleEndpoint 的集合。

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>角色端点元素
`RoleEndpoint` 元素描述角色上允许与之进行通信的终结点。 如果角色上有多个终结点，可以指定多个 `RoleEndpoint` 元素。

| 特性      | 类型     | 说明 |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | 必需。 允许将流量发送到的终结点的名称。|
| `roleName`     | `string` | 必需。 允许与之进行通信的 web 角色的名称。|

## <a name="allowalltraffic-element"></a>AllowAllTraffic 元素
`AllowAllTraffic` 元素是一个规则，它允许所有角色与 `Destinations` 节点中定义的终结点进行通信。

##  <a name="whensource-element"></a><a name="WhenSource"></a>当源元素
`WhenSource` 元素描述可以与 `Destinations` 节点中定义的终结点进行通信的角色的集合。

| 特性 | 类型     | 说明 |
| --------- | -------- | ----------- |
| `matches` | `string` | 必需。 指定允许通信时要应用的规则。 目前唯一有效的值是 `AnyRule`。|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>从角色元素
`FromRole` 元素指定可以与 `Destinations` 节点中定义的终结点进行通信的角色。 如果有多个可以与终结点进行通信的角色，可以指定多个 `FromRole` 元素。

| 特性  | 类型     | 说明 |
| ---------- | -------- | ----------- |
| `roleName` | `string` | 必需。 允许从中进行通信的角色的名称。|

## <a name="see-also"></a>另请参阅
[云服务（经典）定义架构](schema-csdef-file.md)




