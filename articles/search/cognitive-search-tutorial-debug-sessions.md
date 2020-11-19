---
title: 教學課程：使用偵錯工作階段診斷、修正和認可技能集的變更
titleSuffix: Azure Cognitive Search
description: 偵錯工作階段 (預覽) 提供以入口網站為基礎的介面，用以評估和修復技能集中的問題/錯誤
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8ec39c4616f5a34f8326b56d4f0ba6e15cdad91c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699112"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>教學課程：診斷、修復及認可技能集的變更

在本文中，您將使用 Azure 入口網站存取偵測工作階段，以對提供的技能集修復問題。 技能集有一些錯誤需要解決。 本教學課程將引導您完成偵錯工作階段，以識別並解決技能輸入和輸出的問題。

> [!Important]
> 偵錯工作階段是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * Azure 訂用帳戶。 建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，或使用您目前的訂用帳戶
> * Azure 認知搜尋服務執行個體
> * Azure 儲存體帳戶
> * [Postman 桌面應用程式](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>建立服務並載入資料

本教學課程使用 Azure 認知搜尋和 Azure 儲存體服務。

* [下載包含 19 個檔案的範例資料](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)。

* [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)，或[尋找現有帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 

   選擇與 Azure 認知搜尋相同的區域，以避免產生頻寬費用。
   
   選擇 StorageV2 (一般用途 V2) 帳戶類型。

* 開啟儲存體服務頁面，並建立容器。 最佳做法是指定「私人」存取層級。 將您的容器命名為 `clinicaltrialdataset`。

* 在容器中按一下 [上傳]，以上傳您在第一個步驟中下載並解壓縮的範例檔案。

* [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="取得 HTTP 端點和存取金鑰" border="false":::

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="create-data-source-skillset-index-and-indexer"></a>建立資料來源、技能集、索引和索引子

在本節中，會使用 Postman 和提供的集合來建立搜尋服務的資料來源、技能集、索引和索引子。

1. 如果您沒有 Postman，您可以[在這裡下載 Postman 桌面應用程式](https://www.getpostman.com/)。
1. [下載偵錯工作階段 Postman 集合](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. 啟動 Postman
1. 在 [檔案] > [新增] 底下，選取要匯入的集合。
1. 在匯入集合後，展開動作清單 (...)。
1. 按一下 **[編輯]** 。
1. 輸入您的 searchService 名稱 (例如，如果端點為 `https://mydemo.search.windows.net`，則服務名稱為 "`mydemo`")。
1. 輸入 apiKey，其中包含搜尋服務的主要或次要金鑰。
1. 在 Azure 儲存體帳戶的金鑰頁面中，輸入 storageConnectionString。
1. 輸入您在儲存體帳戶中建立之容器的 containerName。

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="在 Postman 中編輯變數":::

集合包含四個用來完成此區段的不同 REST 呼叫。

第一個呼叫會建立資料來源。 第 1 課：建立 Windows Azure 儲存體物件`clinical-trials-ds`。 第二個呼叫會建立技能集 `clinical-trials-ss`。 第三個呼叫會建立索引 `clinical-trials`。 第四個、也就是最後一個呼叫，會建立索引子 `clinical-trials-idxr`。 在集合中的所有呼叫都完成之後，請關閉 Postman，並返回 Azure 入口網站。

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="使用 Postman 建立資料來源":::

## <a name="check-the-results"></a>查看結果

技能集包含一些常見的錯誤。 在本節中，執行空的查詢以傳回所有文件，將會顯示多個錯誤。 在後續步驟中，將會使用偵錯工作階段來解決問題。

1. 在 Azure 入口網站中移至您的搜尋服務。 
1. 選取 [索引] 標籤。 
1. 選取 `clinical-trials` 索引
1. 按一下 [搜尋] 以執行空的查詢。 

搜尋完成後，會列出兩個沒有資料的欄位；[組織] 和 [位置] 會列在視窗中。 請依照下列步驟探索技能集所產生的所有問題。

1. 返回搜尋服務的 [概觀] 頁面。
1. 選取 [索引子] 索引標籤。 
1. 按一下 `clinical-trials-idxr` 並選取警告通知。 

預測輸出欄位對應有許多問題，且第三頁有警告，因為有一或多個技能輸入無效。

返回搜尋服務的 [概觀] 畫面。

## <a name="start-your-debug-session"></a>啟動您的偵錯工作階段

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="啟動新的偵錯工作階段":::

1. 按一下 [偵錯工作階段 (預覽) ] 索引標籤。
1. 選取 +NewDebugSession
1. 指定工作階段名稱。 
1. 將工作階段連線至您的儲存體帳戶。 
1. 提供索引子名稱。 索引子具有資料來源、技能集和索引的參考。
1. 接受選擇集合中第一份文件的預設文件選項。 
1. **儲存** 工作階段。 儲存工作階段後，將會啟動技能集所定義的 AI 擴充管線。

> [!Important]
> 偵錯工作階段僅適用於單一文件。 您可以選取資料集中的特定文件，否則工作階段將會預設為第一份文件。

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="啟動新的偵錯工作階段":::

偵錯工作階段執行完成後，工作階段會預設為 [AI 擴充] 索引標籤，並醒目提示技能圖表。

+ 技能圖表會提供技能集的視覺化階層，及其執行順序 (由上至下)。 圖表中並排顯示的技能會以平行方式執行。 圖表中以色彩編碼的技能，表示正在技能集中執行的技能類型。 在此範例中，綠色技能是文字，紅色技能是視覺。 按一下圖表中的個別技能，將會在工作階段視窗的右窗格中顯示該技能執行個體的詳細資料。 技能設定、JSON 編輯器、執行詳細資料和錯誤/警告全都可供檢閱和編輯。
+ [豐富型資料結構] 會詳細說明技能從來源文件的內容產生的擴充樹狀結構所包含的節點。

[錯誤/警告] 索引標籤會提供比先前所顯示精簡得多的清單，因為此清單只會詳列單一文件的錯誤。 如同索引子所顯示的清單，您可以按一下警告訊息，並查看此警告的詳細資料。

## <a name="fix-missing-skill-input-value"></a>修正遺漏的技能輸入值

在 [錯誤/警告] 索引標籤中，標示為 `Enrichment.NerSkillV2.#1` 的作業有一項錯誤。 此錯誤的詳細資料指出技能輸入值 '/document/languageCode' 有問題。 

1. 返回 [AI 擴充] 索引標籤。
1. 按一下 [技能圖表]。
1. 按一下標示為 #1 的技能，以在右窗格中顯示其詳細資料。
1. 找出 "languageCode" 的輸入。
1. 選取行首的 **</>** 符號，然後開啟運算式評估工具。
1. 按一下 [評估] 按鈕，以確認此運算式會產生錯誤。 這會確認 "languageCode" 屬性不是有效的輸入。

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="運算式評估工具":::

有兩種方式可研究工作階段中的這項錯誤。 第一種方式是查看輸入來自何處 - 階層中的哪項技能最有可能產生此結果？ [技能詳細資料] 窗格中的 [執行] 索引標籤應該會顯示輸入的來源。 如果沒有來源，就表示有欄位對應錯誤。

1. 按一下 [執行] 索引標籤。
1. 查看 INPUTS 並尋找 "languageCode"。 此輸入的來源並未列出。 
1. 切換至左窗格以顯示豐富型資料結構。 沒有任何對應的路徑對應至 "languageCode"。

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="豐富型資料結構":::

"language" 有對應的路徑。 這表示技能設定中有錯字。 若要修正此問題，必須在具有運算式 '/document/language' 的 #1 技能中更新運算式。

1. 對路徑 "language" 開啟運算式評估工具 **</>** 。
1. 複製運算式。 關閉視窗。
1. 移至 #1 技能的 [技能設定]，然後對輸入 "languageCode" 開啟運算式評估工具 **</>** 。
1. 將新的值 '/document/language' 貼入 [運算式] 方塊中，然後按一下 [評估]。
1. 此時應該會顯示正確的輸入 "en"。 按一下 [套用] 以更新運算式。
1. 在右側的 [技能詳細資料] 窗格中，按一下 [儲存]。
1. 在工作階段的視窗功能表中，按一下 [執行]。 如此即會使用文件再次執行技能集。 

在偵錯工作階段執行完成後，按一下 [錯誤/警告] 索引標籤，此時會顯示標示為 "Enrichment.NerSkillV2.#1" 的錯誤已消失。 但仍有兩個警告，指出服務無法將組織和位置的輸出欄位對應至搜尋索引。 有遺漏的值：'/document/merged_content/organizations' 和 '/document/merged_content/locations'。

## <a name="fix-missing-skill-output-values"></a>修正遺漏的技能輸出值

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="錯誤和警告":::

技能有遺漏的輸出值。 若要識別發生錯誤的技能，請移至 [豐富型資料結構]，尋找值名稱，並查看其原始來源。 在遺漏組織和位置值的案例中，這些值是技能 #1 的輸出。 對每個路徑開啟運算式評估工具 </>，將會顯示分別列為 '/document/content/organizations' 和 '/document/content/locations' 的運算式。

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="運算式評估工具組織實體":::

這些實體的輸出是空的，但預期不應空白。 哪些輸入產生了此結果？

1. 移至 [技能圖表]，然後選取技能 #1。
1. 在右側的 [技能詳細資料] 窗格中，選取 [執行] 索引標籤。
1. 對 INPUT "text" 開啟運算式評估工具 **</>** 。

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="文字技能的輸入":::

此輸入的顯示結果看起來不像是文字輸入。 看起來像是以新行圍繞的影像。 缺少文字表示無法識別實體。 查看技能集的階層時，會發現內容先由 #6 (OCR) 技能處理，然後再傳至 #5 (合併) 技能。 

1. 在 [技能圖表] 中選取 #5 (合併) 技能。
1. 在右側的 [技能詳細資料] 窗格中選取 [執行] 索引標籤，然後對 OUTPUTS "mergedText" 開啟運算式評估工具 **</>** 。

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="合併技能的輸出":::

這裡的文字會與影像成對顯示。 查看運算式 '/document/merged_content' 時，可看到 #1 技能的 "organizations" 和 "locations" 路徑中的錯誤。 不應使用 '/document/content'，而應使用 '/document/merged_content' 作為 "text" 輸入。

1. 複製 "mergedText" 輸出的運算式，然後關閉 [運算式評估工具] 視窗。
1. 在 [技能圖表] 中選取技能 #1。
1. 在右側的 [技能詳細資料] 窗格中，選取 [技能設定] 索引標籤。
1. 對 "text" 輸入開啟運算式評估工具 **</>** 。
1. 將新的運算式貼入方塊中。 按一下 [評估]。
1. 此時應該會顯示正確的輸入，內含新增的文字。 按一下 [套用] 以更新技能設定。
1. 在右側的 [技能詳細資料] 窗格中，按一下 [儲存]。
1. 在工作階段視窗功能表中，按一下 [執行]。 如此即會使用文件再次執行技能集。

在索引子執行完成後，錯誤仍存在。 回到技能 #1 加以調查。 該技能的輸入已從 'content' 更正為 'merged_content'。 技能中的這些實體產生了哪些輸出？

1. 選取 [AI 擴充] 索引標籤。
1. 選取 [技能圖表]，然後按一下技能 #1。
1. 瀏覽 [技能設定] 以尋找 "outputs"。
1. 對 "organizations" 實體開啟運算式評估工具 **</>** 。

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="組織實體的輸出":::

評估運算式的結果後，得到了正確的結果。 技能有效識別出實體 "organizations" 的正確值。 不過，實體路徑中的輸出對應仍擲回錯誤。 將技能中的輸出路徑與錯誤中的輸出路徑進行比較時，作為輸出、組織和位置父代的技能位於 /document/content 節點下。 但輸出欄位對應預期結果會在 /document/merged_content 節點下成為父代。 在上一個步驟中，輸入已從 '/document/content' 變更為 '/document/merged_content'。 您必須變更技能設定中的內容，以確保輸出會以正確的內容產生。

1. 選取 [AI 擴充] 索引標籤。
1. 選取 [技能圖表]，然後按一下技能 #1。
1. 瀏覽 [技能設定] 以尋找 "context"。
1. 按兩下 "context" 的設定並加以編輯，使其顯示為 '/document/merged_content'。
1. 在右側的 [技能詳細資料] 窗格中，按一下 [儲存]。
1. 在工作階段視窗功能表中，按一下 [執行]。 如此即會使用文件再次執行技能集。

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="技能設定中的內容更正":::

所有的錯誤都已解決。

## <a name="commit-changes-to-the-skillset"></a>認可技能集的變更

在起始偵錯工作階段時，搜尋服務建立了技能集的複本。 其目的是要避免所做的變更對生產系統造成影響。 現在您已完成技能集的偵錯，您可以將修正 (覆寫原始的技能集) 認可至生產系統。 如果您想要繼續對技能集進行變更而不影響生產系統，您可以儲存偵錯工作階段，後續再加以重新開啟。

1. 按一下主要偵錯工作階段功能表中的 [認可變更]。
1. 按一下 [確定] 以確認您要更新技能集。
1. 關閉偵錯工作階段，然後選取 [索引子] 索引標籤。
1. 開啟您的 'clinical-trials-idxr'。
1. 按一下 [重設]。
1. 按一下 **[執行]** 。 按一下 [ **確定** ] 以確認。

索引子執行完成時，應該會出現綠色的核取記號，且 [執行歷程記錄] 索引標籤中最近一次執行的時間戳記旁會出現「成功」一詞。若要確定變更已套用：

1. 結束 [索引子]，然後選取 [索引] 索引標籤。
1. 開啟 'clinical-trials' 索引，然後在 [搜尋總管] 索引標籤中，按一下 [搜尋]。
1. 在顯示的結果視窗中，組織和位置實體現在應已填入預期的值。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解技能集](./cognitive-search-working-with-skillsets.md)
> [深入了解增量擴充和快取](./cognitive-search-incremental-indexing-conceptual.md)