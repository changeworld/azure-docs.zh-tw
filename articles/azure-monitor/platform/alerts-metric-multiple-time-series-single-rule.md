---
title: 監視單一計量警示規則中的多個時間序列
description: 針對多個時間序列使用單一警示規則的大規模警示
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 10/04/2020
ms.subservice: alerts
ms.openlocfilehash: 81e09e6d9c6a57339f1d6f1eb5ce4f494555fa19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704475"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>監視單一計量警示規則中的多個時間序列

單一計量警示規則可用來監視一或多個計量時間序列，讓您更輕鬆地大規模監視資源。

## <a name="metric-time-series"></a>計量時間序列

計量時間序列是一系列的測量， (或「計量值」 ) 在一段時間內捕捉。 

例如：

- 虛擬機器的 CPU 使用率
- 傳入的位元組 (將) 輸入至儲存體帳戶
- Web 應用程式的失敗要求數目



## <a name="alert-rule-on-a-single-time-series"></a>單一時間序列上的警示規則
警示規則會在符合下列所有條件時，監視單一時間序列：
-   此規則會監視單一目標資源 
-   包含單一條件
-   評估度量，而不選擇維度 (假設計量支援維度) 

這類警示規則的範例 (只會顯示) 顯示的相關屬性：
-   目標資源： *myVM1*
-   度量： *CPU 百分比*
-   運算子： *大於*
-   閾值： *70*


針對此警示規則，會監視單一計量時間序列：
-   *Resource*= ' myVM1 ' > 70% 的 CPU 百分比

