---
title: Azure 監視器活頁簿概觀
description: 瞭解活頁簿如何提供彈性的畫布來分析資料，以及如何在 Azure 入口網站中建立豐富的視覺效果報表。
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: dd0c08fa968f9d029f1b89be1183ab374f1b5348
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932391"
---
# <a name="azure-monitor-workbooks"></a>Azure 監視器活頁簿

活頁簿提供彈性的畫布，可供您用來分析資料，並在 Azure 入口網站中建立豐富的視覺效果報表。 它們可讓您從 Azure 跨多個資料來源，並將它們結合成整合的互動式體驗。 

以下是建立活頁簿的影片逐步解說。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>資料來源

活頁簿可以查詢 Azure 內多個來源的資料。 活頁簿的作者可以轉換此資料，以深入了解底層元件的可用性、效能、使用狀況與整體健康情況。 例如，分析虛擬機器的效能記錄以識別高 CPU 或記憶體不足的實例，並在互動式報表中將結果顯示為方格。
  
但活頁簿的實際威力是能夠在單一報表內結合來自不同來源的資料。 這可讓您建立複合資源檢視或跨資源的聯結，以提供更豐富的資料和深入解析，否則可能會造成無法實現。

活頁簿目前與下列資料來源相容：

* [記錄](workbooks-data-sources.md#logs)
* [計量](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [警示 (預覽) ](workbooks-data-sources.md#alerts-preview)
* [工作負載健全狀況](workbooks-data-sources.md#workload-health)
* [Azure 資源健康狀態](workbooks-data-sources.md#azure-resource-health)
* [Azure 資料總管](workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>視覺效果

活頁簿提供了一組豐富的功能，可將您的資料視覺化。 如需每種視覺效果類型的詳細範例，您可以參考下列範例連結：

* [Text](workbooks-text-visualizations.md)
* [圖表](workbooks-chart-visualizations.md)
* [方格](workbooks-grid-visualizations.md)
* [磚](workbooks-tile-visualizations.md)
* [樹木](workbooks-tree-visualizations.md)
* [Graphs](workbooks-graph-visualizations.md)
* [複合列](workbooks-composite-bar.md)

![範例活頁簿視覺效果](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>開始使用

若要探索活頁簿體驗，請先流覽至 Azure 監視器服務。 在 Azure 入口網站的 [搜尋] 方塊中輸入 [ **監視器** ]，即可完成這項操作。

然後選取 [活頁 **簿**]。

![以紅色方塊反白顯示的活頁簿按鈕螢幕擷取畫面](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>主機庫

這會帶您前往活頁簿資源庫：

![Azure 監視器活頁簿資源庫視圖的螢幕擷取畫面](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>活頁簿與活頁簿範本

您可以看到以綠色顯示的活頁 _簿_ ，以及紫色的活頁 _簿範本_ 數。 範本可做為策劃的報表，其設計目的是要讓多個使用者和小組有彈性地重複使用。 開啟範本時，會建立一個以範本內容填入的暫時性活頁簿。 

您可以調整以範本為基礎的活頁簿參數，並執行分析，而不必擔心為同事的未來報告體驗中斷。 如果您開啟範本，請進行一些調整，然後選取 [儲存] 圖示，您會將範本儲存為活頁簿，然後以綠色顯示，讓原始範本保持不變。 

在幕後，範本也與儲存的活頁簿不同。 儲存活頁簿會建立相關聯的 Azure Resource Manager 資源，而只開啟範本時所建立的暫時性活頁簿沒有與其相關聯的唯一資源。 若要深入瞭解如何在活頁簿中管理存取控制，請參閱活頁 [簿存取控制文章](workbooks-access-control.md)。

### <a name="exploring-a-workbook-template"></a>探索活頁簿範本

選取 [ **應用程式失敗分析** ]，以查看其中一個預設的應用程式活頁簿範本。

![應用程式失敗分析範本的螢幕擷取畫面](./media/workbooks-overview/failure-analysis.png)

如先前所述，開啟範本會建立暫時的活頁簿，讓您能夠與互動。 依預設，活頁簿會在閱讀模式中開啟，只顯示原始範本作者所建立之預定分析體驗的資訊。

在此特定活頁簿的案例中，體驗是互動式的。 您可以調整訂用帳戶、目標應用程式，以及您想要顯示之資料的時間範圍。 一旦您選取了這些選項，HTTP 要求的方格也會互動，而選取個別的資料列將會變更在報表底部的兩個圖表中呈現的資料。

### <a name="editing-mode"></a>編輯模式

若要瞭解如何將此活頁簿範本放在一起，您必須選取 [ **編輯**] 以切換到編輯模式。

![活頁簿中 [編輯] 按鈕的螢幕擷取畫面。](./media/workbooks-overview/edit.png)

切換到編輯模式之後，您會注意到右側會出現一些 **編輯** 框，對應到活頁簿的每個個別層面。

![[編輯] 按鈕的螢幕擷取畫面](./media/workbooks-overview/edit-mode.png)

如果我們在 [要求資料] 方格中選取立即的 [編輯] 按鈕，我們可以看到活頁簿的這個部分是由 Application Insights 資源的資料 Kusto 查詢所組成。

![基礎 Kusto 查詢的螢幕擷取畫面](./media/workbooks-overview/kusto.png)

按一下右側的其他 [ **編輯** ] 按鈕，即可顯示組成活頁簿的一些核心元件，例如以 markdown 為基礎的 [文字方塊](workbooks-visualizations.md#text)、 [參數選取](workbooks-parameters.md) UI 元素，以及其他 [圖表/視覺效果類型](workbooks-visualizations.md)。 

在編輯模式中探索預先建立的範本，然後加以修改以符合您的需求，並儲存您自己的自訂活頁簿，是開始學習 Azure 監視器活頁簿有哪些可能的絕佳方法。

## <a name="pinning-visualizations"></a>釘選視覺效果

活頁簿中的文字、查詢和計量步驟可以使用 [釘選] 按鈕，在活頁簿處於 [釘選] 模式時，使用這些專案的 [釘選] 按鈕來釘選，或者，如果活頁簿作者已啟用該專案的設定，讓釘選圖示可見。 

若要存取 [pin] 模式，請按一下 [ **編輯** ] 進入編輯模式，然後選取頂端列中的藍色釘選圖示。 然後，個別的釘選圖示將會出現在畫面右側每個對應的活頁簿元件的 *編輯* 框上方。

![Pin 體驗](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 活頁簿的狀態會在釘選時儲存，而且如果修改基礎活頁簿，儀表板上的釘選活頁簿將不會更新。 為了更新釘選的活頁簿元件，您必須刪除並重新釘選該部分。

## <a name="dashboard-time-ranges"></a>儀表板時間範圍

如果已釘選的專案設定為使用 *時間範圍* 參數，則已釘選的活頁簿查詢元件會遵守儀表板的時間範圍。 儀表板的時間範圍值將作為時間範圍參數的值，而儀表板時間範圍的任何變更都會導致更新釘選的專案。 如果釘選的部分使用儀表板的時間範圍，您將會看到釘選的部分更新子標題，以在每次時間範圍變更時顯示儀表板的時間範圍。 

此外，使用時間範圍參數釘選的活頁簿部分，將會依儀表板的時間範圍所決定的速率自動重新整理。 最後一次執行查詢時，將會出現在釘選部分的子標題中。

如果釘選的步驟具有明確設定的時間範圍 (不會使用時間範圍參數) ，則不論儀表板的設定為何，該時間範圍一律會用於儀表板。 釘選元件的子標題不會顯示儀表板的時間範圍，而且查詢將不會在儀表板上自動重新整理。 子標題將會顯示上次執行查詢的時間。

> [!NOTE]
> 當釘選到儀表板時，目前不支援使用 *合併* 資料來源的查詢。

## <a name="sharing-workbook-templates"></a>共用活頁簿範本

一旦您開始建立自己的活頁簿範本，您可能會想要與更廣大的社區共用。 若要深入瞭解，並探索其他不屬於預設 Azure 監視器資源庫視圖的範本，請造訪我們的 [GitHub 存放庫](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。 若要流覽現有的活頁簿，請造訪 GitHub 上的活頁 [簿文件庫](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) 。

## <a name="next-step"></a>後續步驟

* [開始深入](workbooks-visualizations.md) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
