---
title: 容毀排除 Azure 同步工作室(預覽)連接 PowerShell
description: 使用 PowerShell 解決 Azure 同步工作室連線問題
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431471"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>診斷使用 PowerShell 文稿的 Azure 突觸工作室(預覽)連接問題

Azure Synapse Studio(預覽版)依賴於一組 Web API 終結點才能正常工作。 本指南將説明您確定連接問題的原因,當您:
- 配置本地網路(如公司防火牆後面的網路)以造訪 Azure 同步工作室。
- 使用 Azure 同步工作室遇到連接問題。

## <a name="prerequisite"></a>必要條件

* Windows 上的 PowerShell 5.0 或更高版本或
* Windows 或 Linux 上的 PowerShell 酷睿 6.0 或更高版本。

## <a name="troubleshooting-steps"></a>疑難排解步驟

右鍵按下以下連結,然後按下「將目標另存為」:

- [測試-Azure 合成器.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

或者,您可以直接打開連結,並儲存打開的文稿檔。 不要保存上面的連結的位址,因為它將來可能會更改。

在文件資源管理器中,右鍵單擊下載的腳本檔,然後單擊"使用 PowerShell 運行"。

![使用 PowerShell 執行下載的文稿檔案](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

出現提示後,輸入當前遇到問題或要測試連接的 Azure Synapse 工作區名稱,然後按 Enter。

![輸入工作區名稱](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

診斷會話將啟動。 等候它完成。

![等待診斷完成](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

最後,將顯示診斷摘要。 如果電腦無法連接到一個或多個終結點,它將在「摘要」部分中顯示一些建議。

![檢視診斷摘要](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

此外,此會話的診斷日誌檔將與故障排除腳本在同一檔夾中生成。 目前位置顯示在「一般提示」 部份 () 中`D:\TestAzureSynapse_2020....log`。 如有必要,您可以將此文件發送給技術支援人員。

如果您是網路管理員並調整 Azure Synapse Studio 的防火牆配置,則上面顯示的技術詳細資訊"摘要"部分可能會有所説明。

* 所有標有"已通過"的測試項(請求)都意味著它們已通過連接測試,而不考慮 HTTP 狀態代碼。
 對於失敗的請求,原因顯示為黃色,如`NamedResolutionFailure`或`ConnectFailure`。 這些原因可以説明您確定網路環境是否存在錯誤配置。


## <a name="next-steps"></a>後續步驟
如果前面的步驟無助於解決您發出"[創建支援票證](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)"。
