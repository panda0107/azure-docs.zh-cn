---
title: 教程：为使用 Azure 活动目录自动预配业务配置 Dropbox |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消将用户帐户预配到业务 Dropbox。
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058371"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>教程：为 Dropbox for Business 配置自动用户预配

本教程的目的是演示在"业务和 Azure 活动目录（Azure AD）Dropbox"中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户和/或组预配到业务放置框。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [商务租户的放置箱](https://www.dropbox.com/business/pricing)
* 具有管理员权限的"企业"Dropbox 中的用户帐户。

## <a name="add-dropbox-for-business-from-the-gallery"></a>从库中添加商务信箱

在使用 Azure AD 为自动用户预配配置业务 Dropbox 之前，需要将业务 Dropbox 从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加业务删除框，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在"**企业"中输入"企业"的"Dropbox"，** 在结果面板中选择 **"企业"的"Dropbox"，** 然后单击"**添加**"按钮以添加应用程序。

    ![结果列表中的 Dropbox for Business](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>将用户分配到 Dropbox for Business

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问业务 Dropbox。 决定后，您可以按照此处的说明将这些用户和/或组分配给 Dropbox 进行业务设置：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>将用户分配到 Dropbox for Business 的重要提示

* 建议将单个 Azure AD 用户分配给企业 Dropbox 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给"业务"Dropbox 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>为业务配置自动用户预配到 Dropbox 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在"企业 Dropbox"中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 您也可以根据["企业单一登录指南的 Dropbox"](dropboxforbusiness-tutorial.md)中提供的说明，选择启用基于 SAML 的单一登录业务。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>要为 Azure AD 中的业务设置拖放箱配置自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Dropbox for Business”****。

    ![应用程序列表中的“Dropbox for Business”链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在“管理员凭据”**** 部分下，单击“授权”****。 随即将在新的浏览器窗口中打开 Dropbox for Business 登录对话框。

    ![设置 ](common/provisioning-oauth.png)

6. 在 **"企业登录到 Dropbox"上，使用 Azure AD**对话框链接，登录到"企业租户的 Dropbox"并验证您的身份。

    ![商务登录的投递箱](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 完成步骤 5 和 6 后，单击 **"测试连接**"以确保 Azure AD 可以连接到业务投递箱。 如果连接失败，请确保您的"业务帐户 Dropbox"具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-oauth.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“保存”。****

10. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到放置框**。

    ![放置框用户映射](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. 查看**属性映射**部分中从 Azure AD 同步到 Dropbox 的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Dropbox 中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![放置框用户属性](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. 在 **"映射**"部分下，选择**将 Azure 活动目录组同步到放置框**。

    ![放置框组映射](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. 查看**属性映射**部分中从 Azure AD 同步到 Dropbox 的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Dropbox 中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![放置框组属性](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 要为 Dropbox 启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

16. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Dropbox 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

17. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 Dropbox 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制
 
* Dropbox 不支持暂停邀请的用户。 如果邀请的用户被挂起，该用户将被删除。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

