---
title: Azure 自動化中的圖形化編寫
description: 圖形化編寫可讓您建立 Azure 自動化的 Runbook，而不使用程式碼。 本文章提供圖形化編寫的簡介和開始建立圖形化 Runbook 所需的所有詳細資料。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: cf8ced05066923c94e80628651d8983560601d69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406040"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure 自動化中的圖形化編寫

圖形創作允許您為 Azure 自動化創建 Runbook,而無需基礎 Windows PowerShell 或 PowerShell 工作流代碼的複雜性。 可以從 cmdlet 和 Runbook 庫中向畫布添加活動,將它們連結在一起,並將它們配置為形成工作流。 如果您曾經使用過系統中心協調器或服務管理自動化 (SMA),圖形創作應該看起來很熟悉。 本文介紹了開始創建圖形 Runbook 所需的概念。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="graphical-runbooks"></a>圖形化 Runbook

Azure 自動化中的所有運行簿都是Windows PowerShell工作流。 圖形執行簿和圖形 PowerShell 工作流執行簿生成了自動化工作人員運行但您無法查看或修改的 PowerShell 程式碼。 您可以將圖形執行簿轉換為圖形 PowerShell 工作流運行簿,反之亦然。 但是,無法將這些 Runbook 轉換為文本 Runbook。 此外,自動化圖形編輯器無法導入文本 Runbook。

## <a name="overview-of-graphical-editor"></a>圖形化編輯器概觀

您可以透過建立或編輯圖形化 Runbook，在 Azure 入口網站中開啟圖形化編輯器。

