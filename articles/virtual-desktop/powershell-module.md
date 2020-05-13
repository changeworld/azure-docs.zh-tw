---
title: PowerShell 模組 Windows 虛擬桌面-Azure
description: 如何安裝和設定 Windows 虛擬桌面的 PowerShell 模組。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6741c034351099f544c20749eb7c7a39e7932181
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195138"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>設定適用于 Windows 虛擬桌面的 PowerShell 模組

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虛擬桌面 PowerShell 模組已整合至 Azure PowerShell 模組。 本文將告訴您如何設定 PowerShell 模組，讓您可以針對 Windows 虛擬桌面執行 Cmdlet。

## <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境

若要開始使用模組，請先安裝[最新版的 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)。 Windows 虛擬桌面 Cmdlet 目前僅適用于 PowerShell Core。

接下來，您必須安裝 DesktopVirtualization 模組，以在您的 PowerShell 會話中使用。

在提高許可權的模式中執行下列 PowerShell Cmdlet，以安裝模組：

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> 如果此 Cmdlet 無法運作，請嘗試使用更高的許可權再次執行它。

接下來，執行下列 Cmdlet 以連接到 Azure：

```powershell
Connect-AzAccount
```

登入您的 Azure 帳戶時，需要在執行 Connect Cmdlet 時產生的程式碼。 若要登入，請移至 <https://microsoft.com/devicelogin> ，輸入程式碼，然後使用您的 Azure 系統管理員認證登入。

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

這會將您直接登入您的系統管理員認證的預設訂用帳戶。

## <a name="change-the-default-subscription"></a>變更預設訂用帳戶

如果您想要在登入之後變更預設訂用帳戶，請執行此 Cmdlet：

```powershell
Select-AzSubscription -SubscriptionName <preferredsubscriptionname>
```

當您選取要使用的新訂用帳戶時，您不需要在之後執行的 Cmdlet 中指定該訂用帳戶的識別碼。 例如，下列 Cmdlet 會在不需要訂用帳戶識別碼的情況下，抓取特定的工作階段主機：

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

您也可以藉由新增所需的訂用帳戶名稱做為參數，來變更每個 Cmdlet 的訂閱。 下一個 Cmdlet 與上一個範例相同，唯一的例外是訂用帳戶 ID 新增為參數，以變更 Cmdlet 所使用的訂用帳戶。

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>取得位置

Location 參數對於建立新物件的所有**AzWVD** Cmdlet 而言是必要的。

執行下列 Cmdlet 以取得您的訂用帳戶支援的位置清單：

```powershell
Get-AzLocation
```

**Get-azlocation**的輸出看起來會像這樣：

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

一旦您知道帳戶的位置之後，就可以在 Cmdlet 中使用它。 例如，以下的 Cmdlet 會在 "southeastasia" 位置建立主機集區：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>後續步驟

既然您已設定 PowerShell 模組，您就可以執行 Cmdlet 來進行 Windows 虛擬桌面中的各種動作。 以下是一些您可以使用模組的地方：

- 執行我們的[Windows 虛擬桌面教學]()課程，以設定您自己的 Windows 虛擬桌面環境。
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)
- [設定 Windows 虛擬桌面負載平衡方法](configure-host-pool-load-balancing.md)
- [設定個人桌面主機集區指派類型](configure-host-pool-personal-desktop-assignment-type.md)
- 還有更多項目！

如果您遇到任何問題，請參閱我們的[PowerShell 疑難排解文章](troubleshoot-powershell.md)以取得協助。

