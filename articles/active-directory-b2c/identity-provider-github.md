---
title: 使用 GitHub 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 GitHub 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188199"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 GitHub 帐户注册与登录

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>创建 GitHub OAuth 应用程序

要将 GitHub 帐户用作 Azure Active Directory B2C (Azure AD B2C) 中的[标识提供者](authorization-code-flow.md)，需要在表示它的租户中创建一个应用程序。 如果您还没有 GitHub 帐户，则可以在 注册[https://www.github.com/](https://www.github.com/)。

1. 使用 GitHub 凭据登录 [GitHub 开发人员](https://github.com/settings/developers)网站。
1. 选择****“OAuth 应用”，然后选择****“新建 OAuth 应用”。
1. 输入**应用程序名称**和**主页 URL**。
1. 在“授权回调 URL”**** 中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 输入租户名称时，全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
1. 单击“Register application”****（注册应用程序）。
1. 复制**客户端 ID**和**客户端机密的值**。 将标识提供者添加到租户时需要这两个值。

## <a name="configure-a-github-account-as-an-identity-provider"></a>将 GitHub 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
1. 选择 Azure 门户左上角的“所有服务”****，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”****，然后选择“GitHub (预览)”****。
1. 输入“名称”****。 例如，GitHub**。
1. 对于**客户端 ID**，输入你之前创建的 GitHub 应用程序的客户端 ID。
1. 对于**客户端密码**，输入你记录的客户端密码。
1. 选择“保存”。****
