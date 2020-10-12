---
title: 在 Azure 自動化中製作圖形化 Runbook
description: 本文說明如何在不使用程式碼的情況下製作圖形化 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 161272fe35ee9ea1e0880b991273e5d1a79eafb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987333"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>在 Azure 自動化中製作圖形化 Runbook

Azure 自動化中的所有 Runbook 都是 Windows PowerShell 工作流程。 圖形化 Runbook 和圖形化 PowerShell 工作流程 Runbook 會產生 PowerShell 程式碼，由自動化背景工作執行 ，且您無法查看或修改。 您可以將圖形化 Runbook 轉換為圖形化 PowerShell 工作流程 Runbook，反之亦然。 不過，您無法將這些 Runbook 轉換成文字 Runbook。 此外，自動化圖形化編輯器也無法匯入文字 Runbook。

圖形化製作可讓您為 Azure 自動化建立 Runbook，而沒有基礎 Windows PowerShell 或 PowerShell 工作流程程式碼的複雜度。 您可以從 Cmdlet 和 Runbook 的程式庫中將活動加入畫布，並將其連結在一起並加以設定來形成工作流程。 如果您曾經用過 System Center Orchestrator 或 Service Management Automation (SMA)，圖形化製作應該看起來很熟悉。 本文章提供簡介及開始建立圖形化 Runbook 所需的概念。

## <a name="overview-of-graphical-editor"></a>圖形化編輯器概觀

您可以透過建立或編輯圖形化 Runbook，在 Azure 入口網站中開啟圖形化編輯器。

