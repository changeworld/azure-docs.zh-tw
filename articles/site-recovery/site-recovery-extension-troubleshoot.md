---
title: 使用 Azure Site Recovery 針對損毀修復的 Azure VM 擴充功能進行疑難排解
description: 針對使用 Azure Site Recovery 進行嚴重損壞修復的 Azure VM 延伸模組問題進行疑難排解。
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184613"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>對 Azure VM 延伸模組的問題進行疑難排解

本文提供疑難排解步驟，可協助您解決與 VM 代理程式和延伸模組相關的 Azure Site Recovery 錯誤。

## <a name="low-system-resources"></a>系統資源不足

當系統的可用記憶體不足，而且無法配置用於行動服務安裝的記憶體時，就會發生此問題。 確定已釋出足夠的記憶體，讓安裝順利完成。

## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 擴充功能超時  

錯誤訊息：「追蹤要啟動的延伸模組作業時，工作執行逾時」<br>
錯誤碼："151076"

 Azure Site Recovery 將擴充功能安裝在虛擬機器上，作為啟用保護工作的一部分。 下列任何一種情況可能會導致無法觸發保護，而導致作業失敗。 請完成下列疑難排解步驟，然後重試作業：

- [代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 擴充功能無法更新或載入](#the-site-recovery-extension-fails-to-update-or-load)

錯誤訊息：「先前的 Site Recovery 延伸模組作業所花費的時間超過預期」。<br>
錯誤碼："150066"

- [代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 擴充功能狀態不正確](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>因為 VM 代理程式沒有回應，而使得保護失敗

錯誤訊息：「追蹤要啟動的延伸模組作業時，工作執行逾時。」<br>
錯誤碼："151099"

如果虛擬機器中的 Azure 來賓代理程式未處於就緒狀態，則會發生此錯誤。

您可以在 [Azure 入口網站](https://portal.azure.com/)中檢查 Azure 來賓代理程式的狀態。 移至您嘗試保護的虛擬機器，並檢查**VM**  >  **設定**  >  **屬性**  >  **代理程式狀態**中的狀態。 在大部分的情況下，在重新開機虛擬機器之後，代理程式的狀態即已就緒。 但是，如果您無法重新開機或仍遇到此問題，請完成下列疑難排解步驟：

- [代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


錯誤訊息：「追蹤要啟動的延伸模組作業時，工作執行逾時。」<br>
錯誤碼："151095"

當 Linux 電腦上的代理程式版本已過期時，就會發生此錯誤。 完成下列疑難排解步驟：

- [VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>原因和解決方案

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)

#### <a name="solution"></a>解決方法
VM 代理程式可能已損毀，或服務可能已停止。 重新安裝 VM 代理程式有助於取得最新版本。 也有助於重新開始與服務通訊。

1. 判斷 Windows Azure 客體代理程式服務是否在 VM 服務 (services.msc) 中執行。 重新開機 Windows Azure 來賓代理程式服務。    
1. 如果 [服務] 中看不到 Windows Azure 來賓代理程式服務，請開啟主控台。 移至 [ **程式和功能** ] 以查看是否已安裝 Windows 來賓代理程式服務。
1. 如果 Windows Azure 客體代理程式顯示在 [程式和功能]**** 中，請將 Windows Azure 客體代理程式解除安裝。
1. 下載並安裝[最新版的代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要系統管理員許可權才能完成安裝。
1. 確認 Windows Azure 來賓代理程式服務出現在 [服務] 中。
1. 重新啟動保護作業。

此外，確認 VM 中[已安裝 Microsoft .NET 4.5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 您需要 .NET 4.5，VM 代理程式才能與服務通訊。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安裝的代理程式已過時 (適用於 Linux VM)

#### <a name="solution"></a>解決方法
針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響過時 VM 代理程式的問題所造成。 若要對此問題進行疑難排解，請遵循下列一般方針：

1. 請遵循[更新 Linux VM 代理程式](../virtual-machines/extensions/update-linux-agent.md)的指示。

   > [!NOTE]
   > 我們強烈建議** 您只透過散發套件存放庫更新代理程式。 我們不建議直接從 GitHub 下載代理程式程式碼並加以更新。 如果您散發的最新代理程式無法使用，請聯絡散發套件支援以取得如何安裝的相關指示。 若要檢查最新的代理程式，請移至 GitHub 儲存機制中的 [Microsoft Azure Linux 代理程式 (英文)](https://github.com/Azure/WALinuxAgent/releases) 頁面。

1. 執行下列命令，確定 Azure 代理程式正在 VM 上執行：`ps -e`

   如果此程序不在執行中，請使用下列命令來重新啟動它：

   - 針對 Ubuntu：`service walinuxagent start`
   - 針對其他散發套件︰`service waagent start`

1. [設定自動重新開機代理程式](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
1. 啟用虛擬機器的保護。

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 延伸模組無法更新或載入

延伸模組狀態會顯示為「空白」、「NotReady」或「轉換」。

#### <a name="solution"></a>解決方法

將延伸模組解除安裝，並再次重新啟動作業。

若要將解除安裝解除安裝：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至遇到備份失敗的 VM。
1. 選取 [設定]  。
1. 選取 [擴充功能]  。
1. 選取 [Site Recovery 延伸模組]****。
1. 選取 [解除安裝]。

針對 Linux VM，如果 VMSnapshot 延伸模組未顯示在 Azure 入口網站中，請 [更新 Azure Linux 代理程式](../virtual-machines/extensions/update-linux-agent.md)。 然後執行保護。

當您完成這些步驟時，會導致在保護期間重新安裝延伸模組。
