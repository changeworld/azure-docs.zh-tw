---
title: Azure 監視器 (Preview 的自訂欄位) |Microsoft Docs
description: Azure 監視器的自訂欄位功能，可讓您從 Log Analytics 工作區中的記錄建立可搜尋的欄位，並將其新增至所收集記錄的屬性。  本文說明用來建立自訂欄位的程序，並透過範例事件提供詳細的逐步解說。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: 496dab24f636c97e1c7b27b871e1fded9216277d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448571"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>在 Azure 監視器 (Preview 的 Log Analytics 工作區中建立自訂欄位) 

> [!NOTE]
> 本文說明如何在 Log Analytics 工作區中剖析收集的文字資料。 我們建議您在依照 [Azure 監視器中剖析文字資料](../log-query/parse-text.md)所述的指引，在收集查詢篩選器之後剖析文字資料。 它比使用自訂欄位提供數個優點。

> [!IMPORTANT]
> 自訂欄位可增加 Log Analytics 工作區中收集的資料量，這會增加您的成本。 如需詳細資訊，請參閱[使用 Azure 監視器記錄來管理使用量和成本](manage-cost-storage.md#pricing-model)。

Azure 監視器的 **自訂欄位** 功能，可讓您新增自己的可搜尋欄位，以擴充 Log Analytics 工作區中的現有記錄。  自訂欄位會自動填入擷取自同一筆記錄中其他屬性的資料。

![圖表顯示與 Log Analytics 工作區中已修改記錄相關聯的原始記錄，並將屬性值組新增至已修改記錄中的原始屬性。](media/custom-fields/overview.png)

例如，以下範例記錄在事件描述中埋藏了有用的資料。 將這份資料解壓縮至不同的屬性，可供排序和篩選這類動作使用。

![範例解壓縮](media/custom-fields/sample-extract.png)

> [!NOTE]
> 在預覽版中，工作區限制只能有 100 個自訂欄位。  這項功能正式上市時，此限制數量將會擴大。

## <a name="creating-a-custom-field"></a>建立自訂欄位
當您在建立自訂欄位時，Log Analytics 必須了解要用來填入其值的資料。  它會使用來自 Microsoft Research 稱為 FlashExtract 的技術快速識別此資料。  Azure 監視器從您提供的範例中學習您想要解壓縮的資料，而不需要提供明確的指示。

下列各節提供用於建立自訂欄位的程序。  本文最後會逐步解說範例擷取作業。

> [!NOTE]
> 當符合指定準則的記錄新增至 Log Analytics 工作區時，會填入自訂欄位，因此它只會出現在自訂欄位建立之後所收集的記錄上。  建立自訂欄位時便已存在於資料存放區的記錄不會新增自訂欄位。
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>步驟 1 – 識別將會具有自訂欄位的記錄
第一個步驟是識別會取得自訂欄位的記錄。  您可以從 [標準記錄查詢](../log-query/log-query-overview.md) 開始，然後選取要作為 Azure 監視器將從中學習的模型。  當您指定您要將資料擷取到自訂欄位時，便會開啟 [欄位擷取精靈] **** 供您驗證及精簡準則。

1. 移至 [ **記錄** 檔]，然後使用 [查詢來](../log-query/log-query-overview.md) 抓取將會具有自訂欄位的記錄。
2. 選取 Log Analytics 將用來做為模型以擷取資料來填入自訂欄位的記錄。  您會識別您想要從這筆記錄擷取的資料，Log Analytics 會使用這項資訊來判斷要為所有類似記錄填入自訂欄位的邏輯。
3. 展開 [記錄屬性]，按一下記錄頂端屬性左邊的省略號，然後選取 [將 **欄位解壓縮**]。
4. [ **欄位解壓縮嚮導]** 隨即開啟，而且您選取的記錄會顯示在 [ **主要範例** ] 資料行中。  這些記錄的自訂欄位將會以所選屬性中的相同值來定義。  
5. 如果已選取的項目未完全符合您想要選取的項目，請選取其他欄位以縮小準則範圍。  若要變更準則的欄位值，您必須先取消，再選取符合您所需準則的不同記錄。

### <a name="step-2---perform-initial-extract"></a>步驟 2 - 執行初始擷取。
一旦您識別出將會具有自訂欄位的記錄，您就已識別您想要擷取的資料。  Log Analytics 會使用這項資訊來識別類似記錄中的類似模式。  在這之後的步驟中，您將可以驗證結果，並提供更進一步的詳細資料以供 Log Analytics 用於其分析中。

1. 在範例記錄中醒目提示您想要填入自訂欄位的文字。  接著，您會看到一個對話方塊，讓您提供欄位的名稱和資料類型，以及執行初始解壓縮。  字元** \_ CF**將會自動附加。
2. 按一下 [擷取] **** 以對收集的記錄進行分析。  
3. [摘要]**** 和 [搜尋結果]**** 區段會顯示擷取結果，以供您檢查其正確性。  **** 會顯示用來識別記錄的準則，以及每個所識別之資料值的計數。  **** 會提供符合準則之記錄的詳細清單。

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>步驟 3 – 驗證擷取的正確性並建立自訂欄位
一旦您執行過初始擷取，Log Analytics 就會根據收集到的資料顯示其結果。  如果結果看起來是正確的，您就可以建立自訂欄位，無須再進行其他工作。  如果不正確，則可以精簡結果以便讓 Log Analytics 提升其邏輯。

1. 如果初始擷取中有任何值不正確，則請按一下錯誤記錄旁的 [編輯]**** 圖示，然後選取 [修改此醒目提示]**** 以修改選取項目。
2. 項目會複製到 [主要範例]**** 底下的 [其他範例]**** 區段。  您可以在此調整醒目提示來協助 Log Analytics 了解其應該選取的項目。
3. 按一下 [擷取] **** ，使用這項新資訊來評估所有現有記錄。  根據這個新情報，記錄的結果可能會有所修改，而不同於您剛才修改的記錄。
4. 繼續加入修正，直到擷取中的所有記錄正確識別要填入新自訂欄位的資料。
5. 當您滿意結果時，按一下 [儲存擷取] **** 。  自訂欄位現已定義完成，但還不會新增到任何記錄中。
6. 請等候符合指定準則的新記錄收集完成，然後再次執行記錄檔搜尋。 新的記錄應該會有自訂欄位。
7. 和任何其他記錄屬性一樣地使用自訂欄位。  您可以使用它來彙總與群組資料，甚至用它來產生新的見解。

## <a name="viewing-custom-fields"></a>檢視自訂欄位
您可以在 Azure 入口網站中，從 Log Analytics 工作區的 [進階設定]**** 功能表檢視管理群組中的所有自訂欄位清單。  依序選取 [資料]**** 和 [自訂欄位]****，可取得工作區中所有自訂欄位的清單。  

![自訂欄位](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>移除自訂欄位
有兩種方式可移除自訂欄位。  第一個方式是在檢視如上所述的完整清單時，每個欄位所具有的 [移除] **** 選項。  另一個方式是擷取一筆記錄，然後按一下欄位左邊的按鈕。  功能表中會有可供移除自訂欄位的選項。

## <a name="sample-walkthrough"></a>範例逐步解說
下面的章節會逐步解說建立自訂欄位的完整範例。  這個範例會擷取 Windows 事件中指出變更狀態之服務的服務名稱。  這取決於 Windows 電腦上的系統啟動期間，由服務控制管理員所建立的事件。  如果您想要跟隨此範例，您必須 [收集系統記錄檔的資訊事件](data-sources-windows-events.md)。

我們輸入下列查詢，以從服務控制管理員傳回事件識別碼為 7036，且為指出服務正在啟動或停止之事件的所有事件。

![螢幕擷取畫面顯示事件來源和識別碼的查詢。](media/custom-fields/query.png)

然後，選取並展開事件識別碼為7036的任何記錄。

![來源記錄](media/custom-fields/source-record.png)

我們會按一下頂端屬性旁的省略號來定義自訂欄位。

![擷取欄位](media/custom-fields/extract-fields.png)

[欄位擷取精靈]**** 會隨即開啟，而且 [主要範例]**** 資料行中已選取 [EventLog]**** 和 [EventID]**** 欄位。  這表示將會為系統記錄檔中事件識別碼為 7036 的事件定義自訂欄位。  這樣就可以了，所以我們不需要再選取其他欄位。

![主要範例](media/custom-fields/main-example.png)

我們醒目提示 [RenderedDescription]**** 屬性中的服務名稱，然後使用 [Service]**** 來識別服務名稱。  自訂欄位將會稱為 **Service_CF**。 此案例中的欄位類型是字串，因此我們可以將它保持不變。

![欄位標題](media/custom-fields/field-title.png)

我們看到某些記錄已正確識別服務名稱，但某些記錄則未如此。   [搜尋結果]**** 顯示 **WMI Performance Adapter** 有部分名稱未能選取到。  **摘要**顯示一個記錄識別**模組安裝程式**，而不是**Windows 模組安裝程式**。  

![螢幕擷取畫面，顯示 [搜尋結果] 窗格中反白顯示的服務名稱部分，以及摘要中反白顯示的錯誤服務名稱。](media/custom-fields/search-results-01.png)

我們先處理 **WMI Performance Adapter** 記錄。  我們按一下它的編輯圖示，然後 [修改此醒目提示] ****。  

![修改醒目提示](media/custom-fields/modify-highlight.png)

我們增加醒目提示範圍來包含 **WMI** 一字，然後重新執行擷取。  

![其他範例](media/custom-fields/additional-example-01.png)

我們可以看到 **WMI Performance Adapter** 的項目已修正，而且 Log Analytics 也已使用該資訊來更正 **Windows Module Installer** 的記錄。

![螢幕擷取畫面，顯示 [搜尋結果] 窗格中反白顯示的完整服務名稱，以及摘要中醒目提示的正確服務名稱。](media/custom-fields/search-results-02.png)

我們現在可以執行查詢來確認已建立 **Service_CF** ，但尚未加入任何記錄。 這是因為自訂欄位不適用於現有的記錄，因此我們需要等待收集新的記錄。

![初始計數](media/custom-fields/initial-count.png)

過了一會兒之後，新事件已收集完成，我們可以看到 [Service_CF]**** 欄位現在會新增到符合準則的記錄中。

![最終結果](media/custom-fields/final-results.png)

現在我們可以和任何其他記錄屬性一樣地使用自訂欄位。  為了說明這一點，我們建立以新的 [Service_CF]**** 欄位來群組的查詢，以檢查哪些服務最常使用。

![以查詢分組](media/custom-fields/query-group.png)

## <a name="next-steps"></a>後續步驟
* 瞭解 [記錄查詢](../log-query/log-query-overview.md) ，以使用自訂欄位來建立查詢，以進行準則。
* 監視可利用自訂欄位來剖析的[自訂記錄檔](data-sources-custom-logs.md)。