![圖形化工作區](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

下列各節說明圖形化編輯器中的控制項。

### <a name="canvas-control"></a>Canvas 控制項

「畫布」控制程式允許您設計 Runbook。 您可以將活動從庫控制件中的節點添加到 Runbook,並將它們與定義 Runbook 邏輯的連結連接。 在畫布的底部,有允許您放大和縮小的控制項。

### <a name="library-control"></a>程式庫控制項

函式庫「控制件」 允許您選擇要新增到 Runbook[的活動](#activities)。 將它們添加到畫布中,您可以在其中將它們連接到其他活動。 庫控件包括下表中定義的部分。

| 區段 | 描述 |
|:--- |:--- |
| 指令程式 |可在 runbook 中使用的所有 cmdlet。 Cmdlet 是依模組組織。 您在自動化帳戶中安裝的所有模組都可用。 |
| Runbook |自動化帳戶中的 Runbook。 您可以將這些 Runbook 添加到畫布以用作子 Runbook。 僅顯示與正在編輯的 Runbook 相同的核心類型的 Runbook。 對於圖形運行簿,僅顯示基於 PowerShell 的 Runbook。 對於圖形 PowerShell 工作流運行簿,僅顯示基於 PowerShell 工作流的 Runbook。 |
| Assets |可在 Runbook 中使用自動化帳戶中的[自動化資產](/previous-versions/azure/dn939988(v=azure.100))。 將資產添加到 Runbook 會添加獲取所選資產的工作流活動。 如果是變數資產，您可以選取是否要加入活動以取得變數或設定變數。 |
| Runbook 控制項 |控制可在當前 Runbook 中使用的活動。 交匯點活動需要多個輸入,並等待所有完成,然後再繼續工作流。 代碼活動運行一行或多行 PowerShell 或 PowerShell 工作流代碼,具體取決於圖形運行簿類型。 您可以對很難利用其他活動來達成的自訂程式碼或功能使用此活動。 |

### <a name="configuration-control"></a>組態控制項

通過「設定」控件,您可以為在畫布上選擇的物件提供詳細資訊。 此控制項中可用的屬性取決於選取對象的類型。 在「配置」控件中選擇一個選項時,它會打開其他邊欄選項卡以提供詳細資訊。

### <a name="test-control"></a>測試控制項

第一次啟動圖形化編輯器時，不會顯示測試控制項。 當您以互動方式 測試圖形化 Runbook時會開啟。

## <a name="activities"></a>活動

活動是 Runbook 的建置區塊。 活動可以是 PowerShell cmdlet、子執行簿或工作流。 您可以通過在「庫」控制器右鍵單擊 Runbook 並選擇「**新增到畫布**」來將活動添加到 Runbook。 然後可以按一下並拖曳活動，將它放置在畫布上的任何位置。 活動在畫布上的位置不會影響 Runbook 的操作。 您可以以任何您認為最適合可視化其操作的方式佈局 Runbook。

![加入至畫布](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

在「設定」邊欄選項卡中選擇畫布上的活動以配置其屬性和參數。 您可以將活動的標籤更改為您找到描述性的名稱。 Runbook 仍然運行原始 cmdlet。 您只需更改圖形編輯器使用的顯示名稱。 請注意,標籤必須在 Runbook 中是唯一的。

### <a name="parameter-sets"></a>參數集

參數集會定義接受特定 Cmdlet 值的強制參數和選用參數。 所有 cmdlet 都至少有一個參數集,有些具有多個集。 如果 cmdlet 具有多個參數集,則必須選擇要使用的參數,然後才能配置參數。 您可以通過選擇**參數集**並選擇另一個集來更改活動使用的參數集。 在這種情況下,您已配置的任何參數值都丟失。

在下面的示例中[,Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet 有三個參數集。 該示例使用一組名為**ListVirtualMachineInResource GroupParamSet,** 以及單個可選參數,用於返回資源組中的所有虛擬機器。 該範例還使用**獲取虛擬電腦資源組ParamSet**參數集來指定要返回的虛擬機器。 此集有兩個必需參數和一個可選參數。

![參數集](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>參數值

在指定參數的值時，您可以選取資料來源以判斷如何指定值。 可用於特定參數的數據源取決於該參數的有效值。 例如，對於不允許 Null 值的參數而言，Null 並非可用選項。

| 資料來源 | 描述 |
|:--- |:--- |
| 常數值 |輸入參數的值。 此資料來源僅適用於以下資料類型:Int32、Int64、字串、布林、日期時間、交換機。 |
| 活動輸出 |使用工作流中當前活動之前的活動的輸出。 列出所有有效的活動。 對於參數值,僅使用生成輸出的活動。 如果活動輸出具有多個屬性的物件,則可以在選擇活動后鍵入特定屬性的名稱。 |
| Runbook 輸入 |選擇 Runbook 輸入作為活動參數的輸入。 |
| 變數資產 |選擇自動化變數作為輸入。 |
| 認證資產 |選擇自動化憑據作為輸入。 |
| 憑證資產 |選擇自動化證書作為輸入。 |
| 連線資產 |選擇自動化連接作為輸入。 |
| PowerShell 運算式 |指定簡單的[PowerShell 表示式](#powershell-expressions)。 在活動和結果用於參數值之前計算表達式。 您可以使用變數來參照活動或 Runbook 的輸入參數的輸出。 |
| 尚未設定 |清除以前配置的任何值。 |

#### <a name="optional-additional-parameters"></a>選擇性的其他參數

所有 Cmdlet 可選擇提供額外的參數。 這些是 PowerShell 通用參數或其他自定義參數。 圖形編輯器提供一個文字框,您可以在其中使用 PowerShell 語法提供參數。 例如,要使用`Verbose`公共參數,應`-Verbose:$True`指定 。

### <a name="retry-activity"></a>重試活動

活動的重試功能允許它多次運行,直到滿足特定條件,就像迴圈一樣。 您可以將此功能用於應多次運行、容易出錯、可能需要多次嘗試才能成功的活動,或者測試活動的輸出資訊以查找有效數據。

當您對活動啟用重試時，您可以設定延遲和條件。 延遲是 Runbook 再次執行活動之前所等待的時間 (以秒或分鐘計算)。 如果未指定延遲,則活動在完成後立即再次運行。

![活動重試延遲](media/automation-graphical-authoring-intro/retry-delay.png)

重試條件是每次運行活動後計算的 PowerShell 運算式。 如果表達式解析為 True,則活動將再次運行。 如果表達式解析為 False,則活動不會再次運行,並且 Runbook 將移動到下一個活動。

![活動重試延遲](media/automation-graphical-authoring-intro/retry-condition.png)

重試條件可以使用名為`RetryData`的 變數,該變數提供對有關活動重試的資訊的訪問。 此變數具有下表中的屬性：

| 屬性 | 描述 |
|:--- |:--- |
| `NumberOfAttempts` |活動已執行的次數。 |
| `Output` |活動上次執行的輸出。 |
| `TotalDuration` |活動第一次開始之後的經過時間。 |
| `StartedAt` |首次啟動活動的時間(UTC 格式)。 |

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

設定活動的重試條件之後，該活動便會包含兩個視覺提示來提醒您。 顯示在活動中,另一個顯示在您查看活動的設定時。

![活動重試視覺指示器](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流程指令碼控制項

工作流腳本控件是一種特殊活動,它接受 PowerShell 或 PowerShell 工作流腳本,具體取決於所創作的圖形運行簿的類型。 此控制項提供可能透過其他方式不可用的功能。 它不能接受參數，但它可以對活動輸出和 Runbook 輸入參數使用變數。 活動的任何輸出都添加到數據總線。 一個例外是輸出,沒有傳出連結,在這種情況下,輸出將添加到 Runbook 的輸出中。

例如,以下代碼使用名為`NumberOfDays`的 Runbook 輸入變數執行日期計算。 然後,它將計算的 DateTime 值作為輸出,用於 Runbook 中的後續活動。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>連結和工作流程

圖形化 Runbook 中的 連結 在連接兩個活動。 就會在畫布上顯示為箭號，從來源活動指向目的地活動。 活動會以箭頭的方向執行，在來源活動完成之後，目的地活動就會啟動。

### <a name="link-creation"></a>連結建立

您可以通過選擇源活動並按下形狀底部的圓圈,在兩個活動之間創建連結。 將箭頭拖曳到目的地活動和版本。

![建立連結](media/automation-graphical-authoring-intro/create-link-options.png)

在 [組態] 分頁中選取連結來設定其屬性。 屬性包括連結類型,下表中介紹。

| 連結類型 | 描述 |
|:--- |:--- |
| 管線 |對於源活動的每個物件輸出,目標活動運行一次。 如果來源活動不會產生任何輸出，則不會執行目的地活動。 從來源活動輸出以物件形式提供。 |
| 順序 |目標活動在從源活動接收輸出時僅運行一次。 從來源活動輸出以物件陣列形式提供。 |

### <a name="start-of-activity"></a>活動開始

圖形化 Runbook 會從沒有傳入連結的活動開始。 通常只有一個活動充當 Runbook 的起始活動。 如果多個活動沒有傳入連結,則 Runbook 首先並行運行它們。 每個活動完成時，會遵循連結以執行其他活動。

### <a name="link-conditions"></a>連結準則

在連結上指定條件時,僅當條件解析為 True 時,目標活動才會運行。 通常使用條件中的`ActivityOutput`變數從源活動檢索輸出。

對於管道連結,必須為單個物件指定條件。 Runbook 按源活動計算每個物件輸出的條件。 然後,它運行滿足條件的每個對象的目標活動。 例如,對於源活動`Get-AzVM`,可以使用以下語法進行條件管道連結,以僅檢索名為 Group1 的資源組中的虛擬機。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

對於序列連結,Runbook 只計算條件一次,因為返回包含源活動中所有對象的單個陣列。 因此,Runbook 不能使用序列鏈接進行篩選,就像使用管道連結一樣。 序列連結可以簡單地確定是否運行下一個活動。

例如,在我們的 **「開始」VM**執行簿中獲取以下一組活動:

![具有順序的條件式連結](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook 使用三個不同的序列鏈接來驗證`VMName`輸入參數 的`ResourceGroupName`值並確定 要執行的適當操作。 可能的操作是啟動單個 VM、啟動資源群組中的所有 VM 或啟動訂閱中的所有 VM。 對於`Get single VM`與`Connect to Azure`之間的序列連結,下面是條件邏輯:

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

使用條件式連結時，可從來源活動提供該分支中的其他活動使用的資料將由條件篩選。 如果活動是多個連結的源,則每個分支中活動可用的數據取決於連接到該分支的連結中的條件。

例如,`Start-AzVM`下面的 Runbook 中的活動將啟動所有虛擬機器。 它有兩個條件式連結。 如果`Start-AzVM`活動成功完成,第`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`一個條件連結使用表達式進行篩選。 如果活動無法啟動虛擬機器,`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true``Start-AzVm`則第二個條件連結使用表達式進行篩選。

![條件式連結範例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

第一個連結之後使用活動輸出的任何活動`Get-AzureVM`將僅檢索運行時`Get-AzureVM`啟動 的虛擬機器。 第二個連結之後的任何活動僅獲取運行時停止`Get-AzureVM`的虛擬機。 遵循第三個連結的任何活動，會取得所有虛擬機器，不論其執行狀態為何。

### <a name="junctions"></a>接合

接合是一個特殊的活動，會等候直到所有傳入的分支完成。 這允許 Runbook 並行運行多個活動,並確保所有活動在繼續之前都已完成。

雖然交匯點可以具有無限數量的傳入鏈路,但這些鏈路中只有一個可以是管道。 內送的順序連結數目不受限制。 您可以建立具有多個傳入管道連結的交匯點並保存 Runbook,但在執行時它將失敗。

下列範例是 Runbook 的一個部分，其會啟動一組虛擬機器，同時下載修補程式以套用到這些機器。 它使用一個交匯點來確保在 Runbook 繼續之前完成這兩個進程。

![接合](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

當目標活動連結回其源活動或最終連結回其源的另一個活動時,將形成一個迴圈。 圖形創作當前不支援週期。 如果您的 Runbook 有循環，它可以正確儲存，但是執行時會收到錯誤。

![循環](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>活動之間的資料共享

具有傳出連結的活動輸出的任何數據都寫入 Runbook 的數據總線。 Runbook 中的任何活動可以在資料匯流排上使用資料來填入參數值，或納入指令碼程式碼。 活動可以存取工作流程中任何先前的活動的輸出。

資料如何寫入至資料匯流排取決於活動上的連結類型。 對於管道鏈接,數據作為多個物件輸出。 針對 順序 連結，資料會輸出為陣列。 如果只有一個值,則將其輸出為單元素陣列。

Runbook 有兩種方法可以訪問數據總線上的數據: 
* 使用活動輸出數據源。
* 使用 PowerShell 表示式數據來源。

第一個機制使用活動輸出數據源填充另一個活動的參數。 如果輸出是物件,則 Runbook 可以指定單個屬性。

![活動輸出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

第二個數據訪問機制使用如下所示的語法檢索 PowerShell 運算式資料來源中活動的輸出`ActivityOutput`或具有變數的工作流腳本活動的輸出。 如果輸出是物件,則 Runbook 可以指定單個屬性。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>檢查點

您可以通過選擇任何作用的**檢查點執行簿**,在圖形 PowerShell 工作流 Runbook 中設定[檢查點](automation-powershell-workflow.md#checkpoints)。 這會導致在執行活動之後設定檢查點。

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

檢查點僅在圖形 PowerShell 工作流執行簿中啟用,在圖形運行簿中不可用。 如果 Runbook 使用 Azure cmdlet,`Connect-AzAccount`它應遵循具有 活動的任何檢查點活動。 連接操作用於運行簿掛起且必須從其他工作人員上的此檢查點重新啟動。

## <a name="runbook-input-and-output"></a>Runbook 輸入和輸出

### <a name="runbook-input"></a>Runbook 輸入<a name="runbook-input"></a>

Runbook 需要從通過 Azure 門戶啟動 Runbook 的使用者或從另一個 Runbook(如果當前運行簿用作子簿)輸入。 例如,對於創建虛擬機器的 Runbook,使用者可能需要在每次 Runbook 啟動時提供虛擬機的名稱和其他屬性等資訊。

Runbook 通過定義一個或多個輸入參數來接受輸入。 每次 Runbook 啟動時,用戶都會提供這些參數的值。 當使用者使用 Azure 門戶啟動 Runbook 時,系統會提示使用者為 Runbook 支援的每個輸入參數提供值。

創作 Runbook 時,可以通過單擊 Runbook 工具列上的 **「輸入」和「輸出**」來訪問其輸入參數。 這會開啟 [輸入和輸出] 控制項，您可以在其中編輯現有的輸入參數，或按一下 [加入輸入]**** 來建立一個新的。

![加入輸入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

下表中的屬性定義每個輸入參數：

| 屬性 | 描述 |
|:--- |:--- |
| 名稱 | 必要。 參數名稱。 該名稱在 Runbook 中必須是唯一的。 它必須以字母開頭,並且只能包含字母、數位和下劃線。 名稱不能包含空格。 |
| 描述 |選擇性。 輸入參數的用途說明。 |
| 類型 | 選擇性。 對參數值預期的資料型別。 提示您輸入時，Azure 入口網站會對每個參數提供適當的資料類型控制項。 支援的參數類型有：String、Int32、Int64、Decimal、Boolean、DateTime、Object。 若未選取資料類型，將預設為 String。|
| 強制性 | 選擇性。 指定是否必須為參數提供值的設置。 如果選擇`yes`,則必須在啟動 Runbook 時提供值。 如果選擇`no`,則在啟動 Runbook 時不需要值,並且可以使用預設值。 如果不為每個未定義預設值的必需參數提供值,則 Runbook 無法啟動。 |
| 預設值 | 選擇性。 在啟動 Runbook 時未傳入參數時用於參數的值。 要設定預設值,請選擇`Custom`。 如果`None`不想提供任何預設值,請選擇該選項。 |

### <a name="runbook-output"></a>Runbook 輸出

圖形創作保存由沒有傳出連結到[Runbook 輸出](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)的任何活動創建的資料。 輸出會隨著 Runbook 工作儲存，並且在 Runbook 用作子項時提供給父 Runbook 使用。

## <a name="powershell-expressions"></a>PowerShell 運算式

圖形創作的優點之一是,它允許您構建一個運行簿,對 PowerShell 的瞭解最少。 不過,目前,您確實需要瞭解一些 PowerShell 來填充某些[參數值](#activities)並設定[連結條件](#links-and-workflow)。 本節簡要介紹 PowerShell 表達式。 PowerShell 的完整詳細資料位於 [使用 Windows PowerShell 撰寫指令碼](https://technet.microsoft.com/library/bb978526.aspx)。

### <a name="powershell-expression-data-source"></a>PowerShell 運算式資料來源

您可以使用 PowerShell 表示式作為數據源,使用 PowerShell 程式碼的結果填充[活動參數](#activities)的值。 表達式可以是執行簡單函數的單行代碼,也可以是執行某些複雜邏輯的多行。 未指派給變數的任何命令輸出都會輸出到參數值。

例如,以下命令輸出當前日期。

```powershell-interactive
Get-Date
```

下一個代碼段從當前日期生成字串並將其分配給變數。 代碼將變數的內容發送到輸出。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

以下命令評估當前日期並返回一個字串,指示當前日期是週末還是工作日。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>活動輸出

要使用 Runbook 中以前活動的輸出,請使用具有以下`ActivityOutput`語法的變數。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

例如,可以具有需要虛擬機名稱的屬性的活動。 在這種情況下,runbook 可以使用以下表達式。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

如果屬性需要虛擬機物件,而不是僅名稱,則 Runbook 使用以下語法返回整個物件。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook 可以在更複雜的運算式中使用活動的輸出,如下所示。 此運算式將文字串聯到虛擬機器名稱。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>條件

使用 [比較運算子](https://technet.microsoft.com/library/hh847759.aspx) 來比較值或判斷值是否符合指定的模式。 比較返回"真"或"真"的值。

例如,以下條件確定來自命名`Get-AzureVM`活動的虛擬機當前是否停止。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

以下條件確定同一虛擬機是否處於停止以外的任何狀態。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

您可以使用[邏輯運算子](https://technet.microsoft.com/library/hh847789.aspx)(如`-and``-or`) 聯接 Runbook 中的多個條件。 例如,以下條件檢查上例中的虛擬機是否處於"停止"或"停止"狀態。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>雜湊表

[哈希表](https://technet.microsoft.com/library/hh847780.aspx)是名稱值對,可用於返回一組值。 您可能還會看到稱為字典的哈希表。 某些活動的屬性需要可哈希值而不是簡單值。

使用以下語法創建哈希表。 它可以包含任意數量的條目,但每個條目由名稱和值定義。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

例如,以下運算式創建一個哈希錶,用作活動參數的數據源,該參數需要 Internet 搜索的值可哈希。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

下面的範例使用調用`Get Twitter Connection`的活動的輸出來填充哈希表。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>向 Azure 資源驗證

管理 Azure 資源之 Azure 自動化中的 Runbook 需要向 Azure 驗證。 [運行 As 帳戶](automation-create-runas-account.md)(也稱為服務主體)是自動化 Runbook 用於造訪訂閱中的 Azure 資源管理器資源的預設機制。 通過將使用 PowerShell`AzureRunAsConnection`[獲取-自動化連接](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx)cmdlet 的連接資產添加到畫布中,可以將此功能添加到圖形 Runbook。 您還可以添加[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet。 以下示例說明了此方案。

![執行身分驗證活動](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

活動`Get Run As Connection``Get-AutomationConnection`或 配置了`AzureRunAsConnection`名為 的常量值數據源。

![執行身分連線設定](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

下一個活動`Connect-AzAccount`, 添加經過身份認證的 Run As 帳戶,用於 runbook 中。

![連線-Azaccount 參數集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>對於 PowerShell`Add-AzAccount``Add-AzureRMAccount`執行簿`Connect-AzAccount`,並且是 的別名。 請注意,這些別名不適用於圖形運行簿。 圖形運行簿只能使用`Connect-AzAccount`自身。

對於參數位段 **「應用ID」、「****證書」** 和 **「TENANTID」,** 指定欄位路徑的屬性的名稱,因為活動輸出具有多個屬性的物件。 否則,當 Runbook 執行時,它嘗試進行身份驗證時失敗。 這就是您使用執行身分帳戶驗證 Runbook 時所需的最低限度。

某些訂閱者使用 Azure AD[使用者帳戶](automation-create-aduser-account.md)創建自動化帳戶來管理 Azure 經典部署或 Azure 資源管理器資源。 為了維護這些訂閱者的向後相容性,runbook 中使用的身份驗證機制是`Add-AzureAccount`具有[憑據資產](automation-credentials.md)的 cmdlet。 該資產表示具有 Azure 帳戶訪問許可權的活動目錄使用者。

您可以通過向畫布添加憑據資產,然後將憑據資產用於其`Add-AzureAccount`輸入的活動,為圖形 Runbook 啟用此功能。 請參閱下列範例。

![驗證活動](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 必須在其開始和之後在每個檢查點後進行身份驗證。 因此,您必須在任何`Add-AzureAccount``Checkpoint-Workflow`活動之後使用活動。 您無需使用其他認證活動。

![活動輸出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>匯出和匯入圖形化 Runbook

您可以只匯出圖形化 Runbook 的已發行版本。 如果運行簿尚未發佈,則禁用 **「匯出」** 按鈕。 按下「**匯出」** 按鈕時,Runbook 將下載到本地電腦。 檔的名稱將 runbook 的名稱與 **.graphrunbook**副檔名匹配。

您可以透過在新增 Runbook 時選擇 **「導入**」選項來匯入圖形或圖形 PowerShell 工作流執行簿檔。 選擇要導入的檔時,可以保留相同的名稱或提供新名稱。 **Runbook 類型**欄位在評估選取的檔案後顯示 Runbook 的類型。 如果嘗試選擇不正確的其他類型,圖形編輯器將顯示一條消息,指出存在潛在衝突,並且轉換期間可能存在語法錯誤。

![匯入 Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>測試圖形化 Runbook

Azure 自動化中的每個圖形運行簿都有草稿版本和已發佈版本。 您只能運行已發佈版本,而只能編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 當草稿版本準備好使用時,您可以發佈它,這將用草稿版本覆蓋當前發佈的版本。

您可以在 Azure 門戶中測試 Runbook 的草稿版本,同時保持已發佈版本不變。 或者,您可以在新 Runbook 發布之前對其進行測試,以便在任何版本替換之前驗證 Runbook 是否正常工作。 Runbook 的測試將執行草稿版本,並確保完成它執行的任何操作。 未創建作業歷史記錄,但「測試輸出」窗格顯示輸出。

以開啟 Runbook 進行編輯,然後按下「測試」窗格,開啟圖形執行簿的 **「測試」控制件**。 測試「控制件提示輸入參數,您可以透過按下 **」開始「啟動**Runbook 來啟動 Runbook。

## <a name="publishing-a-graphical-runbook"></a>發行圖形化 Runbook

通過打開 Runbook 進行編輯,然後單擊 **「發布**」來發佈圖形運行簿。 Runbook 的可能狀態為:

* 新的 - 運行手冊尚未發佈。 
* 已發佈 -- 執行簿已發佈。
* 在編輯中 - Runbook 在發佈後進行了編輯,草稿和已發佈版本也不同。

![Runbook 狀態](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

您可以選擇還原到 Runbook 的已發表版本。 此操作將放棄自上次發佈 Runbook 以來所做的任何更改。 它將 Runbook 的草稿版本替換為已發佈版本。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)。
* 要開始使用圖形執行簿,請參閱[我的第一個圖形執行簿](automation-first-runbook-graphical.md)。
* 要瞭解有關 Runbook 類型及其優點和限制的更多資訊,請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 要瞭解如何使用「作為帳戶」進行自動化執行進行身份驗證,請參閱[設定 Azure 執行作為帳戶](automation-sec-configure-azure-runas-account.md)。
* 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
