---
title: 教程：使用 Azure 活动目录配置拨号板以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 Dialpad 预配用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058332"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>教程：为自动用户预配配置拨号板

本教程的目的是演示在 Dialpad 和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户和/或组预配到拨号板。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

> 此连接器目前提供预览版。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [拨号板租户](https://www.dialpad.com/pricing/)。
* 具有管理员权限的拨号板中的用户帐户。

## <a name="assign-users-to-dialpad"></a>将用户分配到拨号板
Azure 活动目录使用称为分配的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 Dialpad。 决定后，您可以按照此处的说明将这些用户和/或组分配给 Dialpad：
 
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>将用户分配给拨号板的重要提示

 * 建议将单个 Azure AD 用户分配给 Dialpad 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 Dialpad 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有默认访问权限角色的用户从预配中排除。

## <a name="setup-dialpad-for-provisioning"></a>设置拨号板进行预配

在使用 Azure AD 配置拨号板以进行自动用户预配之前，需要从拨号板检索一些预配信息。

1. 登录到您的[拨号板管理控制台](https://dialpadbeta.com/login)并选择**管理设置**。 确保从下拉列表中选择**我的公司**。 导航到**API 密钥>身份验证**。

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. 通过单击"**添加密钥"** 并配置密钥令牌的属性来生成新密钥。

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. **单击"单击"以显示**最近创建的 API 密钥的值按钮，并复制显示的值。 此值将在 Azure 门户中的 Dialpad 应用程序的预配选项卡中的 **"机密令牌"** 字段中输入。 

    ![拨号板创建令牌](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>从库中添加拨号板

要配置 Dialpad 以使用 Azure AD 进行自动用户预配，需要将 Dialpad 从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加拨号板，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在结果面板中输入**拨号板**，选择**拨号板**。
    ![结果列表中的拨号板](common/search-new-app.png)

5. 导航到下面在单独的浏览器中突出显示的**URL。** 

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. 在右上角，选择 **"登录>联机使用拨号板**。

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. 由于拨号板是 OpenIDConnect 应用，因此选择使用 Microsoft 工作帐户登录到拨号板。

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. 身份验证成功后，接受同意页的同意提示。 然后，应用程序将自动添加到您的租户，您将被重定向到您的拨号板帐户。

    ![拨号板添加 SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>将自动用户预配配置为拨号板

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 Dialpad 中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>要在 Azure AD 中配置拨号板的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**拨号板**。

    !["应用程序"列表中的拨号板链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://dialpad.com/scim`，在**租户 URL**中输入 。 输入您以前从"**秘密令牌**"中的 Dialpad 检索和保存的值。 单击 **"测试连接**"以确保 Azure AD 可以连接到拨号板。 如果连接失败，请确保您的 Dialpad 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射**"部分下，选择**将 Azure 活动目录用户同步到拨号板**。

    ![拨号板用户映射](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到拨号板的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Dialpad 中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![拨号板用户属性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为拨号板启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Dialpad 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并跟踪指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 Dialpad 上执行的所有操作。

有关如何读取 Azure AD 预配日志的详细信息，请参阅[报告自动用户帐户预配](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>连接器限制
* 拨号板今天不支持组重命名。 这意味着对 Azure AD 中组显示**名称**的任何更改将不会更新并在拨号板中反映出来。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
