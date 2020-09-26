---
title: 將實例移至不同的 Azure 區域
titleSuffix: Azure Digital Twins
description: 瞭解如何將 Azure 數位 Twins 實例從一個 Azure 區域移至另一個區域。
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 1725c3ff162e4f6b7ac3a5ea1ede6976c827b510
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328488"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>將 Azure 數位 Twins 實例移至不同的 Azure 區域

如果您需要將 Azure 數位 Twins 實例從某個區域移至另一個區域，目前的程式是 **在新的區域中重新建立資源**，然後刪除原始資源。 在此程式結束時，您將使用與第一個相同的新 Azure 數位 Twins 實例，但已更新的位置除外。

本文提供有關如何完成移動的指引，並複製您需要的所有專案，以使新的實例符合原始專案。

這個程序包括下列步驟：
1. 準備：下載原始模型、twins 和圖形。
2. 移動：在新區域中建立新的 Azure 數位 Twins 實例。
3. 移動：重新填入新的 Azure 數位 Twins 實例。
    - 上傳原始模型、twins 和圖形。
    - 重新建立端點和路由。
    - 重新連結連接的資源。
4. 清除來源資源：刪除原始實例。

## <a name="prerequisites"></a>Prerequisites

在嘗試重新建立您的 Azure 數位 Twins 實例之前，最好先流覽原始實例的元件，並清楚瞭解所有需要重新建立的部分。

以下是您可能想要考慮的一些問題：
* 哪些 **模型** 已上傳至我的實例？ 有多少？
* 我的實例中有哪些 **twins** ？ 有多少？
* 我的實例中的 **圖形** 一般圖形是什麼？ 有幾個關聯性？
* 我的實例中有哪些 **端點** ？
* 我的實例中有哪些 **路由** ？ 他們有篩選嗎？
* 我的實例 **如何連接至其他 Azure 服務**？ 一些常見的整合點包括 .。。
    - 事件方格、事件中樞或服務匯流排
    - Azure Functions
    - Logic Apps
    - 時間序列深入解析
    - Azure 地圖服務
    - 裝置布建服務 (DPS) 
* 我有哪些其他 **個人或公司的應用程式** 連接到我的實例？

