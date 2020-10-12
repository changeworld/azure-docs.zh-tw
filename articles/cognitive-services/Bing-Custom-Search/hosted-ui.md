---
title: 設定 Bing 自訂搜尋的託管 UI | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 使用本文來設定及整合Bing 自訂搜尋的託管 UI。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 4a63a54f5ad4d1bcccecb1d1a4892ef55c238b92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319175"
---
# <a name="configure-your-hosted-ui-experience"></a>設定託管的 UI 體驗

Bing 自訂搜尋會提供一個託管 UI，您可輕鬆地將它以 JavaScript 程式碼片段的形式整合到您的網頁和 Web 應用程式中。 使用 Bing 自訂搜尋入口網站，您可以設定 UI 的版面配置、色彩和搜尋選項。



## <a name="configure-the-custom-hosted-ui"></a>設定自訂託管 UI

若要為您的 Web 應用程式設定託管的 UI，請依照下列步驟執行。 當您進行變更時，右側窗格可供您預覽您的 UI。 顯示的搜尋結果並不是您執行個體的實際結果。

1. 登入 Bing 自訂搜尋[入口網站](https://customsearch.ai)。  
  
2. 選取 Bing 自訂搜尋執行個體。

3. 按一下 [託管的 UI] 索引標籤。  
  
4. 選取配置。

    - 搜尋列和結果 (預設) ：在其下方顯示搜尋結果。
    - 僅限結果：只顯示搜尋結果，不含搜尋方塊。 使用此版面配置時，您必須提供搜尋查詢 (`&q=<query string>`)。 在 JavaScript 程式碼片段或 HTML 端點連結中，將查詢參數新增到要求 URL。
    - 快顯視窗：提供搜尋方塊，並在滑動重迭中顯示搜尋結果。

5. 選取色彩佈景主題。 按一下 [自訂佈景主題]****，即可自訂符合您應用程式的色彩。 若要變更色彩，請輸入色彩的 RGB HEX 值 (例如 `#366eb8`)，或按一下色彩預覽。

   您可以在入口網站的右側預覽您的變更。 按一下 [重設為預設]****，您的變更就會還原為所選佈景主題的預設色彩。

   > [!NOTE]
   > 選擇色彩時，請考慮可存取性。

6. 在 [其他組態]**** 下方，為您的應用程式提供適當的值。 這些設定是選用的。 若要檢視套用或移除這些設定的效果，請查看右側的預覽窗格。 可用的組態選項包括：  

7. 輸入搜尋訂用帳戶金鑰，或從下拉式清單中選擇。 下拉式清單中會填入來自您 Azure 帳戶之訂用帳戶的金鑰。 請參閱[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。  

8. 如果您已啟用自動建議，請輸入自動建議訂用帳戶金鑰，或從下拉式清單中選擇。 下拉式清單中會填入來自您 Azure 帳戶之訂用帳戶的金鑰。 自訂自動建議需要特定的訂用帳戶層，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>使用自訂 UI

若要使用託管的 UI，請： 

- 在您的網頁中包含指令碼  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- 或者，您也可以在網頁瀏覽器中使用下列 URL。   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > 視需要將下列的查詢參數加入 URL。 如需有關這些參數的資訊，請參閱[自訂搜尋 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) \(英文\) 參考。
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > 頁面無法顯示您的隱私權聲明或其他通知和條款。 對您的適用性可能有所不同。  

如需相關資訊 (包括您的自訂組態識別碼)，請移至 [生產]**** 索引標籤下的 [端點]****。

## <a name="configuration-options"></a>設定選項

按一下 [其他組態]****，然後提供值，即可設定託管 UI 的行為。 這些設定是選用的。 若要檢視套用或移除這些設定的效果，請查看右側的預覽窗格。 

### <a name="web-search-configurations"></a>Web 搜尋組態

- 啟用 web 結果：決定是否啟用 web 搜尋 (您會在頁面頂端看到 [Web] 索引標籤) 
- 啟用自動建議：判斷是否已啟用自訂自動建議 (請參閱其他成本) 的 [定價](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) 。
- 每一頁面的 web 結果：一次顯示的 web 搜尋結果數目 (最大值為每頁) 的50結果。
- 影像標題：決定是否以搜尋結果顯示影像。

如果您按一下 [ **顯示高級**設定]，則會顯示下列設定：

- 醒目提示單字：決定是否以粗體顯示結果和搜尋字詞。
- 連結目標：決定當使用者按一下搜尋結果時，是否在新的瀏覽器索引標籤中開啟網頁 (空白) 或相同的瀏覽器索引標籤 (自助) 。

### <a name="image-search-configurations"></a>影像搜尋組態

- 啟用影像結果：決定是否啟用影像搜尋 (您會在頁面頂端看到 [影像] 索引標籤) 。
- 每一頁面的影像結果：一次顯示的影像搜尋結果數目 (最大值為每頁) 的150結果。

如果您按一下 [顯示進階設定]****，將顯示下列設定。  
  
- 啟用篩選：新增使用者可用來篩選 Bing 傳回之影像的篩選準則。 例如，使用者可以篩選只顯示動畫 GIF 的結果。

### <a name="video-search-configurations"></a>影片搜尋組態

- 啟用的影片結果：決定是否啟用影片搜尋 (您會在頁面) 頂端看到 [影片] 索引標籤。
- 每一頁面的影片結果：一次顯示的影片搜尋結果數目 (最大值為每頁) 的150結果。

如果您按一下 [顯示進階設定]****，將顯示下列設定。  
  
- 啟用篩選：新增使用者可用來篩選 Bing 傳回之影片的篩選準則。 例如，使用者可以篩選特定解析度的影片結果，或篩選在過去 24 小時內探索到的影片。

### <a name="miscellaneous-configurations"></a>其他組態

- 頁面標題：顯示在 [搜尋結果] 頁面標題區域中的文字 (不適用於彈出版面配置) 。
- 工具列主題：決定搜尋結果頁面標題區域的背景色彩。

如果您按一下 [顯示進階設定]****，將顯示下列設定。  

|Column1  |Column2  |
|---------|---------|
|搜尋方塊文字預留位置   | 在輸入之前顯示於搜尋方塊中的文字。        |
|標題連結 URL    |標題連結的目標。         |
|標誌 URL     | 顯示於標題旁的影像。         |
|Favicon    | 顯示於瀏覽器標題列中的圖示。          |

只有當您透過 HTML 端點取用託管的 UI 時，系統才會套用下列設定 (若使用 JavaScript 程式碼片段則不會套用)。

- 頁面標題
- 工具列佈景主題
- 標題連結 URL
- 標誌 URL
- Favicon URL  

## <a name="next-steps"></a>後續步驟

- [使用裝飾標記醒目提示文字](../bing-web-search/hit-highlighting.md)
- [網頁頁面](./page-webpages.md)
