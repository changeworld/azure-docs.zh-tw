---
title: 快速入門 - 探索範例案例
titleSuffix: Azure Digital Twins
description: 快速入門 - 使用 ADT Explorer 範例將預先建置的案例視覺化並加以探索。
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242274"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>快速入門 - 使用 ADT Explorer 探索 Azure Digital Twins 案例的範例

透過 Azure Digital Twins，您可以建立真實世界環境的即時模型並與之互動。 其完成方式是將個別元素模型化為 **數位分身 (digital twins)** ，然後連結到可回應即時活動及查詢資訊的知識 **圖形** 。

在本快速入門中，您將探索預先建立的 Azure Digital Twins 圖形，並輔以名為 [**Azure Digital Twins (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 的應用程式範例。 ADT Explorer 可讓您上傳環境的數位表示、檢視為了表示 Azure Digital Twins 環境而建立的對應項和圖形視覺影像，並透過瀏覽器型視覺體驗來執行其他管理活動。

此快速入門包含下列主要步驟：

1. 設定Azure Digital Twins 執行個體和 ADT Explorer
1. 上傳預先建立的模型和圖形資料，以建立範例案例
1. 探索建立的案例圖形
1. 變更圖形

您將使用的範例圖形代表具有兩個樓層和兩個房間的建築物。 圖表會如下所示：

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="由以箭號連結的 4 個圓形節點組成的圖形檢視。標示 'Floor1' 的圓形由標示 'contains' 的箭號連結到標示 'Room1' 的圓形；標示 'Floor0' 的圓形由標示 'contains' 的箭號連結到標示 'Room0' 的圓形。'Floor1' 和 'Floor0' 未連結。":::

## <a name="prerequisites"></a>先決條件

您需要 Azure 訂用帳戶來完成本快速入門。 如果您沒有 Azure 訂用帳戶，請立即 **[建立一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** 。

您的機器上也必須要有 **Node.js** 。 您可以透過下列連結取得最新版本：[Node.js](https://nodejs.org/).

最後，您需要下載要在快速入門中使用的範例： **ADT 總管** 應用程式範例。 此範例包含您在快速入門中用來載入和探索 Azure Digital Twins 案例的主要應用程式，以及範例案例檔案。 若要取得範例，請瀏覽此處：[Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 點擊 [下載 ZIP] 按鈕，將此範例程式碼的 .ZIP 檔案下載到您的電腦。 這會將 .ZIP 資料夾以 _**Azure_Digital_Twins__ADT__explorer.zip**_ 的形式下載至您的電腦。 將資料夾解壓縮並擷取檔案。

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>設定 Azure Digital Twins 和 ADT Explorer

使用 Azure Digital Twins 的第一個步驟是設定 **Azure Digital Twins 執行個體** 。 當您建立服務的執行個體，並 **設定認證** 用以向 ADT Explorer 進行驗證之後，您將能夠 **在 ADT Explorer 中連線至執行個體** ，且稍後可在快速入門中為其填入範例資料。

本節的其餘部分會引導您完成這些步驟。

### <a name="set-up-azure-digital-twins-instance"></a>設定 Azure Digital Twins 執行個體

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>設定本機 Azure 認證

在本機電腦上執行時，ADT Explorer 應用程式會使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (屬於 `Azure.Identity` 程式庫) 對 Azure Digital Twins 執行個體驗證使用者。 如需用戶端應用程式可以使用 Azure Digital Twins 進行驗證的不同方式詳細資訊，請參閱 [*操作指南：撰寫應用程式驗證碼*](how-to-authenticate-client.md)。

使用此類型的驗證時，ADT Explorer 會搜尋本機環境中的認證，例如本機 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 或 Visual Studio/Visual Studio Code 中的 Azure 登入。 這表示您應透過下列其中一種機制 **本機登入 Azure** ，以設定 ADT Explorer 應用程式的認證。

如果您已透過上述其中一種方式登入 Azure，您可以跳到[下一節](#run-and-configure-adt-explorer)。

否則，您可以依照下列步驟安裝本機 **Azure CLI** ：
1. 依照 [**此安裝連結**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)中的程序，完成符合您作業系統的安裝。
2. 在您的機器上開啟主控台視窗。
3. 執行 `az login`，並依照驗證提示登入您的 Azure 帳戶。

完成此作業後，當您在下一節中執行時，ADT Explorer 應該會自動選擇您的 Azure 認證。

您可以選擇關閉驗證主控台視窗，或將其保持開啟以供下一個步驟使用。

### <a name="run-and-configure-adt-explorer"></a>執行和設定 ADT Explorer

接下來，執行 ADT Explorer 應用程式，並針對您的 Azure Digital Twins 執行個體進行設定。

瀏覽至已下載並解壓縮的 _**Azure_Digital_Twins__ADT__explorer**_ 資料夾。 開啟主控台視窗並進入資料夾位置 *Azure_Digital_Twins__ADT__explorer/client/src* 。

執行 `npm install` 以下載所有必要的相依性。

然後，執行 `npm run start` 以啟動應用程式。

幾秒後，瀏覽器視窗會隨即開啟，且應用程式會出現在瀏覽器中。

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="顯示應用程式在 localhost:3000 上執行的瀏覽器視窗。此應用程式稱為 ADT Explorer，且包含查詢總管、模型檢視、圖形檢視和屬性總管的方塊。目前尚無任何螢幕資料。" lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

點擊視窗頂端的 [登入] 按鈕 (如下圖所示) 來設定 ADT Explorer，以使用您已設定的執行個體。 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="醒目提示視窗頂端旁 [登入] 圖示的 ADT Explorer。圖示會顯示簡易人像剪影，並在上方覆疊鑰匙剪影。" lightbox="media/quickstart-adt-explorer/sign-in.png":::

輸入您先前在 [必要條件](#prerequisites)一節中收集到的 *Azure Digital Twins 執行個體 URL* ，格式為 *https://{執行個體主機名稱}* 。

>[!NOTE]
> 若要重新瀏覽/編輯此資訊，您可以隨時選取相同圖示來再次提取登入方塊。 其會保留您傳入的值。

> [!TIP]
> 如果在您連線時顯示了 `SignalRService.subscribe` 錯誤訊息，請確定您的 Azure Digital Twins URL 開頭為 *https://* 。

如果您看到來自 Microsoft 的 [權限要求] 快顯視窗，請同意將權限授與此應用程式，並按下接受以繼續。

## <a name="add-the-sample-data"></a>加入範例資料

接下來，您會將範例案例和圖形匯入 ADT Explorer。 範例案例也位於您稍早下載的 **Azure_Digital_Twins__ADT__explorer** 資料夾中。

### <a name="models"></a>模型

Azure Digital Twins 解決方案的第一個步驟是定義您環境的詞彙。 這可藉由建立自訂 [**模型**](concepts-models.md)來完成，模型會描述存在於您環境中的實體類型。 

每個模型都會以名為 **Digital Twins 定義語言 (DTDL)** 的語言所撰寫 (與 JSON-LD 相似)，並且描述單一實體類型的「屬性」、「遙測」、「關聯性」及「元件」。 稍後，您將使用這些模型作為基礎，建立數位分身來代表這些類型的特定執行個體。

通常，您會在建立模型時完成三個步驟：
1. 撰寫模型定義 (在快速入門中，此部分已在範例解決方案中完成)
2. 驗證模型以確定語法正確 (在快速入門中，此部分已在範例解決方案中完成)
3. 將模型上傳至您的 Azure Digital Twins 執行個體
 
本快速入門已為您撰寫和驗證模型檔案，並隨附在您所下載的解決方案中。 在本節中，您會將兩個預先撰寫的模型上傳至執行個體，以定義建築物環境中的下列元素：
* 樓層
* 會議室

#### <a name="upload-models"></a>上傳模型

在 [模型檢視] 方塊中，點擊 [上傳模型] 圖示。

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="在 [模型檢視] 方塊中醒目提示中間圖示。其會顯示指向雲端的箭號。" lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. 在隨即顯示的 [檔案選取器] 方塊中，瀏覽至 *Azure_Digital_Twins__ADT__explorer/client/examples* 資料夾 (位於您下載的存放庫中)。
2. 選取 *Room.json* 和 *Floor.json* ，然後點擊 [確定]。 (您也可以上傳其他模型，但本快速入門中不會用到這些模型。)
3. 遵循要求您登入 Azure 帳戶的快顯視窗對話方塊。

>[!NOTE]
>如果您看到下列錯誤訊息：:::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="快顯視窗顯示「錯誤：提取模型時發生錯誤：ClientAuthError：開啟快顯視窗時發生錯誤。如果您使用 IE，或如果瀏覽器中的快顯視窗遭到封鎖，就可能發生此情況。」，且視窗底部有 [關閉] 按鈕" border="false"::: 
> 請嘗試停用快顯封鎖程式，或使用不同的瀏覽器。

ADT Explorer 現在會將這些模型檔案上傳到您的 Azure Digital Twins 執行個體。 這些模型應該會出現在 [模型檢視] 方塊中，並顯示其自訂名稱和完整模型識別碼。 您可以按一下 [檢視模型] 資訊氣泡，查看其背後的 DTDL 程式碼。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="[模型檢視] 方塊的視圖，其中列出兩個模型定義：Floor (dtmi:example:Floor;1) 和 Room (dtmi:example:Room;1)。醒目提示每個模型的 [檢視模型] 圖示，其顯示有個字母 'i' 在圓圈裡。" lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>分身和對應項圖形

現在您已將一些模型上傳到 Azure Digital Twins 執行個體，接著，您可以新增遵循模型定義的 [**數位分身**](concepts-twins-graph.md)。 

數位分身代表您商務環境中的實體：例如農場中的感應器、汽車的燈，或是建築物樓層的房間 (如本快速入門所述)。 您可以為指定模型類型建立多個分身 (例如，全部使用 Room 模型的多個房間)，並以關聯性來將其連結到代表完整環境的 **對應項圖形** 。

在本節中，您將上傳預先建立且連結到預先建立圖形的分身。 圖形包含兩個樓層和兩個房間，並以下列配置連結：
* *Floor0*
    - 包含 *Room0*
* *Floor1*
    - 包含 *Room1*

#### <a name="import-the-graph"></a>匯入圖形

在 [圖形檢視] 方塊中，點擊 [匯入圖形] 圖示。

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="在 [圖形檢視] 方塊中醒目提示圖示。其會顯示指向雲端的箭號。" lightbox="media/quickstart-adt-explorer/import-graph.png":::

在 [檔案選取器] 方塊中，瀏覽至 *Azure_Digital_Twins__ADT__explorer/client/examples* 資料夾，然後選擇 _**buildingScenario.xlsx**_ 試算表檔案。 此檔案包含範例圖形的描述。 點擊 [確定]。

幾秒後，ADT Explorer 會開啟 [匯入] 視圖，顯示即將載入的圖形預覽。

確認要上傳的圖形後，點擊 [圖形檢視] 右上角的 [儲存] 圖示：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="醒目提示圖形預覽窗格中的儲存圖示" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer 現在會使用上傳的檔案來建立要求的分身，以及分身之間的關聯性。 顯示匯入完成的對話方塊會隨即顯示。 請點擊 [關閉]。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="指出圖形匯入成功的對話方塊。其顯示「匯入成功。已匯入 4 個對應項。已匯入 2 個關聯性。」" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

圖形現在已上傳至 ADT Explorer。 若要查看圖形，請在 [ADT Explorer] 視窗頂端附近的 [圖形總管] 方塊中，點擊 [執行查詢] 按鈕。 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="醒目提示靠近視窗頂端的 [執行查詢] 按鈕" lightbox="media/quickstart-adt-explorer/run-query.png":::

這會執行預設查詢來選取並顯示所有數位分身。 ADT Explorer 會從服務擷取所有分身和關聯性，並在 [圖形檢視] 方塊中繪製其定義的圖形。

## <a name="explore-the-graph"></a>探索圖形

現在，您可以看到已上傳的範例案例圖形：

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="內部有對應項圖形的 [圖形檢視] 方塊視圖。標示 'floor1' 的圓形由標示 'contains' 的箭號連結到標示 'room1' 的圓形；標示 'floor0' 的圓形由標示 'contains' 的箭號連結到標示 'room0' 的圓形。":::

圓形 (圖形「節點」) 代表數位分身，而線條代表關聯性。 您會看到 *Floor0* 對應項包含 *Room0* ，而 *Floor1* 對應項包含 *Room1* 。

如果您使用滑鼠，您可以按一下並拖曳圖形的片段來四處移動。

### <a name="view-twin-properties"></a>檢視對應項屬性 

您可以選取對應項，在 [屬性總管] 方塊中查看其屬性及其值的清單。 

以下是 *Room0* 的屬性：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="醒目提示顯示 Room0 屬性的 [屬性總管] 方塊，其中包括 (但不限於) Room0 的 $dtId 欄位、溫度欄位 (70) 及濕度欄位 (30)。" lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

請注意， *Room0* 的溫度為 **70** 。

以下是 *Room1* 的屬性：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="醒目提示顯示 Room1 屬性的 [屬性總管] 方塊，其中包括 (但不限於) Room1 的 $dtId 欄位、溫度欄位 (80) 及濕度欄位 (60)。" lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

請注意， *Room1* 的溫度為 **80** 。

### <a name="query-the-graph"></a>查詢圖形

Azure Digital Twins 的主要功能是能夠輕鬆且有效率地[查詢](concepts-query-language.md)分身圖表，以回答有關環境的問題。 

在圖形中查詢分身的其中一種方式，就是根據其「屬性」。 根據屬性進行查詢有助於回答各種問題，包括尋找您環境中需要注意的極端值。

在本節中，您將執行查詢來回答下列問題： _**我的環境中有哪些分身的溫度高於 75？**_

若要查看答案，請在 [查詢總管] 方塊中執行下列查詢：

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

回想一下您稍早看到的對應項屬性， *Room0* 的溫度是 **70** ，而 *Room1* 的溫度是 **80** 。 因此，只有 _**Room1**_ 會顯示在此結果中。
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="屬性查詢的結果，僅顯示 Room1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> 上述查詢中也支援其他比較運算子 ( *<* 、 *>* 、 *=* 或 *!=* )。 您可以嘗試將不同的值或不同的對應項屬性插入查詢中，以嘗試回答您自己的問題。

## <a name="edit-data-in-the-graph"></a>編輯圖形中的資料

您可以使用 ADT Explorer 來為圖形中所呈現的分身編輯屬性。 在本節中，我們要將 **Room0 的溫度提高到 76** 。

若要這麼做，請選取 [Room0]，然後在 [屬性總管] 方塊中叫出其屬性清單。

此清單中的屬性皆可編輯。 選取溫度值 **70** ，即可輸入新的值。 輸入 **76** ，然後點擊 [儲存] 圖示，將溫度更新為 **76** 。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="顯示 Room0 屬性的 [屬性總管] 方塊。溫度值是顯示 76 的可編輯方塊，而 [儲存] 圖示周圍有醒目提示。" lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

成功儲存後，您會看到顯示修補程式碼的 [修補程式資訊] 視窗，這些程式碼會在幕後搭配 Azure Digital Twins [API](how-to-use-apis-sdks.md) 使用，以進行更新。 請點擊 [關閉]。

### <a name="query-to-see-the-result"></a>查詢以查看結果

若要確認圖形是否已成功將更新登錄到 *Room0* 的溫度，請重新執行先前的查詢，以 **取得環境中所有溫度高於 75 的分身** ：

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

現在， *Room0* 的溫度已從 **70** 變更為 **76** ，因此這兩個分身應該都會顯示在結果中。

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="屬性查詢的結果，同時顯示 Room0 和 Room1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>檢閱和情境化學習

在本快速入門中，您已建立 Azure Digital Twins 執行個體、將其連結到 ADT Explorer，並以範例案例填入執行個體。 

接著，您可以透過下列方式探索圖形...
1. 使用查詢來回答有關案例的問題。
2. 編輯數位對應項的屬性。
3. 再次執行查詢，以查看回應如何因更新而變更。

本練習的目的是要示範如何使用 Azure Digital Twins 圖形來回答有關您環境的問題 (即使環境持續改變)。 

雖然在本快速入門中，您已手動更新溫度，但在 Azure Digital Twins 中，通常會將數位分身連結到真實的 IoT 裝置，以根據遙測資料來自動接收更新。 這可讓您建立即時圖形，永遠反映您環境的實際狀態，並使用查詢來即時取得環境中所發生的一切相關資訊。

## <a name="clean-up-resources"></a>清除資源

若要結束本快速入門的工作，請先結束執行中的主控台應用程式。 這會在瀏覽器中關閉與 ADT Explorer 應用程式之間的連線，而且您將無法再於瀏覽器中檢視即時資料。 您可以關閉瀏覽器索引標籤。

如果您打算繼續進行 Azure Digital Twins 教學課程，本快速入門中使用的執行個體可重複用於這些文章，因此您不需要將其移除。
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

最後，刪除您下載到本機電腦的專案範例資料夾 ( **Azure_Digital_Twins__ADT__explorer** )。 您可能必須將壓縮和解壓縮的版本都刪除。

## <a name="next-steps"></a>後續步驟 

接下來，請繼續進行 Azure Digital Twins 教學課程，以建立您自己的 Azure Digital Twins 案例和互動工具。

> [!div class="nextstepaction"]
> [*教學課程：撰寫用戶端應用程式的程式碼*](tutorial-code.md)