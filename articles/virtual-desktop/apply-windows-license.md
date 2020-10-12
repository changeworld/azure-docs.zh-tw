---
title: 將 Windows 授權套用至工作階段主機虛擬機器-Azure
description: 說明如何套用適用于 Windows 虛擬桌面 Vm 的 Windows 授權。
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010135"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>將 Windows 授權套用至工作階段主機虛擬機器

經適當授權可以執行 Windows 虛擬桌面工作負載的客戶，有資格將 Windows 授權套用至其工作階段主機虛擬機器，並在不需支付其他授權的情況下執行。 如需詳細資訊，請參閱 [Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>使用 Windows 虛擬桌面授權的方式
Windows 虛擬桌面授權可讓您將授權套用至任何已註冊為主機集區中之工作階段主機的 Windows 或 Windows Server 虛擬機器，並接收使用者連接。 此授權並不適用于以檔案共用伺服器、網域控制站等執行的虛擬機器。

有幾種方式可使用 Windows 虛擬桌面授權：
- 您可以使用 [Azure Marketplace](./create-host-pools-azure-marketplace.md)供應專案來建立主機集區及其工作階段主機虛擬機器。 以這種方式建立的虛擬機器會自動套用授權。
- 您可以使用 [GitHub Azure Resource Manager 範本](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)，建立主機集區及其工作階段主機虛擬機器。 以這種方式建立的虛擬機器會自動套用授權。
- 您可以將授權套用至現有的工作階段主機虛擬機器。 若要這樣做，請先遵循 [使用 PowerShell 建立主機集](./create-host-pools-powershell.md) 區中的指示，建立主機集區和相關聯的 vm，然後回到本文以瞭解如何套用授權。

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>將 Windows 授權套用至工作階段主機 VM
確定您已 [安裝並設定最新的 Azure PowerShell](/powershell/azure/)。 執行下列 PowerShell Cmdlet 以套用 Windows 授權：

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>確認您的工作階段主機 VM 正在利用授權權益
部署 VM 之後，請執行此 Cmdlet 來驗證授權類型：
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

具有已套用 Windows 授權的工作階段主機 VM 會顯示如下的內容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

沒有套用 Windows 授權的 Vm 將會顯示如下的內容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

執行下列 Cmdlet，以查看已在您的 Azure 訂用帳戶中套用 Windows 授權的所有工作階段主機 Vm 清單：

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
