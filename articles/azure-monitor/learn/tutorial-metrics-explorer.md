---
title: 教學課程 - 在 Azure 監視器中建立計量圖表
description: 了解如何使用 Azure 計量瀏覽器建立您的第一個計量圖表。
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 72f90918afa275e56528016539ecb2eec93d5db0
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090086"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>教學課程：在 Azure 監視器中建立計量圖表
計量瀏覽器是 Azure 入口網站中 Azure 監視器的功能之一，可讓您從計量值建立圖表、以視覺化方式將趨勢相互關聯，以及調查計量值的遽增和滑落。 使用計量瀏覽器可調查 Azure 資源的健康情況和使用量，或是從自訂計量繪製圖表。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 選取要繪製圖表的計量
> * 執行不同的計量值彙總
> * 修改圖表的時間範圍和細微性

以下影片顯示比本文中所述程序廣泛的案例。 如果您不熟悉計量，建議您先閱讀這篇文章，然後觀看影片以了解更多細節。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要可監視的 Azure 資源。 您可以使用 Azure 訂用帳戶中任何支援計量的資源。 若要判斷資源是否支援計量，請移至 Azure 入口網站中的對應功能表，並確認該功能表的 [監視]  區段中有 [計量]  選項。


## <a name="log-in-to-azure"></a>登入 Azure
在 [https://portal.azure.com](https://portal.azure.com) 上登入 Azure 入口網站。

## <a name="open-metrics-explorer-and-select-a-scope"></a>開啟計量瀏覽器並選取範圍
您可以從 [Azure 監視器] 功能表或從 Azure 入口網站中的資源功能表，來開啟計量瀏覽器。 無論您使用哪一個選項，都可以取得所有資源的計量。 

1. 從 [Azure 監視器]  功能表或從資源功能表的 [監視]  區段中，選取 [計量]  。

1. 選取 [範圍]  ，這是您想要查看其計量的資源。 如果您是從資源的功能表開啟計量瀏覽器，則會預先填入範圍。

    ![選取範圍](media/tutorial-metrics-explorer/scope-picker.png)

2. 如果範圍有多個**命名空間**，請加以選取。 命名空間只是組織計量而讓您可以輕鬆找到計量的一種方式。 例如，儲存體帳戶有個別的命名空間，用來儲存檔案、資料表、Blob 和佇列計量。 許多資源類型只有一個命名空間。

3. 從所選範圍和命名空間的可用計量清單中選取計量。

    ![選取計量](media/tutorial-metrics-explorer/metric-picker.png)

4. 選擇性地變更計量**彙總**。 這會定義如何依據圖形的時間細微性來彙總計量值。 例如，如果時間細微性設定為 15 分鐘，而彙總設定為總和，則圖形中的每個點將是每個 15 分鐘的區段所收集到的所有值之總和。

    ![顯示 contosoretailweb 圖表 (標題為「總計輸入」) 的螢幕擷取畫面。](media/tutorial-metrics-explorer/chart.png)

5. 如果您想要查看在相同圖表中繪製的多個計量，請使用 [新增計量]  按鈕，並重複前述步驟。 請針對單一檢視中的多個圖表，選取 [新增圖表]  按鈕。

## <a name="select-a-time-range-and-granularity"></a>選取時間範圍和細微性

根據預設，圖表會顯示最近 24 小時的計量資料。 您可以使用時間選擇器來變更圖表的 [時間範圍]  ，或選取 [時間細微性]  來定義每個資料點的時間範圍。 圖表會使用指定的彙總，依據指定的時間細微性來計算所有取樣值。

![變更時間範圍面板](media/tutorial-metrics-explorer/time-picker.png)


使用 [時間筆刷]  可調查值得留意的圖表區域，例如遽增或滑落。 將滑鼠指標放在區域的開頭，然後按住滑鼠左鍵，拖曳至區域的另一端，再放開按鈕。 圖表將會放大該時間範圍。 

![時間筆刷](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>套用維度篩選和分割
請參閱下列適用於進階功能的參考，以利對計量執行額外的分析，並找出資料中可能的極端值。

- [篩選](../platform/metrics-charts.md#apply-filters-to-charts)可讓您選擇要包含在圖表中的維度值。 例如，在繪製*伺服器回應時間*計量的圖表時，您可以僅顯示成功的要求。 

- [分割](../platform/metrics-charts.md#apply-splitting-to-a-chart)可控制圖表是要針對維度的每個值顯示個別的線條，還是將值彙總成單一線條。 例如，您可以將所有伺服器執行個體的平均回應時間顯示為單一線條，或是讓每個伺服器有各自的線條。 

請參閱已套用篩選和分割的[圖表範例](../platform/metric-chart-samples.md)。

## <a name="advanced-chart-settings"></a>進階圖表設定

您可以自訂圖表樣式、標題，以及修改進階圖表設定。 完成自訂後，請將其釘選到儀表板，以儲存您的工作。 您也可以設定計量警示。 請參閱 [Azure 計量瀏覽器的進階功能](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis)，以了解 Azure 監視器計量瀏覽器的前述和其他進階功能。


## <a name="next-steps"></a>後續步驟
現在您已了解如何在 Azure 監視器中使用計量，接下來請了解如何使用計量來傳送主動式警示。

> [!div class="nextstepaction"]
> [使用 Azure 監視器建立、檢視及管理計量警示](../platform/alerts-metric.md)

