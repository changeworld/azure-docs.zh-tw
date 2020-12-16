---
title: 內建角色 Windows 虛擬桌面-Azure
description: 概述適用于 Windows 虛擬桌面的內建角色，適用于 Azure RBAC。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 52f0151fd4be7505cf7beea0eeb54f8e34404997
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577646"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>適用于 Windows 虛擬桌面的內建角色

Windows 虛擬桌面使用 Azure 角色型存取控制 (RBAC) 將角色指派給使用者和系統管理員。 這些角色會授與系統管理員執行特定工作的許可權。 若要深入瞭解 Azure RBAC 的內建角色，請參閱 [azure 內建角色](../role-based-access-control/built-in-roles.md)。

適用于 Azure 的標準內建角色為擁有者、參與者和讀者。 不過，Windows 虛擬桌面具有其他角色，可讓您區隔主機集區、應用程式群組和工作區的管理角色。 這種分隔可讓您更精細地控制系統管理工作。 這些角色的命名方式符合 Azure 的標準角色和最低許可權的方法。

Windows 虛擬桌面沒有特定的擁有者角色。 不過，您可以針對服務物件使用標準擁有者角色。

## <a name="desktop-virtualization-contributor"></a>桌面虛擬化參與者

桌面虛擬化參與者角色可讓您管理部署的所有層面。 不過，它不會授與您計算資源的存取權。 您也需要「使用者存取系統管理員」角色，才能將應用程式群組發佈給使用者或使用者群組。


- DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>桌上型電腦虛擬化讀者

桌上型電腦虛擬化讀者角色可讓您查看部署中的所有專案，但不會讓您進行任何變更。

- DesktopVirtualization/ \* /read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>主機集區參與者

「主機集區參與者」角色可讓您管理主機集區的所有層面，包括對資源的存取。 您將需要額外的「參與者」角色（「虛擬機器參與者」）來建立虛擬機器。 您將需要 AppGroup 和工作區參與者角色，才能使用入口網站建立主機集區，也可以使用桌面虛擬化參與者角色。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>主機集區讀取器

「主機集區讀者」角色可讓您查看主機集區中的所有專案，但不允許您進行任何變更。

- DesktopVirtualization/hostpools/ \* /read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>應用程式群組參與者

應用程式群組參與者角色可讓您管理應用程式群組的所有層面。 如果您想要將應用程式群組發佈給使用者或使用者群組，您需要「使用者存取系統管理員」角色。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/applicationgroups/\*
- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>應用程式群組讀者

應用程式群組讀者角色可讓您查看應用程式群組中的所有專案，而不會讓您進行任何變更。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/applicationgroups/ \* /read
- DesktopVirtualization/applicationgroups/read
- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>工作區參與者

工作區參與者角色可讓您管理工作區的所有層面。 若要取得已新增至應用程式群組之應用程式的相關資訊，您也必須將應用程式群組讀者角色指派給您。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/workspace/\*
- DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>工作區讀者

工作區讀者角色可讓您查看工作區中的所有專案，但不允許您進行任何變更。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/workspace/read
- DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>使用者會話操作員

「使用者會話操作員」角色可讓您傳送訊息、中斷連線會話，並使用「登出」功能來將會話從工作階段主機登出。 不過，此角色不會讓您執行工作階段主機管理，例如移除工作階段主機、變更清空模式等等。 此角色可以看到指派，但無法修改系統管理員。 建議您將此角色指派給特定的主機集區。 如果您在資源群組層級授與此許可權，系統管理員將擁有資源群組下所有主機集區的讀取權限。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/read
- DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>工作階段主機操作員

「工作階段主機參與者」角色可讓您查看和移除工作階段主機，以及變更清空模式。 他們無法使用 Azure 入口網站新增工作階段主機，因為它們沒有主機集區物件的寫入權限。 如果註冊權杖有效 (產生且未過期) ，您可以使用此角色，將工作階段主機新增至 Azure 入口網站外部的主機集區（如果系統管理員具有透過「虛擬機器參與者」角色的計算許可權）。

下列清單描述此角色可存取的許可權：

- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
