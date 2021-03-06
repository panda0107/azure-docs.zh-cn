---
title: 使用 Twitter 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为 应用程序中的客户提供通过 Twitter 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051468"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录

## <a name="create-an-application"></a>创建应用程序

若要将 Twitter 用作 Azure AD B2C 中的身份提供程序，则需要创建 Twitter 应用程序。 如果您还没有 Twitter 帐户，您可以在 注册[https://twitter.com/signup](https://twitter.com/signup)。

1. 使用 Twitter 帐户凭据登录到 [Twitter 开发人员](https://developer.twitter.com/en/apps)网站。
1. 选择“创建应用”。****
1. 输入“应用名称”和“应用程序说明”********。
1. 在“网站 URL”**** 中，输入 `https://your-tenant.b2clogin.com`。 将 `your-tenant` 替换为租户的名称。 例如，`https://contosob2c.b2clogin.com` 。
1. 对于“回调 URL”****，输入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`。 将 `your-tenant` 替换为你的租户的名称，并将 `your-user-flow-Id` 替换为你的用户流的标识符。 例如，`b2c_1A_signup_signin_twitter` 。 输入租户名称和用户流 ID 时，需要使用所有小写字母，即使这些小写字母在 Azure AD B2C 中使用大写字母定义也是如此。
1. 在页面底部，阅读并接受条款，然后选择“创建”。****
1. 在“应用详细信息”**** 页上，选择“编辑”>“编辑详细信息”****，勾选“启用 Twitter 登录”**** 框，然后选择“保存”****。
1. 选择“密钥和令牌”**** 并记录“使用者 API 密钥”**** 和“使用者 API 密钥”**** 的值，以便稍后使用。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>将 Twitter 配置为租户中的标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
1. 选择 Azure 门户左上角的“所有服务”****，搜索并选择 **Azure AD B2C**。
1. 选择**身份提供者**，然后选择**推特**。
1. 输入“名称”****。 例如，*推特*。
1. 对于**客户端 ID，** 输入您之前创建的 Twitter 应用程序的使用者 API 密钥。
1. 对于**客户端密钥**，输入您录制的使用者 API 密钥。
1. 选择“保存”。****
