---
title: 自動調整 Azure Batch 集區中的計算節點
description: 在雲端集區上啟用自動調整，以動態調整集區中的計算節點數目。
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 033272f22b98b27c67e9a551bce952368d35a043
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95737287"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>建立自動公式以調整 Batch 集區中的計算節點

Azure Batch 可以根據您定義的參數自動調整集區，以節省您的時間和金錢。 使用自動調整時，Batch 會在工作需求增加時，以動態方式將節點新增至集區，並在工作需求降低時移除計算節點。

若要在計算節點集區上啟用自動調整，您可以將集區與您定義的 *自動調整公式* 產生關聯。 Batch 服務會使用自動調整公式來判斷執行工作負載所需的節點數目。 這些節點可能是專用節點或 [低優先順序節點](batch-low-pri-vms.md)。 Batch 會接著定期審核服務計量資料，並使用它來根據您的公式和您所定義的間隔來調整集區中的節點數目。

您可以在建立集區時啟用自動調整，或將它套用至現有的集區。 Batch 可讓您在將公式指派給集區之前評估公式，以及監視自動調整回合的狀態。 當您設定具有自動調整的集區之後，您可以稍後再對公式進行變更。

> [!IMPORTANT]
> 當您建立 Batch 帳戶時，您可以指定 [集區配置模式](accounts.md)，以決定是否在 Batch 服務訂用帳戶中組態集區 (預設) 或您的使用者訂用帳戶中。 如果您使用預設的 Batch 服務設定建立 Batch 帳戶，則您的帳戶會限制為可用於處理的核心數目上限。 Batch 服務只會將計算節點調整為最多達到該核心限制。 基於這個理由，Batch 服務不會達到自動調整公式所指定的目標計算節點數目。 如需檢視和增加帳戶配額的相關資訊，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md) 。
>
>如果您建立了具有使用者訂用帳戶模式的帳戶，則您的帳戶會以訂用帳戶的核心配額來共用。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)中的 [虛擬機器限制](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits)。

## <a name="autoscale-formulas"></a>自動調整公式

自動調整公式是您定義的字串值，其中包含一個或多個語句。 自動調整公式會指派給集區的 [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) 元素 (Batch REST) 或 [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) 屬性 (Batch .NET)。 Batch 集區會使用您的公式來決定集區中可供下一個間隔處理的目標計算節點數目。 公式字串不能超過 8 KB，最多可包含100語句（以分號分隔），並可包含分行符號和批註。

