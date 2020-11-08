---
title: 設定 Bing 自訂搜尋體驗 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 入口網站可讓您建立搜尋實例，以指定 web 的磁區;網域、子頁面和網頁。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 52819caeb833eb475c8ea2c476ef5ba27681a50c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367551"
---
# <a name="configure-your-bing-custom-search-experience"></a>設定 Bing 自訂搜尋體驗

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

自訂搜尋執行個體可讓您量身訂做搜尋體驗，以便只包含使用者感興趣的網站內容。 Bing 不會執行全網路搜尋，只會搜尋您感興趣的網路配量。 若要建立自訂的網頁檢視，請使用 Bing 自訂搜尋[入口網站](https://customsearch.ai)。

入口網站可讓您建立搜尋執行個體，以指定要 Bing 搜尋的 Web 配量 (網域、子網頁和網頁)，以及不想要搜尋的項目。 入口網站也可建議您需要包含的內容。

定義 Web 配量時，請使用下列項目：

| 配量名稱 | 說明                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | 網域配量包括在某個網際網路網域內找到的所有內容。 例如 `www.microsoft.com`。 省略 `www.` 會使得 Bing 也搜尋該網域的子網域。 例如，如果您指定 `microsoft.com` ，Bing 也會從或傳回 `support.microsoft.com` 結果 `technet.microsoft.com` 。 |
| 子頁面    | 子頁面配量包括在子頁面及其下的路徑中找到的所有內容。 您在路徑中最多可以指定兩個子頁面。 例如， `www.microsoft.com/en-us/windows/`                                                                                                                       |
| 網頁    | 網頁配量在自訂搜尋中只能包括該網頁。 您可以選擇性地指定是否要包括子頁面。                                                                                                                                                                                  |

> [!IMPORTANT]
> 您指定的所有網域、子頁面和網頁都必須公開，而且都必須讓 Bing 編製索引。 如果您擁有想要在搜尋中包括的公開網站，但是 Bing 尚未對它編製索引，請參閱 Bing [網站管理員文件](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) \(英文\) 以了解讓 Bing 編製索引的詳細資訊。 另外也可以參閱網站管理員文件，了解讓 Bing 更新您搜耙過的網站 (如果索引過期) 的詳細資訊。

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>將 Web 配量新增至自訂搜尋執行個體

當您建立自訂搜尋執行個體時，您可以指定希望搜尋結果包含或封鎖的 Web 配量 (網域、子頁面和網頁)。 

如果您知道要包括在自訂搜尋執行個體中的配量，請在執行個體的 [作用中] 清單新增配量。 

如果您不確定要包含哪些配量，可以在 [預覽] 窗格中將搜尋查詢傳送至 Bing，然後選取您要的配量。 若要這樣做： 

1. 從 [預覽] 窗格中的下拉式清單選取 [Bing]，然後輸入搜尋查詢

2. 在您要包括的結果旁邊，按一下 [新增網站]。 然後按一下 [確定]。

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>使用作用中和已封鎖清單自訂搜尋體驗 

在自訂搜尋執行個體中按一下 [作用中] 和 [已封鎖] 索引標籤，即可存取作用中和已封鎖的配量清單。 新增至作用中清單的配量會包含在您的自訂搜尋中。 已封鎖的配量不會進行搜尋，而且不會出現在搜尋結果中。

若要指定您希望 Bing 搜尋的 Web 配量，請按一下 [作用中] 索引標籤，然後新增一或多個 URL。 若要編輯或刪除 URL，請使用 [控制項] 欄下的選項。 

將 URL 新增至 [作用中] 請單時，您可以使用上傳圖示來上傳文字檔案，一次新增單一 URL 或多個 URL。

![Bing 自訂搜尋作業中索引標籤](media/file-upload-icon.png)

若要上傳檔案，請建立文字檔案，一行指定一個網域、子頁面或網頁。 如果您的檔案格式不正確，則會遭到拒絕。

> [!NOTE]
> * 您只能將檔案上傳到 [作用中] 清單。 您無法使用它來將配量新增至 [已封鎖] 清單。  
> * 如果 [已封鎖] 清單包含您在上傳檔案中指定的網域、子頁面或網頁，該項目就會從 [已封鎖] 清單中移除，然後新增至 [作用中] 清單。
> * Bing 自訂搜尋會忽略已上傳檔案中重複的項目。 

### <a name="get-website-suggestions-for-your-search-experience"></a>取得您搜尋體驗的網站建議

將 web 快訊新增至使用 **中清單之後** ，Bing 自訂搜尋入口網站會在索引標籤底部產生網站和子頁面的建議。這些是 Bing 自訂搜尋認為您可能想要包含的配量。 按一下 [重新整理] 以在更新自訂搜尋執行個體的設定之後，取得更新後的建議。 有可用的建議時，才看得見這個區段。

## <a name="search-for-images-and-videos"></a>搜尋影像和影片

您可以使用 [Bing 自訂影像搜尋 API](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) 或 [Bing 自訂影片搜尋 API](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)，搜尋類似於 Web 內容的影像和影片。 您可以透過[託管的 UI](hosted-ui.md) 或 API 顯示這些結果。 

這些 API 類似於非自訂 [Bing 影像搜尋](../Bing-Image-Search/overview.md)和 [Bing 影片搜尋](../bing-video-search/overview.md) API，但是會搜尋整個網站，而且不需要 `customConfig` 查詢參數。 如需處理影像和影片的詳細資訊，請參閱下列文件集。 

## <a name="test-your-search-instance-with-the-preview-pane"></a>使用預覽窗格測試搜尋執行個體

您可以使用入口網站右側的預覽窗格送出搜尋查詢，以測試搜尋執行個體，而後檢視結果。 

1. 在搜尋方塊下方，選取 [我的執行個體]。 您可藉由選取 **Bing** ，比較您的搜尋體驗與 Bing 的結果。 
2. 選取安全搜尋篩選條件以及要搜尋的市場 (請參閱[查詢參數](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters))。
3. 輸入查詢並按下 Enter，或按一下搜尋圖示以檢視目前設定的結果。 按一下 [Web]、[影像] 或 [影片] 來取得對應的結果，即可變更您執行的搜尋類型。 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>調整特定搜尋結果的順位

入口網站可讓您調整特定網域、子頁面和網頁內容的搜尋順位。 在預覽窗格中傳送搜尋查詢之後，每個搜尋結果都會包含您可以進行的調整清單：  

| 調整 | 說明 |
|------------|-------------|
| 封鎖      | 將網域、子頁面或網頁移到 [已封鎖] 清單中。 Bing 會在搜尋結果中排除來自所選網站的內容。                    |
| 提升      | 將搜尋結果中的網域或子頁面的內容提升到較高的順位。                                                                                        |
| 降級     | 將搜尋結果中的網域或子頁面的內容降級到較低的順位。 您要選取是否要將網頁所屬的網域或子頁面的內容降級。 |
| 釘選到頂端 | 將網域、子頁面或網頁移到 [已釘選] 清單。 這會強制網頁顯示為特定搜尋查詢的第一項搜尋結果。                   |

調整順位不適用於影像或影片搜尋。

### <a name="boosting-and-demoting-search-results"></a>搜尋結果的提升和降級

您可以大幅提升、提升或降級 [作用中] 清單中任何的網域或子頁面。 根據預設，新增所有配量時不會調整順位。 經過大幅提升或提升的 Web 配量在搜尋結果中會排在較高的順位 (大幅提升的順位又高於提升)。 經過降級的項目在搜尋結果中會排在較低的順位。

您可以 **使用 [使用中] 清單中** 的 **排名調整** 控制項，或在預覽窗格中使用 [提升] 和 [降級] 控制項，以大幅提升、提升或降級專案。 服務會在 [使用中] 清單中加入配量，並據此調整順位。

> [!NOTE] 
> 將網域和子頁面提升及降級是 Bing 自訂搜尋用來判斷搜尋結果順序的很多方法之一。 因為其他影響不同 Web 內容順位的因素，調整順位的效果可能會有所不同。 您可以使用 [預覽] 窗格來測試調整搜尋結果順位的效果。 

大幅提升、提升和降級不適用於影像和影片搜尋。

## <a name="pin-slices-to-the-top-of-search-results"></a>將配量釘選到搜尋結果的頂端

入口網站也可讓您使用 **釘** 選的索引標籤，將 url 釘選到搜尋結果的頂端，以尋找特定的搜尋字詞。輸入 URL 和查詢，以指定將顯示為最上層結果的網頁。 請注意，每個搜尋查詢最多可以釘選一個網頁，而且只有已編製索引的網頁會顯示在搜尋中。 釘選結果不適用於影像或影片搜尋。

您有兩種方式可將網頁釘選到頂端：

* 在 [已 **釘** 選] 索引標籤中，輸入要釘選到頂端的網頁 URL，以及其對應的查詢。

* 在 [預覽] 窗格中輸入搜尋查詢，然後按一下搜尋。 尋找您要為查詢釘選的網頁，然後按一下 [ **釘選到頁首** ]。 網頁和查詢都會新增至 [已釘選] 清單。

### <a name="specify-the-pins-match-condition"></a>指定釘選的比對條件

根據預設，當使用者的查詢字串完全符合 [已釘選] 清單中所列的一個項目時，網頁只會釘選到搜尋結果的頂端。 您可以指定下列其中一個比對條件來變更此行為：

> [!NOTE]
> 使用者的搜尋查詢與釘選的搜尋查詢之間的所有比較都不區分大小寫。

| 值 | 說明                                                                          |
|---------------|----------------------------------------------------------------------------------|
| 開始於 | 如果使用者的查詢字串開頭為釘選查詢字串，該釘選即為相符項目。 |
| 結束於   | 如果使用者的查詢字串結尾為釘選查詢字串，該釘選即為相符項目。  |
| 包含    | 如果使用者的查詢字串包含釘選查詢字串，該釘選即為相符項目。   |


若要變更釘選比對條件，請按一下釘選的編輯圖示。 在 [查詢比對條件] 欄中，按一下下拉式清單並選取要使用的新條件。 接著，按一下儲存圖示以儲存變更。

### <a name="change-the-order-of-your-pinned-sites"></a>變更已釘選網站的順序

若要變更釘選的順序，您可加以拖放，或按一下 [已釘選] 清單的 [控制項] 欄中的 [編輯] 圖示，以編輯其順序編號。

如果有多個釘選滿足比對條件，則 Bing 自訂搜尋會使用清單中的第一項。

## <a name="view-statistics"></a>檢視統計資料

如果您訂閱了適當層級的自訂搜尋 (請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/))，您的生產執行個體中就會加入 [統計資料] 索引標籤。 統計資料索引標籤顯示如何使用自訂搜尋端點的詳細資料，包括呼叫量、前幾名查詢、地理分佈、回應碼以及安全搜尋。 您可以使用提供的控制項篩選詳細資料。

## <a name="usage-guidelines"></a>使用指導方針

- 針對每個自訂搜尋執行個體，對 [使用中] 和 [已封鎖] 配量調整順位的最大數目限制為 400 個。
- 在 [使用中] 或 [已封鎖] 索引標籤加入配量計算為一個順位調整。
- 提升及降級計算為兩個順位調整。
- 針對每個自訂搜尋執行個體，釘選項目的最大數目限制為 200 個。

## <a name="next-steps"></a>後續步驟

- [呼叫您的自訂搜尋](./search-your-custom-view.md)
- [設定託管的 UI 體驗](./hosted-ui.md)
- [使用裝飾標記醒目提示文字](../bing-web-search/hit-highlighting.md)
- [網頁頁面](../bing-web-search/paging-search-results.md)