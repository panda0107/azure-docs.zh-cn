---
title: 发布产品/服务 |Azure 应用商店
description: 用于发布指定产品/服务的 API。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280518"
---
<a name="publish-an-offer"></a>发布产品/服务
================

启动指定产品/服务的发布过程。 此调用是一个长时间运行的操作。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 参数
--------------

|  **名称**      |    **说明**                               |  **数据类型** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 发布者标识符，例如 `contoso`      |   String       |
|  offerId       | 产品/服务标识符                                 |   String       |
|  api-version   | API 最新版本                        |   Date         |
|  |  |


<a name="header"></a>标头
------

|  **名称**        |    **价值**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  授权   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>正文示例
------------

### <a name="request"></a>请求

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>请求正文属性

|  **名称**               |   **说明**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 逗号分隔的电子邮件地址列表，用于通知发布操作的进度。 |
|  |  |


### <a name="response"></a>响应

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>响应标头

|  **名称**             |    **价值**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | URL，可以查询它以确定操作的当前状态。    |
|  |  |


### <a name="response-status-codes"></a>响应状态代码

| **代码** |  **说明**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - 已成功接受请求。 响应包含一个位置，该位置可用于跟踪已启动的操作。 |
| 400   | `Bad/Malformed request` - 错误响应正文可以提供更多信息。                                                               |
| 422   | `Un-processable entity` -指示要发布的实体未通过验证。                                                        |
| 404   | `Not found` - 客户端指定的实体不存在。                                                                              |
|  |  |
