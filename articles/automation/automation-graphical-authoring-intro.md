---
title: Azure 自動化中的圖形化編寫
description: 圖形化編寫可讓您建立 Azure 自動化的 Runbook，而不使用程式碼。 本文章提供圖形化編寫的簡介和開始建立圖形化 Runbook 所需的所有詳細資料。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 15f63544a98c6d7bb7171081d9c3e084890e15ec
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255988"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure 自動化中的圖形化編寫

圖形化編寫可讓您建立 Azure 自動化的 runbook，而不需要基礎 Windows PowerShell 或 PowerShell 工作流程程式碼的複雜性。 您可以從 Cmdlet 和 runbook 的程式庫將活動新增至畫布、將它們連結在一起，並將其設定為形成工作流程。 如果您曾經用過 System Center Orchestrator 或 Service Management Automation （SMA），圖形化撰寫應該看起來很熟悉。 本文提供您開始建立圖形化 runbook 所需的概念簡介。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="graphical-runbooks"></a>圖形化 Runbook

Azure 自動化中的所有 runbook 都是 Windows PowerShell 工作流程。 圖形化 runbook 和圖形化 PowerShell 工作流程 runbook 會產生執行自動化背景工作的 PowerShell 程式碼，但您無法加以查看或修改。 您可以將圖形化 runbook 轉換為圖形化 PowerShell 工作流程 runbook，反之亦然。 不過，您無法將這些 runbook 轉換成文字 runbook。 此外，自動化圖形化編輯器也無法匯入文字 runbook。

## <a name="overview-of-graphical-editor"></a>圖形化編輯器概觀

您可以透過建立或編輯圖形化 Runbook，在 Azure 入口網站中開啟圖形化編輯器。

