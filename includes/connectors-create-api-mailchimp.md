---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789638"
---
## <a name="prerequisites"></a>先决条件

* [邮件池](https://www.MailChimp.com/)帐户 

可以在逻辑应用中使用 MailChimp 帐户之前，必须先授权该逻辑应用可以连接到 MailChimp 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 MailChimp 帐户的步骤：

1. 要创建与 MailChimp 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”****，并在搜索框中输入“MailChimp”**。 选择要使用的触发器或操作：  
   ![MailChimp 步骤 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. 如果以前尚未创建任何与 MailChimp 的连接，系统会提示提供 MailChimp 凭据。 这些凭据将用于授权逻辑应用连接到 MailChimp 帐户，并访问该帐户中的数据：  
   ![MailChimp 步骤 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. 提供 MailChimp 用户名和密码，对逻辑应用进行授权：  
   ![MailChimp 步骤 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![MailChimp 步骤 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

