---
title: 針對已啟用 Azure Arc 的伺服器 VM 擴充功能問題進行疑難排解
description: 本文說明如何針對已啟用 Azure Arc 的伺服器所引發的 Azure VM 擴充功能進行疑難排解並解決問題。
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: ffd7db5ff7da3d7f60762117f80d7b9b5af6f646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342670"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>針對已啟用 Arc 的伺服器 VM 擴充功能問題進行疑難排解

本文提供在啟用 Arc 的伺服器上部署或移除 Azure VM 擴充功能時，可能發生之問題的疑難排解和解決相關資訊。 如需一般資訊，請參閱 [管理和使用 AZURE VM 擴充](./manage-vm-extensions.md)功能。

## <a name="general-troubleshooting"></a>一般疑難排解

您可以從 Azure 入口網站抓取有關延伸模組部署狀態的資料。

下列疑難排解步驟適用於所有虛擬機器擴充功能。

1. 若要檢查來賓代理程式記錄檔，請查看您的延伸模組在中布建于 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` Windows 和下的 Linux 時的活動 `/var/lib/GuestConfig/ext_mgr_logs` 。

2. 如需 Windows 的詳細資訊，請參閱特定延伸模組的延伸模組記錄檔 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` 。 擴充功能輸出會記錄到安裝在 Linux 上的每個擴充功能的檔案 `/var/lib/GuestConfig/extension_logs` 。

3. 檢查延伸模組特定的檔疑難排解章節，瞭解錯誤碼、已知問題等。您可以在延伸模組總覽的 [ **疑難排解和支援** ] 區段中找到每個擴充功能的其他疑難排解資訊。 這包括寫入記錄檔的錯誤碼描述。 擴充功能文章會連結到延伸模組 [資料表](manage-vm-extensions.md#extensions)中。

4. 查看系統記錄。 檢查是否有其他作業對擴充功能造成干擾，例如，長時間執行且需要以獨佔方式存取套件管理員的其他應用程式安裝。

## <a name="troubleshooting-specific-extension-scenarios"></a>針對特定擴充案例進行疑難排解

### <a name="vm-insights"></a>VM Insights

- 針對已啟用 Azure Arc 的伺服器啟用 VM 深入解析時，會安裝相依性和 Log Analytics 代理程式。 在緩慢的電腦上或網路連線緩慢的電腦上，可能會在安裝過程中看到超時。 Microsoft 正在採取步驟，在連線的機器代理程式中解決此問題，以協助改善此情況。 在過渡期間，安裝的重試可能會成功。

### <a name="log-analytics-agent-for-linux"></a>Log Analytics Linux 代理程式

- Log Analytics 代理程式版本 1.13.9 (對應的延伸模組版本為 1.13.15) 未正確地將上傳的資料標示為 Azure Arc 啟用伺服器的資源識別碼。 雖然記錄會傳送至服務，但當您在選取 **記錄** 檔或 **見解**之後，嘗試從選取的啟用伺服器中查看資料時，不會傳回任何資料。 您可以從 Azure 監視器記錄或從適用於 VM 的 Azure 監視器（範圍設定為工作區）執行查詢，以查看其資料。

- 適用于 Linux 的 Log Analytics 代理程式目前不支援某些散發套件。 代理程式需要安裝其他相依性，包括 Python 2。 請參閱 [這裡](../../azure-monitor/platform/agents-overview.md#supported-operating-systems)的支援矩陣和必要條件。

- 狀態訊息中的錯誤碼52表示缺少相依性。 檢查輸出和記錄檔，以取得遺漏何種相依性的詳細資訊。

- 如果安裝失敗，請查看延伸模組總覽中的 [ **疑難排解和支援** ] 區段。 在大部分情況下，狀態訊息中會包含錯誤碼。 若為適用于 Linux 的 Log Analytics 代理程式，會 [在此說明狀態](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)消息，以及此 VM 擴充功能的一般疑難排解資訊。

## <a name="next-steps"></a>後續步驟

如果在這裡沒有看到問題或無法解決問題，請嘗試下列其中一個管道以取得其他支援：

- 透過 [Microsoft Q&A](/answers/topics/azure-arc.html)取得 Azure 專家的解答。

- 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會將 Azure 社群連線到解答、支援及專家。

- 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
