---
title: 處理 Azure 自動化圖形化 Runbook 中的錯誤
description: 本文說明如何在圖形化 Runbook 中實作錯誤處理邏輯。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 8af64f2189625bcff5271855d6c0102551d1a535
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185954"
---
# <a name="handle-errors-in-graphical-runbooks"></a>處理圖形化 Runbook 中的錯誤

針對 Azure 自動化圖形化 Runbook 要考慮的主要設計原則，是如何找出 Runbook 在執行期間可能會遭遇的問題。 這些問題包括成功、預期的錯誤狀態，以及非預期的錯誤情況。

通常，如果 Runbook 活動發生非終止錯誤，Windows PowerShell 往往不管此錯誤仍會處理其後的任何活動。 此錯誤可能會產生例外狀況，但仍允許執行下一個活動。

您的圖形化 Runbook 應該包含錯誤處理程式碼，以處理執行問題。 若要驗證活動的輸出或處理錯誤，您可以使用 PowerShell 程式碼活動、定義活動輸出連結的條件式邏輯，或套用其他方法。

Azure 自動化的圖形化 Runbook 已納入錯誤處理功能。 您現在可以將例外狀況變成非終止錯誤，並建立活動之間的錯誤連結。 此改善的流程可讓您的 Runbook 捕捉到錯誤，以及管理已實現或非預期的狀況。 

## <a name="powershell-error-types"></a>PowerShell 錯誤類型

Runbook 執行期間可能發生的 PowerShell 錯誤類型為終止錯誤或非終止錯誤。
 
### <a name="terminating-error"></a>終止錯誤

終止錯誤是執行期間所發生的嚴重錯誤，此錯誤會完全終止命令或指令碼執行。 範例包括不存在的 Cmdlet、防止 Cmdlet 執行的語法錯誤，以及其他嚴重錯誤。

### <a name="non-terminating-error"></a>非終止錯誤

非終止錯誤是非嚴重錯誤，其允許繼續執行，而不理會錯誤狀況。 範例包括作業錯誤，例如找不到錯誤和權限問題。

## <a name="when-to-use-error-handling"></a>何時使用錯誤處理

當重要活動擲回錯誤或例外狀況時，在您的 Runbook 中使用錯誤處理。 請務必防止 Runbook 中的下一個活動進行處理，並適當地處理錯誤。 當 Runbook 支援商務或服務營運流程時，處理錯誤格外重要。

## <a name="add-error-links"></a>新增錯誤連結

對於每個可產生錯誤的活動，您皆可新增指向任何其他活動的錯誤連結。 目的地活動可以是任何類型，包括程式碼活動、叫用 Cmdlet、叫用另一個 Runbook 等等。 目的地活動也可以有連出連結 (一般或錯誤連結)。 這些連結可讓 Runbook 實作複雜的錯誤處理邏輯，而不需訴諸程式碼活動。

建議作法是建立搭配常用功能的專用錯誤處理 Runbook，但這不是強制作法。 例如，請考慮嘗試啟動虛擬機器的 Runbook 並在其上安裝應用程式。 如果 VM 未正確啟動，其會執行下列動作：

1. 傳送關於這個問題的通知。
2. 改為啟動另一個會自動佈建新 VM 的 Runbook。

解決方案之一是讓 Runbook 中的錯誤連結指向可處理步驟 1 的活動。 例如，Runbook 可以將 `Write-Warning` Cmdlet 連線至步驟 2 的活動，例如 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) Cmdlet。

您也可以將這兩個活動放在不同的錯誤處理 Runbook 中，讓此行為廣泛用於許多 Runbook。 在呼叫此錯誤處理 Runbook 之前，您的原始 Runbook 可以從其資料建構自訂訊息，然後將此訊息做為參數傳遞至錯誤處理 Runbook。

## <a name="turn-exceptions-into-non-terminating-errors"></a>將例外狀況變成非終止錯誤

Runbook 中的每個活動均有組態設定可將例外狀況變成非終止錯誤。 此設定預設為停用狀態。 建議您在 Runbook 處理錯誤的任何活動上啟用此設定。 此設定可確保 Runbook 使用錯誤連結，同時將活動中的終止和非終止錯誤當作非終止錯誤處理。  

在啟用組態設定之後，請讓您的 Runbook 建立可處理錯誤的活動。 如果活動產生任何錯誤，隨後會出現傳出錯誤連結。 即使活動會產生一般輸出，隨後也不會出現一般連結。<br><br> ![自動化 Runbook 的錯誤連結範例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在下列範例中，Runbook 會擷取一個變數，其中包含 VM 的電腦名稱。 接著嘗試使用下一個活動啟動虛擬機器。<br><br> ![自動化 Runbook 的錯誤處理範例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` 活動和 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) Cmdlet 會設定為將例外狀況轉換為錯誤。 如果無法取得變數或啟動 VM，程式碼就會產生錯誤。<br><br> ![自動化 Runbook 的錯誤處理活動設定](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)。

錯誤連結會從這些活動流向單一 `error management` 程式碼活動。 此活動已設定了簡單的 PowerShell 運算式，其使用 `throw` 關鍵字來停止處理，以及使用 `$Error.Exception.Message` 來取得說明目前例外狀況的訊息。<br><br> ![自動化 Runbook 的錯誤處理程式碼範例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>後續步驟

* 如需解決圖形化 Runbook 錯誤的相關資訊，請參閱[針對 Runbook 問題進行疑難排解](troubleshoot/runbooks.md)。
