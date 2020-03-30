---
title: 使用 Azure 指標資源管理器入門
description: 了解如何使用 Azure 計量瀏覽器建立您的第一個計量圖表。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248771"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>開始使用 Azure 計量瀏覽器

## <a name="where-do-i-start"></a>我在哪裡開始
Azure 監視器計量瀏覽器是 Microsoft Azure 入口網站的一個元件，可用來繪製圖表、以視覺方式串連趨勢，以及調查計量值中的突增值與突降值。 使用計量瀏覽器來調查資源的健康情況和使用量。 按以下順序開始：

1. [選擇資源和指標](#create-your-first-metric-chart)，您將看到一個基本圖表。 然後[選擇與您的調查相關的時間範圍](#select-a-time-range)。

1. 嘗試[應用維度篩選器和拆分](#apply-dimension-filters-and-splitting)。 篩選器和拆分允許您分析指標的哪些段有助於總體指標值並識別可能的異常值。

1. 在固定到儀表板之前，使用[高級設置](#advanced-chart-settings)自訂圖表。 [將警報配置為](alerts-metric-overview.md)在指標值超過或低於閾值時接收通知。

## <a name="create-your-first-metric-chart"></a>創建第一個指標圖表

要從資源、資源組、訂閱或 Azure 監視器視圖中創建指標圖表，請打開 **"指標"** 選項卡，然後按照以下步驟操作：

1. 使用資源選取器，選擇要查看指標的資源。 （如果在特定資源的上下文中打開**指標**，則預先選擇資源）。

    > ![選取資源](./media/metrics-getting-started/resource-picker.png)

2. 對於某些資源，必須選擇命名空間。 命名空間只是組織計量而讓您可以輕鬆找到計量的一種方式。 例如，儲存體帳戶有個別的命名空間，用來儲存檔案、資料表、Blob 和佇列計量。 許多資源類型只有一個命名空間。

3. 從可用指標清單中選擇指標。

    > ![選取計量](./media/metrics-getting-started/metric-picker.png)

4. 或者，您可以更改指標聚合。 例如，您可能希望圖表顯示指標的最小值、最大值或平均值。

> [!NOTE]
> 如果您想要查看在相同圖表中繪製的多個計量，請使用 [新增計量]**** 按鈕，並重複前述步驟。 對於一個視圖中的多個圖表，選擇頂部的 **"添加圖表**"按鈕。

## <a name="select-a-time-range"></a>選取時間範圍

根據預設，圖表會顯示最近 24 小時的計量資料。 使用**時間選取器**面板更改圖表上的時間範圍、放大或縮小。 

![變更時間範圍面板](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> 使用**時間畫筆**調查圖表的有趣區域（尖峰或凹陷）。 將滑鼠指標放在區域的開頭，按一下並按住滑鼠左鍵，拖動到區域的另一側，然後鬆開該按鈕。 圖表將會放大該時間範圍。 

## <a name="apply-dimension-filters-and-splitting"></a>套用維度篩選和分割

[篩選](metrics-charts.md#apply-filters-to-charts)和[拆分](metrics-charts.md#apply-splitting-to-a-chart)是具有維度的指標的強大診斷工具。 這些功能顯示各種指標段（"維度值"）如何影響指標的總價值，並允許您識別可能的異常值。

- **篩選**可讓您選擇要包含在圖表中的維度值。 例如，在繪製*伺服器回應時間*指標時，您可能希望顯示成功的請求。 您需要對*請求維度的成功*應用篩選器。 

- **分割**可控制圖表是要針對維度的每個值顯示個別的線條，還是將值彙總成單一線條。 例如，您可以看到一行跨所有伺服器實例的平均回應時間，或者查看每個伺服器的單獨行。 您需要在*伺服器實例*維度上應用拆分才能看到單獨的行。

請參閱已套用篩選和分割的[圖表範例](metric-chart-samples.md)。 本文顯示這些步驟用於配置圖表。

## <a name="advanced-chart-settings"></a>進階圖表設定

您可以自訂圖表樣式、標題，以及修改進階圖表設定。 完成自訂後，請將其釘選到儀表板，以儲存您的工作。 您也可以設定計量警示。 請按照[產品文檔](metrics-charts.md)瞭解 Azure 監視器指標資源管理器的這些和其他高級功能。

## <a name="next-steps"></a>後續步驟

* [瞭解指標資源管理器的高級功能](metrics-charts.md)
* [計量瀏覽器的疑難排解](metrics-troubleshoot.md)
* [查看 Azure 服務的可用計量清單](metrics-supported.md)
* [查看已設定的圖表範例](metric-chart-samples.md)
