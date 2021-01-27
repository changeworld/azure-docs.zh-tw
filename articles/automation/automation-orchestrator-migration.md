---
title: 從 Orchestrator 移轉到 Azure 自動化 (Beta)
description: 本文描述如何將 Runbook 和整合套件從 Orchestrator 移轉到 Azure 自動化。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896930"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>從 Orchestrator 移轉到 Azure 自動化 (Beta)

[System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) 中的 Runbook 是根據來自專為 Orchestrator 編寫的整合套件的活動，而 Azure 自動化中的 Runbook 則是根據 Windows PowerShell。 Azure 自動化中[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 的外觀都類似 Orchestrator Runbook，其活動代表 PowerShell Cmdlet、子 Runbook 和資產。 除了轉換 Runbook 本身，您必須將具有 Runbook 所使用活動的整合套件轉換為具有 Windows PowerShell Cmdlet 的整合模組。 

[服務管理自動化](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) 會在您的本機資料中心 (例如 Orchestrator) 中儲存並執行 Runbook，而它使用與 Azure 自動化相同的整合模組。 Runbook Converter 會將 Orchestrator Runbook 轉換為圖形化 Runbook，不過 SMA 不支援後者。 您仍然可以將標準活動模組和 System Center Orchestrator 整合模組安裝到 SMA，但是您必須手動[重新編寫您的 Runbook](/system-center/sma/authoring-automation-runbooks)。

## <a name="download-the-orchestrator-migration-toolkit"></a>下載 Orchestrator 移轉工具組

移轉的第一個步驟是下載 [System Center Orchestrator 移轉工具組](https://www.microsoft.com/download/details.aspx?id=47323)。 此工具組包含的工具可協助您將 Runbook 從 Orchestrator 轉換成 Azure 自動化。  

## <a name="import-the-standard-activities-module"></a>匯入標準活動模組

將[標準活動模組](/system-center/orchestrator/standard-activities)匯入 Azure 自動化。 這包括轉換後的圖形化 Runbook 可使用的標準 Orchestrator 活動的轉換後版本。

## <a name="import-orchestrator-integration-modules"></a>匯入 Orchestrator 整合模組

Microsoft 提供 [整合套件](/previous-versions/system-center/packs/hh295851(v=technet.10)) ，用於建立 Runbook 以自動化 System Center 元件和其他產品。 這些整合套件有一些目前是基於 OIT，但因為已知的問題，目前無法轉換為整合模組。 針對存取 System Center 的 Runbook 所使用的整合套件，將 [System Center Orchestrator 整合模組](https://www.microsoft.com/download/details.aspx?id=49555) 匯入 Azure 自動化。 此套件包含整合套件的已轉換版本，可以匯入 Azure 自動化和 Service Management Automation 中。  

## <a name="convert-integration-packs"></a>轉換整合套件

使用[整合套件轉換器](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard)將使用 [Orchestrator 整合工具組 (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) 所建立的整合套件轉換為 PowerShell 型的整合模組，以便匯入 Azure 自動化或 Service Management Automation。 執行整合套件轉換器時，您會看到一個精靈，可讓您選取整合套件 (.oip) 檔案。 然後精靈會列出該整合套件所包含的活動，並可讓您選取要移轉的活動。 完成精靈時，它會建立整合模組，其中包含針對原始整合套件中的每個活動相對應的 Cmdlet。

> [!NOTE]
> 未使用 OIT 建立的整合套件，無法使用整合套件轉換器來進行轉換。 另外還有一些 Microsoft 提供的整合套件目前無法使用此工具轉換。 這些整合套件的轉換後版本已提供下載，供您可以將它們安裝在 Azure 自動化或 Service Management Automation 中。

### <a name="parameters"></a>參數

整合套件中的活動的任何屬性都會轉換成整合模組中對應的 Cmdlet 的參數。  Windows PowerShell Cmdlet 有一組 [一般參數](/powershell/module/microsoft.powershell.core/about/about_commonparameters) ，可以搭配所有 Cmdlet 使用。 例如，-Verbose 參數會導致 Cmdlet 輸出其工作的詳細資訊。  沒有 Cmdlet 可以具有與一般參數同名的參數。 如果活動具有與一般參數同名的屬性，精靈會提示您為參數提供另一個名稱。

### <a name="monitor-activities"></a>監視器活動

在 Runbook Orchestrator 中監視從 [監視活動](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) 開始，並持續執行等候特定事件叫用。 Azure 自動化不支援監視 Runbook，因此，不會轉換整合套件中的任何監視活動。 相反地，會在監視活動的整合模組中建立預留位置 Cmdlet。  此 Cmdlet 不具任何功能，但它可讓您安裝使用它的任何轉換的 Runbook。 此 Runbook 無法在 Azure 自動化中執行，但可以安裝，使您可以修改它。

Orchestrator 包含未包含在整合套件中、但會使用許多 Runbook 的一組 [標準活動](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) 。  標準活動模組是包含這些活動的每個對等的 Cmdlet 的整合模組。 您必須在匯入使用標準活動的任何轉換的 Runbook 之前，於 Azure 自動化中安裝此整合模組。

除了支援轉換的 Runbook，標準活動模組中的 Cmdlet 也可由熟悉 Orchestrator 的其他人在 Azure 自動化中建置新的 Runbook。 雖然所有標準活動的功能都可使用 Cmdlet 執行的，它們的運作方式可能不同。 轉換後標準活動模組中的 Cmdlet 的運作與對應的活動相同，並使用相同的參數。 這可協助您產品轉換到 Azure 自動化 Runbook。

## <a name="convert-orchestrator-runbooks"></a>轉換 Orchestrator Runbook

Orchestrator Runbook Converter 會將 Orchestrator Runbook 轉換為可以匯入 Azure 自動化的[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)。 Runbook Converter 會實作為 PowerShell 模組，並使用 `ConvertFrom-SCORunbook` Cmdlet 來進行轉換。 安裝 Converter 時，它會建立可載入 Cmdlet 的 PowerShell 工作階段捷徑。   

以下是轉換 Runbook 並將其匯入 Azure 自動化的基本步驟。 本節稍後會提供使用 Cmdlet 的詳細資料。

1. 從 Orchestrator 匯出一或多個 Runbook。
2. 取得 Runbook 中所有活動的整合模組。
3. 轉換所匯出檔案中的 Orchestrator Runbook。
4. 檢閱記錄中的資訊，以驗證轉換並判斷任何必要的手動工作。
5. 將轉換的 Runbook 匯入至 Azure 自動化。
6. 在 Azure 自動化中建立任何必要的資產。
7. 在 Azure 自動化中編輯 Runbook，以修改任何所需的活動。

`ConvertFrom-SCORunbook` 的語法為：

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - 包含要轉換之 Runbook 的匯出檔案的路徑。
* Module - 逗號分隔的整合模組清單，其中包含 Runbook 中的活動。
* OutputFolder - 建立所轉換圖形的 Runbook 之資料夾的路徑。

下列範例命令會在名為 **MyRunbooks.ois_export** 的匯出檔案中轉換 Runbook。  這些 Runbook 使用 Active Directory 和 Data Protection Manager 整合套件。

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>使用 Runbook Converter 記錄檔

Runbook Converter 會在與轉換後 Runbook 相同的位置中建立下列記錄檔。  如果檔案已經存在，則會以最後一個轉換的資訊加以覆寫。

| 檔案 | 內容 |
|:--- |:--- |
| Runbook Converter - Progress.log |轉換的詳細步驟包括成功轉換的每個活動的資訊，以及未轉換的每個活動的警告。 |
| Runbook Converter - Summary.log |最後一個轉換的摘要包括任何警告，以及您需要執行的後續工作，例如：建立變數轉換的 Runbook 所需的變數。 |

### <a name="export-runbooks-from-orchestrator"></a>從 Orchestrator 匯出 Runbook

Runbook Converter 可與來自包含一或多個 Runbook 的 Orchestrator 匯出檔案搭配使用。  它會為匯出檔案中的每個 Orchestrator Runbook 建立對應的 Azure 自動化 Runbook。  

若要從 Orchestrator 匯出 Runbook，請在 Runbook Designer 中的 Runbook 名稱上按一下滑鼠右鍵，然後選取 [匯出] 。  若要匯出資料夾中的所有 Runbook，請在資料夾名稱上按一下滑鼠右鍵，並選取 [匯出] 。

### <a name="convert-runbook-activities"></a>轉換 Runbook 活動

Runbook Converter 會將 Orchestrator Runbook 中的每個活動轉換成 Azure 自動化中的對應活動。  針對無法轉換的這些活動，會在 Runbook 中建立預留位置活動，並帶有警告文字。  將轉換的 Runbook 匯入 Azure 自動化之後，您必須將這些活動取代為可執行所需功能的有效活動。

會轉換標準活動模組中的所有 Orchestrator 活動。 不過，在此模組有一些標準 Orchestrator 活動，並不會被轉換。 例如，`Send Platform Event` 沒有 Azure 自動化對等項目，因為該事件是 Orchestrator 特有。

[監視器活動](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) ，因為它們在 Azure 自動化中沒有對等項目。 例外狀況是在轉換後整合套件中的監視活動，會轉換成預留位置活動。

如果您使用 `modules` 參數提供整合模組路徑，轉換後整合套件中的所有活動都會被轉換。 針對 System Center 整合套件，您可以使用 System Center Orchestrator 整合模組。

### <a name="manage-orchestrator-resources"></a>管理 Orchestrator 資源

Runbook Converter 只會轉換 Runbook，而不會轉換其他 Orchestrator 資源，例如計數器、變數或連線。  Azure 自動化中不支援計數器。  支援變數和連線，但是您必須手動建立它們。 記錄檔會通知您 Runbook 是否需要這類資源，並指定您需要在 Azure 自動化中建立的對應資源，轉換後的 Runbook 才能正常運作。

比方說，Runbook 可能會使用變數來填入活動中的特定值。  轉換後 Runbook 會轉換活動，並使用與 Orchestrator 變數相同的名稱指定 Azure 自動化中的變數資產。 這個動作會記錄在轉換後建立的 **Runbook Converter - Summary.log** 檔案中。 在使用 Runbook 之前，您必須在 Azure 自動化中手動建立此變數資產。

### <a name="work-with-orchestrator-input-parameters"></a>使用 Orchestrator 輸入參數

Orchestrator 中的 Runbook 會接受具有 `Initialize Data` 活動的輸入參數。  如果要轉換的 Runbook 包含此活動，則會對活動中的每個參數在 Azure 自動化 Runbook 中建立 [輸入參數](automation-graphical-authoring-intro.md#handle-runbook-input) 。  在擷取並傳回每個參數的轉換 Runbook 中建立 [工作流程指令碼控制項](automation-graphical-authoring-intro.md#use-activities) 活動。 Runbook 中使用輸入參數的任何活動會參照此活動的輸出。

使用此策略的原因是最能與 Orchestrator Runbook 中的功能對映。  新圖形化 Runbook 中的活動應該使用 Runbook 輸入資料來源直接參照輸入參數。

### <a name="invoke-runbook-activity"></a>叫用 Runbook 活動

Orchestrator 中的 Runbook 會使用 `Invoke Runbook` 活動來啟動其他 Runbook。 如果要轉換的 Runbook 包含此活動，並且設定了 `Wait for completion` 選項，則會為它在轉換後的 Runbook 中建立 Runbook 活動。  如果未設定 `Wait for completion` 選項，則會建立使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) 來啟動 Runbook 的工作流程指令碼活動。 將轉換的 Runbook 匯入 Azure 自動化之後，您必須以活動中指定的資訊修改此活動。

## <a name="create-orchestrator-assets"></a>建立 Orchestrator 資產

Runbook Converter 不會轉換 Orchestrator 資產。 您必須在 Azure 自動化中手動建立任何必要的 Orchestrator 資產。

## <a name="configure-hybrid-runbook-worker"></a>設定混合式 Runbook 背景工作角色

Orchestrator 會將 Runbook 儲存在資料庫伺服器上，並在Runbook 伺服器上執行，這兩個伺服器都位在您的本機資料中心 。 Azure 自動化中的 Runbook 會儲存在 Azure 雲端，可以在您的本機資料中心使用[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)執行。 將背景工作角色設定為執行從 Orchestrator 轉換的 Runbook，因為它們就是設計成在本機伺服器上執行並存取本機資源。

## <a name="related-articles"></a>相關文章

* 如需 Orchestrator 的詳細資訊，請參閱 [System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))。
* 深入瞭解如何將 [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) 中的服務管理自動化。
* Orchestrator 活動的詳細資料可在 [Orchestrator 標準活動](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))中找到。
* 若要取得 Orchestrator 移轉工具組，請參閱[下載 System Center Orchestrator 移轉工具組](https://www.microsoft.com/download/details.aspx?id=47323)。
* 如需 Azure 自動化混合式 Runbook 背景工作角色的概觀，請參閱[混合式 Runbook 背景工作角色概觀](automation-hybrid-runbook-worker.md)。
