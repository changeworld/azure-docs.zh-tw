---
title: Windows 虛擬桌面 (傳統) 個人桌面指派類型-Azure
description: 如何設定 Windows 虛擬桌面 (傳統) 個人桌面主機集區的指派類型。
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669010d2de90498c98fc685fe931b084a11cd104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008503"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>設定 Windows 虛擬桌面 (傳統) 的個人桌面主機集區指派類型

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../configure-host-pool-personal-desktop-assignment-type.md)。

您可以設定個人桌面主機集區的指派類型來調整您的 Windows 虛擬桌面環境，使其更符合您的需求。 在本主題中，我們將示範如何為您的使用者設定自動或直接指派。

>[!NOTE]
> 本文中的指示僅適用於個人桌面主機集區，而非集區式主機集區，因為集區式主機集區中的使用者不會指派給特定的工作階段主機。

## <a name="configure-automatic-assignment"></a>設定自動指派

自動指派是在 Windows 虛擬桌面環境中建立的新個人桌面主機集區，其中的預設指派類型。 自動指派使用者不需要特定的工作階段主機。

若要自動指派使用者，請先將其指派給個人桌面主機集區，使其可以在自己的摘要中看見桌面。 當指派的使用者在自己的摘要中啟動桌面時，如果他們尚未連線到完成指派程式的主機集區，則將宣告可用的工作階段主機。

開始之前，請先[下載並匯入 Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/) (如果您還沒這麼做的話)。

> [!NOTE]
> 遵循這些指示之前，請確定您已安裝 Windows 虛擬桌面 PowerShell 模組 1.0.1534.2001 或更新版本。

之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

若要將主機集區設定為自動將使用者指派給 VM，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

若要將使用者指派給個人桌面主機集區，請執行下列 PowerShell Cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>設定直接指派

不同於自動指派，當您使用直接指派時，必須將使用者指派給個人桌面主機集區與特定的工作階段主機，才能連線到他們的個人桌面。 如果使用者只指派給沒有工作階段主機指派的主機集區，則使用者將無法存取資源。

若要設定主機集區來要求將使用者直接指派給工作階段主機，請執行下列 PowerShell Cmdlet：

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

## <a name="remove-a-user-assignment"></a>移除使用者指派

建議您移除使用者指派，因為使用者不再需要個人桌面、或使用者已離開公司，或者您想要為其他人重複使用桌面。

目前，您可以移除個人桌面使用者指派的唯一方式，就是完全移除工作階段主機。 若要移除工作階段主機，請執行此 Cmdlet：

```powershell
Remove-RdsSessionHost
```

如果您需要將工作階段主機新增回個人桌面主機集區，請卸載該電腦上的 Windows 虛擬桌面，然後依照[使用 PowerShell 建立主機集區](create-host-pools-powershell-2019.md) (機器翻譯) 重新註冊工作階段主機中的步驟進行。

## <a name="next-steps"></a>後續步驟

現在您已完成設定個人桌面指派類型，您可以登入 Windows 虛擬桌面用戶端，在使用者工作階段中進行測試。 接下來的兩個操作說明將會說明如何使用您所選擇的用戶端，連線至工作階段：

- [與 Windows 桌面用戶端連線](connect-windows-7-10-2019.md)
- [與 Web 用戶端連線](connect-web-2019.md)
