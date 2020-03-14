---
title: Azure 自動化之圖形化 Runbook 中的錯誤處理
description: 本文說明如何在 Azure 自動化的圖形化 Runbook 中實作錯誤處理邏輯。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367068"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure 自動化之圖形化 Runbook 中的錯誤處理

針對您的 Azure 自動化圖形化 runbook，要考慮的主要設計原則，是 runbook 在執行期間可能會遇到的問題的識別。 這些問題包括成功、預期的錯誤狀態，以及非預期的錯誤情況。

通常，如果 runbook 活動發生非終止錯誤，Windows PowerShell 會處理後續的任何活動來處理活動，而不論錯誤為何。 此錯誤可能會產生例外狀況，但仍允許執行下一個活動。

您的圖形化 runbook 應該包含錯誤處理常式代碼，以處理執行問題。 若要驗證活動的輸出或處理錯誤，您可以使用 PowerShell 程式碼活動、在活動的輸出連結上定義條件式邏輯，或套用其他方法。

Azure 自動化的圖形化 Runbook 已納入錯誤處理功能。 您現在可以將例外狀況變成非終止錯誤，並建立活動之間的錯誤連結。 改良的程式可讓您的 runbook 攔截錯誤，並管理已實現或非預期的狀況。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="powershell-error-types"></a>PowerShell 錯誤類型

Runbook 執行期間可能發生的 PowerShell 錯誤類型為終止錯誤和非終止錯誤。
 
### <a name="terminating-error"></a>終止錯誤

終止錯誤是執行期間嚴重的錯誤，會使命令或腳本完全停止執行。 範例包括不存在的 Cmdlet、防止 Cmdlet 執行的語法錯誤，以及其他嚴重錯誤。

### <a name="non-terminating-error"></a>非終止錯誤

非終止錯誤是一個不嚴重的錯誤，允許繼續執行，而不管錯誤狀況。 範例包括操作錯誤，例如找不到檔案錯誤和許可權問題。

## <a name="when-to-use-error-handling"></a>何時使用錯誤處理

當重要活動擲回錯誤或例外狀況時，在您的 runbook 中使用錯誤處理。 請務必避免 runbook 中的下一個活動進行處理，並適當地處理錯誤。 當您的 runbook 支援商務或服務作業程式時，處理錯誤就特別重要。

針對每個可能產生錯誤的活動，您可以加入指向任何其他活動的錯誤連結。 目的地活動可以是任何類型，包括程式碼活動、Cmdlet 的叫用、另一個 runbook 的調用等等。 目的地活動也可以有連出連結，也就是一般或錯誤連結。 這些連結可讓 runbook 執行複雜的錯誤處理邏輯，而不需要使用程式碼活動。

建議的作法是建立具有一般功能的專用錯誤處理 runbook，但這不是必要的作法。 例如，請考慮嘗試啟動虛擬機器並在其上安裝應用程式的 runbook。 如果 VM 未正確啟動，它會：

1. 傳送有關此問題的通知。
2. 啟動另一個 runbook，它會自動布建新的 VM。

其中一個解決方案是將 runbook 中的錯誤連結指向處理第一個步驟的活動。 例如，runbook 可以將 `Write-Warning` Cmdlet 連線到步驟2的活動，例如[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)指令程式。

您也可以將這兩個活動放在不同的錯誤處理 runbook 中，將此行為一般化以用於許多 runbook。 在原始 runbook 呼叫此錯誤處理 runbook 之前，它可以從其資料中建立自訂訊息，然後將它當做參數傳遞至錯誤處理 runbook。

## <a name="how-to-use-error-handling"></a>如何使用錯誤處理

Runbook 中的每個活動都有一項設定，可將例外狀況變成非終止錯誤。 此設定預設為停用狀態。 建議您在 runbook 處理錯誤的任何活動上啟用此設定。 此設定可確保 runbook 會使用錯誤連結，將活動中的終止和非終止錯誤處理為非終止錯誤。  

啟用設定之後，請讓您的 runbook 建立可處理錯誤的活動。 如果活動產生任何錯誤，則會遵循傳出錯誤連結。 即使活動也會產生一般的輸出，也不會遵循一般的連結。<br><br> ![自動化 Runbook 的錯誤連結範例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在下列範例中，runbook 會抓取包含 VM 電腦名稱稱的變數。 然後，它會嘗試使用下一個活動來啟動 VM。<br><br> ![自動化 runbook 錯誤處理範例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` 活動和[update-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) Cmdlet 已設定為將例外狀況轉換為錯誤。 如果取得變數或啟動 VM 時發生問題，則程式碼會產生錯誤。<br><br> ![自動化 runbook 錯誤處理活動設定](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)。

錯誤連結會從這些活動流向單一 `error management` 程式碼活動。 此活動設定了簡單的 PowerShell 運算式，它會使用 `throw` 關鍵字來停止處理，並使用 `$Error.Exception.Message` 來取得描述目前例外狀況的訊息。<br><br> ![自動化 runbook 錯誤處理常式代碼範例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化 Runbook 中的連結和連結類型，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md#links-and-workflow)。

* 若要深入瞭解 runbook 執行、監視 runbook 作業和其他技術詳細資料，請參閱[Azure 自動化中的 runbook 執行](automation-runbook-execution.md)。