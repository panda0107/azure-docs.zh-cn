---
title: Azure AD v2 Windows 桌面入门 - 配置
description: Windows 桌面 .NET (XAML) 应用程序如何获取访问令牌并调用 Azure Active Directory v2 终结点保护的 API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885763"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>向应用添加应用程序的注册信息
在此步骤中，需要将应用程序 ID 添加到项目。

1.  打开 `App.xaml.cs`，并将包含 `ClientId` 的行替换为：

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>后续操作

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
