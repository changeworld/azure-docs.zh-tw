---
title: 如何從電源應用查詢 Azure 認知搜索
titleSuffix: Azure Cognitive Search
description: 有關如何創建認知搜索的自訂連接器以及如何從 Power App 視覺化的分步指南
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385107"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>如何從電源應用查詢認知搜索索引

本文檔演示如何創建 Power Apps 自訂連接器，以便從搜索索引檢索搜尋結果。 它還演示如何發出搜索查詢並視覺化來自 Power App 的結果。 

## <a name="prerequisites"></a>Prerequisites
*    能夠創建自訂連接器的"為應用應用帳戶訪問供電"。
*    我們假設您已經創建了 Azure 搜索索引。

## <a name="create-a-custom-connector-to-query-azure-search"></a>創建自訂連接器以查詢 Azure 搜索

有兩個主要步驟是使用顯示 Azure 認知搜尋結果的 PowerApp。 首先，讓我們創建一個可以查詢搜索索引的連接器。 [在下一節中](#visualize-results-from-the-custom-connector)，我們將更新您的 Power Apps 應用程式，以視覺化連接器返回的結果。

1. 轉到[make.powerapps.com](http://make.powerapps.com)並**登錄**。

1. 搜索**資料** > **自訂連接器**
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="自訂連接器功能表" border="true":::

1. 按一下 **= 新建自訂連接器**，然後選擇**從空白創建**。

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="從空白功能表創建" border="true":::

1. 為自訂連接器指定名稱。 （即*Azure 搜索查詢*），然後按一下"**繼續**"。 這將啟動一個嚮導來創建新連接器。

1. 在"常規頁面"中輸入資訊。

    - 圖示背景顏色（例如，#007ee5）
    - 說明（例如，"Azure 認知搜索的連接器"）
    - 在主機中，您需要輸入搜索服務 URL（例如， `<yourservicename>.search.windows.net`
    - 對於基本 URL，只需輸入"/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="一般資訊對話" border="true":::

1. 在"安全頁"中，將*API 金鑰*設置為**身份驗證類型**，將參數標籤和參數名稱欄位設置為*api 鍵*。 對於**參數位置**，選擇*如下所示的"標題*"。
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="身份驗證類型選項" border="true":::

1. 在"定義頁"中，選擇 **"新建操作"** 以創建將查詢索引的操作。 輸入摘要的值"查詢"和操作 ID 的名稱。 輸入類似 *"查詢搜索索引"* 的描述。
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="新的操作選項" border="true":::


1. 按 **" 從示例導入**"按鈕定義參數和標頭。 接下來，您將定義查詢請求。  

    * 選擇動詞`GET`
    * 對於 URL，請輸入搜索索引的依例查詢，例如：
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=[&api 版本=2019-05-06-預覽
    

    **Power Apps**將使用語法從查詢中提取參數。 請注意，我們顯式定義了搜索欄位。 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="從範例匯入" border="false":::

1.  按一下"**導入**"可自動預填"請求"對話方塊。

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="從示例對話導入" border="false":::


1. 通過按一下 **...** 每個參數旁邊的符號。

    - 對於*搜索*：設置為`*`**預設值**，**將所需**設置為*false，* 並將**可見度**設置為*none*。 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="搜索參數中繼資料" border="true":::

    - 對於*api 版本*：`2019-05-06-Preview`設置為**預設值**，將可見度設置為內部 **，** 並將**可見度**設置為*True*。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="版本參數中繼資料" border="true":::

    - 同樣，對於*api 鍵*，根據需要設置**required**它，具有*內部***可見度**。 輸入搜索服務 API 金鑰作為**預設值**。
    
    進行這些更改後，將切換至 **"斯瓦格編輯器"** 視圖。 在參數部分中，您應該看到以下配置：    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. 在"回應"部分，按一下 **"添加預設回應"。** 這一點至關重要，因為它將説明**Power Apps**瞭解回應的架構。 粘貼示例回應。

    > [!TIP] 
    > 您可以輸入的 JSON 回應存在字元限制，因此您可能需要簡化 JSON，以便在粘貼 JSON 之前進行。 回應的重要方面架構/格式。 示例回應中的實際值不太重要，可以簡化以減少字元計數。
    

1.    按一下螢幕右上角的 **"創建連接器**"按鈕，然後才能對其進行測試。

1.  在"測試頁"中，按一下 **+ 新連接**，然後輸入搜索服務查詢鍵作為*api 鍵*的值。

    此步驟可能會將您帶到嚮導的外，並進入"連接"頁。 您可能希望返回自訂連接編輯器以實際測試連接。 轉到**自訂連接器**>選擇新創建的連接器 *>...* > **查看屬性** > **編輯** > **4。測試**以返回測試頁。

1.    現在按一下 **"測試"操作**，以確保從索引中獲得結果。 如果您成功，您應該會看到 200 狀態，在回應正文中，您應該會看到 JSON，其中描述了您的搜尋結果。




## <a name="visualize-results-from-the-custom-connector"></a>視覺化來自自訂連接器的結果
本教程的目標不是向您展示如何使用電源應用創建花哨的使用者體驗，因此 UI 佈局將是極簡的。 讓我們創建一個帶有搜索框的 PowerApp，一個搜尋按鈕，並在庫控制項中顯示結果。  PowerApp 將連接到我們最近創建的自訂連接器，以便從 Azure 搜索獲取資料。

1. 創建新的電源應用。 轉到 **"應用"** 部分，按一下 **"新建應用**"，然後選擇 **"畫布**"。

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="創建畫布應用" border="true":::

1. 選擇您喜歡的應用程式類型。 對於本教程創建一個**空白應用程式**與**電話佈局**。 **將顯示"電源應用工作室**"。

1. 進入演播室後，選擇"**資料來源**"選項卡，然後按一下剛剛創建的新連接器。 在我們的案例中，它被稱為*Azure 搜索查詢*。 按一下"**添加連接**"。

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="連接連接器" border="true":::

    現在 *，AzureSearchQuery*是一個資料來源，可從應用程式使用。
    
1. 導航到 **"插入"選項卡**，以便我們可以向表單中添加一些控制項。

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="插入控制項" border="true":::

1.  插入以下元素：
    -   值"查詢" 的文字標籤：
    -   文本輸入元素（稱為*txtQuery，* 預設值："*"
    -   帶有文本"搜索"的按鈕 
    -   稱為垂直庫（稱為*庫結果*）
    
    您的表單應如下所示：

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="控制項佈局" border="true":::

1. 要使 **"搜索"按鈕**發出查詢，請選擇該按鈕，並粘貼以下操作以執行**OnSelect**：

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="按鈕打開選擇" border="true":::
 
    此操作將導致按鈕使用*txtQuery*文字方塊中的文本作為查詢術語，使用搜索查詢的結果更新名為*azResult*的新集合。
    
1.  作為下一步，我們將我們創建的垂直庫連結到*azResult*集合。 選擇庫控制項，並在屬性窗格中執行以下操作。

    -  將**資料來源**設置為*azResult*。
    
    -  根據索引中的資料類型選擇適合您的**佈局**。 在這種情況下，我們使用*標題、字幕和正文*佈局。
    
    -  **編輯欄位**，並選擇要視覺化的欄位。

    由於我們在定義連接器時提供了示例結果，因此應用知道索引中可用的欄位。
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="資源庫欄位" border="true":::   
 
1.  按**F5**預覽應用。  

    請記住，欄位可以設置為計算值。      
    對於示例，使用 *"圖像、標題和字幕"* 佈局設置並指定*Image*函數作為資料和檔案名（例如`"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`）的根路徑的串聯將生成以下結果。

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="最終應用程式" border="true":::        

## <a name="next-steps"></a>後續步驟

有關詳細資訊和線上培訓，請參閱[電源應用學習目錄](https://docs.microsoft.com/powerapps/learning-catalog/get-started)。

