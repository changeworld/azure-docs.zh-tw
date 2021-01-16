---
title: Azure 監視器度量計量匯總和顯示說明
description: 如何匯總計量的詳細資訊 Azure 監視器
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: 1d83ef07714e0ce69f01aa240cc3058195c7b1af
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251976"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure 監視器度量計量匯總和顯示說明

本文說明 Azure 監視器 [平臺計量](data-platform.md) 和 [自訂計量](metrics-custom-overview.md)的 Azure 監視器時間序列資料庫中的計量匯總。 本文也適用于標準 [Application Insights 計量](../app/app-insights-overview.md)。 

這是很複雜的主題，而不需要瞭解本文中的所有資訊，就能有效地使用 Azure 監視器計量。

## <a name="overview-and-terms"></a>總覽和條款

當您將計量新增至圖表時，計量瀏覽器會自動預先選取其預設匯總。 在基本案例中，預設值是合理的，但您可以使用不同的匯總來取得度量的更深入解析。 若要在圖表上查看不同的匯總，您必須瞭解計量瀏覽器如何處理它們。

讓我們先明確地定義一些詞彙：

- **度量值** –針對特定資源收集的單一度量值。
- **時間週期** -一段一般時間。
- **時間間隔** –收集兩個度量值之間的時間間隔。 
- **時間範圍** –圖表上顯示的時間週期。 一般預設值為24小時。 只有特定範圍可供使用。 
- **時間細微性** 或 **時間** 範圍–用來將值匯總在一起以允許在圖表上顯示的時間週期。 只有特定範圍可供使用。 目前的最小值為1分鐘。 時間資料細微性值應該小於所選的時間範圍，如此才能發揮效用，否則只會顯示整個圖表的一個值。 
- **匯總類型** -從多個計量值計算的統計資料類型。  
- **匯總** –接受多個輸入值，然後使用這些值透過匯總類型所定義的規則來產生單一輸出值的程式。 例如，採用多個值的平均值。  

