---
title: 開始使用 Azure 計量瀏覽器
description: 了解如何使用 Azure 計量瀏覽器建立您的第一個計量圖表。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 14f21b1c0bff5f4d5f77b1547c09ddd6c370ece0
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276425"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>開始使用 Azure 計量瀏覽器

## <a name="where-do-i-start"></a>我該從哪裡開始
Azure 監視器計量瀏覽器是 Microsoft Azure 入口網站的一個元件，可用來繪製圖表、以視覺方式串連趨勢，以及調查計量值中的突增值與突降值。 使用計量瀏覽器來調查資源的健康情況和使用量。 以下列順序啟動：

1. [挑選資源和](#create-your-first-metric-chart)計量，您會看到基本圖表。 然後選取與調查相關的[時間範圍](#select-a-time-range)。

1. 請嘗試套用[維度篩選和分割](#apply-dimension-filters-and-splitting)。 篩選和分割可讓您分析計量的哪些區段會構成整體計量值，以及識別可能的極端值。

1. 在釘選到儀表板之前，請先使用 [[高級設定](#advanced-chart-settings)] 來自訂圖表。 [設定警示](alerts-metric-overview.md)，以在計量值超過或低於閾值時收到通知。

## <a name="create-your-first-metric-chart"></a>建立您的第一個度量圖表

若要建立度量圖表，請從資源、資源群組、訂用帳戶或 Azure 監視器視圖中開啟 [**計量**] 索引標籤，並遵循下列步驟執行：

1. 使用資源選擇器，選取您要查看其計量的資源。 如果您在特定資源) 的內容中開啟**計量**， (會預先選取資源。

    > ![選取資源](./media/metrics-getting-started/resource-picker.png)

2. 對於某些資源，您必須選取命名空間。 命名空間只是組織計量而讓您可以輕鬆找到計量的一種方式。 例如，儲存體帳戶有個別的命名空間，用來儲存檔案、資料表、Blob 和佇列計量。 許多資源類型只有一個命名空間。

3. 從可用的計量清單中選取 [度量]。

    > ![選取計量](./media/metrics-getting-started/metric-picker.png)

4. （選擇性）您可以[變更計量匯總](metrics-charts.md#changing-aggregation)。 例如，您可能想要讓圖表顯示度量的最小值、最大值或平均值。

> [!NOTE]
> 如果您想要查看在相同圖表中繪製的多個計量，請使用 [新增計量]**** 按鈕，並重複前述步驟。 針對單一視圖中的多個圖表，選取頂端的 [**新增圖表**] 按鈕。

## <a name="select-a-time-range"></a>選取時間範圍

根據預設，圖表會顯示最近 24 小時的計量資料。 使用 [**時間選擇器**] 面板來變更圖表的時間範圍、放大或縮小。 

![變更時間範圍面板](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> 使用**時間筆刷**來調查圖表的有趣區域 (尖峰或 dip) 。 將滑鼠指標放在區域的開頭，按一下並按住滑鼠左鍵，拖曳至 [區域] 的另一端，然後放開按鈕。 圖表將會放大該時間範圍。 

## <a name="apply-dimension-filters-and-splitting"></a>套用維度篩選和分割

[篩選](metrics-charts.md#apply-filters-to-charts)和[分割](metrics-charts.md#apply-splitting-to-a-chart)是具有維度之計量的強大診斷工具。 這些功能顯示不同的計量區段 ( 「維度值」 ) 如何影響度量的整體值，並可讓您識別可能的極端值。

- **篩選**可讓您選擇要包含在圖表中的維度值。 例如，您可能會想要在繪製*伺服器回應時間*度量的圖表時，顯示成功的要求。 您必須在 [要求] 維度的 [*成功*] 上套用篩選準則。 

- **分割**可控制圖表是要針對維度的每個值顯示個別的線條，還是將值彙總成單一線條。 例如，您可以在所有伺服器實例的平均回應時間看到一行，或查看每部伺服器的個別行。 您必須在*伺服器實例*維度上套用分割，才能看到不同的行。

請參閱已套用篩選和分割的[圖表範例](metric-chart-samples.md)。 本文說明用來設定圖表的步驟。

## <a name="advanced-chart-settings"></a>進階圖表設定

您可以自訂圖表樣式、標題，以及修改進階圖表設定。 完成自訂後，請將其釘選到儀表板，以儲存您的工作。 您也可以設定計量警示。 請遵循[產品檔](metrics-charts.md)，以瞭解 Azure 監視器計量瀏覽器的這些和其他先進功能。

## <a name="next-steps"></a>後續步驟

* [瞭解計量瀏覽器的 advanced 功能](metrics-charts.md)
* [計量瀏覽器的疑難排解](metrics-troubleshoot.md)
* [查看 Azure 服務的可用計量清單](metrics-supported.md)
* [查看已設定的圖表範例](metric-chart-samples.md)
