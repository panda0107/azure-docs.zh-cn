---
title: Microsoft 标识平台身份验证协议
description: Microsoft 标识平台支持的身份验证协议概述
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884675"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft 标识平台身份验证协议

Microsoft 标识平台支持多个最广泛使用的身份验证和授权协议。 本部分中的主题介绍 Microsoft 标识平台中支持的协议及其实现。 这些主题包括支持的声明类型的回顾、联合元数据的使用简介、详细的 OAuth 2.0。 和 SAML 2.0 协议参考文档，以及故障排除部分。

## <a name="authentication-protocols-articles-and-reference"></a>身份验证协议文章和参考

* [有关在 Microsoft 标识平台中签名密钥滚动的重要信息](active-directory-signing-key-rollover.md)– 了解 Microsoft 标识平台的签名密钥滚动节奏、可以进行更改以自动更新密钥以及如何更新最常见的应用程序方案的讨论。
* [支持的令牌和声明类型](id-tokens.md) - 了解 Microsoft 标识平台颁发的令牌中的声明。
* [Microsoft 标识平台中的 OAuth 2.0](v2-oauth2-auth-code-flow.md) - 了解 Microsoft 标识平台中 OAuth 2.0 的实现。
* [OpenID Connect 1.0](v2-protocols-oidc.md) - 了解如何使用 OAuth 2.0（一种授权协议）进行身份验证。
* [使用客户端凭据的服务间调用](v2-oauth2-client-creds-grant-flow.md) - 了解如何对服务到服务调用使用 OAuth 2.0 客户端凭据授权流。
* [使用代理流的服务间调用](v2-oauth2-on-behalf-of-flow.md) - 了解如何对服务到服务调用使用 OAuth 2.0 代理流。
* [SAML 协议引用](active-directory-saml-protocol-reference.md)- 了解 Microsoft 标识平台的单一登录和单签出 SAML 配置文件。

## <a name="see-also"></a>请参阅

* [Microsoft 标识平台概述](v2-overview.md)
* [Active Directory 代码示例](sample-v2-code.md)
