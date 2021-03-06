---
title: 运行 Azure 容器实例 - 语音服务
titleSuffix: Azure Cognitive Services
description: 将语音服务容器部署到 Azure 容器实例，并在 Web 浏览器中测试它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 610d5ce095097a31ee92c67f0112d1657424858e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878742"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>将语音服务容器部署到 Azure 容器实例

了解如何将认知服务[语音服务](speech-container-howto.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示了 Azure 语音转换服务资源的创建。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

在请求访问容器之前，必须先填写并提交[认知服务语音容器请求表单](https://aka.ms/speechcontainerspreview/)。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
