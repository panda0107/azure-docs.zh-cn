---
title: Azure Active Directory 图形 API
description: 有关 Azure AD Graph API 的概述和快速入门指南，其中允许通过 REST API 终结点以编程方式访问 Azure AD。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 73cdac1a372b42df5a8f52ea09f04ecc40031698
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885712"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory 图形 API

> [!IMPORTANT]
> 强烈建议使用 [Microsoft Graph](https://developer.microsoft.com/graph)（而非 Azure AD Graph API）访问 Azure Active Directory (Azure AD) 资源。 目前，我们在集中开发 Microsoft Graph，未计划进一步改进 Azure AD Graph API。 Azure AD Graph API 仍可能适用的方案数量非常有限；有关详细信息，请参阅 [Microsoft Graph or the Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/)（Microsoft Graph 或 Azure AD Graph）博客文章和[将 Azure AD Graph 应用迁移到 Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview)。

本文适用于 Azure AD 图形 API。 有关与 Microsoft Graph API 相关的类似信息，请参阅[使用 Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)。

Azure Active Directory 图形 API 通过 REST API 终结点提供对 Azure AD 的编程访问权限。 应用程序可以使用 Azure AD 图形 API 对目录数据和对象执行创建、读取、更新和删除 (CRUD) 操作。 例如，Azure AD 图形 API 支持对用户对象执行以下常见操作：

* 在目录中创建新用户
* 获取用户的详细属性，如其组
* 更新用户的属性，如其位置和电话号码，或更改其密码
* 检查用户的组成员身份，了解基于角色的访问权限
* 禁用用户帐户或将其完全删除

此外，还可以对其他对象（如组和应用程序）执行类似操作。 若要对目录调用 Azure AD 图形 API，必须向 Azure AD 注册应用程序。 应用程序还必须有权访问 Azure AD 图形 API。 通常可以通过用户或管理员同意流来实现此访问。

要开始使用 Azure 活动目录图形 API，请参阅[Azure AD 图形 API 快速入门指南](active-directory-graph-api-quickstart.md)，或查看交互式 Azure AD 图形 API[参考文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

## <a name="features"></a>功能

Azure AD 图形 API 提供以下功能：

* **REST API 终结点**：Azure AD 图形 API 是一个 RESTful 服务，该服务由使用标准 HTTP 请求访问的终结点组成。 Azure AD 图形 API 支持对请求和响应使用 XML 或 Javascript 对象表示法 (JSON) 内容类型。 有关详细信息，请参阅[Azure AD 图形 REST API 引用](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。
* **向 Azure AD 进行身份验证**：必须在请求的 Authorization 标头中追加 JSON Web 令牌 (JWT)，以便对向 Azure AD 图形 API 发出的每个请求进行身份验证。 此令牌是通过向 Azure AD 的令牌终结点发出请求并提供有效的凭据来获取的。 可以使用 OAuth 2.0 客户端凭据流或授权代码授予流来获取调用 Graph 所需的令牌。 有关详细信息，请参阅 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
* **基于角色的授权 (RBAC)**：安全组用于在 Azure AD 图形 API 中执行 RBAC。 例如，如果要确定用户是否有权访问特定资源，应用程序可以调用[Check 组成员身份（传递）](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups)操作，该操作返回 true 或 false。
* **差异查询**：如果要查看两个时间段之间对目录所做的更改，而不对 Azure AD 图形 API 进行频繁的查询，可以发出差异查询请求。 这种类型的请求将只返回在上一个差异查询请求与当前请求之间所做的更改。 有关详细信息，请参阅[Azure AD 图形 API 差异查询](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)。
* **目录扩展**：可将自定义属性添加到目录对象，而无需外部数据存储。 例如，如果应用程序需要每个用户的 Skype ID 属性，则可以在目录中注册新属性，即可在每个用户对象上获取该属性。 有关详细信息，请参阅[Azure AD 图形 API 目录架构扩展](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)。
* **受权限范围保护**：Azure AD 图形 API 公开权限范围，支持使用 OAuth 2.0 对 Azure AD 数据进行安全访问。 它支持各种客户端应用类型，包括：
  
  * 具有用户界面的应用，这类应用通过登录用户（委派）授权而获得对数据的委派访问权限
  * 在后台运行的服务/守护程序，无需用户登录且使用应用程序定义的基于角色的访问控制
    
    委派和应用程序权限范围都代表 Azure AD 图形 API 公开的特权，且客户端应用程序可通过 [Azure 门户](https://portal.azure.com)中的应用程序注册权限功能进行请求。 [Azure AD 图形 API 权限范围](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)提供有关客户端应用程序可以使用的内容的信息。

## <a name="scenarios"></a>方案

Azure AD 图形 API 可实现许多应用程序方案。 以下方案最常见：

* **业务线（单租户）应用程序**：在此方案中，一个企业开发人员为一个拥有 Office 365 订阅的组织工作。 开发人员将构建与 Azure AD 交互的 Web 应用程序，用于执行将许可证分配给用户等任务。 此任务需要访问 Azure AD 图形 API，因此开发人员在 Azure AD 中注册单租户应用程序，并为 Azure AD 图形 API 配置读取和写入权限。 然后，将应用程序配置为使用其自己的凭据或当前登录用户的凭据来获取调用 Azure AD 图形 API 所需的令牌。
* **服务型软件应用程序（多租户）**：在此方案中，独立软件供应商 (ISV) 将开发一个托管多租户 Web 应用程序，该应用程序为使用 Azure AD 的其他组织提供用户管理功能。 这些功能需要访问目录对象，因此该应用程序需要调用 Azure AD 图形 API。 开发人员在 Azure AD 中注册该应用程序，将它配置为需要对 Azure AD 图形 API 的读取和写入权限，然后启用了外部访问，这样其他组织便可以同意在其目录中使用该应用程序。 当其他组织中的用户首次向该应用程序进行身份验证时，他们会看到一个同意对话框，该对话框包含应用程序请求的权限。 然后，授予同意将授予应用程序这些请求的权限到用户目录中的 Azure AD 图形 API。 有关同意框架的详细信息，请参阅[同意框架概述](consent-framework.md)。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Active Directory 图形 API，请参阅以下主题：

* [Azure AD 图形 API 快速入门指南](active-directory-graph-api-quickstart.md)
* [Azure AD Graph REST 文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
