---
title: Windows 虚拟桌面主机池 Azure 资源管理器 - Azure
description: 如何使用 Azure 资源管理器模板在 Windows 虚拟桌面中创建主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292332"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建主机池

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

按照本节的说明，使用 Microsoft 提供的 Azure 资源管理器模板为 Windows 虚拟桌面租户创建主机池。 本文将介绍如何在 Windows 虚拟桌面中创建主机池、在 Azure 订阅中使用 VM 创建资源组、将这些 VM 加入 AD 域以及向 Windows 虚拟桌面注册 VM。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>运行 Azure 资源管理器模板所要做好的准备

在运行 Azure 资源管理器模板之前，请确保知道以下事项：

- 要使用的图像源位于何处。 它是从 Azure 库中更新还是自定义？
- 您的域加入凭据。
- 您的 Windows 虚拟桌面凭据。

使用 Azure 资源管理器模板创建 Windows 虚拟桌面主机池时，可以从 Azure 库、托管映像或非托管映像创建虚拟机。 要了解有关如何创建 VM 映像的信息，请参阅[准备 Windows VHD 或 VHDX 以上载到 Azure，](../virtual-machines/windows/prepare-for-upload-vhd-image.md)并在[Azure 中创建通用 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>运行 Azure 资源管理器模板以预配新的主机池

要开始，转到[此 GitHub URL。](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)

### <a name="deploy-the-template-to-azure"></a>将模板部署到 Azure

如果要在企业版订阅中部署，请向下滚动并选择"**部署到 Azure"，** 然后跳过前面的基于映像源填写参数。

如果要在云解决方案提供程序订阅中部署，请按照以下步骤部署到 Azure：

1. 向下滚动并右键单击“部署到 Azure”，然后选择“复制链接位置”。********
2. 打开一个文本编辑器（例如记事本）并在其中粘贴该链接。
3. 在""https://portal.azure.com/之后和井号标签 （#） 输入一个在符号 （+） 后跟租户域名之前。 下面是您应该使用的格式的示例： `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`。
4. 以对“云解决方案提供商”订阅拥有“管理员/参与者”权限的用户身份登录到 Azure 门户。
5. 将已复制到文本编辑器的链接粘贴到地址栏中。

有关应为方案输入哪些参数的指导，请参阅 Windows 虚拟桌面[Readme 文件](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)。 Readme 始终使用最新更改进行更新。

## <a name="assign-users-to-the-desktop-application-group"></a>将用户分配到桌面应用程序组

GitHub Azure 资源管理器模板完成后，在开始测试虚拟机上的完整会话桌面之前，分配用户访问权限。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。

要将用户分配到桌面应用程序组，请打开 PowerShell 窗口并运行此 cmdlet 以登录到 Windows 虚拟桌面环境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

之后，使用以下 cmdlet 将用户添加到桌面应用程序组：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

用户的 UPN 应与 Azure 活动目录中的用户标识匹配（例如， user1@contoso.com 若要添加多个用户，必须针对每个用户运行此 cmdlet。

完成这些步骤后，添加到桌面应用程序组的用户可以使用支持的远程桌面客户端登录到 Windows 虚拟桌面，并且可以看到会话桌面的资源。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../security-center/security-center-just-in-time.md)。