![圖形化工作區](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

下列各節說明圖形化編輯器中的控制項。

### <a name="canvas-control"></a>Canvas 控制項

Canvas 控制項可讓您設計 runbook。 您可以從程式庫控制項中的節點將活動新增至 runbook，並將其連接至定義 runbook 邏輯的連結。 畫布底部有一些控制項，可讓您放大和縮小。

### <a name="library-control"></a>程式庫控制項

[程式庫] 控制項可讓您選取要新增至 runbook 的[活動](#activities)。 您可以將它們新增至畫布，以便將它們連接到其他活動。 程式庫控制項包含下表中所定義的區段。

| 區段 | 描述 |
|:--- |:--- |
| Cmdlet |可在 runbook 中使用的所有 Cmdlet。 Cmdlet 是依模組組織。 您已在自動化帳戶中安裝的所有模組都可供使用。 |
| Runbook |您的自動化帳戶中的 runbook。 您可以將這些 runbook 新增至畫布，以作為子 runbook 使用。 只會顯示與所編輯的 runbook 屬於相同核心類型的 runbook。 針對圖形化 runbook，只會顯示以 PowerShell 為基礎的 runbook。 針對圖形化 PowerShell 工作流程 runbook，只會顯示以 PowerShell 工作流程為基礎的 runbook。 |
| Assets |您的自動化帳戶中可用於 runbook 的[自動化資產](/previous-versions/azure/dn939988(v=azure.100))。 將資產新增至 runbook 會新增可取得所選資產的工作流程活動。 如果是變數資產，您可以選取是否要加入活動以取得變數或設定變數。 |
| Runbook 控制項 |控制可在目前 runbook 中使用的活動。 連接點活動會接受多個輸入，並等待全部完成，然後再繼續工作流程。 程式碼活動會執行一或多行 PowerShell 或 PowerShell 工作流程程式碼，視圖形化 runbook 類型而定。 您可以對很難利用其他活動來達成的自訂程式碼或功能使用此活動。 |

### <a name="configuration-control"></a>組態控制項

設定控制項可讓您提供在畫布上選取之物件的詳細資料。 此控制項中可用的屬性取決於所選取的物件類型。 當您選擇設定控制項中的選項時，它會開啟其他的 blade，以提供詳細資訊。

### <a name="test-control"></a>測試控制項

第一次啟動圖形化編輯器時，不會顯示測試控制項。 當您以互動方式測試圖形化 runbook 時，就會開啟它。

## <a name="activities"></a>活動

活動是 Runbook 的建置區塊。 活動可以是 PowerShell Cmdlet、子 runbook 或工作流程。 您可以在 [程式庫] 控制項中以滑鼠右鍵按一下活動，然後選取 [**新增至畫布**]，將其新增至 runbook。 然後可以按一下並拖曳活動，將它放置在畫布上的任何位置。 活動在畫布上的位置不會影響 runbook 的作業。 您可以用您認為最適合的方式來配置您的 runbook，以視覺化其作業。

![加入至畫布](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

選取畫布上的活動，以設定其在 [設定] 分頁中的屬性和參數。 您可以將活動的 [標籤] 變更為您發現描述性的名稱。 Runbook 仍會執行原始 Cmdlet。 您只需要變更圖形化編輯器所使用的顯示名稱即可。 請注意，標籤在 runbook 內必須是唯一的。

### <a name="parameter-sets"></a>參數集

參數集會定義接受特定 Cmdlet 值的強制參數和選用參數。 所有 Cmdlet 都至少有一個參數集，而有些則有數個集合。 如果 Cmdlet 有多個參數集，您就必須選取要使用的參數，才能設定參數。 您可以藉由選取 [**參數集**] 並選擇另一個集合，來變更活動使用的參數集。 在此情況下，您已設定的任何參數值都會遺失。

在下列範例中， [update-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) Cmdlet 有三個參數集。 此範例會使用一個名為**ListVirtualMachineInResourceGroupParamSet**的集合，其中包含一個選擇性參數，用來傳回資源群組中的所有虛擬機器。 此範例也會使用**getvirtualmachineinresourcegroupparamset 適用**參數集來指定要傳回的虛擬機器。 這個集合有兩個強制參數和一個選擇性參數。

![參數集](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>參數值

在指定參數的值時，您可以選取資料來源以判斷如何指定值。 適用于特定參數的資料來源取決於該參數的有效值。 例如，對於不允許 null 值的參數而言， **Null**不是可用的選項。

| 資料來源 | 描述 |
|:--- |:--- |
| 常數值 |輸入參數的值。 此資料來源僅適用于下列資料類型： Int32、Int64、String、Boolean、DateTime、Switch。 |
| 活動輸出 |在工作流程中的目前活動之前，使用活動的輸出。 列出所有有效的活動。 針對參數值，只使用產生輸出的活動。 如果活動輸出具有多個屬性的物件，您可以在選取活動之後輸入特定屬性的名稱。 |
| Runbook 輸入 |選取 runbook 輸入作為活動參數的輸入。 |
| 變數資產 |選取自動化變數做為輸入。 |
| 認證資產 |選取自動化認證做為輸入。 |
| 憑證資產 |選取 [自動化憑證] 做為輸入。 |
| 連線資產 |選取自動化連接做為輸入。 |
| PowerShell 運算式 |指定簡單的[PowerShell 運算式](#powershell-expressions)。 在活動之前評估運算式，並將結果用於參數值。 您可以使用變數來參照活動或 Runbook 的輸入參數的輸出。 |
| 尚未設定 |清除先前設定的任何值。 |

#### <a name="optional-additional-parameters"></a>選擇性的其他參數

所有 Cmdlet 可選擇提供額外的參數。 這些是 PowerShell 一般參數或其他自訂參數。 圖形化編輯器會顯示一個文字方塊，您可以在其中使用 PowerShell 語法來提供參數。 例如，若要使用*Verbose*一般參數，您應該指定 `-Verbose:$True`。

### <a name="retry-activity"></a>重試活動

活動的重試功能可讓它執行多次，直到符合特定條件為止（與迴圈非常類似）。 您可以將這項功能用於應該執行多次的活動、容易出錯、可能需要一次以上的成功嘗試，或針對有效資料測試活動的輸出資訊。

當您對活動啟用重試時，您可以設定延遲和條件。 延遲是 Runbook 再次執行活動之前所等待的時間 (以秒或分鐘計算)。 如果您未指定延遲，活動會在完成後立即執行。

![活動重試延遲](media/automation-graphical-authoring-intro/retry-delay.png)

重試條件是在每次活動執行之後評估的 PowerShell 運算式。 如果運算式解析為 True，則活動會再次執行。 如果運算式解析為 False，則活動不會再次執行，且 runbook 會移至下一個活動。

![活動重試延遲](media/automation-graphical-authoring-intro/retry-condition.png)

重試條件可以使用名為*RetryData*的變數，以提供有關活動重試資訊的存取權。 此變數具有下表中的屬性：

| 屬性 | 描述 |
|:--- |:--- |
| NumberOfAttempts |活動已執行的次數。 |
| 輸出 |活動上次執行的輸出。 |
| TotalDuration |活動第一次開始之後的經過時間。 |
| StartedAt |第一次啟動活動時的時間（UTC 格式）。 |

以下是活動重試條件的範例。

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

設定活動的重試條件之後，該活動便會包含兩個視覺提示來提醒您。 其中一個會顯示在活動中，而另一個則會在您檢查活動的設定時顯示。

![活動重試視覺指示器](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流程指令碼控制項

「工作流程腳本控制」是一種特殊的活動，可接受 PowerShell 或 PowerShell 工作流程腳本，視所撰寫的圖形化 runbook 類型而定。 此控制項提供其他方法可能無法使用的功能。 它不能接受參數，但它可以對活動輸出和 Runbook 輸入參數使用變數。 活動的任何輸出都會新增至 databus。 例外狀況是沒有連出連結的輸出，在此情況下，會將輸出新增至 runbook 的輸出。

例如，下列程式碼會使用名為*NumberOfDays*的 runbook 輸入變數來執行日期計算。 然後，它會傳送匯出的 DateTime 值做為輸出，供 runbook 中的後續活動使用。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>連結和工作流程

圖形化 runbook 中的連結會連接兩個活動。 就會在畫布上顯示為箭號，從來源活動指向目的地活動。 活動會以箭頭的方向執行，在來源活動完成之後，目的地活動就會啟動。

### <a name="link-creation"></a>連結建立

您可以選取來源活動，然後按一下圖形底部的圓形，建立兩個活動之間的連結。 將箭頭拖曳到目的地活動和版本。

![建立連結](media/automation-graphical-authoring-intro/create-link-revised20165.png)

在 [組態] 分頁中選取連結來設定其屬性。 屬性包含連結類型，如下表所述。

| 連結類型 | 描述 |
|:--- |:--- |
| 管線 |目的地活動會針對來源活動的每個物件輸出執行一次。 如果來源活動不會產生任何輸出，則不會執行目的地活動。 從來源活動輸出以物件形式提供。 |
| 順序 |只有當目的地活動收到來源活動的輸出時，才會執行一次。 從來源活動輸出以物件陣列形式提供。 |

### <a name="start-of-activity"></a>活動開始

圖形化 Runbook 會從沒有傳入連結的活動開始。 通常只有一個活動會作為 runbook 的啟動活動。 如果有多個活動沒有連入連結，runbook 就會開始以平行方式執行。 每個活動完成時，會遵循連結以執行其他活動。

### <a name="link-conditions"></a>連結條件

當您在連結上指定條件時，只有在條件解析為 True 時，目的地活動才會執行。 您通常會在條件中使用*ActivityOutput*變數來取出來源活動的輸出。

對於管線連結，您必須指定單一物件的條件。 Runbook 會根據來源活動來評估每個物件輸出的條件。 然後，它會針對滿足條件的每個物件執行目的地活動。 例如，透過 Update-azvm 的來源活動，您可以針對條件**式**管線連結使用下列語法，只取出資源群組中名為 Group1 的虛擬機器。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

針對順序連結，runbook 只會評估條件一次，因為會傳回包含來自來源活動之所有物件的單一陣列。 因此，runbook 無法使用序列連結進行篩選，就像它可以使用管線連結一樣。 [順序] 連結可以直接判斷下一個活動是否已執行。

例如，在我們的**啟動 VM** runbook 中採取下列一組活動：

![具有順序的條件式連結](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook 會使用三種不同的順序連結來驗證輸入參數*VMName*和*ResourceGroupName*的值，以判斷要採取的適當動作。 可能的動作包括啟動單一 VM、啟動資源群組中的所有 Vm，或啟動訂用帳戶中的所有 Vm。 針對 [**連接到 Azure]** 和 [**取得單一 VM**] 之間的順序連結，以下是條件邏輯：

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

使用條件式連結時，可從來源活動提供該分支中的其他活動使用的資料將由條件篩選。 如果活動是多個連結的來源，則每個分支中的活動可用的資料，取決於連接到該分支的連結中的條件。

例如，下列 runbook 中的**update-azvm**活動會啟動所有虛擬機器。 它有兩個條件式連結。 第一個條件式連結會使用運算式 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`，以篩選**update-azvm**活動是否成功完成。 第二個條件式連結會使用運算式 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` 來篩選**update-azvm**活動是否無法啟動虛擬機器。

![條件式連結範例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

遵循第一個連結並使用來自**get-help**的活動輸出的任何活動，只會抓取在執行**get-help**時啟動的虛擬機器。 遵循第二個連結的任何活動，只會取得在執行**get-help**時停止的虛擬機器。 遵循第三個連結的任何活動，會取得所有虛擬機器，不論其執行狀態為何。

### <a name="junctions"></a>接合

接合是一個特殊的活動，會等候直到所有傳入的分支完成。 這可讓 runbook 平行執行多個活動，並確保所有動作都已完成，再繼續進行。

雖然連接可以有不限數目的傳入連結，但只有其中一個連結可以是管線。 內送的順序連結數目不受限制。 您可以使用多個連入管線連結建立連接，並儲存 runbook，但是執行時將會失敗。

下列範例是 Runbook 的一個部分，其會啟動一組虛擬機器，同時下載修補程式以套用到這些機器。 它會使用連接點，以確保這兩個處理常式都會在 runbook 繼續之前完成。

![接合](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

當目的地活動連結回其來源活動或最後連結回其來源的另一個活動時，就會形成迴圈。 圖形化撰寫目前不支援迴圈。 如果您的 Runbook 有循環，它可以正確儲存，但是執行時會收到錯誤。

![循環](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>活動之間的資料共用

具有輸出連結之活動輸出的任何資料，都會寫入至該 runbook 的 databus。 Runbook 中的任何活動可以在資料匯流排上使用資料來填入參數值，或納入指令碼程式碼。 活動可以存取工作流程中任何先前的活動的輸出。

資料如何寫入至資料匯流排取決於活動上的連結類型。 對於管線連結，資料會輸出為多個物件。 若為順序連結，資料會輸出為數組。 如果只有一個值，則會輸出為單一元素陣列。

您的 runbook 有兩種方式可存取 databus 上的資料： 
* 使用活動輸出資料來源。
* 使用 PowerShell 運算式資料來源。

第一種機制會使用活動輸出資料來源來填入另一個活動的參數。 如果輸出是物件，runbook 就可以指定單一屬性。

![活動輸出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

第二個數據存取機制會使用如下所示的語法，透過*ActivityOutput*變數抓取 PowerShell 運算式資料來源中的活動輸出或工作流程腳本活動。 如果輸出是物件，您的 runbook 可以指定單一屬性。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>檢查點

您可以在圖形化 PowerShell 工作流程 runbook 中設定[檢查](automation-powershell-workflow.md#checkpoints)點，方法是在任何活動上選取 [**檢查點 runbook** ]。 這會導致在執行活動之後設定檢查點。

![檢查點](media/automation-graphical-authoring-intro/set-checkpoint.png)

只有圖形化 PowerShell 工作流程 runbook 才會啟用檢查點，而圖形化 runbook 無法使用。 如果 runbook 使用 Azure Cmdlet，則應該遵循具有**disconnect-azaccount**活動的任何檢查點活動。 當 runbook 暫停，而且必須在不同的背景工作上從這個檢查點重新開機時，就會使用 connect 作業。

## <a name="runbook-input-and-output"></a>Runbook 輸入和輸出

### Runbook 輸入<a name="runbook-input"></a>

Runbook 需要使用者透過 Azure 入口網站或從另一個 runbook 啟動 runbook 時的輸入，如果目前的帳戶是用來做為子系。 例如，針對建立虛擬機器的 runbook，使用者可能需要在每次 runbook 啟動時，將這類資訊提供為虛擬機器的名稱和其他屬性。

Runbook 會藉由定義一或多個輸入參數來接受輸入。 使用者每次啟動 runbook 時，都會提供這些參數的值。 當使用者使用 Azure 入口網站啟動 runbook 時，系統會提示使用者為 runbook 支援的每個輸入參數提供值。

撰寫 runbook 時，您可以按一下 runbook 工具列上的 [**輸入和輸出**] 按鈕來存取其輸入參數。 這會開啟 [輸入和輸出] 控制項，您可以在其中編輯現有的輸入參數，或按一下 [**加入輸入**] 來建立一個新的。

![加入輸入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

下表中的屬性定義每個輸入參數：

| 屬性 | 描述 |
|:--- |:--- |
| 名稱 | 必要。 參數名稱。 此名稱在 runbook 內必須是唯一的。 它必須以字母開頭，且只能包含字母、數位和底線。 名稱不能包含空格。 |
| 描述 |選擇性。 輸入參數用途的描述。 |
| 類型 | 選擇性。 對參數值預期的資料型別。 提示您輸入時，Azure 入口網站會對每個參數提供適當的資料類型控制項。 支援的參數類型為 String、Int32、Int64、Decimal、Boolean、DateTime 和 Object。 如果未選取資料類型，則會預設為 String。|
| 強制性 | 選擇性。 設定，指定是否必須提供參數的值。 如果您選擇 [**是]** ，則必須在 runbook 啟動時提供值。 如果您選擇 [**否**]，則在 runbook 啟動時不需要值，而且可以使用預設值。 如果您沒有為每個未定義預設值的強制參數提供值，runbook 就無法啟動。 |
| 預設值 | 選擇性。 當 runbook 啟動時，用於參數的值（如果未傳入的話）。 若要設定預設值，請選擇 [自訂]。 如果您不想提供任何預設值，請選取 [**無**]。 |

### <a name="runbook-output"></a>Runbook 輸出

圖形化編寫會將沒有輸出連結的任何活動所建立的資料，儲存至[runbook 的輸出](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)。 輸出會隨著 Runbook 工作儲存，並且在 Runbook 用作子項時提供給父 Runbook 使用。

## <a name="powershell-expressions"></a>PowerShell 運算式

圖形化撰寫的其中一個優點是，它可讓您以最少的 PowerShell 知識建立 runbook。 不過，目前您需要知道一些 PowerShell，以填入某些[參數值](#activities)和設定[連結條件](#links-and-workflow)。 本節提供 PowerShell 運算式的快速簡介。 PowerShell 的完整詳細資料位於 [使用 Windows PowerShell 撰寫指令碼](https://technet.microsoft.com/library/bb978526.aspx)。

### <a name="powershell-expression-data-source"></a>PowerShell 運算式資料來源

您可以使用 PowerShell 運算式做為資料來源，以使用 PowerShell 程式碼的結果來填入[活動參數](#activities)的值。 運算式可以是執行簡單函式的一行程式碼，或執行一些複雜邏輯的多行。 未指派給變數的任何命令輸出都會輸出到參數值。

例如，下列命令會輸出目前的日期。

```powershell-interactive
Get-Date
```

下一個程式碼片段會從目前的日期建立字串，並將其指派給變數。 程式碼會將變數的內容傳送至輸出。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

下列命令會評估目前的日期，並傳回字串，指出當天是週末還是工作日。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>活動輸出

若要在 runbook 中使用上一個活動的輸出，請使用*ActivityOutput*變數搭配下列語法。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

例如，您可以有一個活動，其屬性需要虛擬機器的名稱。 在此情況下，您的 runbook 可以使用下列運算式。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

如果屬性需要虛擬機器物件，而不只是名稱，則 runbook 會使用下列語法傳回整個物件。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook 可在更複雜的運算式中使用活動的輸出，如下所示。 此運算式會將文字串連到虛擬機器名稱。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>條件

使用 [比較運算子](https://technet.microsoft.com/library/hh847759.aspx) 來比較值或判斷值是否符合指定的模式。 比較會傳回 True 或 False 的值。

例如，下列條件會判斷來自名為**get-help**之活動的虛擬機器目前是否已停止。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

下列條件會判斷相同的虛擬機器是否處於 [已停止] 以外的任何狀態。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

您可以使用[邏輯運算子](https://technet.microsoft.com/library/hh847789.aspx)（例如 **-and**或 **-or**）聯結 runbook 中的多個條件。 例如，下列條件會檢查上一個範例中的虛擬機器是否處於**已停止**或**正在停止**的狀態。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>雜湊表

[雜湊表](https://technet.microsoft.com/library/hh847780.aspx)是成對的名稱/值組，適用于傳回值集合。 您也可能會看到雜湊表，稱為「字典」。 某些活動的屬性預期會有雜湊表，而不是簡單的值。

使用下列語法建立雜湊表。 它可以包含任意數目的專案，但每個都是由名稱和值所定義。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

例如，下列運算式會建立雜湊表，以作為活動參數的資料來源，其預期會有網際網路搜尋值的雜湊表。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

下列範例使用稱為 **Get Twitter Connection** 的活動的輸出來填入雜湊表。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>向 Azure 資源驗證

管理 Azure 資源之 Azure 自動化中的 Runbook 需要向 Azure 驗證。 [執行身分帳戶](automation-create-runas-account.md)（也稱為服務主體）是自動化 runbook 用來存取訂用帳戶中 Azure Resource Manager 資源的預設機制。 您可以藉由將使用 PowerShell [get-automationconnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) Cmdlet 的**AzureRunAsConnection**連線資產新增至畫布，將此功能新增至圖形化 runbook。 您也可以新增[disconnect-azaccount](/powershell/module/az.profile/connect-azaccount) Cmdlet。 下列範例說明此案例。

![執行身分驗證活動](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

「**取得執行**身分」連線活動（或**get-automationconnection**）是使用名為**AzureRunAsConnection**的常數值資料來源來設定。

![執行身分連線設定](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

下一個活動（ **disconnect-azaccount**）會新增已驗證的執行身分帳戶，以便在 runbook 中使用。

![Connect-Disconnect-azaccount 參數集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>針對 PowerShell runbook， **disconnect-azaccount**和**add-AzureRMAccount**是**disconnect-azaccount**的別名。 請注意，您的圖形化 runbook 無法使用這些別名。 圖形化 runbook 只能使用**disconnect-azaccount**本身。

針對參數欄位、 **APPLICATIONID**、 **CERTIFICATETHUMBPRINT**和**TENANTID**，請指定欄位路徑的屬性名稱，因為活動會輸出具有多個屬性的物件。 否則，當 runbook 執行時，它會在嘗試進行驗證時失敗。 這就是您使用執行身分帳戶驗證 Runbook 時所需的最低限度。

有些訂閱者會使用[Azure AD 使用者帳戶](automation-create-aduser-account.md)建立自動化帳戶，以管理 Azure 傳統部署或 Azure Resource Manager 資源。 為維持這些訂閱者的回溯相容性，在 runbook 中使用的驗證機制是具有認證[資產](automation-credentials.md)的**add-azureaccount** Cmdlet。 資產代表可存取 Azure 帳戶的 Active Directory 使用者。

您可以藉由將認證資產新增至畫布，然後按使用認證資產進行輸入的**add-azureaccount**活動，為您的圖形化 runbook 啟用這項功能。 請參閱下列範例。

![驗證活動](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 必須在其啟動時和每個檢查點之後進行驗證。 因此，在任何**檢查點工作流程**活動之後，您必須使用**add-azureaccount**活動。 您不需要使用額外的認證活動。

![活動輸出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>匯出和匯入圖形化 Runbook

您可以只匯出圖形化 Runbook 的已發行版本。 如果尚未發行 runbook，則會停用 [**匯出**] 按鈕。 當您按一下 [**匯出**] 按鈕時，runbook 會下載至您的本機電腦。 檔案名與副檔名為 **.graphrunbook**的 runbook 名稱相符。

新增 runbook 時，您可以選取 [匯**入**] 選項來匯入圖形化或圖形化 PowerShell 工作流程 runbook 檔案。 當您選取要匯入的檔案時，您可以保留相同的名稱，或提供一個新檔案。 [ **Runbook 類型**] 欄位會在評估選取的檔案之後，顯示 runbook 的類型。 如果您嘗試選取不正確的不同類型，圖形化編輯器會顯示一則訊息，指出有潛在的衝突，而且轉換期間可能會有語法錯誤。

![匯入 Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

## <a name="testing-a-graphical-runbook"></a>測試圖形化 Runbook

Azure 自動化中的每個圖形化 runbook 都有草稿版本和已發佈版本。 您只能執行已發行的版本，而只可以編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 當草稿版本準備好可供使用時，您會將它發佈，以草稿版本覆寫目前已發行的版本。

您可以在 Azure 入口網站中測試 runbook 的草稿版本，同時讓已發佈的版本保持不變。 或者，您可以在發佈新的 runbook 之前先進行測試，讓您可以在任何版本取代之前確認 runbook 是否正常運作。 Runbook 的測試會執行草稿版本，並確保其執行的任何動作都已完成。 不會建立任何工作歷程記錄，但 [測試輸出] 窗格會顯示輸出。

開啟您圖形化 runbook 的測試控制項，方法是開啟 runbook 進行編輯，然後按一下 [**測試窗格]** 。 測試控制項會提示輸入參數，您可以按一下 [**啟動**] 來啟動 runbook。

## <a name="publishing-a-graphical-runbook"></a>發行圖形化 Runbook

開啟 runbook 進行編輯，然後按一下 [**發佈**]，以發佈圖形化 runbook。 Runbook 的可能狀態如下：

* 新增--runbook 尚未發佈。 
* 已發佈--runbook 已發佈。
* 在 [編輯中]--runbook 已在發行後進行編輯，而且草稿和已發行的版本不同。

![Runbook 狀態](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

您可以選擇還原為已發佈版本的 runbook。 此作業會擲回上次發佈 runbook 之後所做的任何變更。 它會以已發行的版本取代 runbook 的草稿版本。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)。
* 若要開始使用圖形化 runbook，請參閱[我的第一個圖形化 runbook](automation-first-runbook-graphical.md)。
* 若要深入瞭解 runbook 類型以及其優點和限制，請參閱[Azure 自動化 runbook 類型](automation-runbook-types.md)。
* 若要瞭解如何使用自動化執行身分帳戶進行驗證，請參閱[設定 Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。
