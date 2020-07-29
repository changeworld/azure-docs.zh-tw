---
title: PowerShell 模組 Windows 虛擬桌面 - Azure
description: 如何安裝及設定適用於 Windows 虛擬桌面的 PowerShell 模組。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0d5193cbaf759e513eb8d6e88e70abc74a2dd30d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291401"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>設定適用於 Windows 虛擬桌面的 PowerShell 模組

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager 整合的 Windows 虛擬桌面。

Windows 虛擬桌面 PowerShell 模組已整合至 Azure PowerShell 模組。 此文章將會說明如何設定 PowerShell 模組，使您可以針對 Windows 虛擬桌面執行 Cmdlet。

## <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境

若要開始使用模組，請先安裝[最新版本的 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)。 Windows 虛擬桌面 Cmdlet 目前只能搭配 PowerShell Core 運作。

接下來，您將必須安裝 DesktopVirtualization 模組以用於您的 PowerShell 工作階段。

在提升權限的模式中執行下列 PowerShell Cmdlet 來安裝該模組：

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> 如果此 Cmdlet 無法運作，請嘗試以提升的權限再次加以執行。

接下來，使用下列 Cmdlet 來連線至 Azure：

```powershell
Connect-AzAccount
```

登入 Azure 帳戶會需要您在執行 Connect Cmdlet 時所產生的驗證碼。 若要登入，請移至 <https://microsoft.com/devicelogin>、輸入驗證碼，然後使用您的 Azure 系統管理員認證登入。

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

這會直接將您登入到您系統管理員認證的預設訂用帳戶。

## <a name="change-the-default-subscription"></a>變更預設訂用帳戶

如果您想要變更登入後的預設訂用帳戶，請執行此 Cmdlet：

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

您也可以使用 Out-GridView Cmdlet 從清單中選取一個：

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

當您選取要使用的新訂用帳戶時，您在後續執行的 Cmdlet 中並不需要指定該訂用帳戶的識別碼。 例如，下列 Cmdlet 可在無需訂用帳戶識別碼的情況下，擷取特定的工作階段主機：

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

您也可以針對每個 Cmdlet 變更訂用帳戶，方法是將所需的訂用帳戶名稱新增為參數。 下一個 Cmdlet 與上一個範例相同，其中唯一的差別是將訂用帳戶識別碼新增為參數，以變更該 Cmdlet 使用的訂用帳戶。

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>取得位置

位置參數對於能建立新物件的所有 **New-AzWVD** Cmdlet 來說都是必要的。

執行下列 Cmdlet 來取得您訂用帳戶支援的位置清單：

```powershell
Get-AzLocation
```

**Get-AzLocation** 的輸出看起來會像這樣：

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

在您知道自己帳戶的位置之後，便可以將其用於 Cmdlet 中。 例如，以下是會在 "southeastasia" 位置建立主機集區的 Cmdlet：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>後續步驟

既然您已經設定 PowerShell 模組，現在您可以執行 Cmdlet 來在 Windows 虛擬桌面中執行各種動作。 以下是您可以使用模組的一些位置：

- 完成我們的 [Windows 虛擬桌面教學課程]()以設定您自己的 Windows 虛擬桌面環境。
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)
- [設定 Windows 虛擬桌面負載平衡方法](configure-host-pool-load-balancing.md)
- [設定個人桌面主機集區指派類型](configure-host-pool-personal-desktop-assignment-type.md)
- 不勝枚舉！

如果您遇到任何問題，請參閱我們的 [PowerShell 疑難排解文章](troubleshoot-powershell.md)以取得協助。