![圖形化工作區](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

下列各節說明圖形化編輯器中的控制項。

### <a name="canvas-control"></a>畫布控制項

畫布控制項可讓您設計 Runbook。 您可以從程式庫控制項中的節點將活動加入至 Runbook，並用連結將它們連接來定義 Runbook 邏輯。 畫布底部有一些控制項，可讓您放大和縮小。

### <a name="library-control"></a>程式庫控制項

程式庫控制項可讓您選取要加入 Runbook 的[活動](#use-activities)。 您將它們加入畫布，並在畫布中將它們連接到其他活動。 程式庫控制項包含下表中定義的區段。

| 區段 | 描述 |
|:--- |:--- |
| 指令程式 |可以在 Runbook 中使用的所有 Cmdlet。 Cmdlet 是依模組組織。 已在您的自動化帳戶中安裝的所有模組皆可供使用。 |
| Runbook |自動化帳戶中的 Runbook。 您可以將這些 Runbook 加入畫布以做為子 Runbook。 系統只會顯示與所編輯 Runbook 相同核心類型的 Runbook。 針對圖形化 Runbook，只會顯示 PowerShell 型的 Runbook。 針對圖形化 PowerShell 工作流程 Runbook，只會顯示 PowerShell 工作流程型的 Runbook。 |
| Assets |您的自動化帳戶中可以在 Runbook 中使用的[自動化資產](/previous-versions/azure/dn939988(v=azure.100)) 。 將資產加入 Runbook 會新增工作流程活動，此活動可取得所選資產。 如果是變數資產，您可以選取是否要加入活動以取得變數或設定變數。 |
| Runbook 控制項 |可在目前的 Runbook 中使用的控制項活動。 「接合」活動會接受多個輸入，並等待所有項目完成，然後再繼續工作流程。 「程式碼」活動會根據圖形化 Runbook 類型而定，執行一或多行 PowerShell 或 PowerShell 工作流程程式碼。 您可以對很難利用其他活動來達成的自訂程式碼或功能使用此活動。 |

### <a name="configuration-control"></a>組態控制項

設定控制項可讓您為在畫布上選取的物件提供詳細資料。 此控制項中的可用屬性取決於所選物件的類型。 當您選擇設定控制項中的選項時，會開啟其他的刀鋒視窗，以提供詳細資訊。

### <a name="test-control"></a>測試控制項

第一次啟動圖形化編輯器時，不會顯示測試控制項。 當您以互動方式測試圖形化 Runbook 時才會開啟。

## <a name="use-activities"></a>使用活動

活動是 Runbook 的建置區塊。 活動可以是 PowerShell Cmdlet、子 Runbook 或工作流程。 以滑鼠右鍵按一下程式庫控制項的活動，然後選取 [加入畫布]，即可將活動加入 Runbook。 然後可以按一下並拖曳活動，將它放置在畫布上的任何位置。 活動在畫布上的位置不會影響 Runbook 的作業。 您可以任何您認為最適合以視覺呈現作業的方式配置您的 Runbook。

![加入至畫布](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

在設定刀鋒視窗中的畫布上選取活動以設定其屬性和參數。 您可以將活動的標籤變更為您認為合適的描述性名稱。 Runbook 仍會執行原始 Cmdlet。 您只需要變更圖形化編輯器所使用的顯示名稱即可。 請注意，標籤在 Runbook 內必須是唯一的。

### <a name="parameter-sets"></a>參數集

參數集會定義接受特定 Cmdlet 值的強制參數和選用參數。 所有的 Cmdlet 至少有一個參數集，而某些則有數個參數集。 如果 Cmdlet 有多個參數集，您必須先選取要使用的參數集，才能設定參數。 您可以變更活動使用的參數集，方法是選取 [參數集]，然後選擇其他參數集。 在此情況下，您已設定的任何參數值都會遺失。

在下列範例中，[Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0&preserve-view=true) Cmdlet 有三個參數集。 範例中使用**ListVirtualMachineInResourceGroupParamSet** 參數集，搭配單一選用參數，來傳回資源群組中的所有虛擬機器。 範例也會使用 **GetVirtualMachineInResourceGroupParamSet** 參數集來指定要傳回的虛擬機器。 這個參數集有兩個必要參數和一個選用參數。

![參數集](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>參數值

在指定參數的值時，您可以選取資料來源以判斷如何指定值。 特定參數可使用的資料來源取決於該參數的有效值。 例如，對於不允許 Null 值的參數而言，Null 並非可用選項。

| 資料來源 | 描述 |
|:--- |:--- |
| 常數值 |輸入參數的值。 這個資料來源只適用於下列資料類型：Int32、Int64、String、Boolean、DateTime、Switch。 |
| 活動輸出 |使用工作流程中優先於目前活動的活動輸出。 列出所有有效的活動。 針對參數值，只使用產生輸出的活動。 如果活動會輸出具有多個屬性的物件，您可以在選取活動之後輸入特定屬性的名稱。 |
| Runbook 輸入 |選取 Runbook 輸入做為活動參數的輸入。 |
| 變數資產 |選取「自動化變數」做為輸入。 |
| 認證資產 |選取「自動化認證」做為輸入。 |
| 憑證資產 |選取「自動化憑證」做為輸入。 |
| 連線資產 |選取「自動化連線」做為輸入。 |
| PowerShell 運算式 |指定簡單 [PowerShell 運算式](#work-with-powershell-expressions)。 會在活動之前評估運算式，然後將結果用於參數值。 您可以使用變數來參照活動或 Runbook 的輸入參數的輸出。 |
| 未設定 |清除先前設定的任何值。 |

#### <a name="optional-additional-parameters"></a>選擇性的其他參數

所有 Cmdlet 可選擇提供額外的參數。 這些是 PowerShell 的一般參數或其他自訂參數。 圖形化編輯器會顯示一個文字方塊，您可以在其中使用 PowerShell 語法提供參數。 例如，若要使用 `Verbose` 一般參數，應該指定 `-Verbose:$True`。

### <a name="retry-activity"></a>重試活動

活動的「重試」功能可讓活動執行多次，直到符合特定條件為止 (與迴圈非常類似)。 您可以對應該執行多次的活動、容易出錯、可能需要嘗試一次以上才會成功的活動，或者針對有效資料測試活動輸出資訊的活動，使用這項功能。

當您對活動啟用重試時，您可以設定延遲和條件。 延遲是 Runbook 再次執行活動之前所等待的時間 (以秒或分鐘計算)。 如果您未指定延遲，活動會在完成後立即再度執行。

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="啟用重試功能設定的螢幕擷取畫面。":::

重試條件是 PowerShell 運算式，在每次活動執行之後評估。 如果運算式解析為 True，活動就會再次執行。 如果運算式解析為 False，活動不會再次執行，且 Runbook 會移至下一個活動。

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="啟用重試功能設定的螢幕擷取畫面。":::

重試條件可以使用名為 `RetryData` 的變數來提供活動重試相關資訊的存取權。 此變數具有下表中的屬性：

| 屬性 | 描述 |
|:--- |:--- |
| `NumberOfAttempts` |活動已執行的次數。 |
| `Output` |活動上次執行的輸出。 |
| `TotalDuration` |活動第一次開始之後的經過時間。 |
| `StartedAt` |活動第一次開始的時間 (UTC 格式)。 |

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

設定活動的重試條件之後，該活動便會包含兩個視覺提示來提醒您。 一個顯示於活動中，另一個則會在您檢閱活動的設定時顯示。

![活動重試視覺指示器](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流程指令碼控制項

「工作流程指令碼控制」是一種特殊的活動，可接受 PowerShell 或 PowerShell 工作流程指令碼，視所製作的圖形化 Runbook 類型而定。 此控制項提供其他方法可能無法使用的功能。 它不能接受參數，但它可以對活動輸出和 Runbook 輸入參數使用變數。 活動的任何輸出都會新增至 databus。 例外狀況是沒有傳出連結的輸出，在此情況下，會將輸出新增至 Runbook 的輸出。

例如，下列程式碼會使用名為 `NumberOfDays` 的 Runbook 輸入變數來執行日期計算。 然後將計算出來的 DateTime 值做為輸出傳送，供 Runbook 中後續的活動使用。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>在工作流程中使用連結

圖形化 Runbook 中的連結會連接兩個活動。 就會在畫布上顯示為箭號，從來源活動指向目的地活動。 活動會以箭頭的方向執行，在來源活動完成之後，目的地活動就會啟動。

### <a name="create-a-link"></a>建立連結

您可以在兩個活動之間建立連結，方法是選取來源活動，然後按一下圖形底部的圓形。 將箭頭拖曳到目的地活動和版本。

![建立連結](media/automation-graphical-authoring-intro/create-link-options.png)

在 [組態] 分頁中選取連結來設定其屬性。 屬性包括下表所述的連結類型。

| 連結類型 | 描述 |
|:--- |:--- |
| 管線 |目的地活動會針對從來源活動輸出的每一個物件執行一次。 如果來源活動不會產生任何輸出，則不會執行目的地活動。 從來源活動輸出以物件形式提供。 |
| 順序 |目的地活動只有在收到來源活動的輸出時，才會執行一次。 從來源活動輸出以物件陣列形式提供。 |

### <a name="start-runbook-activity"></a>啟動 Runbook 活動

圖形化 Runbook 會從沒有傳入連結的活動開始。 通常只有一個活動，做為 Runbook 的啟動活動。 如果多個活動沒有傳入連結，Runbook 會以平行執行它們的方式啟動。 每個活動完成時，會遵循連結以執行其他活動。

### <a name="specify-link-conditions"></a>指定連結條件

當您指定連結的條件時，只有條件解析為 True 時才會執行目的地活動。 您通常會在條件中使用 `ActivityOutput` 變數從來源活動擷取輸出。

針對管線連結，您必須指定單一物件的條件。 Runbook 會根據來源活動來評估每個物件輸出的條件。 然後為符合條件的每個物件執行目的地活動。 例如，有了 `Get-AzVM` 的來源活動，您可以對條件式管線連結使用下列語法，便可僅擷取 Group1 資源群組中的虛擬機器。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

針對序列連結，Runbook 只會評估條件一次，因為傳回的單一陣列包含了從來源活動輸出的所有物件。 因此，Runbook 無法將序列連結用於篩選，像使用管線連結進行篩選那樣。 序列連結可以直接判斷下一個活動是否已執行。

例如，在我們的**啟動 VM** Runbook 中有下列這組活動：

![具有順序的條件式連結](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

此 Runbook 會使用三種不同的序列連結來確認輸入參數 `VMName` 和 `ResourceGroupName` 的值，以判斷要採取的適當動作。 可能的動作包括啟動單一 VM、啟動資源群組中的所有 VM、或啟動訂用帳戶中的所有 VM。 `Connect to Azure` 和 `Get single VM` 之間序列連結的條件邏輯如下：

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

使用條件式連結時，可從來源活動提供該分支中的其他活動使用的資料將由條件篩選。 如果活動是多個連結的來源，每個分支中活動可用的資料取決於連線到該分支的連結中的條件。

例如，下列 Runbook 中的 `Start-AzVM` 活動會啟動所有虛擬機器。 它有兩個條件式連結。 第一個條件式連結會使用 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` 運算式來篩選 `Start-AzVM` 活動是否成功完成。 第二個條件式連結會使用 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` 運算式來篩選 `Start-AzVm` 活動是否無法啟動虛擬機器。

![條件式連結範例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

遵循第一個連結並使用 `Get-AzureVM` 活動輸出的任何活動，將只會擷取 `Get-AzureVM` 執行時啟動的虛擬機器。 遵循第二個連結的任何活動，只會取得 `Get-AzureVM` 執行時已停止的虛擬機器。 遵循第三個連結的任何活動，會取得所有虛擬機器，不論其執行狀態為何。

### <a name="use-junctions"></a>使用接合

接合是一個特殊的活動，會等候直到所有傳入的分支完成。 這可讓 Runbook 平行執行多個活動，並確保所有活動已完成再繼續。

雖然接合可以有無限的數量的連入連結，但這些連結中只有一個可以是管線。 內送的順序連結數目不受限制。 您可以使用多個傳入管線連結建立接合，並儲存 Runbook，但是執行時將會失敗。

下列範例是 Runbook 的一個部分，其會啟動一組虛擬機器，同時下載修補程式以套用到這些機器。 這裡使用接合來確保兩個程序都已完成，才繼續 Runbook。

![接合](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>使用循環

當目的地活動連結回到其來源活動，或是走到最後將連結回其來源的另一個活動，便形成一個循環。 圖形化製作目前不支援循環。 如果您的 Runbook 有循環，它可以正確儲存，但是執行時會收到錯誤。

![循環](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>在活動之間共用資料

活動以傳出連結輸出的所有資料皆會寫入 Runbook 的 databus。 Runbook 中的任何活動可以在資料匯流排上使用資料來填入參數值，或納入指令碼程式碼。 活動可以存取工作流程中任何先前的活動的輸出。

資料如何寫入至資料匯流排取決於活動上的連結類型。 管線連結的資料會輸出為多個物件。 序列連結的資料會輸出為一個陣列。 如果只有一個值，則會輸出為單一元素陣列。

您的 runbooR 有兩種方式可存取 databus 上的資料： 
* 使用活動輸出資料來源。
* 使用 PowerShell 運算式資料來源。

第一個機制使用活動輸出資料來源來填入另一個活動的參數。 如果輸出是物件，Runbook 可以指定單一屬性。

![活動輸出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

第二個資料存取機制使用如下語法，在 PowerShell 運算式資料來源中擷取活動的輸出，或使用 `ActivityOutput` 變數來擷取工作流程指令碼活動。 如果輸出是物件，您的 Runbook 可以指定單一屬性。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>使用檢查點

您可以在圖形化 PowerShell 工作流程 Runbook 中設定[檢查點](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)，方法是在任何活動上選取 [檢查點 Runbook]。 這會導致在執行活動之後設定檢查點。

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

檢查點只能在圖形化 PowerShell 工作流程 Runbook 中啟用，無法在圖形化 Runbook 中使用。 如果 Runbook 使用 Azure Cmdlet，則應該跟在具有 `Connect-AzAccount` 活動的任何有檢查點的活動之後。 當 Runbook 暫停，必須在不同背景工作上的這個檢查點重新開機時，就會使用 connect 作業。

## <a name="handle-runbook-input"></a>處理 Runbook 輸入

Runbook 需要來自使用者的輸入內容 (透過 Azure 入口網站啟動 Runbook 時進行的輸入)；如果目前的 Runbook 為子系，則需要來自另一個 Runbook 的輸入內容。 例如，有一個 Runbook 會建立虛擬機器，使用者可能需要在每次啟動此 Runbook 時提供資訊，例如虛擬機器的名稱和其他屬性。

Runbook 會定義一或多個輸入參數，以接受輸入。 使用者會在每次啟動 Runbook 時提供值給這些參數。 當使用者使用 Azure 入口網站啟動 Runbook 時，系統會提示使用者為 Runbook 支援的每個輸入參數提供值。

製作 Runbook 時，您可以按一下 Runbook 工具列上的 [輸入和輸出] 來存取其輸入參數。 這會開啟輸入和輸出控制項，您可以在其中編輯現有的輸入參數，或按一下 [加入輸入] 來建立新的輸入。

![加入輸入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

下表中的屬性定義每個輸入參數：

| 屬性 | 描述 |
|:--- |:--- |
| 名稱 | 必要。 參數名稱。 名稱在 Runbook 內必須是唯一的。 名稱必須以字母開頭，而且只可以包含字母、數字、底線。 名稱不可包含空格。 |
| 描述 |選擇性。 描述輸入參數的用途。 |
| 類型 | 選擇性。 對參數值預期的資料型別。 提示您輸入時，Azure 入口網站會對每個參數提供適當的資料類型控制項。 支援的參數類型有 String、Int32、Int64、Decimal、Boolean、DateTime、Object。 若未選取資料類型，將預設為 String。|
| 強制性 | 選擇性。 此設定指定是否必須為參數提供值。 如果您選擇 `yes`，則在 Runbook 啟動時必須提供值。 如果您選擇 `no`，則在 Runbook 啟動時不一定需要值，且可使用預設值。 如果您沒有為未定義預設值的每個強制參數提供值，則無法啟動 Runbook。 |
| 預設值 | 選擇性。 當 Runbook 啟動時，如未傳入參數值時所使用的的值。 若要設定預設值，請選擇 `Custom`。 若不想提供任何預設值，請選擇 `None`。 |

## <a name="handle-runbook-output"></a>處理 Runbook 輸出

圖形化製作會儲存沒有 [Runbook 的輸出](./automation-runbook-output-and-messages.md)傳出連結的任何活動所建立的資料。 輸出會隨著 Runbook 工作儲存，並且在 Runbook 用作子項時提供給父 Runbook 使用。

## <a name="work-with-powershell-expressions"></a>使用 PowerShell 運算式

圖形化製作的優點之一是可讓您以最少的 PowerShell 知識建立 Runbook。 然後，目前您還是需要稍微了解如何用 PowerShell 填入某些[參數值](#use-activities)和設定[連結條件](#use-links-for-workflow)。 本節快速簡介 PowerShell 運算式。 PowerShell 的完整詳細資料位於 [使用 Windows PowerShell 撰寫指令碼](/powershell/scripting/overview)。

### <a name="use-a-powershell-expression-as-a-data-source"></a>使用 PowerShell 運算式做為資料來源

您可以使用 PowerShell 運算式做為資料來源，使用 PowerShell 程式碼的結果來填入[活動參數](#use-activities)的值。 此運算式可以是執行簡單函式的單行程式碼，或執行一些複雜邏輯的多行程式碼。 未指派給變數的任何命令輸出都會輸出到參數值。

例如，下列命令會輸出目前的日期。

```powershell-interactive
Get-Date
```

下一個程式碼片段會用目前的日期建立字串，並將其指派給變數。 程式碼會將變數的內容傳送至輸出。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

下列命令會評估目前的日期，並傳回表示當天是工作日或週末的字串。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>使用活動輸出

若要在 Runbook 中使用上一個活動的輸出，請以下列語法使用 `ActivityOutput` 變數。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

例如，您可以有一個活動，其屬性需要虛擬機器的名稱。 在此情況下，您的 Runbook 可以使用下列運算式。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

如果屬性需要虛擬機器物件而不只是名稱，Runbook 會使用下列語法傳回整個物件。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook 可在更複雜的運算式中使用活動的輸出，如下所示。 此運算式會將文字串連到虛擬機器名稱。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>比較值

使用 [比較運算子](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) 來比較值或判斷值是否符合指定的模式。 比較會傳回 True 或 False 值。

例如，下列條件會判斷來自 `Get-AzureVM` 活動的虛擬機器目前是否已停止。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

下列條件會判斷相同的虛擬機器是否處於「已停止」以外的任何狀態。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

您可以使用[邏輯運算子](/powershell/module/microsoft.powershell.core/about/about_logical_operators) (例如 `-and` 或 `-or`) 加入多個條件。 例如，下列條件會檢查上述範例中虛擬機器的狀態是否為「已停止」或「正在停止」。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>使用雜湊表

[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)是名稱/值組，很適合用於傳回一組值。 您可能也看過雜湊表被稱為字典。 某些活動的屬性可能是雜湊表而不是簡單值。

使用下列語法建立雜湊表。 雜湊表可以包含任意數目的項目，但是每個項目都由名稱和值定義。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

例如，下列運算式會建立雜湊表，用來做為活動參數的資料來源 (該活動參數需要以用於網際網路搜尋的值所構成的雜湊表)。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

下列範例使用名為 `Get Twitter Connection` 活動的輸出來填入雜湊表。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>向 Azure 資源驗證

管理 Azure 資源之 Azure 自動化中的 Runbook 需要向 Azure 驗證。 [執行身分帳戶](./manage-runas-account.md)也稱為服務主體，是自動化 Runbook 用於存取您訂用帳戶中 Azure Resource Manager 資源的預設機制。 您可以將此功能加入圖形化 Runbook，方法是將使用 PowerShell [Get-AutomationConnection](/system-center/sma/manage-global-assets) Cmdlet 的 `AzureRunAsConnection` 連線資產加入畫布。 您也可以加入 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet。 以下範例說明此案例。

![執行身分驗證活動](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection` 活動 (或 `Get-AutomationConnection`) 是使用名為 `AzureRunAsConnection` 的常數值資料來源進行設定。

![執行身分連線設定](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

下一個活動 `Connect-AzAccount` 會加入已驗證的執行身分帳戶，以便在 Runbook 中使用。

![Connect-AzAccount 參數集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 請注意，您的圖形化 Runbook 無法使用這些別名。 圖形化 Runbook 只能使用 `Connect-AzAccount` 本身。

在 **APPLICATIONID**、**CERTIFICATETHUMBPRINT**、**TENANTID** 參數欄位，請針對欄位路徑指定屬性的名稱，因為活動會輸出具有多個屬性的物件。 否則，當 Runbook 執行時，會在嘗試進行驗證時失敗。 這就是您使用執行身分帳戶驗證 Runbook 時所需的最低限度。

有些訂閱者會使用 [Azure AD 使用者帳戶](./shared-resources/credentials.md)建立自動化帳戶，用於管理 Azure 傳統部署或用於 Azure Resource Manager 資源。 為了維持這些訂閱者的回溯相容性，在 Runbook 中使用的驗證機制是 `Add-AzureAccount` Cmdlet 搭配[認證資產](./shared-resources/credentials.md)。 資產代表可存取 Azure 帳戶的 Active Directory 使用者。

您可以為圖形化 Runbook 啟用這項功能，方法是將認證資產加入畫布，後面接著使用認證資產進行輸入的 `Add-AzureAccount` 活動。 請參閱下列範例。

![驗證活動](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 必須在其啟動時和每個檢查點之後進行驗證。 因此，在任何 `Checkpoint-Workflow` 活動之後，您都必須使用 `Add-AzureAccount` 活動。 您不需要使用額外的認證活動。

![活動輸出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>匯出圖形化 Runbook

您可以只匯出圖形化 Runbook 的已發行版本。 如果尚未發佈 Runbook，[匯出] 按鈕會停用。 當您按一下 [匯出] 按鈕，Runbook 就會下載到您的本機電腦。 檔案名稱須符合帶有 **graphrunbook** 副檔名的 Runbook 名稱。

## <a name="import-a-graphical-runbook"></a>匯入圖形化 Runbook

您可以在加入 Runbook 時選取 [匯入] 選項，藉以匯入圖形化或圖形化 PowerShell 工作流程 Runbook 檔案。 當您選取要匯入的檔案時，可以保留同一個名稱或提供新名稱。 [Runbook 類型] 欄位會在評估選取的檔案之後顯示 Runbook 的類型。 如果您嘗試選取不正確的其他類型，圖形化編輯器會顯示一則訊息，指出有潛在的衝突，而且轉換期間可能會有語法錯誤。

![匯入 Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>測試圖形化 Runbook

Azure 自動化中的每個圖形化 Runbook 都有草稿版本和已發行版本。 您只能執行已發行的版本，且只能編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 當草稿版本就緒可供使用時，您將它發行，草稿版本就會覆寫您目前的已發行版本。

您可以在 Azure 入口網站中測試 Runbook 的草稿版本，此時已發行的 Runbook 版本維持不變。 或者，您可以在發佈新的 Runbook 之前先進行測試，讓您可以在任何版本取代之前確認 Runbook 是否正常運作。 Runbook 的測試會執行草稿版本，並確保其執行的任何動作都已完成。 不會建立任何作業記錄，但 [測試輸出] 窗格會顯示輸出。

開啟圖形化 Runbook 的測試控制項，方法是開啟 Runbook 進行編輯，然後按一下 [測試窗格]。 測試控制項會提示您輸入輸入參數，然後您可以按一下 [開始] 按鈕來啟動 Runbook。

## <a name="publish-a-graphical-runbook"></a>發行圖形化 Runbook

開啟 Runbook 進行編輯，然後按一下 [發行] 按鈕即可發行圖形化 Runbook。 Runbook 的可能狀態如下：

* 新的 -- Runbook 尚未發行。 
* 已發行 -- Runbook 已發行。
* 編輯中 -- 在 Runbook 發行之後又編輯 Runbook，且草稿和已發行版本不同。

![Runbook 狀態](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

您可以選擇還原至 Runbook 的已發行版本。 此作業會擲回 Runbook 上次發行之後所做的任何變更。 它會以已發行的版本取代 Runbook 的草稿版本。

## <a name="next-steps"></a>後續步驟

* 若要開始使用圖形化 Runbook，請參閱[教學課程：建立圖形化 Runbook](learn/automation-tutorial-runbook-graphical.md)。
* 若要深入了解 Runbook 類型及其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 若要了解如何使用自動化執行身分帳戶進行驗證，請參閱[執行身分帳戶](automation-security-overview.md#run-as-account)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation)。