![單一時間序列上的警示規則](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>多個時間序列上的警示規則
如果警示規則至少使用下列其中一項功能，則會監視多個時間序列： 
-   多個資源
-   多重條件 
-   多維度


## <a name="multiple-resources-multi-resource"></a>多資源)  (多個資源

單一計量警示規則可以監視多個資源，但前提是資源的類型相同，而且存在於相同的 Azure 區域中。 使用這種類型的規則可降低複雜性以及您必須維護的警示規則總數。 

這類警示規則的範例：
-   目標資源： *myVM1、myVM2*
-   度量： *CPU 百分比*
-   運算子： *大於*
-   閾值： *70*

針對此警示規則，會另外監視兩個計量時間序列：
-   *Resource*= ' myVM1 ' > 70% 的 CPU 百分比
-   *Resource*= ' myVM2 ' > 70% 的 CPU 百分比

![多重資源警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
在多重資源警示規則中，會針對每個資源的每個計量時間序列對應， **個別評估每個資源的條件** (或更精確地) 。 這表示也會分別對每個資源引發警示。

例如，假設我們已設定上方的警示規則來監視超過70% 的 CPU。 在評估期間 (也就是最後5分鐘) 
-   *MyVM1*的*CPU 百分比*大於70% 
-   *MyVM2*的*百分比 CPU*是50%

警示規則會在 *myVM1*（但不是 *myVM2*）上觸發。 這些觸發的警示是獨立的。 它們也可以在不同的時間解決，視每個虛擬機器的個別行為而定。

如需多重資源警示規則和這項功能所支援資源類型的詳細資訊，請參閱 [Azure 監視器中的計量警示大規模監視](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

> [!NOTE] 
> 在監視多個資源的計量警示規則中，只允許單一條件。

## <a name="multiple-conditions-multi-condition"></a>多重條件 (多個條件) 

單一計量警示規則也可以監視每個警示規則最多五個條件。 

例如：

- 目標資源： *myVM1*
- Condition1
  - 度量： *CPU 百分比*
  - 運算子： *大於*
  - 閾值： *70*
- Condition2
  - 度量： *網路總計*
  - 運算子： *大於*
  - 閾值： *20 MB*

針對此警示規則，系統會監視兩個計量時間序列：

- *Resource*= ' myVM1 ' > 70% 的 CPU 百分比
- Network In Total *Resource*= ' myVM1 ' > 20 MB

![多重條件警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
條件之間會使用 ' AND ' 運算子。 當 **所有** 條件都符合時，警示規則就會引發警示。 如果不再符合其中一個條件，就會解決引發的警示。 

> [!NOTE]
> 在具有多個條件的警示規則中使用維度時，會有一些限制。 如需詳細資訊，請參閱在 [度量警示規則中使用具有多個條件的維度時的限制](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions)。


## <a name="multiple-dimensions-multi-dimension"></a>多維度)  (多維度

單一計量警示規則也可以監視度量的多個維度值。 計量的維度是名稱/值組，其中包含用來描述度量值的額外資料。 例如，儲存體帳戶的 **交易** 計量有一個稱為 **api 名稱**的維度，其描述每個交易所呼叫的 Api 名稱 (例如 GetBlob、DeleteBlob、PutPage) 。 維度的使用是選擇性的，但它可讓您篩選計量並僅監視特定的時間序列，而不是監視計量，以將所有維度值組合在一起。 

例如，您可以選擇在所有 API 名稱中的交易數目很高 (（匯總資料) ）時引發警示，或進一步將其細分為只有當特定 API 名稱的交易數目很高時，才會發出警示。 

監視多個維度的警示規則範例如下：

- 目標資源： *myStorage1*
- 度量： *交易*
- 維度
  * API 名稱 = *GetBlob、DeleteBlob、PutPage*
- 運算子： *大於*
- 閾值： *70*

針對此警示規則，系統會監視三個計量時間序列：

- *資源*= ' myStorage1 ' 和*API 名稱*= ' GetBlob ' > 70 的交易
- *資源*= ' myStorage1 ' 和*API 名稱*= ' DeleteBlob ' > 70 的交易
- *資源*= ' myStorage1 ' 和*API 名稱*= ' PutPage ' > 70 的交易

![具有來自某個維度之值的多維度警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

多維度計量警示規則也可以從計量的 **不同** 維度監視多個維度值。 在此情況下，警示規則會 **分開** 監視所選取維度值的所有維度值組合。

這種類型的警示規則的範例：

- 目標資源： *myStorage1*
- 度量： *交易*
- 維度
  * API 名稱 = *GetBlob、DeleteBlob、PutPage*
  * 驗證 = *SAS、AccountKey*
- 運算子： *大於*
- 閾值： *70*

針對此警示規則，會另外監視六個計量時間序列：

- *資源*= ' myStorage1 '、 *API 名稱*= ' GetBlob ' 和*Authentication*= ' SAS ' > 70 的交易
- *資源*= ' myStorage1 '、 *API 名稱*= ' GetBlob ' 和*Authentication*= ' AccountKey ' > 70 的交易
- *資源*= ' myStorage1 '、 *API 名稱*= ' DeleteBlob ' 和*Authentication*= ' SAS ' > 70 的交易
- *資源*= ' myStorage1 '、 *API 名稱*= ' DeleteBlob ' 和*Authentication*= ' AccountKey ' > 70 的交易
- *資源*= ' myStorage1 '、 *API 名稱*= ' PutPage ' 和*Authentication*= ' SAS ' > 70 的交易
- *資源*= ' myStorage1 '、 *API 名稱*= ' PutPage ' 和*Authentication*= ' AccountKey ' > 70 的交易

![多維度警示規則，其中包含來自多個維度的值](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Advanced 多重維度功能

1.  **選取所有目前和未來的維度** -您可以選擇監視維度的所有可能值，包括未來的值。 這類警示規則會自動調整以監視維度的所有值，而不需要在每次新增或移除維度值時修改警示規則。
2.  **排除維度** –選取 ' ≠ ' (將維度值排除) 運算子相當於選取該維度的所有其他值，包括未來的值。
3.  **新的和自訂維度** – Azure 入口網站中顯示的維度值是以過去三天內收集的計量資料為基礎。 如果您要尋找的維度值尚未發出，則可以加入自訂維度值。

![Advanced 多重維度功能](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>計量警示定價

計量警示規則的價格可在 [Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)上取得。

建立計量警示規則時，所提供的價格估計是根據選取的功能以及受監視的時間序列數目（由規則設定和目前的計量值所決定）。 不過，每月費用是以時間序列的實際評估為基礎，因此，如果某些時間序列沒有要評估的資料，或警示規則使用可動態調整的功能，則會與原始估計值不同。

例如，如果警示規則利用多重維度功能，並選取大量的維度值組合，而導致監視多個時間序列，則會顯示高價格估計。 但是，如果維度值組合所產生的所有時間序列實際上都沒有要評估的資料，則該警示規則的實際費用可以較低。

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>由單一警示規則監視的時間序列數目

為了避免超額成本，每個警示規則預設最多可監視5000的時間序列。 若要從您的訂用帳戶增益此限制，請開啟支援票證。


## <a name="next-steps"></a>後續步驟

深入瞭解如何使用計量警示和 [動態閾值](alerts-dynamic-thresholds.md)來大規模進行監視。
