---
title: 監視單一計量警示規則中的多個時間序列
description: 使用多個時間序列的單一警示規則進行大規模警示
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 08/11/2020
ms.subservice: alerts
ms.openlocfilehash: f7ca91ca49d9357285e1307c5051ef5685ad24c9
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186890"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>監視單一計量警示規則中的多個時間序列

單一計量警示規則可用來監視一或多個計量時間序列，讓您更輕鬆地大規模監視資源。

## <a name="metric-time-series"></a>度量時間序列

計量時間序列是一系列量測 (或「度量值」，) 在一段時間內捕捉。 

例如：

- 虛擬機器的 CPU 使用率
-  (輸入) 至儲存體帳戶的傳入位元組
- Web 應用程式的失敗要求數



## <a name="alert-rule-on-a-single-time-series"></a>單一時間序列上的警示規則
當警示規則符合下列所有條件時，會監視單一時間序列：
-   此規則會監視單一目標資源 
-   包含單一條件
-   評估度量，而不選擇維度 (假設計量支援維度) 

這類警示規則的範例 (僅包含) 顯示的相關屬性：
-   目標資源： *myVM1*
-   度量： *CPU 百分比*
-   運算子：*大於*
-   閾值： *70*


針對此警示規則，會監視單一計量時間序列：
-   CPU 百分比，其中*Resource*= ' myVM1 ' > 70%