計量是一系列定期捕捉的度量值。 當您繪製圖表時，所選度量的值會在時間細微性上分開匯總 (也稱為時間細微性) 。 您可以使用 [計量瀏覽器時間選擇器面板](metrics-getting-started.md#select-a-time-range)來選取時間細微性的大小。 如果您未進行明確的選取，則會根據目前選取的時間範圍自動選取時間細微性。 一旦選取之後，就會匯總每個時間細微性間隔期間所捕獲的計量值，並放在圖表上（每個間隔一個資料點）。

## <a name="aggregation-types"></a>彙總類型 

計量瀏覽器中有五種可用的基本匯總類型。 計量瀏覽器會隱藏不相關的匯總，而且無法用於指定的度量。 

- **Sum** –在匯總間隔中捕捉的所有值的總和。 有時稱為總匯總。
- **Count** –匯總間隔所捕捉的度量數目。 Count 不會查看測量的值，只會查看記錄的數目。 
- **平均** –透過匯總間隔所捕捉的度量值平均值。 對於大部分的計量而言，此值是 Sum/Count。 
- **最** 小值–透過匯總間隔所捕捉的最小值。
- **Max** -透過匯總間隔所捕捉的最大值。

例如，假設有一個圖表顯示了在過去24小時的時間範圍內，使用 **總和** 匯總的 VM 的 **網路輸出** 度量。 您可以從圖表的右上方變更時間範圍和細微性，如下列螢幕擷取畫面所示。

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="顯示時間範圍和時間細微性選擇器的螢幕擷取畫面" border="true":::

針對時間細微性 = 30 分鐘，時間範圍 = 24 小時：

- 圖表是從48資料點繪製。 這是24小時 x 2 資料點每小時 (60min/30 分鐘) 匯總1分鐘資料點。 
- 折線圖會連接圖表繪圖區域中的48點。 
- 每個點都代表每個相關30分鐘期間內送出之所有網路輸出位元組的總和。 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="螢幕擷取畫面：顯示折線圖上的資料設定為24小時的時間範圍和30分鐘的時間細微性" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*按一下此區段中的影像以查看較大的版本。*

如果您將時間細微性切換至15分鐘，則會從96匯總資料點繪製圖表。 亦即，每小時 60min/15 分鐘 = 4 資料點 x 24 小時。

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="螢幕擷取畫面：顯示折線圖上的資料設定為24小時的時間範圍和15分鐘的時間細微性" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

若為5分鐘的時間資料細微性，您會獲得 24 x (60/5) = 288 點。 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="螢幕擷取畫面：顯示折線圖上的資料設定為24小時的時間範圍和5分鐘時間的資料細微性" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

若為1分鐘的時間細微性 (圖表) 的最小可能值，您會取得 24 x 60/1 = 1440 點。 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="螢幕擷取畫面：顯示折線圖上的資料設定為24小時的時間範圍和1分鐘的時間細微性" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

這些總和的圖表看起來會不同，如先前的螢幕擷取畫面所示。  請注意，此 VM 在相對於時間範圍其餘部分的短暫時間內，有很多的輸出。  

時間細微性可讓您調整圖表的「雜訊雜訊」比例。 較高的匯總會移除雜訊並使尖峰變平滑。  請注意底部1分鐘圖表的變化，以及當您移至更高的資料細微性值時，它們如何平滑。 

當您將此資料傳送至其他系統時（例如警示），此平滑行為相當重要。 通常，您通常不會想要在 CPU 時間超過90% 的尖峰時間內收到警示。 但是，如果 CPU 的5分鐘維持90%，這可能很重要。 如果您在 CPU (或任何計量) 上設定警示規則，讓時間細微性變大可以減少您收到的錯誤警示數目。 

請務必為您的工作負載建立「正常」，以瞭解最適合的時間間隔。 這是 [動態警示](alerts-dynamic-thresholds.md)的優點之一，也就是此處未涵蓋的不同主題。  

## <a name="how-the-system-collects-metrics"></a>系統如何收集計量

資料收集因度量而異。 有兩種類型的收集週期。

### <a name="measurement-collection-frequency"></a>測量收集頻率 

- **標準-計量** 會以不會改變的一致時間間隔進行收集。

- 以 **活動為基礎**-度量是根據特定類型的交易發生時所收集。 每筆交易都有計量專案和時間戳記。 系統不會定期進行收集，因此在指定的時間週期內會有不同數目的記錄。 

### <a name="granularity"></a>細微度

最小時間間隔為1分鐘，但基礎系統可能會根據度量更快地捕捉資料。 例如，每隔15秒會定期追蹤 CPU 百分比。 由於 HTTP 失敗會視為交易來追蹤，因此可以輕鬆地超過一分鐘。 系統會每隔20分鐘捕獲其他計量，例如 SQL 儲存體。 此選項是由個別的資源提供者和類型所決定。 大部分嘗試盡可能提供最小的間隔。

### <a name="dimensions-splitting-and-filtering"></a>維度、分割和篩選

系統會為每個個別資源捕獲計量。 不過，計量收集、儲存和可以繪製的層級可能會有所不同。 此層級是以 **計量維度** 中可用的其他計量來表示。 每個個別資源提供者都會取得定義所收集資料的詳細程度。 Azure 監視器只會定義應該如何呈現和儲存這類詳細資料。 

當您在計量瀏覽器中為計量建立圖表時，您可以選擇依據維度來「分割」圖表。  分割圖表表示您正在查看基礎資料，以取得更多詳細資料，並在 [計量瀏覽器] 中查看資料繪製或篩選。

例如， [ApiManagement/服務](metrics-supported.md#microsoftapimanagementservice) 有許多度量的 *位置* 做為維度。 

- **容量** 是一種這類計量。 具有 *位置* 維度表示基礎系統正在儲存每個位置之容量的度量記錄，而不只是匯總數量的計量記錄。 然後，您可以在計量圖表中取出或分割該資訊。  

- 查看 **閘道要求的整體持續時間**，有2個維度 *位置* 和 *主機名稱*，再次讓您知道持續時間和其來源主機名稱的位置。  

- 其中一個更具彈性的計量（ **要求**）有7個不同的維度。 
 
如需每個計量和可用維度的詳細資料，請參閱 Azure 監視器 [計量支援](metrics-supported.md) 的文章。 此外，每個資源提供者和類型的檔可能會針對維度和其測量的內容提供其他資訊。

您可以同時使用分割和篩選來深入探索問題。 以下圖形範例顯示資源群組中一組 Vm 的 *平均磁片寫入位元組* 。 我們有所有 Vm 的匯總套件具有此計量，但我們可能想要深入瞭解上午6點的尖峰。 它們是相同的電腦嗎？ 牽涉到多少部電腦？  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="顯示 Contoso 飯店資源群組中所有虛擬機器的磁片寫入位元組總計的螢幕擷取畫面" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*按一下此區段中的影像以查看較大的版本。*

當我們套用分割時，我們可以看到基礎資料，但有點混亂。 結果會將20個 Vm 匯總到上圖中。 在此情況下，我們使用滑鼠將滑鼠停留在上午6點的大量尖峰，告訴我們 DCVM11 是原因。 但很難看到與該 VM 相關聯的其餘資料，因為其他 Vm 會讓圖表雜亂。 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="螢幕擷取畫面，顯示 Contoso 飯店資源群組中所有虛擬機器的磁片寫入位元組（依虛擬機器名稱分割）" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

使用篩選可讓我們清除圖表以查看真正發生的情況。 您可以檢查或取消核取您想要查看的 Vm。 請注意虛線。 稍後的章節將會加以說明。

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="螢幕擷取畫面：顯示 Contoso 飯店資源群組中所有虛擬機器的磁片寫入位元組，並依虛擬機器名稱進行分割和篩選" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

如需有關如何在計量瀏覽器圖表上顯示分割維度資料的詳細資訊，請參閱 [計量瀏覽器的 Advanced 功能-篩選和分割](metrics-charts.md#filters)。

### <a name="null-and-zero-values"></a>Null 和零值

當系統預期來自資源但未收到的計量資料時，它會記錄 Null 值。  Null 與零值不同，這在計算匯總和圖表時變得很重要。 Null 值不會計算為有效的測量值。 

Null 在不同的圖表上會以不同方式顯示。 散佈圖略過在圖表上顯示點。 橫條圖略過顯示列。 在折線圖上，Null 可能會顯示為 [點線或虛線](metrics-troubleshoot.md#chart-shows-dashed-line) ，如同上一節中的螢幕擷取畫面所示。 在計算包含 Null 的平均時，會有較少的資料點來取平均值。  這種行為有時可能會導致圖表上的值發生未預期的放置，但通常不會比將值轉換為零，並當做有效的資料點使用。  

[自訂計量](metrics-custom-overview.md) 在未收到任何資料時，一律會使用 null。 使用 [平臺計量](data-platform.md)時，每個資源提供者都會根據對指定計量最有意義的方式，決定是否要使用零或 null。

Azure 監視器警示會使用資源提供者寫入計量資料庫的值，因此請務必瞭解資源提供者如何藉由先查看資料來處理 Null。

## <a name="how-aggregation-works"></a>匯總的運作方式

先前系統中的計量圖表會顯示不同類型的匯總資料。 系統會預先匯總資料，讓要求的圖表能夠更快速地顯示，而不會有大量的重複計算。  

在此範例中：

- 我們正在收集稱為 **HTTP 失敗** 的 **虛構** 交易計量 
- *伺服器* 是 **HTTP 失敗** 度量的維度。
- 我們有3部伺服器-伺服器 A、B 和 C。

為了簡化說明，我們只會從 SUM 匯總類型開始。 

### <a name="sub-minute-to-1-minute-aggregation"></a>從一分鐘到1分鐘的匯總

第一個原始度量資料會收集並儲存在 Azure 監視器計量資料庫中。 在此情況下，每一部伺服器都有以時間戳記儲存的交易記錄，因為 *伺服器* 是維度。 假設您可以視為客戶的最小時間週期是1分鐘，則系統會先將這些時間戳記匯總為每個個別伺服器的1分鐘度量值。  伺服器 B 的匯總進程如下圖所示。 伺服器 A 和 C 以相同方式完成，並擁有不同的資料。  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="螢幕擷取畫面，顯示1分鐘匯總的分次交易專案。 " border="false":::

產生的1分鐘匯總值會儲存為計量資料庫中的新專案，讓您可以針對稍後的計算進行收集。 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="螢幕擷取畫面：顯示跨伺服器維度的多個1分鐘匯總專案。伺服器 A、B 和 C 個別顯示" border="false":::

### <a name="dimension-aggregation"></a>維度匯總

1分鐘的計算接著會依維度折迭，並再次儲存為個別記錄。   在此情況下，所有個別伺服器的所有資料都會匯總成1分鐘的間隔計量，並儲存在計量資料庫中，以用於後續的匯總。

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="螢幕擷取畫面，顯示伺服器 A、B 和 C 匯總成1分鐘所有伺服器的多個1分鐘匯總專案 entires" border="false":::

為了清楚起見，下表顯示匯總的方法。

| 期間   | 伺服器 A | 伺服器 B | 伺服器 C | Sum (A + B + C) |
| -------- | -------- | -------- | -------- | --------   |  
| 分鐘1 | 1        | 1        | 1        | 3          |
| 第2分鐘 | 0        | 5        | 1        | 6          |
| 第3分鐘 | 0        | 5        | 1        | 6          |
| 分鐘4 | 2        | 3        | 4        | 9          |
| 第5分鐘 | 1        | 0        | 3        | 4          |
| 分鐘6 | 1        | 0        | 4        | 5          |
| 第7分鐘 | 1        | 2        | 4        | 7          |
| 第8分鐘 | 0        | 1        | 0        | 1          |
| 第9分鐘 | 1        | 1        | 4        | 6          |
| 分鐘10| 2        | 1        | 0        | 3          |

上面只會顯示一個維度，但會針對計量所支援的 **所有維度** 進行相同的匯總和儲存程式。

- 將值收集到該維度的1分鐘匯總集。 儲存這些值。 
- 將維度折迭為1分鐘的匯總總和。 儲存這些值。 

讓我們介紹另一個稱為 NetworkAdapter 的 HTTP 失敗維度。 假設每台伺服器的介面卡數目各有不同。

- 伺服器 A 有1張介面卡
- 伺服器 B 有2張介面卡
- 伺服器 C 有3張介面卡

我們會分開收集下列交易的資料。 它們會標示為：

- 時間
- 值
- 交易的來源伺服器
- 交易來源的介面卡

然後，每個 subminute 串流都會匯總成1分鐘的時間序列值，並儲存在 Azure 監視器計量資料庫中：

- 伺服器 A，Adapter 1
- 伺服器 B，Adapter 1
- 伺服器 B，Adapter 2
- 伺服器 C，Adapter 1
- 伺服器 C，Adapter 2
- 伺服器 C，Adapter 3

此外，也會儲存下列折迭的匯總：

- 伺服器 A，Adapter 1 (因為沒有任何要折迭的專案，所以會再次儲存) 
- 伺服器 B、Adapter 1 + 2
- 伺服器 C，Adapter 1 + 2 + 3
- 所有伺服器，所有介面卡

這會顯示具有大量維度的度量有大量的匯總。 瞭解所有排列並不重要，只是瞭解原因。 系統想要將個別的資料和匯總的資料儲存在任何圖表上進行快速抓取以進行存取。 系統會根據您選擇要顯示的內容，挑選最相關的已儲存匯總或基礎原始資料。 

### <a name="aggregation-with-no-dimensions"></a>沒有維度的匯總

因為此計量有維度 *伺服器*，所以您可以透過分割和篩選來取得上述 server a、B 和 C 的基礎資料，如本文稍早所述。 如果計量沒有作為維度的 *伺服器* ，則您的客戶只能存取圖表上以黑色顯示的匯總1分鐘總和。 也就是3、6、6、9等的值。系統也不會執行基礎工作來匯總分割值，它永遠不會在計量瀏覽器中使用它們，或透過計量 REST API 傳送這些值。 

## <a name="viewing-time-granularities-above-1-minute"></a>查看超過1分鐘的時間資料細微性

如果您以較大的資料細微性要求度量，系統會使用1分鐘的匯總總和來計算較大時間資料細微性的總和。  以下虛線顯示2分鐘和5分鐘時間資料細微性的總和方法。 同樣地，為了簡單起見，我們只會顯示 SUM 匯總類型。

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="螢幕擷取畫面，顯示在伺服器的整個維度中匯總為2分鐘和5分鐘時間週期的多個1分鐘匯總專案。" border="false":::

適用于2分鐘的時間資料細微性。

| 期間       | 款項       
| -------------|-------------|  
| 第 1 & 2 分鐘 |  (3 + 6) = 9 |
| 第3分鐘 & 4 |  (6 + 9) = 15|
| 第4分鐘 & 5 |  (4 + 5) = 9 |
| 分鐘 6 & 7 |  (7 + 1) = 8 |
| 第 8 & 9 分鐘 |  (6 + 3) = 9 |

適用于5分鐘的時間細微性。

| 期間              |            款項        |
|---------------------|------------------------|  
| 第1到5分鐘  | 3 + 6 + 6 + 9 + 4 = 28 |
| 分鐘6到10 | 5 + 7 + 1 + 6 + 3 = 22 |

系統會使用可提供最佳效能的儲存匯總資料。 

以下是上述1分鐘匯總程式較大的圖表，其中有一些箭號可改善可讀性。

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="顯示前三個螢幕擷取畫面匯總的螢幕擷取畫面。在1分鐘、2分鐘和5分鐘間隔內匯總的伺服器維度中，多個1分鐘的匯總專案。伺服器 A、B 和 C 個別顯示" border="false":::

## <a name="more-complex-example"></a>更複雜的範例

以下是較大的範例，其使用稱為 HTTP 回應時間的虛構計量值（以毫秒為單位）。  在這裡，我們會介紹其他層級的複雜度。

1. 我們會顯示總和、計數、最小值和最大值的匯總，以及平均值的計算。
2. 我們會顯示 Null 值，以及它們會如何影響計算。 

請思考一下下列範例。 方塊和箭號會顯示如何匯總和計算值的範例。 

上一節所述的相同1分鐘 preaggregation 程式會發生在 Sum、Count、最小值和最大值上。  但是，不會預先匯總平均值。 它會使用匯總資料進行重新計算，以避免計算錯誤。
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="顯示匯總和計算總和、計數、最小值、最大值和平均（從1分鐘到10分鐘）的複雜範例的螢幕擷取畫面。" border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

如上面所強調的1分鐘匯總，請考慮第6分鐘。 這分鐘是伺服器 B 離線和停止報告資料的時間點，可能是因為重新開機。 

從上述的第6分鐘開始，計算的1分鐘匯總類型為： 

| 彙總類型 | 值        | 注意 |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| 計數            | 2            | 顯示 Null 的效果。  如果伺服器已上線，此值會是3。  |
| 最低需求          | 20           | |
| 最大值          | 53           | |
| 平均          | 73/2       | 一律除以計數的總和。 它永遠不會儲存，而且一律會使用該資料細微性的匯總數位，針對每個時間細微性進行重新計算。 請注意，上述5分鐘和10分鐘的時間資料細微性重新計算。 |

紅色文字色彩表示可能會被視為超出正常範圍的值，並顯示這些值如何傳播 (或在時間細微性增加時無法) 。 請注意 *Min* 和 *Max* 如何表示有根本的異常，而當您的時間細微性增加時， *平均* 和 *總和* 會遺失該資訊。  

您也可以看到 Null 可提供比使用零更好的平均計算。  

> [!NOTE] 
> 不過，在此範例中，當一律以值1來捕捉計量時， *Count* 等於 *Sum* 。 當計量追蹤交易事件發生時（例如，本文先前範例中所述的 HTTP 失敗數目），這是很常見的情況。

## <a name="next-steps"></a>後續步驟

- [開始使用計量瀏覽器](metrics-getting-started.md)
- [Advanced 計量瀏覽器](metrics-charts.md)
