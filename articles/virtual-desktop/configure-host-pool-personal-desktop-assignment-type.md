---
title: Windows 虛擬桌面個人桌面分配類型 - Azure
description: 如何為 Windows 虛擬桌面個人桌面主機池配置分配類型。
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128280"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>配置個人桌面主機池分配類型

您可以配置個人桌面主機池的分配類型，以調整 Windows 虛擬桌面環境以更好地滿足您的需要。 在本主題中，我們將向您展示如何為使用者配置自動或直接分配。

>[!NOTE]
> 本文中的說明僅適用于個人桌面主機池，不適用於池主機池，因為池主機池中的使用者未分配給特定的工作階段主機。

## <a name="configure-automatic-assignment"></a>配置自動分配

自動分配是在 Windows 虛擬桌面環境中創建新的個人桌面主機池的預設分配類型。 自動分配使用者不需要特定的工作階段主機。

要自動分配使用者，請先將它們分配給個人桌面主機池，以便他們可以在源中看到桌面。 當分配的使用者在其源中啟動桌面時，如果尚未連接到主機池，他們將聲明可用的工作階段主機，主機池完成分配過程。

在開始之前，如果尚未[下載並導入 Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)。 

> [!NOTE]
> 在按照這些說明操作之前，請確保您已安裝 Windows 虛擬桌面 PowerShell 模組版本 1.0.1534.2001 或更高版本。

之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

要將主機池配置為自動將使用者分配給 VM，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

要將使用者分配給個人桌面主機池，請運行以下 PowerShell Cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>配置直接分配

與自動分配不同，使用直接分配時，必須將使用者分配給個人桌面主機池和特定工作階段主機，然後才能連接到其個人桌面。 如果使用者僅分配到沒有工作階段主機分配的主機池，則他們將無法訪問資源。

要將主機池配置為需要將使用者直接分配給工作階段主機，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

要將使用者分配給個人桌面主機池，請運行以下 PowerShell Cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

要將使用者分配給特定工作階段主機，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>後續步驟

現在配置了個人桌面分配類型，可以登錄到 Windows 虛擬桌面用戶端以作為使用者會話的一部分對其進行測試。 接下來的兩個"如何"將告訴您如何使用您選擇的用戶端連接到會話：

- [與 Windows 桌面用戶端連線](connect-windows-7-and-10.md)
- [與 Web 用戶端連線](connect-web.md)
