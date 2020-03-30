---
title: 使用 Azure 網站恢復對 Azure VM 擴展進行故障排除以進行災害復原
description: 使用 Azure 網站恢復解決 Azure VM 擴展以進行災害復原的問題。
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190732"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>解決 Azure VM 擴展問題

本文提供了故障排除步驟，可説明您解決與 VM 代理和擴展相關的 Azure 網站恢復錯誤。


## <a name="azure-site-recovery-extension-time-out"></a>Azure 網站恢復擴展超時  

錯誤訊息：「追蹤要啟動的延伸模組作業時，工作執行逾時」<br>
錯誤碼："151076"

 Azure 網站恢復在虛擬機器上安裝擴展，作為啟用保護作業的一部分。 以下任何條件都可能阻止觸發保護，並導致作業失敗。 請完成下列疑難排解步驟，然後重試作業：

- [代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 擴充功能無法更新或載入](#the-site-recovery-extension-fails-to-update-or-load)

錯誤訊息："以前的網站恢復擴展操作佔用的時間比預期的要多。<br>
錯誤碼："150066"

- [代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 擴充功能狀態不正確](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>因為 VM 代理程式沒有回應，而使得保護失敗

錯誤訊息：「追蹤要啟動的延伸模組作業時，工作執行逾時。」<br>
錯誤碼："151099"

如果虛擬機器中的 Azure 來賓代理未處於就緒狀態，則可能發生此錯誤。

您可以在[Azure 門戶](https://portal.azure.com/)中檢查 Azure 來賓代理的狀態。 轉到您嘗試保護的虛擬機器，並在**VM** > **設置** > **屬性** > **代理狀態**中檢查狀態。 大多數時候，在重新開機虛擬機器後，代理的狀態已準備就緒。 但是，如果您無法重新開機或仍然面臨此問題，請完成以下故障排除步驟：

- [代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


錯誤訊息：「追蹤要啟動的延伸模組作業時，工作執行逾時。」<br>
錯誤碼："151095"

當 Linux 電腦上的代理版本過期時，將發生此錯誤。 完成以下故障排除步驟：

- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>原因和解決方案

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)

#### <a name="solution"></a>解決方法
VM 代理程式可能已損毀，或服務可能已停止。 重新安裝 VM 代理程式有助於取得最新版本。 也有助於重新開始與服務通訊。

1. 判斷 Windows Azure 客體代理程式服務是否在 VM 服務 (services.msc) 中執行。 重新開機 Windows Azure 來賓代理服務。    
1. 如果 Windows Azure 來賓代理服務在服務中不可見，則打開控制台。 轉到**程式和功能**以查看是否安裝了 Windows 來賓代理服務。
1. 如果 Windows Azure 客體代理程式顯示在 [程式和功能]**** 中，請將 Windows Azure 客體代理程式解除安裝。
1. 下載並安裝[最新版的代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 完成安裝需要管理員許可權。
1. 驗證 Windows Azure 來賓代理服務是否顯示在服務中。
1. 重新啟動保護作業。

此外，確認 VM 中[已安裝 Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 需要 .NET 4.5 使 VM 代理與服務通信。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安裝的代理程式已過時 (適用於 Linux VM)

#### <a name="solution"></a>解決方法
針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響過時 VM 代理程式的問題所造成。 若要對此問題進行疑難排解，請遵循下列一般方針：

1. 請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md)的指示。

   > [!NOTE]
   > 我們強烈建議** 您只透過散發套件存放庫更新代理程式。 我們不建議直接從 GitHub 下載代理代碼並更新它。 如果分發的最新代理不可用，請與分發支援部門聯繫，瞭解如何安裝它。 若要檢查最新的代理程式，請移至 GitHub 儲存機制中的 [Microsoft Azure Linux 代理程式 (英文)](https://github.com/Azure/WALinuxAgent/releases) 頁面。

1. 執行下列命令，確定 Azure 代理程式正在 VM 上執行：`ps -e`

   如果此程序不在執行中，請使用下列命令來重新啟動它：

   - 針對 Ubuntu：`service walinuxagent start`
   - 針對其他散發套件︰`service waagent start`

1. [配置自動重新開機代理](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
1. 啟用虛擬機器的保護。

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 延伸模組無法更新或載入

擴展狀態將顯示為"空"、"未就緒"或"轉換"。

#### <a name="solution"></a>解決方法

將延伸模組解除安裝，並再次重新啟動作業。

若要將解除安裝解除安裝：

1. 在[Azure 門戶](https://portal.azure.com/)中，轉到遇到備份失敗的 VM。
1. 選取 [設定]****。
1. 選擇**擴展**。
1. 選取 [Site Recovery 延伸模組]****。
1. 選取 [解除安裝]****。

對於 Linux VM，如果 VMSnapshot 擴展不在 Azure 門戶中顯示，請[更新 Azure Linux 代理](../virtual-machines/linux/update-agent.md)。 然後運行保護。

完成這些步驟後，它會導致在保護期間重新安裝擴展。
