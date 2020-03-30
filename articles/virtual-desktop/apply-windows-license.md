---
title: 將 Windows 許可證應用於工作階段主機虛擬機器 - Azure
description: 介紹如何為 Windows 虛擬桌面 VM 應用 Windows 許可證。
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254231"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>將 Windows 許可證應用於工作階段主機虛擬機器

獲得正確授權以運行 Windows 虛擬桌面工作負荷的客戶有資格將 Windows 許可證應用於其工作階段主機虛擬機器，並在無需支付其他許可證的情況下運行它們。 有關詳細資訊，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>使用 Windows 虛擬桌面許可證的方法
Windows 虛擬桌面許可允許您將許可證應用於在主機池中註冊為工作階段主機並接收使用者連接的任何 Windows 或 Windows Server 虛擬機器。 此許可證不適用於作為檔共用伺服器、網域控制站等運行的虛擬機器。

有幾種方法可以使用 Windows 虛擬桌面許可證：
- 可以使用[Azure 應用商店產品](./create-host-pools-azure-marketplace.md)創建主機池及其工作階段主機虛擬機器。 以這種方式創建的虛擬機器會自動應用許可證。
- 您可以使用[GitHub Azure 資源管理器範本](./create-host-pools-arm-template.md)創建主機池及其工作階段主機虛擬機器。 以這種方式創建的虛擬機器會自動應用許可證。
- 您可以將許可證應用於現有工作階段主機虛擬機器。 為此，請先按照使用[PowerShell 創建主機池](./create-host-pools-powershell.md)的說明創建主機池和相關 VM，然後返回到本文瞭解如何應用許可證。

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>將 Windows 許可證應用於工作階段主機 VM
確定您已 [安裝並設定最新的 Azure PowerShell](/powershell/azure/overview)。 運行以下 PowerShell Cmdlet 以應用 Windows 許可證：

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>驗證工作階段主機 VM 是否利用許可優勢
部署 VM 後，運行此 Cmdlet ot 驗證許可證類型：
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

具有應用 Windows 許可證的工作階段主機 VM 將顯示如下所示的內容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

沒有應用 Windows 許可證的 VM 將顯示如下所示的內容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

運行以下 Cmdlet 以查看在 Azure 訂閱中應用 Windows 許可證的所有工作階段主機 VM 的清單：

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
