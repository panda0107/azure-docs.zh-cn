---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279403"
---
> [!WARNING]
> 为现有角色启用诊断时，将禁用部署包时已设置的所有扩展。 其中包括:
>
> * Microsoft 监视代理诊断
> * Microsoft Azure 安全监视
> * Microsoft 反恶意软件                 
> * Microsoft Monitoring Agent
> * Microsoft 服务探查器代理      
> * Microsoft Azure 域扩展        
> * Microsoft Azure 诊断扩展   
> * Microsoft Azure 远程桌面扩展
> * Microsoft Azure 日志收集器
>
> 部署更新的角色后，可以通过 Azure 门户或 PowerShell 重置扩展。
>
