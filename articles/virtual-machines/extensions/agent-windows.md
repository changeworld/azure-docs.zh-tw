---
title: Azure 虛擬機器代理程式概觀
description: Azure 虛擬機器代理程式概觀
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: f29a20ddeb93ec3d4aa98bbcb36f50456b543667
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452565"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 虛擬機器代理程式概觀
Microsoft Azure 虛擬機器代理程式 (VM 代理程式) 是一個安全的輕量型處理程序，可管理虛擬機器 (VM) 與 Azure 網狀架構控制器的互動。 VM 代理程式已啟用主要角色並執行 Azure 虛擬機器擴充功能。 VM 擴充功能可啟用 VM 的部署後組態，例如安裝和設定軟體。 VM 擴充功能也會啟用復原功能，例如重設 VM 的系統管理密碼。 若沒有 Azure VM 代理程式，便無法執行 VM 擴充功能。

本文詳細介紹了 Azure 虛擬機代理的安裝和檢測。

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
Windows VM 代理程式可以使用 Windows Installer 套件來手動安裝。 當您建立部署至 Azure 的自訂 VM 映像時，可能需要手動安裝。 若要手動安裝 Windows VM 代理程式，[下載 VM 代理程式安裝程式](https://go.microsoft.com/fwlink/?LinkID=394789)。 Windows 伺服器 2008 R2 及更高版本支援 VM 代理。

> [!NOTE]
> 在未啟用預配 VMAgent 的情況下從映射部署的 VM 上手動安裝 VMAgent 後,更新「允許擴展操作」選項非常重要。

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Prerequisites
- Windows VM 代理至少需要運行 Windows 伺服器 2008 R2(64 位元),並且使用 .Net 框架 4.0。 請參考[Azure 中虛擬機器代理的最低版本支援](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- 確保您的 VM 有權訪問 IP 位址 168.63.129.16。 有關詳細資訊,請參閱[什麼是 IP 位址 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)。

## <a name="detect-the-vm-agent"></a>偵測 VM 代理程式

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell 模組可以用來擷取 Azure VM 的相關資訊。 若要查看 VM 的相關資訊，例如 Azure VM 代理程式的佈建狀態，請使用 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

下列緊縮的範例輸出顯示 *ProvisionVMAgent* 屬性會以巢狀方式置於 *OSProfile* 內。 請注意，這個屬性可用來判斷 VM 代理程式是否已部署至 VM：

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
適用於 Windows 的 Azure VM 代理程式會自動升級。 當新的 VM 部署至 Azure 時，這些 VM 在 VM 佈建時，會收到最新的 VM 代理程式。 自訂 VM 映像應進行手動更新，以便在映像建立時，納入新的 VM 代理程式。

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows 來賓代理自動紀錄集合
Windows 來賓代理具有自動收集某些日誌的功能。 此功能由 CollectGuestLogs.exe 進程進行控制器控制。 它同時存在於 PaaS 雲端服務和 IaaS 虛擬機器中,其目標是快速&自動從 VM 收集一些診斷日誌,以便它們可用於離線分析。 收集的日誌是事件日誌、操作系統日誌、Azure 日誌和某些註冊表項。 它生成傳輸到 VM 主機的 ZIP 檔。 然後,工程團隊和支援專業人員可以查看此 ZIP 檔,以便根據擁有 VM 的客戶的要求調查問題。

## <a name="next-steps"></a>後續步驟
如需關於虛擬機器擴充功能的詳細資訊，請參閱 [Azure 虛擬機器擴充功能和功能概觀](overview.md)。
