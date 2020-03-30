---
title: Azure 自動化之圖形化 Runbook 中的錯誤處理
description: 本文說明如何在 Azure 自動化的圖形化 Runbook 中實作錯誤處理邏輯。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367068"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure 自動化之圖形化 Runbook 中的錯誤處理

Azure 自動化圖形運行簿需要考慮的一個關鍵設計原則是確定 Runbook 在執行過程中可能會遇到的問題。 這些問題包括成功、預期的錯誤狀態，以及非預期的錯誤情況。

通常，如果 Runbook 活動發生非終止錯誤，Windows PowerShell 會通過處理以下任何活動來處理活動，而不管錯誤如何。 此錯誤可能會產生例外狀況，但仍允許執行下一個活動。

圖形運行簿應包括錯誤處理代碼，以處理執行問題。 要驗證活動的輸出或處理錯誤，可以使用 PowerShell 代碼活動、在活動的輸出連結上定義條件邏輯或應用其他方法。

Azure 自動化的圖形化 Runbook 已納入錯誤處理功能。 您現在可以將例外狀況變成非終止錯誤，並建立活動之間的錯誤連結。 改進後的過程允許您的 Runbook 捕獲錯誤並管理已實現或意外的情況。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="powershell-error-types"></a>電源殼錯誤類型

運行簿執行期間可能發生的 PowerShell 錯誤類型包括終止錯誤和非終止錯誤。
 
### <a name="terminating-error"></a>終止錯誤

終止錯誤是執行期間完全停止命令或腳本執行的嚴重錯誤。 示例包括不存在的 Cmdlet、阻止 Cmdlet 運行的語法錯誤以及其他致命錯誤。

### <a name="non-terminating-error"></a>非終止錯誤

非終止錯誤是非嚴重錯誤，允許執行繼續，儘管錯誤條件。 示例包括操作錯誤，例如未發現檔錯誤和許可權問題。

## <a name="when-to-use-error-handling"></a>何時使用錯誤處理

當關鍵活動引發錯誤或異常時，在 Runbook 中使用錯誤處理。 請務必防止 Runbook 中的下一個活動處理並適當地處理錯誤。 當 Runbook 支援業務或服務操作流程時，處理該錯誤尤為重要。

對於每個可能產生錯誤的活動，可以添加指向任何其他活動的錯誤連結。 目標活動可以是任何類型的活動，包括代碼活動、Cmdlet 的調用、另一個 Runbook 的調用等。 目標活動還可以具有傳出連結，無論是常規連結還是錯誤連結。 這些連結允許 Runbook 實現複雜的錯誤處理邏輯，而無需訴諸代碼活動。

建議的做法是創建具有常見功能的專用錯誤處理 Runbook，但這種做法不是強制性的。 例如，考慮嘗試啟動虛擬機器並在其上安裝應用程式的 Runbook。 如果 VM 啟動不正確，它將：

1. 發送有關此問題的通知。
2. 啟動另一個運行簿，自動設定新的 VM。

一個解決方案是在 Runbook 中具有指向處理步驟 1 的活動的錯誤連結。 例如，runbook 可以將`Write-Warning`Cmdlet 連接到步驟 2 的活動，例如[啟動-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) Cmdlet。

還可以通過將這兩個活動放在單獨的錯誤處理 Runbook 中來概括此行為，以便在許多 Runbook 中使用。 在原始 Runbook 調用此錯誤處理 Runbook 之前，它可以從其資料構造自訂消息，然後將其作為參數傳遞給錯誤處理 Runbook。

## <a name="how-to-use-error-handling"></a>如何使用錯誤處理

Runbook 中的每個活動都有一個配置設置，可將異常轉換為非終止錯誤。 此設定預設為停用狀態。 我們建議在 Runbook 處理錯誤的任何活動上啟用此設置。 此設置可確保 Runbook 使用錯誤連結將活動中的終止錯誤和非終止錯誤作為非終止錯誤處理。  

啟用配置設置後，讓 Runbook 創建處理錯誤的活動。 如果活動產生任何錯誤，則遵循傳出錯誤連結。 即使活動也生成常規輸出，也不會遵循常規連結。<br><br> ![自動化 Runbook 的錯誤連結範例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在下面的示例中，Runbook 檢索包含 VM 電腦名稱稱的變數。 然後，它嘗試使用下一個活動啟動 VM。<br><br> ![自動化運行簿錯誤處理示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

活動和`Get-AutomationVariable`[啟動-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) Cmdlet 配置為將異常轉換為錯誤。 如果在獲取變數或啟動 VM 時出現問題，代碼將建置錯誤。<br><br> ![自動化運行簿錯誤處理活動設置](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)。

錯誤連結從這些活動流向單個`error management`代碼活動。 此活動配置了一個簡單的 PowerShell 運算式，`throw`該運算式使用 關鍵字停止處理，`$Error.Exception.Message`以及獲取描述當前異常的消息。<br><br> ![自動化運行簿錯誤處理代碼示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化 Runbook 中的連結和連結類型，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md#links-and-workflow)。

* 要瞭解有關 Runbook 執行、監視 Runbook 作業和其他技術詳細資訊的詳細資訊，請參閱[Azure 自動化 中的 Runbook 執行](automation-runbook-execution.md)。