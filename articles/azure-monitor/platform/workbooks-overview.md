---
title: Azure 監視器活頁簿概觀
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658229"
---
# <a name="azure-monitor-workbooks"></a>Azure 監視器活頁簿

活頁簿提供彈性的畫布來進行資料分析，並在 Azure 入口網站中建立豐富的視覺效果報表。 它們可讓您從 Azure 中深入瞭解多個資料來源，並將它們結合成整合的互動體驗。 

## <a name="data-sources"></a>資料來源

活頁簿可以查詢 Azure 內多個來源的資料。 活頁簿的作者可以轉換這項資料，以提供基礎元件的可用性、效能、使用狀況和整體健全狀況的深入解析。 例如，分析來自虛擬機器的效能記錄，以識別高 CPU 或低記憶體實例，並將結果顯示為互動式報表中的方格。
  
但活頁簿的實際威力是能夠在單一報表內結合不同來源的資料。 這可讓您在資源之間建立複合資源檢視或聯結，以提供更豐富的資料和深入解析，否則可能無法進行。

活頁簿目前與下列資料來源相容：

* [記錄](workbooks-data-sources.md#logs)
* [計量](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [警示 (預覽)](workbooks-data-sources.md#alerts-preview)
* [工作負載健全狀況（預覽）](workbooks-data-sources.md#workload-health-preview)
* [Azure 資源健康狀態（預覽）](workbooks-data-sources.md#azure-resource-health)
* [Azure 資料總管（預覽）](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>視覺效果

活頁簿提供了一組豐富的功能，可將您的資料視覺化。 如需每個視覺效果類型的詳細範例，您可以查閱下列範例連結：

* [Text](workbooks-visualizations.md#text)
* [圖表](workbooks-visualizations.md#charts)
* [表格](workbooks-visualizations.md#grids)
* [並排顯示](workbooks-visualizations.md#tiles)
* [樹木](workbooks-visualizations.md#trees)
* [圖表](workbooks-visualizations.md#graphs)

![範例活頁簿視覺效果](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>開始使用

若要探索活頁簿體驗，請先流覽至 Azure 監視器服務。 這可以藉由在 [Azure 入口網站] 的 [搜尋] 方塊中輸入**Monitor**來完成。

然後選取 **[活頁簿（預覽）**]。

![紅色方塊中反白顯示之活頁簿預覽按鈕的螢幕擷取畫面](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>主機庫

這會帶您前往 [活頁簿] 圖庫：

![Azure 監視器活頁簿資源庫視圖的螢幕擷取畫面](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>活頁簿與活頁簿範本的比較

您可以看到以綠色呈現的活頁_簿_，並以紫色顯示一些活頁_簿範本_。 範本會作為策劃報表，專為多個使用者和小組彈性地重複使用所設計。 開啟範本時，會建立以範本內容填入的暫時性活頁簿。 

您可以調整以範本為基礎的活頁簿參數，並執行分析，而不需要擔心未來的同事報告體驗。 如果您開啟範本、進行一些調整，然後選取 [儲存] 圖示，則您會將範本儲存為活頁簿，這樣會以綠色顯示原始範本。 

實際上，範本也與儲存的活頁簿不同。 儲存活頁簿會建立相關聯的 Azure Resource Manager 資源，而只開啟範本時所建立的暫時性活頁簿沒有與其相關聯的唯一資源。 若要深入瞭解如何在活頁簿中管理存取控制，請參閱活頁[簿存取控制文章](workbooks-access-control.md)。

### <a name="exploring-a-workbook-template"></a>探索活頁簿範本

選取 [**應用程式失敗分析**]，以查看其中一個預設的應用程式活頁簿範本。

![應用程式失敗分析範本的螢幕擷取畫面](./media/workbooks-overview/failure-analysis.png)

如先前所述，開啟範本會建立暫時的活頁簿，讓您能夠與進行互動。 根據預設，活頁簿會在閱讀模式中開啟，只顯示原始範本作者所建立之預期分析體驗的資訊。

在這個特定的活頁簿案例中，體驗是互動式的。 您可以調整 [訂用帳戶]、[目標應用程式]，以及您想要顯示之資料的時間範圍。 一旦您進行這些選擇，HTTP 要求的方格也會互動式，而選取個別的資料列將會變更在報表底部的兩個圖表中呈現的資料。

### <a name="editing-mode"></a>編輯模式

若要瞭解如何將此活頁簿範本放在一起，您必須選取 [**編輯**] 以交換至編輯模式。 

![應用程式失敗分析範本的螢幕擷取畫面](./media/workbooks-overview/edit.png)

當您切換到編輯模式時，您會注意到右側會出現一些**編輯**框，對應于活頁簿的每個個別層面。

![[編輯] 按鈕的螢幕擷取畫面](./media/workbooks-overview/edit-mode.png)

如果我們選取 [要求資料] 方格底下的 [編輯] 按鈕，我們可以看到活頁簿的這個部分是由 Application Insights 資源的資料 Kusto 查詢所組成。

![基礎 Kusto 查詢的螢幕擷取畫面](./media/workbooks-overview/kusto.png)

按一下右側的其他 [**編輯**] 按鈕，將會顯示構成活頁簿的一些核心元件，例如以 markdown 為基礎的[文字方塊](workbooks-visualizations.md#text)、[參數選取](workbooks-parameters.md)的 UI 元素，以及其他[圖表/視覺效果類型](workbooks-visualizations.md)。 

在編輯模式中探索預先建立的範本，然後加以修改以符合您的需求，並儲存您自己的自訂活頁簿，是開始瞭解 Azure 監視器活頁簿可能發生之情況的絕佳方式。

## <a name="pinning-visualizations"></a>釘選視覺效果

活頁簿中的文字、查詢和計量步驟可以在活頁簿處於 pin 模式時，使用這些專案上的 [釘選] 按鈕來釘選，或是活頁簿作者已針對該元素啟用設定，讓釘選圖示可見。 

若要存取 pin 模式，請按一下 [**編輯**] 進入編輯模式，然後選取頂端列中的藍色釘選圖示。 然後，個別的釘選圖示就會出現在畫面右側每個對應的活頁簿元件的*編輯*框上方。

![Pin 體驗](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 活頁簿的狀態會在釘選時儲存，而在基礎活頁簿已修改時，儀表板上的已釘選活頁簿將不會更新。 為了更新釘選的活頁簿元件，您必須刪除並重新釘選該元件。

## <a name="dashboard-time-ranges"></a>儀表板時間範圍

如果已釘選的專案已設定為使用*時間範圍*參數，則釘選的活頁簿查詢元件會遵循儀表板的時間範圍。 儀表板的時間範圍值將用來做為時間範圍參數的值，而儀表板時間範圍的任何變更都會導致已釘選的專案更新。 如果釘選的部分使用儀表板的時間範圍，您就會看到 [釘選的部分] 更新的子標題，以在每次時間範圍變更時顯示儀表板的時間範圍。 

此外，使用時間範圍參數釘選的活頁簿元件，將會以儀表板的時間範圍所決定的速率自動重新整理。 上次執行查詢的時間會出現在釘選部分的子標題中。

如果釘選的步驟已明確設定時間範圍（不使用時間範圍參數），則不論儀表板的設定為何，該時間範圍一律會用於儀表板。 釘選的部分的子標題不會顯示儀表板的時間範圍，而且查詢不會在儀表板上自動重新整理。 子標題會顯示上次執行查詢的時間。

> [!NOTE]
> 釘選到儀表板時，目前不支援使用*合併*資料來源的查詢。

## <a name="sharing-workbook-templates"></a>共用活頁簿範本

當您開始建立自己的活頁簿範本時，您可能會想要將它與更廣大的社區分享。 若要深入瞭解並探索不屬於預設 Azure 監視器資源庫視圖的其他範本，請造訪我們的[GitHub 存放庫](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。 若要流覽現有的活頁簿，請造訪 GitHub 上的活頁[簿文件庫](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)。

## <a name="next-step"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
