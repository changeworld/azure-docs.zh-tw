---
title: Windows 虛擬桌面個人桌面指派類型-Azure
description: 如何設定 Windows 虛擬桌面個人桌面主機集區的指派類型。
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128280"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>設定個人桌面主機集區指派類型

您可以設定個人桌面主機集區的指派類型，以調整您的 Windows 虛擬桌面環境，使其更符合您的需求。 在本主題中，我們將示範如何為您的使用者設定自動或直接指派。

>[!NOTE]
> 本文中的指示僅適用于個人桌面主機集區，而非集區主機集區，因為集區主機集區中的使用者不會指派給特定的工作階段主機。

## <a name="configure-automatic-assignment"></a>設定自動指派

自動指派是在 Windows 虛擬桌面環境中建立之新個人桌面主機集區的預設指派類型。 自動指派使用者不需要特定的工作階段主機。

若要自動指派使用者，請先將它們指派給個人桌面主機集區，讓他們可以在其摘要中看見桌面。 當指派的使用者在其摘要中啟動桌面時，如果已連線到主機集區（完成指派程式），則會宣告可用的工作階段主機。

開始之前，請先[下載並匯入 Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)（如果尚未這麼做）。 

> [!NOTE]
> 遵循這些指示之前，請確定您已安裝 Windows 虛擬桌面 PowerShell 模組1.0.1534.2001 或更新版本。

之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

若要將主機集區設定為自動將使用者指派給 Vm，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

若要將使用者指派給個人桌面主機集區，請執行下列 PowerShell Cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>設定直接指派

與自動指派不同的是，當您使用直接指派時，必須將使用者指派給個人桌面主機集區和特定的工作階段主機，才能連線到其個人桌面。 如果使用者只指派給沒有工作階段主機指派的主機集區，他們將無法存取資源。

若要設定主機集區以要求將使用者直接指派給工作階段主機，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

若要將使用者指派給個人桌面主機集區，請執行下列 PowerShell Cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

若要將使用者指派給特定的工作階段主機，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>後續步驟

現在您已設定個人桌面指派類型，您可以登入 Windows 虛擬桌面用戶端，在使用者會話中進行測試。 接下來的兩個操作說明會告訴您如何使用您選擇的用戶端連接到會話：

- [與 Windows 桌面用戶端連接](connect-windows-7-and-10.md)
- [與 web 用戶端連接](connect-web.md)
