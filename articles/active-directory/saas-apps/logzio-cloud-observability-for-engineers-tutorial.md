---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Logz.io - Cloud Observability for Engineers 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Logz.io - Cloud Observability for Engineers 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0984471-d12f-4f58-896e-194ea45b01fd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1c929ffa790d2abe3a1922cecc2175cd7a8e12
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385269"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logzio---cloud-observability-for-engineers"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Logz.io - Cloud Observability for Engineers 的集成

本教程介绍如何将 Logz.io - Cloud Observability for Engineers 与 Azure Active Directory (Azure AD) 集成。 将 Logz.io - Cloud Observability for Engineers 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Logz.io - Cloud Observability for Engineers。
* 让用户使用其 Azure AD 帐户自动登录到 Logz.io - Cloud Observability for Engineers。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Logz.io - Cloud Observability for Engineers 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Logz.io - Cloud Observability for Engineers 支持 **IDP** 发起的 SSO
* 配置 Logz.io - Cloud Observability for Engineers 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-logzio---cloud-observability-for-engineers-from-the-gallery"></a>从库中添加 Logz.io - Cloud Observability for Engineers

若要配置 Logz.io - Cloud Observability for Engineers 与 Azure AD 的集成，需要从库中将 Logz.io - Cloud Observability for Engineers 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Logz.io - Cloud Observability for Engineers**。 
1. 在结果面板中选择“Logz.io - Cloud Observability for Engineers”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-logzio---cloud-observability-for-engineers"></a>配置并测试 Logz.io - Cloud Observability for Engineers 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户为 Logz.io - Cloud Observability for Engineers 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Logz.io - Cloud Observability for Engineers 相关用户之间建立关联。

若要为 Logz.io - Cloud Observability for Engineers 配置和测试 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Logz.io - Cloud Observability for Engineers SSO](#configure-logzio-cloud-observability-for-engineers-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Logz.io - Cloud Observability for Engineers 测试用户](#create-logzio-cloud-observability-for-engineers-test-user)** - 在 Logz.io - Cloud Observability for Engineers 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Logz.io - Cloud Observability for Engineers”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`urn:auth0:logzio:CONNECTION-NAME`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://logzio.auth0.com/login/callback?connection=CONNECTION-NAME`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Logz.io - Cloud Observability for Engineers 客户端支持团队](mailto:help@logz.io)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Logz.io - Cloud Observability for Engineers 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Logz.io - Cloud Observability for Engineers 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | ---------------| --------- |
    | session-expiration | user.session-expiration |
    | 电子邮件 | user.mail |
    | 组 | user.groups |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Logz.io - Cloud Observability for Engineers”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 B.Simon 访问 Logz.io - Cloud Observability for Engineers 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Logz.io - Cloud Observability for Engineers”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-logzio-cloud-observability-for-engineers-sso"></a>配置 Logz.io Cloud Observability for Engineers SSO

若要在 **Logz.io - Cloud Observability for Engineers** 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [Logz.io - Cloud Observability for Engineers 支持团队](mailto:help@logz.io)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-logzio-cloud-observability-for-engineers-test-user"></a>创建 Logz.io - Cloud Observability for Engineers 测试用户

在本部分，我们在 Logz.io - Cloud Observability for Engineers 中创建一个名为“Britta Simon”的用户。 请与 [Logz.io - Cloud Observability for Engineers 支持团队](mailto:help@logz.io)协作，将用户添加到 Logz.io - Cloud Observability for Engineers 平台中。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Logz.io - Cloud Observability for Engineers 磁贴时，应当会自动登录到设置了 SSO 的 Logz.io - Cloud Observability for Engineers。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Logz.io - Cloud Observability for Engineers](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Logz.io - Cloud Observability for Engineers](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