您可以使用 [Azure 入口網站](https://portal.azure.com)、 [Azure 數位 Twins api 和 Sdk](how-to-use-apis-sdks.md)、 [azure 數位 Twins CLI 命令](how-to-use-cli.md)或 [azure 數位 Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 範例來收集此資訊。

## <a name="prepare"></a>準備

在本節中，您將準備從原始實例 **下載原始模型、twins 和圖形** ，以重新建立實例。 本文使用 [Azure 數位 Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 範例來完成這項工作。

>[!NOTE]
>您可能已經有檔案，其中包含您實例中的模型和/或圖形。 若是如此，您就不需要再次下載所有專案，只是您所遺漏的部分，或是自從最初上傳這些檔案之後可能已變更的部分 (例如可能已使用新資料) 更新的 twins。

### <a name="limitations-of-adt-explorer"></a>ADT Explorer 的限制

[Azure 數位 Twins (ADT) Explorer 範例](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)是用戶端應用程式範例，可支援圖表的視覺標記法，並提供與您的實例的視覺化互動。 本文說明如何使用它來下載和稍後重新上傳、您的模型、twins 和圖形。

不過請注意，這是一個 **範例** ，而不是完整的工具。 它並未經過壓力測試，且不是為了處理大小較大的圖形而設計。 因此，請記住下列的現成範例限制：
* 此範例目前僅經過圖形大小的測試，最多可達1000節點和2000關聯性
* 在任何間歇性失敗的情況下，此範例不支援重試
* 如果上傳的資料不完整，此範例不一定會通知使用者
* 此範例不會處理由於大型圖形超過可用資源（例如記憶體）而產生的錯誤

如果範例無法處理您圖形的大小，您可以使用其他 Azure 數位 Twins 開發人員工具來匯出和匯入圖形：
* [Azure 數位 Twins CLI 命令](how-to-use-cli.md)
* [Azure 數位 Twins Api 和 Sdk](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>設定 ADT Explorer 應用程式

若要繼續使用 ADT Explorer，請先下載範例應用程式程式碼，並將它設定為在您的電腦上執行。 

流覽至此處的範例： [Azure 數位 Twins (ADT) explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 點擊 [下載 ZIP] 按鈕，將此程式碼範例的 .ZIP 檔案下載到您的機器，並命名為 _**ADT_Explorer.zip**_。 解壓縮檔案。

接下來，設定 ADT Explorer 在您的電腦上執行的許可權。 若要這樣做，請依照 Azure 數位 Twins 快速入門的 [*SET ADT Explorer 許可權*](quickstart-adt-explorer.md#set-adt-explorer-permissions) 一節中的步驟執行。

最後，執行並設定 ADT Explorer 以連接到原始的 Azure 數位 Twins 實例。 遵循快速入門的 [*執行和設定 ADT Explorer*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) 一節中的步驟。

現在，您應該會在電腦上的瀏覽器中執行 ADT Explorer 範例應用程式。 此範例應連接到您的原始 Azure 數位 Twins 實例。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="顯示應用程式在 localhost:3000 上執行的瀏覽器視窗。此應用程式稱為 ADT Explorer，且包含查詢總管、模型檢視、圖形檢視和屬性總管的方塊。目前尚無任何螢幕資料。" lightbox="media/how-to-move-regions/explorer-blank.png":::

若要確認連接，您可以按 [ *執行查詢* ] 按鈕來執行預設查詢，以在 graph *EXPLORER* 方塊中顯示圖形中的所有 twins 和關聯性。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="醒目提示靠近視窗頂端的 [執行查詢] 按鈕" lightbox="media/how-to-move-regions/run-query.png":::

您可以讓 ADT Explorer 執行，因為您稍後會在本文稍後再次使用它，將這些專案重新上傳至目的地區域中的新實例。

### <a name="download-models-twins-and-graph"></a>下載模型、twins 和圖形

接下來，將解決方案中的模型、twins 和圖形下載到您的電腦。

若要一次下載所有這些檔案，請先確定 *圖形視圖* 框中顯示完整的圖形 (您可以在 [ `SELECT * FROM digitaltwins` *查詢瀏覽器* ] 方塊中重新執行的預設查詢，) 。
 
然後，按下 [*圖形] 視圖*框中的 [*匯出圖形]* 圖示。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="在 [圖形] 視圖框中，會反白顯示圖示。它會顯示從雲端向下指向的箭號。" lightbox="media/how-to-move-regions/export-graph.png":::

這會啟用*圖形視圖*中的*下載*連結。 選取它來下載以 JSON 為基礎的查詢結果標記法，包括您的模型、twins 和關聯性。 這應該會將 *json* 檔案下載至您的電腦。

>[!NOTE]
>如果下載的檔案看起來有不同的副檔名，請嘗試直接編輯擴充功能，並將其變更為 *json*。

## <a name="move"></a>移動

接下來，您將在目的地區域中建立新的實例，並使用原始實例中的資料和元件，來完成實例的「移動」。

### <a name="create-a-new-instance"></a>建立新的實例

首先， **在您的目的地區域中建立新的 Azure 數位 Twins 實例**。 若要這樣做，請遵循 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md)中的步驟，讓這些指標牢記在心：
* **如果**新的實例位於不同的資源群組中，您可以保留相同的名稱。 如果您需要使用包含原始實例的相同資源群組，則新的實例將需要它自己的相異名稱。
* 當系統提示您輸入位置時，請輸入目標新區域。
* 您 **不需要** 重新建立應用程式註冊。 您的新實例可以重複使用您已經擁有的相同應用程式註冊。
    - 如果使用已 [編寫腳本](how-to-set-up-instance-scripted.md) 的安裝程式文章，您可以在出現提示時，重新輸入現有應用程式註冊的詳細資料，而不是輸入新的名稱。
    - 如果使用手動 [入口網站](how-to-set-up-instance-portal.md) 或 [CLI](how-to-set-up-instance-cli.md) 安裝程式文章，您可以在 *建立 Azure 數位 Twins 實例* 並 *設定使用者存取權限* 步驟之後停止。 不需要繼續 *設定用戶端應用程式的存取權限*。

完成之後，您將需要新實例的 **主機名稱** ，以繼續設定您的資料。 如果您未在安裝期間記下此項，您可以依照下列 [指示](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) ，立即從 Azure 入口網站取得。

### <a name="repopulate-old-instance"></a>重新填入舊的實例

接下來，您將設定新的實例，使其成為原始實例的複本。

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>使用 ADT Explorer 上傳原始模型、twins 和圖形

在本節中，您可以將模型、twins 和圖形重新上傳至新的實例。 如果您的原始實例中沒有任何模型、twins 或圖形，或是您不想將它們移至新的實例，您可以跳到下一 [節](#recreate-endpoints-and-routes)。

否則，若要繼續，請返回執行 **ADT Explorer** 的瀏覽器視窗，並遵循下列步驟。

##### <a name="connect-to-the-new-instance"></a>連接到新的實例

ADT Explorer 目前已連線到您的原始 Azure 數位 Twins 實例。 藉由叫用視窗頂端的 [登 *入* ] 按鈕，切換連接以指向新的實例。 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="醒目提示視窗頂端旁 [登入] 圖示的 ADT Explorer。圖示會顯示簡易人像剪影，並在上方覆疊鑰匙剪影。" lightbox="media/how-to-move-regions/sign-in.png":::

由於您要重複使用應用程式註冊，因此您只需要取代 *ADT URL*。 將此值變更為讀取 *HTTPs：//{new instance hostname}*。

點擊 *連接*。 系統可能會要求您使用您的 Azure 認證再次登入，並/或授與此應用程式同意給您的實例。

##### <a name="upload-models-twins-and-graph"></a>上傳模型、twins 和圖形

接下來，將您稍早下載的方案元件上傳至新的實例。

若要上傳您的**模型、twins 和圖形**，請點擊 [*圖形] 視圖*框中的 [匯*入圖形]* 圖示。 此選項會一次上傳上述三個元件 (即使是目前未在圖形) 中使用的模型。

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="在 [圖形檢視] 方塊中醒目提示圖示。其會顯示指向雲端的箭號。" lightbox="media/how-to-move-regions/import-graph.png":::

在 [檔案選取器] 方塊中，流覽至您下載的圖形。 選取 *[graph]* 檔案，然後按 [ *開啟*]。

幾秒後，ADT Explorer 會開啟 [匯入] 視圖，顯示即將載入的圖形預覽。

確認要上傳的圖形後，點擊 [圖形檢視] 右上角的 [儲存] 圖示：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="醒目提示圖形預覽窗格中的儲存圖示" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer 現在會將您的模型和圖形 (（包括 twins 和) 關聯性）上傳至新的 Azure 數位 Twins 實例。 您應該會看到成功訊息，指出已上傳多少個模型、twins 和關聯性：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="表示圖形匯入成功的對話方塊。它會讀取「匯入成功。已匯入2個模型。4 twins 已匯入。已匯入2個關聯性。" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

若要確認所有專案都已成功上傳，請按 [ *GRAPH EXPLORER]* 方塊中的 [*執行查詢*] 按鈕，以執行顯示圖形中所有 twins 和關聯性的預設查詢。 這也會重新整理 *模型視圖*中的模型清單。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="在靠近視窗頂端的相同 [執行查詢] 按鈕周圍醒目提示" lightbox="media/how-to-move-regions/run-query.png":::

您應該會看到圖形及其所有的 twins 和關聯性顯示在 [ *GRAPH EXPLORER]* 方塊中。 您也應該會在 [ *模型] 視圖* 框中看到您的模型。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="ADT Explorer 的視圖顯示 [模型視圖] 方塊中醒目提示的2個模型，以及在 [圖形 Explorer] 方塊中反白顯示的圖形" lightbox="media/how-to-move-regions/post-upload.png":::

這會確認您的模型、twins 和圖表已重新上傳至目的地區域中的新實例。

#### <a name="recreate-endpoints-and-routes"></a>重新建立端點和路由

如果您的原始實例中有 **端點和（或）路由** ，您必須在新的實例中重新建立它們。 如果您的原始實例中沒有任何端點或路由，或是您不想將它們移至新的實例，您可以跳到 [下一節](#re-link-connected-resources)。

否則，請繼續進行，並遵循 how to：使用新實例 [*管理端點和路由*](how-to-manage-routes-portal.md) 中的步驟，並記住這些指標： 
* 您 **不需要** 重新建立您要用於端點的事件方格、事件中樞或服務匯流排 *資源 (端點* 指示) 中的必要條件區段。 您只需要在 Azure 數位 Twins 實例上重新建立端點。
* 您可以重複使用端點和路由 **名稱**，因為它們的範圍設定為不同的實例。
* 請記得將任何必要的 **篩選準則** 新增至您所建立的路由。

#### <a name="re-link-connected-resources"></a>重新連結連接的資源

如果您有其他應用程式或 Azure 資源連接到您的原始 Azure 數位 Twins 實例，則必須編輯連線，才能改為連線至新的實例。 這可能包括其他 Azure 服務，或您已設定為使用 Azure 數位 Twins 的個人或公司應用程式。

如果您沒有任何其他資源連接到原始實例，或是您不想將它們移至新的實例，您可以跳到下一 [節](#verify)。

否則，若要繼續，請考慮您案例中的已連線資源。 您不需要刪除及重新建立任何連線的資源;相反地，您只需要編輯透過其 **主機名稱**連接至 Azure 數位 Twins 實例的點，並將其更新為使用新實例的主機名稱，而不是原始的實例。

您需要編輯的確切資源取決於您的案例，但以下是一些常見的整合點：
* Azure Functions。 如果您的 Azure 函式的程式碼包含原始實例的主機名稱，您應該將此值更新為新實例的主機名稱，並重新發佈函數。
* 事件方格、事件中樞或服務匯流排
* Logic Apps
* 時間序列深入解析
* Azure 地圖服務
* 裝置布建服務 (DPS) 
* Azure 外部的個人或公司應用程式，例如在教學課程中建立的 **用戶端** 應用程式 [*：撰寫用戶端應用程式*](tutorial-code.md)的程式碼，以連接到實例並呼叫 Azure 數位 Twins api

完成此步驟之後，目的地區域中的新實例應該是原始實例的複本。

## <a name="verify"></a>確認

若要確認您的新實例是否已正確設定，您可以使用下列工具：
* [**Azure 入口網站**](https://portal.azure.com) (適合用來確認新的實例存在且位於正確的目的地區域中;也適用于驗證端點和路由，以及其他 Azure 服務的連線) 
* [Azure 數位 Twins **CLI 命令**](how-to-use-cli.md) (適用于驗證新的實例存在且位於正確的目的地區域中;也可以用來驗證實例資料) 
* [**ADT Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (適用于驗證實例資料，例如模型、twins 和圖形) 
* [Azure 數位 Twins api 和 sdk](how-to-use-apis-sdks.md) (適用于驗證實例資料，例如模型、Twins 和圖形;也適用于驗證端點和路由) 

您也可以嘗試執行任何自訂的應用程式，或是您使用原始實例執行的端對端流程，以協助您確認它們正在正確地使用新的實例。

## <a name="clean-up-source-resources"></a>清除來源資源

現在您已在目的地區域中設定新的實例，並具有原始實例資料和連接的複本，您可以 **刪除原始的實例**。

您可以在 [Azure 入口網站](https://portal.azure.com)、使用 [CLI](how-to-use-cli.md)或使用 [控制平面 api](how-to-use-apis-sdks.md#overview-control-plane-apis)來進行這項作業。

若要使用 Azure 入口網站刪除實例，請在瀏覽器視窗中 [開啟入口網站](https://portal.azure.com) ，並在入口網站的搜尋列中搜尋其名稱，以流覽至您的原始 Azure 數位 Twins 實例。

按下 [ *刪除* ] 按鈕，然後遵循提示來完成刪除。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="在 [總覽] 索引標籤上的 Azure 入口網站中，查看 Azure 數位 Twins 實例詳細資料。[刪除] 按鈕會反白顯示":::