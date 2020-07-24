---
title: 針對使用 PowerShell 的 Synapse Studio （預覽）連線進行疑難排解
description: 使用 PowerShell 為 Azure Synapse Studio 連線進行疑難排解
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f4afaf536a9c65758ad030e5cdeeee5fb97074d7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046671"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>診斷 PowerShell 腳本的 Azure Synapse Studio （預覽）連線問題

Azure Synapse Studio （預覽）相依于一組 Web API 端點，才能正常運作。 本指南將協助您找出連線問題的原因：
- 設定局域網路（例如公司防火牆後方的網路），以存取 Azure Synapse Studio。
- 使用 Azure Synapse Studio 遇到連線問題。

## <a name="prerequisite"></a>必要條件

* Windows 上的 PowerShell 5.0 或更高版本，或
* Windows 或 Linux 上的 PowerShell Core 6.0 或更高版本。

## <a name="troubleshooting-steps"></a>疑難排解步驟

以滑鼠右鍵按一下下列連結，然後按一下 [另存目標]：

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

或者，您可以直接開啟連結，然後儲存已開啟的腳本檔案。 請不要儲存上述連結的位址，因為未來可能會有所變更。

在 [檔案瀏覽器] 中，以滑鼠右鍵按一下下載的腳本檔案，然後按一下 [以 PowerShell 執行]。

![使用 PowerShell 執行下載的腳本檔案](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

出現提示時，請輸入目前遇到問題的 Azure Synapse 工作區名稱，或您想要測試連線能力，然後按 enter。

![輸入工作區名稱](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

診斷會話將會啟動。 等候它完成。

![等待診斷完成](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

最後會顯示診斷摘要。 如果您的電腦無法連接到一或多個端點，它會在 [摘要] 區段中顯示一些建議。

![審查診斷摘要](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

此外，此會話的診斷記錄檔將會在與疑難排解腳本相同的資料夾中產生。 其位置會顯示在「一般秘訣」區段（ `D:\TestAzureSynapse_2020....log` ）。 如有需要，您可以將此檔案傳送給技術支援人員。

如果您是網路系統管理員並調整 Azure Synapse Studio 的防火牆設定，則 [摘要] 區段上方顯示的技術詳細資料可能會有説明。

* 所有標示為「已通過」的測試專案（要求）表示它們已通過連線測試，不論 HTTP 狀態碼為何。
 對於失敗的要求，原因會以黃色顯示，例如 `NamedResolutionFailure` 或 `ConnectFailure` 。 這些原因可協助您找出網路環境是否有錯誤的錯誤。


## <a name="next-steps"></a>後續步驟
如果先前的步驟無法協助您解決問題，請[建立支援票證](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)。
