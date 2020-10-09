---
title: Azure 虛擬機器代理程式概觀
description: Azure 虛擬機器代理程式概觀
services: virtual-machines-windows
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 07/20/2019
ms.author: mimckitt
ms.openlocfilehash: 2db83b643ec3000c5b86388f4b603bba32f2a9a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855770"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 虛擬機器代理程式概觀
Microsoft Azure 虛擬機器代理程式 (VM 代理程式) 是一個安全的輕量型處理程序，可管理虛擬機器 (VM) 與 Azure 網狀架構控制器的互動。 VM 代理程式已啟用主要角色並執行 Azure 虛擬機器擴充功能。 VM 擴充功能可啟用 VM 的部署後組態，例如安裝和設定軟體。 VM 擴充功能也會啟用復原功能，例如重設 VM 的系統管理密碼。 若沒有 Azure VM 代理程式，便無法執行 VM 擴充功能。

本文詳述 Azure 虛擬機器代理程式的安裝和偵測。

## <a name="install-the-vm-agent"></a>安裝 VM 代理程式

### <a name="azure-marketplace-image"></a>Azure Marketplace 映像

根據預設，Azure VM 代理程式會安裝在從 Azure Marketplace 映像部署的任何 Windows VM 上。 從入口網站、PowerShell、命令列介面或 Azure Resource Manager 範本部署 Azure Marketplace 映像時，也會安裝 Azure VM 代理程式。

Windows 客體代理程式套件將分成兩個部分：

- 佈建代理程式 (PA)
- Windows 客體代理程式 (WinGA)

若要啟動 VM，您必須在 VM 上安裝 PA，但是不需要安裝 WinGA。 在 VM 部署時，您可以選擇不安裝 WinGA。 下列範例示範如何使用 Azure Resource Manager 範本選取 *provisionVmAgent* 選項：

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

如果您沒有安裝代理程式，就無法使用部分 Azure 服務，例如 Azure 備份或 Azure 安全性。 這些服務需要安裝擴充功能。 如果您已部署不含 WinGA 的 VM，之後就可以安裝最新版的代理程式。

### <a name="manual-installation"></a>手動安裝
Windows VM 代理程式可以使用 Windows Installer 套件來手動安裝。 當您建立部署至 Azure 的自訂 VM 映像時，可能需要手動安裝。 若要手動安裝 Windows VM 代理程式，[下載 VM 代理程式安裝程式](https://go.microsoft.com/fwlink/?LinkID=394789)。 Windows Server 2008 (64 位) 和更新版本支援 VM 代理程式。

> [!NOTE]
> 在未啟用 ProvisionVMAgent 的情況下，在從映射部署的 VM 上手動安裝 VMAgent 之後，請務必更新且 osprofile.allowextensionoperations 選項。

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>必要條件

- Windows VM 代理程式至少需要 Windows Server 2008 SP2 (64 位) ，才能執行 .NET Framework 4.0。 請參閱 [Azure 中虛擬機器代理程式的最低版本支援](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

- 確定您的 VM 可存取 IP 位址168.63.129.16。 如需詳細資訊，請參閱 [什麼是 IP 位址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

- 確定已在來賓 VM 內啟用 DHCP。 若要從 DHCP 取得主機或網狀架構位址，讓 IaaS VM 代理程式和擴充功能運作，必須這樣做。 如果您需要靜態私人 IP，您應該透過 Azure 入口網站或 PowerShell 進行設定，並確定已啟用 VM 內的 DHCP 選項。 [深入瞭解](https://docs.microsoft.com/azure/virtual-network/virtual-networks-static-private-ip-arm-ps#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) 如何使用 PowerShell 設定靜態 IP 位址。


## <a name="detect-the-vm-agent"></a>偵測 VM 代理程式

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell 模組可以用來擷取 Azure VM 的相關資訊。 若要查看 VM 的相關資訊，例如 Azure VM 代理程式的佈建狀態，請使用 [Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

下列壓縮的範例輸出顯示內有嵌套的 *ProvisionVMAgent* 屬性 `OSProfile` 。 請注意，這個屬性可用來判斷 VM 代理程式是否已部署至 VM：

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

下列指令碼可以用來傳回簡明的 VM 名稱清單和 VM 代理程式的狀態：

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手動偵測

登入 Windows VM 時，可以使用工作管理員來檢查執行中的程序。 若要檢查 Azure VM 代理程式，請開啟 [工作管理員]、按一下 [詳細資料]** 索引標籤，然後尋找程序名稱 **WindowsAzureGuestAgent.exe**。 此程序的目前狀態表示已安裝 VM 代理程式。


## <a name="upgrade-the-vm-agent"></a>升級 VM 代理程式
適用于 Windows 的 Azure VM 代理程式會在從 Azure Marketplace 部署的映射上自動升級。 當新的 VM 部署至 Azure 時，這些 VM 在 VM 佈建時，會收到最新的 VM 代理程式。 如果您已手動安裝代理程式或正在部署自訂 VM 映射，您必須在建立映射時手動更新以包含新的 VM 代理程式。

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows 來賓代理程式自動記錄收集
Windows 來賓代理程式有一項功能，可自動收集一些記錄。 這項功能是由 CollectGuestLogs.exe 進程的控制器。 PaaS 雲端服務和 IaaS 虛擬機器都存在，其目標是快速 & 自動從 VM 收集一些診斷記錄，讓它們可用於離線分析。 收集到的記錄包括事件記錄檔、OS 記錄檔、Azure 記錄檔和一些登錄機碼。 它會產生轉送至 VM 主機的 ZIP 檔案。 然後，工程小組和支援專業人員可以查看此 ZIP 檔案，以調查擁有 VM 之客戶的要求問題。

## <a name="guest-agent-and-osprofile-certificates"></a>來賓代理程式和 OSProfile 憑證
Azure VM 代理程式負責安裝 `OSProfile` VM 或虛擬機器擴展集內所參考的憑證。 如果您從來賓 VM 內的憑證 MMC 主控台手動移除這些憑證，來賓代理程式應該會將它們新增回來。
若要永久移除憑證，您必須將它從移除，然後 `OSProfile` 從客體作業系統中移除它。

若為虛擬機器，請使用 [AzVMSecret]() 移除中的憑證 `OSProfile` 。

如需虛擬機器擴展集憑證的詳細資訊，請參閱 [虛擬機器擴展集-如何? 移除已淘汰的憑證？](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-remove-deprecated-certificates)


## <a name="next-steps"></a>後續步驟
如需關於虛擬機器擴充功能的詳細資訊，請參閱 [Azure 虛擬機器擴充功能和功能概觀](overview.md)。