![單一時間序列上的警示規則](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>多個時間序列的警示規則
如果警示規則使用至少下列其中一項功能，則會監視多個時間序列： 
-   多個資源
-   多個條件 
-   多維度


## <a name="multiple-resources-multi-resource"></a>多項資源 (多資源) 

如果資源屬於相同類型且存在於相同的 Azure 區域中，則單一計量警示規則可以監視多個資源。 使用這種類型的規則可減少複雜度，以及您必須維護的警示規則總數。 

這類警示規則的範例如下：
-   目標資源： *myVM1、myVM2*
-   度量： *CPU 百分比*
-   運算子：*大於*
-   閾值： *70*

針對此警示規則，會分別監視兩個計量時間序列：
-   CPU 百分比，其中*Resource*= ' myVM1 ' > 70%
-   CPU 百分比，其中*Resource*= ' myVM2 ' > 70%

![多資源警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
在多資源警示規則中，會針對每個資源) 的每個計量時間序列雖然該值，**分別**評估每個資源的條件 (或更精確。 這表示也會分別針對每個資源引發警示。

例如，假設我們已將上述的警示規則設定為監視高於70% 的 CPU。 在評估的時間週期 (也就是，最後5分鐘) 
-   *MyVM1*的*CPU 百分比*大於70% 
-   *MyVM2*的*CPU 百分比*為50%

警示規則會在*myVM1*上觸發，但不會在*myVM2*。 這些觸發的警示是獨立的。 它們也可以在不同的時間解決，視每個虛擬機器的個別行為而定。

如需多資源警示規則和此功能所支援之資源類型的詳細資訊，請參閱[在 Azure 監視器中使用計量警示大規模監視](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

> [!NOTE] 
> 在監視多個資源的計量警示規則中，只允許單一條件。

## <a name="multiple-conditions-multi-condition"></a>多個條件 (多重條件) 

單一計量警示規則也可以監視每個警示規則最多五個條件。 

例如：

- 目標資源： *myVM1*
- Condition1
  - 度量： *CPU 百分比*
  - 運算子：*大於*
  - 閾值： *70*
- Condition2
  - 度量：*網路總計*
  - 運算子：*大於*
  - 閾值： *20 MB*

針對此警示規則，會監視兩個計量時間序列：

- CPU 百分比，其中*Resource*= ' myVM1 ' > 70%
- 網路總計，其中*Resource*= ' myVM1 ' > 20 MB

![多重條件警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
條件之間會使用 ' AND ' 運算子。 當**所有**條件都符合時，警示規則就會引發警示。 如果至少有一個條件已不再符合，則引發的警示會解決。 

> [!NOTE]
> 在具有多個條件的警示規則中使用維度時，會有一些限制。 如需詳細資訊，請參閱在[具有多個條件的計量警示規則中使用維度時的限制](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions)。


## <a name="multiple-dimensions-multi-dimension"></a>多維度 (多重維度) 

單一計量警示規則也可以監視度量的多個維度值。 計量的維度是成對的名稱/值，其中包含用來描述計量值的其他資料。 例如，儲存體帳戶的**交易**計量有一個稱為**API 名稱**的維度，描述每個交易所呼叫的 API 名稱 (例如 GetBlob、DeleteBlob、PutPage) 。 維度的使用是選擇性的，但它可讓您篩選計量並僅監視特定的時間序列，而不是將計量當做所有維度值的匯總一起監視。 

例如，您可以選擇在所有 API 名稱中的交易數目很高時引發警示 (這是匯總的資料) ，或進一步將其細分為只有當交易數對特定 API 名稱而言很高時才會發出警示。 

監視多個維度的警示規則範例為：

- 目標資源： *myStorage1*
- 度量：筆*交易*
- 維度
  * API 名稱 = *GetBlob，DeleteBlob，PutPage*
- 運算子：*大於*
- 閾值： *70*

針對此警示規則，會監視三個計量時間序列：

- *資源*= ' myStorage1 ' 且*API 名稱*= ' GetBlob ' > 70 的交易
- *資源*= ' myStorage1 ' 且*API 名稱*= ' DeleteBlob ' > 70 的交易
- *資源*= ' myStorage1 ' 且*API 名稱*= ' PutPage ' > 70 的交易

![具有一個維度值的多維度警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

多維度計量警示規則也可以從度量的**不同**維度監視多個維度值。 在此情況下，警示規則會**分別**監視所選取維度值的所有維度值組合。

這種警示規則的範例如下：

- 目標資源： *myStorage1*
- 度量：筆*交易*
- 維度
  * API 名稱 = *GetBlob，DeleteBlob，PutPage*
  * Authentication = *SAS，AccountKey*
- 運算子：*大於*
- 閾值： *70*

針對此警示規則，會分別監視六個計量時間序列：

- *資源*= ' myStorage1 ' 且*API 名稱*= ' GetBlob ' 且*驗證*= ' SAS ' 的交易 > 70
- *資源*= ' myStorage1 ' 且*API 名稱*= ' GetBlob ' 且*Authentication*= ' AccountKey ' 的交易 > 70
- *資源*= ' myStorage1 ' 且*API 名稱*= ' DeleteBlob ' 且*驗證*= ' SAS ' 的交易 > 70
- *資源*= ' myStorage1 ' 且*API 名稱*= ' DeleteBlob ' 且*Authentication*= ' AccountKey ' 的交易 > 70
- *資源*= ' myStorage1 ' 且*API 名稱*= ' PutPage ' 且*驗證*= ' SAS ' 的交易 > 70
- *資源*= ' myStorage1 ' 且*API 名稱*= ' PutPage ' 且*Authentication*= ' AccountKey ' 的交易 > 70

![包含來自多個維度之值的多維度警示規則](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>先進的多維度功能

1.  **選取所有目前和未來的維度**–您可以選擇監視維度的所有可能值，包括未來的值。 這類警示規則會自動調整以監視維度的所有值，而您不需要在每次新增或移除維度值時修改警示規則。
2.  **排除維度**–選取 [≠] ([排除維度值的) 運算子] 相當於選取該維度的所有其他值，包括未來的值。
3.  **新的和自訂維度**– Azure 入口網站中顯示的維度值是以過去三天內收集的計量資料為基礎。 如果您要尋找的維度值尚未發出，您可以加入自訂維度值。

![先進的多維度功能](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>度量警示定價

計量警示規則的定價可在[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)上取得。

建立計量警示規則時，所提供的價格估計是以選取的功能和受監視的時間序列數目為依據，這取決於規則設定和目前的計量值。 不過，每月費用是以時間序列的實際評估為基礎，因此，如果某個時間序列沒有要評估的資料，或如果警示規則使用可讓它以動態方式擴充的功能，則會與原始估計值不同。

例如，如果警示規則利用多維度功能，而且選取了大量的維度值組合，而導致監視許多時間序列，則會顯示高價格估計。 但是，如果並非所有由維度值組合所產生的時間序列，實際上有要評估的資料，則該警示規則的實際費用可能較低。

## <a name="next-steps"></a>後續步驟

深入瞭解如何使用計量警示和[動態閾值](alerts-dynamic-thresholds.md)大規模進行監視。
