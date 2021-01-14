---
title: 在 Windows VM 中重設遠端桌面服務或其系統管理員密碼 | Microsoft Docs
description: 了解如何使用 Azure 入口網站或 Azure PowerShell，來重設 Windows VM 上的帳戶密碼或遠端桌面服務。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 2f242de3bfa6a2bc95dc46b4ca5c10222b755a2f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200918"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>在 Windows VM 中重設遠端桌面服務或其系統管理員密碼
如果您無法連線到 Windows 虛擬機器 (VM)，您可以重設本機系統管理員密碼，或重設遠端桌面服務設定 (Windows 網域控制站上不支援)。 若要重設密碼，請使用 Azure 入口網站或 Azure PowerShell 中的 VM 存取延伸模組。 當您登入 VM 之後，請重設該本機系統管理員的密碼。  
如果您使用的是 PowerShell，確定您已[安裝並設定最新的 PowerShell 模組](/powershell/azure/)，而且已登入您的 Azure 訂用帳戶。 您也可以 [針對以傳統部署模型建立的 vm 執行這些步驟](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp)。

您可以透過下列方式來重設遠端桌面服務和認證：

- [使用 Azure 入口網站重設](#reset-by-using-the-azure-portal)

- [使用 VMAccess 延伸模組和 PowerShell 重設](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>使用 Azure 入口網站重設

請先登入 [Azure 入口網站](https://portal.azure.com)，然後選取左側功能表上的 [虛擬機器]。 

### <a name="reset-the-local-administrator-account-password"></a>**重設本機系統管理員帳戶密碼**

1. 選取您的 Windows VM，然後選取 [支援與疑難排解] 下方的 [重設密碼]。 [重設密碼] 視窗隨即顯示。

2. 選取 [重設密碼]、輸入使用者名稱和密碼，然後選取 [更新]。 

3. 嘗試再次連接到您的 VM。

### <a name="reset-the-remote-desktop-services-configuration"></a>**重設遠端桌面服務設定**

此程式會在 VM 中啟用遠端桌面服務，並建立預設 RDP 埠3389的防火牆規則。

1. 選取您的 Windows VM，然後選取 [支援與疑難排解] 下方的 [重設密碼]。 [重設密碼] 視窗隨即顯示。 

2. 選取 [僅重設設定]，然後選取 [更新]。 

3. 嘗試再次連接到您的 VM。

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>使用 VMAccess 延伸模組和 PowerShell 重設

首先，請確定您已[安裝並設定最新的 PowerShell 模組](/powershell/azure/)，並且已使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) \(英文\) Cmdlet 登入您的 Azure 訂用帳戶。

### <a name="reset-the-local-administrator-account-password"></a>**重設本機系統管理員帳戶密碼**

- 請使用 [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) PowerShell Cmdlet，來重設系統管理員密碼或使用者名稱。 `typeHandlerVersion` 設定必須是 2.0 或更新版本，因為第 1 版已淘汰。 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > 如果您輸入的名稱與 VM 上目前的本機系統管理員帳戶不同，VMAccess 延伸模組將會以該名稱新增一個本機系統管理員帳戶，然後將您指定的密碼指派給該帳戶。 如果您 VM 上的本機系統管理員帳戶存在，VMAccess 延伸模組將會重設密碼。 如果帳戶已停用，則 VMAccess 延伸模組將會啟用它。

### <a name="reset-the-remote-desktop-services-configuration"></a>**重設遠端桌面服務設定**

1. 使用 [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) PowerShell Cmdlet，重設對您 VM 的遠端存取。 下列範例會在資源群組 `myResourceGroup` 中的 VM `myVM` 上重設存取擴充功能 `myVMAccess`：

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > 不論如何，一部 VM 只能有一個 VM 存取代理程式。 若要設定 VM 存取代理程式屬性，請使用 `-ForceRerun` 選項。 當您使用 `-ForceRerun` 時，確定您會針對 VM存取代理程式，使用您可能已經在任何前述命令中使用的相同名稱。

1. 如果您仍然無法從遠端連線到虛擬機器，請參閱[針對以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線進行疑難排解](troubleshoot-rdp-connection.md)。 如果您與 Windows 網域控制站的連線中斷，您將必須從網域控制站備份復原連線。

## <a name="next-steps"></a>後續步驟

- 如果 Azure VM 存取延伸模組沒有回應，而且您無法重設密碼，則可[離線重設本機 Windows 密碼](reset-local-password-without-agent.md)。 此為更進階的方法，會要求您將有問題之 VM 中的虛擬硬碟連線至另一個 VM。 請先依照本文中說明的步驟進行，只有在這些步驟不可行時才嘗試離線密碼重設方法。

- [瞭解 AZURE VM 擴充功能和功能](../extensions/features-windows.md)。

- [使用 RDP 或 SSH 連接到 Azure 虛擬機器](/previous-versions/azure/dn535788(v=azure.100))。

- [針對以 Windows 為基礎的 Azure 虛擬機器的遠端桌面連線進行疑難排解](troubleshoot-rdp-connection.md)。