您可以將自動調整公式視為 Batch 自動調整「語言」。 公式語句是一種無格式的運算式，可同時包含) 和使用者定義變數 (由 Batch 服務定義的服務定義變數。 公式可以使用內建類型、運算子和函數，對這些值執行各種作業。 例如，陳述式可能會採用下列格式：

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

公式通常包含多個陳述式，其可對在先前陳述式中取得的值執行作業。 例如，首先我們會取得 `variable1` 的值，然後將它傳遞至函式以填入 `variable2`：

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

在您的自動調整公式中包含這些陳述式，以達成目標計算節點數目。 專用節點和低優先順序節點各有自己的目標設定。 自動調整公式可以包含專用節點目標值、低優先順序節點目標值，或同時包含兩者。

目標節點數目可能會更高、更低，或與集區中該類型目前的節點數目相同。 Batch 會以特定的 [自動調整間隔](#automatic-scaling-interval)評估集區的自動調整公式。 Batch 會將集區中每個節點類型的目標數目調整為自動調整公式在評估時指定的數目。

### <a name="sample-autoscale-formulas"></a>自動調整公式範例

以下是兩個自動調整公式的範例，可調整以適用於大部分情況。 您可以依照需求調整範例公式中的 `startingNumberOfVMs` 和 `maxNumberofVMs` 變數。

#### <a name="pending-tasks"></a>暫止工作

使用此自動調整公式，一開始會建立包含單一 VM 的集區。 `$PendingTasks` 計量會定義執行中或已排入佇列的工作數目。 公式會尋找過去 180 秒內的平均擱置中工作數目，並據以設定 `$TargetDedicatedNodes` 變數。 公式會確保目標專用節點數目絕不會超出 25 部 VM。 集區會隨著新工作的提交而自動成長。 當工作完成時，Vm 就會變成可用，且自動調整公式會縮減集區。

此公式會調整專用節點，但是可加以修改，套用來調整低優先順序節點。

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>先佔節點

這個範例建立的集區最初有 25 個低優先順序節點。 低優先順序節點每次被先佔就會換成專用節點。 在第一個範例中，`maxNumberofVMs` 變數會防止集區超過 25 個 VM。 這個範例有助於使用低優先順序 VM，同時確保集區的存留期只發生固定次數的先佔。

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

您將深入瞭解 [如何建立自動調整公式](#write-an-autoscale-formula) ，以及稍後在本主題中如何查看其他 [範例自動調整公式](#example-autoscale-formulas) 。

## <a name="variables"></a>變數

您可以在自動調整公式中同時使用 **服務定義** 和 **使用者定義** 的變數。

服務定義的變數內建在 Batch 服務中。 有些服務定義的變數是讀寫，有些是唯讀。

使用者定義的變數是您定義的變數。 在上面顯示的範例公式中， `$TargetDedicatedNodes` 和 `$PendingTasks` 是服務定義的變數，而 `startingNumberOfVMs` 和 `maxNumberofVMs` 是使用者定義的變數。

> [!NOTE]
> 服務定義的變數前面一律會加上貨幣符號 ($)。 針對使用者定義的變數而言，貨幣符號是選擇性的。

下表顯示 Batch 服務所定義的讀寫和唯讀變數。

### <a name="read-write-service-defined-variables"></a>讀寫服務定義變數

您可以取得並設定這些服務定義變數的值，以管理集區中的計算節點數目。

| 變數 | 描述 |
| --- | --- |
| $TargetDedicatedNodes |集區之專用計算節點的目標數目。 這會指定為目標，因為集區不一定會達到想要的節點數目。 例如，如果在集區達到初始目標之前，自動調整評估會修改目標專用節點數目，則集區可能不會到達目標。 <br /><br /> 如果目標超過 Batch 帳戶節點或核心配額，在 Batch 服務模式中建立的帳戶集區可能無法達到其目標。 如果目標超過訂用帳戶的共用核心配額，在使用者訂用帳戶模式中建立的帳戶集區可能無法達到其目標。|
| $TargetLowPriorityNodes |集區之低優先順序計算節點的目標數目。 這會指定為目標，因為集區不一定會達到想要的節點數目。 例如，如果在集區達到初始目標之前，自動調整評估會修改低優先順序節點的目標數目，則集區可能不會到達目標。 如果目標超過 Batch 帳戶節點或核心配額，則集區也可能未達到其目標。 <br /><br /> 如需低優先順序計算節點的詳細資訊，請參閱[使用低優先順序 VM 搭配 Batch](batch-low-pri-vms.md)。 |
| $NodeDeallocationOption |計算節點從集區移除時所發生的動作。 可能的值包括：<ul><li>**requeue**：預設值。 立即結束工作，並將它們放回工作佇列，以便重新排程。 此動作可確保達到最快的目標節點數目。 不過，它可能較不有效率，因為任何執行中的工作將會中斷，而且必須完全重新開機。 <li>**terminate**：立即結束工作，並將它們從工作佇列中移除。<li>**taskcompletion**：等候目前正在執行的工作完成，然後將節點從集區中移除。 使用這個選項可避免工作中斷並重新排入佇列，並浪費工作完成的任何工作。<li>**retaineddata**：等候節點上的所有本機工作保留資料清除，然後再從集區中移除節點。</ul> |

> [!NOTE]
> 您也可以使用別名 `$TargetDedicated` 來指定 `$TargetDedicatedNodes` 變數。 同樣地，您可以使用別名 `$TargetLowPriority` 來指定 `$TargetLowPriorityNodes` 變數。 如果完整命名的變數及其別名都由公式設定，則以指派給完整命名變數的值為優先。

### <a name="read-only-service-defined-variables"></a>唯讀服務定義變數

您可以取得這些服務定義變數的值，以根據 Batch 服務中的計量進行調整。

> [!IMPORTANT]
> 作業釋放工作目前不包含在提供工作計數的變數中，例如 $ActiveTasks 和 $PendingTasks。 根據您的自動調整公式，這可能會導致節點移除，而不會有可用的節點來執行作業釋放工作。

| 變數 | 描述 |
| --- | --- |
| $CPUPercent |CPU 使用量的平均百分比。 |
| $WallClockSeconds |已耗用的秒數。 |
| $MemoryBytes |已使用的平均 MB 數目。 |
| $DiskBytes |已在本機磁碟上使用的平均 GB 數目。 |
| $DiskReadBytes |讀取的位元組數。 |
| $DiskWriteBytes |已寫入的位元組數目。 |
| $DiskReadOps |已執行的讀取磁碟作業計數。 |
| $DiskWriteOps |已執行的寫入磁碟作業計數。 |
| $NetworkInBytes |輸入位元組的數目。 |
| $NetworkOutBytes |輸出位元組的數目。 |
| $SampleNodeCount |計算節點的計數。 |
| $ActiveTasks |準備好執行但還未執行的工作數目。 這包括所有處於作用中狀態且已滿足其相依性的工作。 處於作用中狀態但未滿足其相依性的任何工作會從 $ActiveTasks 計數排除。 若為多重執行個體工作，$ActiveTasks 將包含工作上設定的執行個體數目。|
| $RunningTasks |處於執行中狀態的工作數目。 |
| $PendingTasks |$ActiveTasks 和 $RunningTasks 的總和。 |
| $SucceededTasks |已成功完成的工作數目。 |
| $FailedTasks |失敗的工作數目。 |
| $CurrentDedicatedNodes |目前的專用計算節點數目。 |
| $CurrentLowPriorityNodes |目前的低優先順序計算節點數目，包括任何已先佔的節點。 |
| $PreemptedNodeCount | 優先佔用狀態的集區中之節點數目。 |

> [!TIP]
> 這些唯讀服務定義變數是提供各種方法來存取每個相關聯資料的 *物件* 。 如需詳細資訊，請參閱本文稍後的[取得範例資料](#obtain-sample-data)。

> [!NOTE]
> 在 `$RunningTasks` 根據某個時間點執行的工作數目進行調整時，以及 `$ActiveTasks` 根據已排入執行佇列的工作數目進行調整時使用。

## <a name="types"></a>類型

自動調整公式支援下列類型：

- double
- doubleVec
- doubleVecList
- 字串
- timestamp--包含下列成員的複合結構：
  - year
  - month (1-12)
  - day (1-31)
  - weekday (以數字格式表示，例如 1 代表星期一)
  - hour (以 24 小時制數字格式表示，例如 13 代表下午 1:00)
  - minute (00-59)
  - second (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>作業

上一節列出的類型允許這些作業。

| 作業 | 支援的運算子 | 結果類型 |
| --- | --- | --- |
| double 運算子  double |+、-、*、/ |double |
| double 運算子  timeinterval |* |timeinterval |
| doubleVec 運算子  double |+、-、*、/ |doubleVec |
| doubleVec 運算子  doubleVec |+、-、*、/ |doubleVec |
| timeinterval 運算子  double |*, / |timeinterval |
| timeinterval 運算子  timeinterval |+、 - |timeinterval |
| timeinterval 運算子  timestamp |+ |timestamp |
| timestamp 運算子  timeinterval |+ |timestamp |
| timestamp  timestamp |- |timeinterval |
| *運算子* double |-, ! |double |
| *運算子* timeinterval |- |timeinterval |
| double 運算子  double |<、<=、==、>=、>、!= |double |
| string  string |<、<=、==、>=、>、!= |double |
| timestamp  timestamp |<、<=、==、>=、>、!= |double |
| timeinterval 運算子  timeinterval |<、<=、==、>=、>、!= |double |
| double 運算子  double |&&, &#124;&#124; |double |

測試具有三元運算子的雙精準數 (`double ? statement1 : statement2`) 時，非零為 **true**，而零則為 **false**。

## <a name="functions"></a>函式

您可以在定義自動調整公式時使用這些預先定義的 **函數** 。

| 函式 | 傳回類型 | 描述 |
| --- | --- | --- |
| avg(doubleVecList) |double |傳回 doubleVecList 中所有值的平均值。 |
| len(doubleVecList) |double |傳回 doubleVecList 建立的向量的長度。 |
| lg(double) |double |傳回 double 的對數底數 2。 |
| lg(doubleVecList) |doubleVec |傳回 doubleVecList 的全元件對數底數 2。 vec(double) 必須針對此參數明確傳遞。 否則會假設為 double lg(double) 版本。 |
| ln(double) |double |傳回 double 的自然底數。 |
| ln(doubleVecList) |doubleVec |傳回 double 的自然底數。 |
| log(double) |double |傳回 double 的對數底數 10。 |
| log(doubleVecList) |doubleVec |傳回 doubleVecList 的全元件對數底數 10。 vec(double) 必須針對單一 double 參數明確傳遞。 否則，會假設為 double log (double) 版本。 |
| max(doubleVecList) |double |傳回 doubleVecList 中的最大值。 |
| min(doubleVecList) |double |傳回 doubleVecList 中的最小值。 |
| norm(doubleVecList) |double |傳回 doubleVecList 建立的向量的 2-norm。 |
| percentile(doubleVec v, double p) |double |傳回向量 v 的百分位數元素。 |
| rand() |double |傳回介於 0.0 到 1.0 之間的隨機值。 |
| range(doubleVecList) |double |傳回 doubleVecList 中最小和最大值之間的差異。 |
| std(doubleVecList) |double |傳回 doubleVecList 中值的標準差範例。 |
| stop() | |停止評估自動調整運算式。 |
| sum(doubleVecList) |double |傳回 doubleVecList 所有元件的總和。 |
| time(string dateTime="") |timestamp |如果未傳遞任何參數，則傳回目前時間的時間戳記，如果傳遞，則傳回 dateTime 字串的時間戳記。 支援的 dateTime 格式為 W3C-DTF 和 RFC 1123。 |
| val(doubleVec v, double i) |double |傳回向量 v 中位置 i 的元素值，起始索引為零。 |

上表中所述的某些函式可以接受清單作為引數。 逗號分隔清單是 *double* 和 *doubleVec* 的任意組合。 例如：

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

評估之前，*doubleVecList* 值會轉換成單一的 *doubleVec*。 例如，如果 `v = [1,2,3]`，呼叫 `avg(v)` 就相當於呼叫 `avg(1,2,3)`。 呼叫 `avg(v, 7)` 就相當於呼叫 `avg(1,2,3,7)`。

## <a name="metrics"></a>計量

您可以在定義公式時使用資源和工作計量。 您會根據您取得和評估的度量資料來調整集區中專用節點的目標數目。 如需每個度量的詳細資訊，請參閱上述的 [變數](#variables) 一節。

<table>
  <tr>
    <th>Metric</th>
    <th>描述</th>
  </tr>
  <tr>
    <td><b>Resource</b></td>
    <td><p>資源計量是以計算節點的 CPU、頻寬和記憶體使用量以及節點數目為基礎。</p>
        <p> 這些服務定義的變數適合用於根據節點計數進行調整：</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>這些服務定義的變數適合用於根據節點資源使用量進行調整：</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Task</b></td>
    <td><p>工作計量是根據工作的狀態，例如作用中、暫止和已完成。 下列服務定義的變數適合用於根據工作度量進行集區大小調整：</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>取得樣本資料

自動調整公式的核心作業是)  (範例取得工作和資源度量資料，然後根據該資料來調整集區大小。 因此，請務必清楚瞭解自動調整公式如何與範例互動。

### <a name="methods"></a>方法

自動調整公式會針對 Batch 服務所提供的計量資料樣本進行動作。 公式會根據所取得的值來增加或縮減集區大小。 服務定義變數是提供方法來存取與該物件相關聯之資料的物件。 例如，下列運算式顯示取得最後五分鐘的 CPU 使用量的要求：

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

您可以使用下列方法來取得有關服務定義變數的範例資料。

| 方法 | 描述 |
| --- | --- |
| GetSample() |`GetSample()` 方法會傳回資料樣本的向量。<br/><br/>一個樣本有 30 秒的度量資料。 換句話說，每隔 30 秒會取得範例。 如下所述，從收集樣本到可用於公式之間會延遲。 因此，並非一段指定時間內的所有樣本都可供公式評估。<ul><li>`doubleVec GetSample(double count)`：指定要從收集的最新樣本中取得的樣本數。 `GetSample(1)` 會傳回最後一個可用的樣本。 但不應該使用像這樣的計量， `$CPUPercent` `GetSample(1)` 因為無法知道收集樣本的 *時間* 。 這可能是最新的，或是因為系統問題，可能會比以往更舊。 在這種情況下，最好使用如下所示的時間間隔。<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`：指定用來收集範例資料的時間範圍。 它也會選擇性地指定在要求的時間範圍內必須可用的樣本數百分比。 例如， `$CPUPercent.GetSample(TimeInterval_Minute * 10)` 如果歷程記錄中有最後10分鐘的所有樣本，則會傳回20個樣本 `CPUPercent` 。 如果歷程記錄的最後一分鐘未提供，則只會傳回18個樣本。 在此情況下 `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` 會失敗，因為只有90% 的樣本可供使用，但 `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` 會成功。<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`：指定收集資料的時間範圍，同時具有開始時間和結束時間。 如上所述，收集樣本以及將它變成可供公式使用之間有延遲。 當您使用 `GetSample` 方法時，請考慮此延遲。 請參閱下文中的 `GetSamplePercent`。 |
| GetSamplePeriod() |傳回歷史範例資料集中取得範例的期間。 |
| Count() |傳回度量歷程記錄中的範例總數。 |
| HistoryBeginTime() |傳回度量的最舊可用資料範例的時間戳記。 |
| GetSamplePercent() |傳回指定的時間間隔內可用的樣本百分比。 例如： `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )` 。 因為 `GetSample` 方法在傳回樣本的百分比小於指定的 `samplePercent` 時會失敗，因此，您可以先使用 `GetSamplePercent` 方法進行檢查。 然後您可以在樣本不足時執行替代動作，而不暫停自動調整評估。 |

### <a name="samples"></a>範例

Batch 服務會定期取得工作和資源計量的樣本，使其可供自動調整公式使用。 Batch 服務會每隔 30 秒記錄一次這些樣本。 不過，記錄樣本的時間與樣本可供自動調整公式使用 (與讀取) 的時間之間有所延遲。 此外，由於網路或其他基礎結構問題等因素，可能不會針對特定間隔記錄範例。

### <a name="sample-percentage"></a>樣本百分比

將 `samplePercent` 傳遞至 `GetSample()` 方法，或呼叫 `GetSamplePercent()` 方法時，_percent_ 是指 Batch 服務可能記錄的樣本總數，與自動調整公式實際可用的樣本數目之間的比較。

讓我們以 10 分鐘的時間範圍為例。 由於範例會每隔30秒記錄一次10分鐘的時間範圍內，因此批次所記錄的樣本總數上限為每分鐘 (2) 的20個樣本。 不過，由於回報機制固有的延遲，以及 Azure 中的其他問題，可能只有 15 個樣本可供自動調整公式讀取。 因此，舉例來說，在這 10 分鐘的期間內，記錄的樣本總數只有 75% 可供您的公式使用。

### <a name="getsample-and-sample-ranges"></a>GetSample() 和樣本範圍

您的自動調整公式會藉由新增或移除節點來擴大和縮減集區。 由於節點會產生費用，請確定您的公式使用以足夠資料為基礎的智慧型分析方法。 我們建議您在公式中使用趨勢類型分析。 此類型會根據所收集樣本的範圍來擴大和縮減集區。

若要這樣做，請使用 `GetSample(interval look-back start, interval look-back end)` 傳回樣本的向量：

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Batch 評估上述程式碼後，它會以值的向量形式傳回樣本範圍。 例如：

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

收集樣本向量後，您便可使用 `min()`、`max()` 和 `avg()` 等函式從所收集的範圍衍生出有意義的值。

若要增加安全性，如果特定一段時間可用的樣本小於特定百分比，您可以強制公式評估為失敗。 強制公式評估為失敗會指示 Batch 在指定的樣本百分比無法使用時停止進一步評估公式。 在此情況下，不會變更集區大小。 若要指定評估成功所需的樣本百分比，請將其指定為 `GetSample()`的第三個參數。 以下指定了 75% 的樣本需求：

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

因為範例可用性可能會有延遲，所以您應該一律指定具有早于一分鐘的反向查看開始時間的時間範圍。 樣本需要花大約一分鐘的時間才能傳播到整個系統，所以通常無法使用 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 範圍中的樣本。 同樣地，您可以使用 `GetSample()` 的百分比參數來強制特定樣本百分比需求。

> [!IMPORTANT]
> 我們強烈建議您 ***避免「只」* `GetSample(1)`依賴自動調整公式中的** 。 這是因為 `GetSample(1)` 基本上會向 Batch 服務表示：「不論您多久以前擷取最後一個樣本，請將它提供給我」。 因為它只是單一樣本，而且可能是較舊的樣本，所以可能無法代表最近工作或資源狀態的全貌。 如果您使用 `GetSample(1)`，請確定它是較大的陳述式，而且不是您的公式所依賴的唯一資料點。

## <a name="write-an-autoscale-formula"></a>撰寫自動調整公式

您建置自動調整公式的方式是使用上述元件撰寫陳述式，再將這些陳述式結合成完整的公式。 在本節中，我們會建立範例自動調整公式，以執行實際的調整規模決策並進行調整。

首先，讓我們定義新自動調整公式的需求。 公式應該︰

- 如果 CPU 使用率偏高，則增加集區中專用計算節點的目標數目。
- 如果 CPU 使用率偏低，則減少集區中專用計算節點的目標數目。
- 一律以 400 為專用節點的數目上限。
- 減少節點數目時，請勿移除正在執行工作的節點;如有必要，請等候工作完成，再移除節點。

公式中的第一個語句會增加高 CPU 使用率期間的節點數目。 我們會定義一個語句，以將使用者自訂變數填入 () ，其 `$totalDedicatedNodes` 值為專用節點目前目標數目的110%，但僅限在過去10分鐘內的最小平均 CPU 使用率高於70% 時。 否則，它會使用目前專用節點數目的值。

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

若要減少低 CPU 使用率期間的專用節點數目， `$totalDedicatedNodes` 如果過去60分鐘的平均 CPU 使用量低於20%，公式中的下一個語句會將相同的變數設定為專用節點目前目標數目的90%。 否則，它會使用 `$totalDedicatedNodes` 我們在上面的語句中填入的目前值。

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

現在，我們會將專用計算節點的目標數目限制為最大值400。

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

最後，我們會確保在工作完成之前，不會移除節點。

```
$NodeDeallocationOption = taskcompletion;
```

以下是完整的公式：

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> 如果您選擇，可以在公式字串中包含批註和分行符號。 也請注意，遺失分號可能會導致評估錯誤。

## <a name="automatic-scaling-interval"></a>自動調整間隔

依預設，Batch 服務會根據其自動調整公式每隔 15 分鐘調整集區的大小。 可使用下列的集區屬性設定此間隔：

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

最小間隔為 5 分鐘，而最大間隔為 168 小時。 如果指定此範圍以外的時間間隔，則 Batch 服務會傳回「不正確的要求 (400)」錯誤。

> [!NOTE]
> 自動調整目前不適合作為低於一分鐘的變更回應，而是要在您執行工作負載時逐步調整您的集區大小。

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>使用 Batch SDK 建立已啟用自動調整的集區

[Batch SDK](batch-apis-tools.md#azure-accounts-for-batch-development)、[Batch REST API](/rest/api/batchservice/)、[Batch PowerShell Cmdlet](batch-powershell-cmdlets-get-started.md) 和 [Batch CLI](batch-cli-get-started.md) 都可用來設定集區自動調整。 在本節中，您會看到 .NET 和 Python 的範例。

### <a name="net"></a>.NET

若要在 .NET 中建立已啟用自動調整的集區，請遵循下列步驟：

1. 使用 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) 建立集區。
1. 將 [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) 屬性設定為 `true`。
1. 使用自動調整公式來設定 [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) 屬性。
1. (選擇性) 設定 [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) 屬性 (預設值為 15 分鐘)。
1. 使用 [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) 或 [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) 認可集區。

下列範例會在 .NET 中建立已啟用自動調整的集區。 集區的自動調整公式會將星期一的專用節點目標數目設定為5，並將每隔一周的每一天設定為1。 [自動調整間隔](#automatic-scaling-interval) 設定為 30 分鐘。 在此程式碼片段及本文的其他 C# 程式碼片段中，`myBatchClient` 是 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 類別適當初始化的執行個體。

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> 當您建立已啟用自動調整的集區時，請勿在對 **>batchclient.pooloperations.createpool** 的呼叫中指定 _>createpool_ 參數或 _>targetdedicatednodes_ 參數。 請改為在集區上指定 **AutoScaleEnabled** 和 **AutoScaleFormula** 屬性。 這些屬性的值會判斷每個節點類型的目標數目。
>
> 若要手動調整已啟用自動調整集區的大小 (例如，使用 [BatchClient. >batchclient.pooloperations.createpool. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)) ，您必須先停用集區上的自動調整，然後調整其大小。

### <a name="python"></a>Python

使用 Python SDK 建立已啟用自動調整的集區：

1. 建立集區並指定其設定。
1. 將集區新增至服務用戶端。
1. 使用您撰寫的公式在集區上啟用自動調整。

下列範例說明這些步驟。

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> 在 GitHub 上的 [Batch Python 快速入門存放庫](https://github.com/Azure-Samples/batch-python-quickstart)中，可找到更多使用 Python SDK 的範例。

## <a name="enable-autoscaling-on-an-existing-pool"></a>在現有集區啟用自動調整

每個 Batch SDK 都提供一種方式來啟用自動調整。 例如：

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [在集區上啟用自動調整](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

當您在現有集區上啟用自動調整時，請記住：

- 如果集區上的自動調整目前已停用，您必須在發出要求時指定有效的自動調整公式。 您可以選擇性地指定自動調整間隔。 如果您未指定間隔，則會使用預設值15分鐘。
- 如果目前已在集區上啟用自動調整，您可以指定新的公式、新的間隔，或兩者。 您必須至少指定其中一個屬性。
  - 如果您指定新的自動調整間隔，則會停止現有的排程並啟動新的排程。 新排程的開始時間是發出啟用自動調整要求的時間。
  - 如果您省略自動調整公式或間隔，則 Batch 服務將繼續使用該設定的目前值。

> [!NOTE]
> 如果您在 .NET 中建立集區時，針對 **>batchclient.pooloperations.createpool** 方法的 *>createpool* 或 *>targetdedicatednodes* 參數指定值，或在其他語言中為可比較的參數指定值，則在評估自動調整公式時，會忽略這些值。

這個 c # 範例會使用 [Batch .net](/dotnet/api/microsoft.azure.batch) 程式庫，在現有的集區上啟用自動調整。

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>更新自動調整公式

若要更新現有已啟用自動調整集區上的公式，呼叫作業以使用新的公式再次啟用自動調整。 例如，如果在執行下列 .NET 程式碼時，已在 `myexistingpool` 上啟用自動調整，則會以 `myNewFormula` 的內容取代其自動調整公式。

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>更新自動調整間隔

若要更新現有已啟用自動調整集區的自動調整評估間隔，呼叫作業以使用新的間隔再次啟用自動調整。 例如，若要針對在 .NET 中已啟用自動調整的集區，將自動調整評估間隔設定為 60 分鐘︰

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>評估自動調整公式

您可以先評估公式，再將它套用至集區。 這可讓您在將公式的結果放入生產環境之前，先進行測試。

在評估自動調整公式之前，您必須先使用有效的公式在集區上啟用自動調整，例如單行公式 `$TargetDedicatedNodes = 0` 。 然後，使用下列其中之一來評估您想要測試的公式︰

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) 或 [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    這些 Batch .NET 方法都需要現有集區的識別碼以及包含要評估之自動調整公式的字串。

- [評估自動調整公式](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    在這個 REST 要求中，於 URI 中指定集區識別碼，以及於要求主體的 *autoScaleFormula* 元素中指定自動調整公式。 作業的回應會包含可能與公式相關的任何錯誤資訊。

此 [批次 .net](/dotnet/api/microsoft.azure.batch) 範例會評估自動調整公式。 如果集區尚未使用自動調整，我們會先加以啟用。

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

成功評估此程式碼片段所顯示的公式會產生類似以下的結果：

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>取得自動調整執行的相關資訊

若要確保您的公式如預期般執行，我們建議您定期檢查 Batch 對您的集區執行之自動調整執行的結果。 若要這樣做，請取得 (或重新整理) 集區的參考，然後檢查其上次自動調整執行的屬性。

在 Batch .NET 中，[CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) 屬性有數個屬性，可提供最近在集區上執行之自動調整的相關資訊：

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

在 REST API 中，[取得集區的相關資訊](/rest/api/batchservice/get-information-about-a-pool)要求會傳回集區的相關資訊，其中包含 [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) 屬性中最近執行的自動調整資訊。

下列 c # 範例會使用 Batch .NET 程式庫來列印集區 _myPool_ 上上次自動調整執行的相關資訊。

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

上述範例的範例輸出：

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>使用集區自動調整事件取得自動調整執行歷程記錄
您也可以藉由查詢 [PoolAutoScaleEvent](batch-pool-autoscale-event.md)來檢查自動調整歷程記錄。 Batch 服務會發出此事件，以記錄每次出現的自動調整公式評估和執行，這有助於疑難排解可能的問題。

PoolAutoScaleEvent 的範例事件：
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>自動調整公式範例

讓我們看看下面幾個公式，其顯示調整集區中計算資源數量的不同方式。

### <a name="example-1-time-based-adjustment"></a>範例 1：以時間為基礎的調整

假設您想要根據一週的天數和一天的時間，調整集區大小。 這個範例示範如何據以增加或減少集區中的節點數目。

此公式會先取得目前的時間。 如果是工作日 (1-5) 且在上班時間內 (上午 8:00 - 下午 6:00)，則將目標集區大小設為 20 個節點。 否則，它會設定為 10 個節點。

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

在 `$curTime` 中，您可以將 `time()` 加上 `TimeZoneInterval_Hour` 和 UTC 時差的乘積，以反映本地時區。 例如，使用 `$curTime = time() + (-6 * TimeInterval_Hour);` 表示美加山區日光節約時間 (MDT)。 請記住，在日光節約時間開始和結束時，必須調整時差 (如果適用)。

### <a name="example-2-task-based-adjustment"></a>範例 2：以工作為基礎的調整

在此 c # 範例中，會根據佇列中的工作數目調整集區大小。 我們已在公式字串中包含批註和分行符號。

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>範例 3：考量平行工作

這個 c # 範例會根據工作數目來調整集區大小。 此公式也會考慮已針對集區設定的 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 值。 已在集區上啟用[平行工作執行](batch-parallel-node-tasks.md)的情況下，這個方法很有用。

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>範例 4：設定初始集區大小

此範例顯示 c # 範例，其中包含自動調整公式，可將集區大小設定為初始時間週期的指定節點數目。 之後，它會根據執行中和作用中的工作數目來調整集區大小。

具體而言，此公式會執行下列動作：

- 將初始的集區大小設為 4 個節點。
- 在集區生命週期的最初 10 分鐘內不調整集區大小。
- 10 分鐘後，取得過去 60 分鐘內執行中和作用中工作數目的最大值。
  - 如果這兩個值都是 0 (表示過去 60 分鐘沒有執行或作用中的工作)，集區大小就設為 0。
  - 如果其中一個值大於零，則不進行任何變更。

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在集區中的計算節點上同時執行多個工作](batch-parallel-node-tasks.md)。 除了自動調整功能，這有助於降低某些工作負載的工作持續時間，以節省成本。
- 瞭解如何 [有效率地查詢 Azure Batch 服務](batch-efficient-list-queries.md) ，以提升效率。
