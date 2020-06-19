---
title: 使用範例用戶端應用程式探索基本概念
titleSuffix: Azure Digital Twins
description: 使用範例命令列應用程式探索 Azure Digital Twins SDK 的教學課程
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 2534b2ef2eec0ca993ee2506dcc285d750ba38ce
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737187"
---
# <a name="explore-azure-digital-twins-with-a-sample-client-app"></a>使用範例用戶端應用程式探索 Azure Digital Twins

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

本教學課程介紹實作命令列用戶端應用程式的範例應用程式，可用於與 Azure Digital Twins 執行個體進行互動。 該用戶端應用程式與[教學課程：撰寫用戶端應用程式](tutorial-code.md)中所介紹的用戶端應用程式相似。

您可以使用此範例來執行基本的 Azure Digital Twins 動作，例如上傳模型、建立與修改分身，以及建立關聯性。 您也可以查看範例的程式碼以了解 Azure Digital Twins API，並依您想要的方式修改範例專案來練習實作命令。

在本教學課程中，您將會…
1. 設定 Azure Digital Twins 執行個體
2. 設定範例命令列應用程式以與執行個體互動
3. 使用命令列應用程式探索 Azure Digital Twins，包括**模型**、**數位分身**、**關聯性**與**查詢**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>使用範例解決方案進行探索

現在您已設定執行個體及範例應用程式，將會使用範例專案與一些預先撰寫的範例程式碼，來建置與探索基本的 Azure Digital Twins 解決方案。 主要解決方案元件包括**模型**、**數位分身**、**關聯性**，從而產生可查詢的環境**分身圖表**。

### <a name="model-a-physical-environment-with-dtdl"></a>使用 DTDL 建立實體環境的模型

建立 Azure Digital Twins 解決方案的第一個步驟是定義您環境的分身[**模型**](concepts-models.md)。 

模型類似物件導向程式設計語言中的類別，為[數位分身](concepts-twins-graph.md)提供日後可遵循並具現化的使用者定義範本。 其是以名為 **Digital Twins 定義語言 (DTDL)** 的語言所撰寫 (與 JSON 相似)，並且可定義分身的「屬性」、「遙測」、「關聯性」及「元件」。

> [!NOTE]
> DTDL 也允許在數位分身上定義「命令」。 但目前在 Azure Digital Twins 服務上不支援命令。

在您開啟 _**AdtE2ESample**_ 專案的 Visual Studio 視窗中，使用 [方案總管] 窗格巡覽至 *AdtSampleApp\SampleClientApp\Models* 資料夾。 此資料夾包含範例模型。

選取 *Room.json* 在編輯視窗中開啟，並以下列方式進行變更：

* **更新版本號碼**，指出您正在提供此模型的更新版本。 您可以透過將 `@id` 值結尾的 *1*變更為 *2* 來進行此操作。 任何大於目前版本號碼的數字也都能運作。
* **編輯屬性**。 將 `Humidity` 屬性的名稱變更為 *HumidityLevel* (或任何您想要的名稱。 若您使用與 *HumidityLevel* 不同的名稱，請記住您使用的名稱，並繼續在本教學課程中使用該名稱，而非 *HumidityLevel*)。
* **新增屬性**。 在第 15 行結束的 `HumidityLevel` 屬性下方，貼上下列程式碼以將 `RoomName` 屬性新增到 Room：

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **新增關聯性**。 在您剛新增的 `RoomName` 屬性下方，貼上下列程式碼讓此類型的分身能夠與其他分身產生「包含」關聯性：

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

當您完成時，更新的模型看起來應該像這樣：

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="編輯後的 Room.json，其中含有更新後的版本號碼、HumidityLevel 與 RoomName 屬性，並包含關聯性" border="false":::

請務必儲存檔案再繼續。

> [!TIP]
> 若您想要嘗試建立自己的模型，您可以將 *Room* 模型的程式碼貼到您在 *AdtSampleApp\SampleClientApp\Models* 資料夾中使用 *.json* 副檔名儲存的新檔案。 然後，試著新增屬性和關聯性，以代表您想要的項目。 您也可以查看此資料夾中的其他範例模型來獲得靈感。

> [!TIP] 
> 有一種適用各種語言的 [DTDL 驗證程式範例](https://github.com/Azure-Samples/DTDL-Validator)，可讓您用於檢查模型文件，以確定 DTDL 有效。 其建置基礎為 DTDL 剖析器程式庫，詳細資訊請參閱[操作指南：剖析與驗證模型](how-to-use-parser.md)。

### <a name="get-started-with-the-command-line-app"></a>開始使用命令列應用程式

現在您已定義模型，剩餘的步驟涉及使用範例應用程式來與您的 Azure Digital Twins 執行個體進行互動。 使用工具列中的這個按鈕以執行專案：

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Visual Studio 啟動按鈕 (SampleClientApp 專案)":::

主控台視窗隨即會開啟、執行驗證，然後等候命令。 
* 這會透過瀏覽器處理驗證，因此您的預設網頁瀏覽器將開啟驗證提示。 使用此提示與您的 Azure 認證登入。 接著，您可以關閉瀏覽器索引標籤或視窗。

以下為專案主控台外觀的螢幕擷取畫面：

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="命令列應用程式的歡迎訊息":::

> [!TIP]
> 如需您可以搭配此專案使用的所有可能命令清單，請在專案主控台中輸入 `help`，然後按返回。
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Help 命令的輸出":::

請將專案主控台維持在執行狀態，以用於本教學課程中的剩餘步驟。

#### <a name="upload-models-to-azure-digital-twins"></a>將模型上傳到 Azure Digital Twins

設計模型之後，您需要將其上傳到您的 Azure Digital Twins 執行個體。 這會使用您自己的自訂網域詞彙來設定 Azure Digital Twins 服務執行個體。 在您上傳模型後，就可以建立使用模型的分身執行個體。

在專案主控台視窗中，執行下列命令以上傳您更新的 *Room* 模型，以及您在下一節中還會使用的 *Floor* 模型，以建立其他分身類型。

```cmd/sh
CreateModels Room Floor
```

輸出應該會指出模型已成功建立。

> [!TIP]
> 若您先前已設計自己的模型，也可以將其檔案名稱 (副檔名可省略) 新增至上述命令中的 `Room Floor` 清單，以進行上傳。

執行 `GetModels true` 命令以驗證模型已建立。 這將會為所有已上傳的模型查詢 Azure Digital Twins 執行個體，並列印出其完整資訊。 在結果中尋找編輯過的 *Room* 模型：

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="GetModels 的結果，顯示更新的 Room 模型":::

#### <a name="errors"></a>Errors

範例應用程式也會處理來自服務的錯誤。 

重新執行 `CreateModels` 命令，並嘗試第二次重新上傳您剛才上傳的相同模型之一：

```cmd/sh
CreateModels Room
```

因為模型無法覆寫，這現在會傳回服務錯誤：
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>建立數位分身

現在您已將一些模型上傳到 Azure Digital Twins 執行個體，您可以依據模型定義來建立[**數位分身**](concepts-twins-graph.md)。 數位分身代表您商務環境中的實體，例如伺服陣列上的感應器、建築物中的房間，或汽車的燈。 

若要建立數位分身，您可以使用 `CreateDigitalTwin` 命令。 您必須參考作為分身基礎的模型，並且可以選擇性地定義模型中任何屬性的初始值。 您不需要在此階段傳遞任何關聯性資訊。

在正在執行專案的主控台中執行此程式碼，依據您先前更新的 *Room* 模型及另外一個 *Floor* 模型建立數個分身。 回想一下，由於 *Room* 具備三個屬性，因此您可以提供引數來為這些屬性提供初始值。

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> 若您先前上傳了自己的模型，請嘗試根據上述命令建立您自己的 `CreateDigitalTwin` 命令，以新增模型類型的分身。

這些命令的輸出應該會指出分身已成功建立。 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="CreateDigitalTwin 命令結果的摘要，顯示 floor0、floor1、room0 與 room1":::

您也可以透過執行 `Query` 命令來驗證分身已建立。 此命令向 Azure Digital Twins 執行個體查詢其包含的所有數位分身。 在結果中尋找 *floor0*、*floor1*、*room0* 與 *room1* 分身。

#### <a name="modify-a-digital-twin"></a>修改數位分身

您也可以修改已建立的分身屬性。 請嘗試執行此命令，將 *room0* 的 RoomName 從 *Room0* 變更為 *PresidentialSuite*：

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

輸出應該會指出分身已成功更新。

您也可以透過執行此命令，查看 *room0* 的資訊以進行驗證：

```cmd/sh
GetDigitalTwin room0
```

輸出應該會反映更新的名稱。

> [!NOTE]
> 基礎 REST API 使用 JSON Patch 定義對分身的更新。 命令列應用程式會反映此格式，因此您可以實驗基礎 API 實際上應有的內容。

### <a name="create-a-graph-by-adding-relationships"></a>新增關聯性以建立圖表

接下來，您可以在這些分身之間建立一些**關聯性**，將這些分身連線到[**分身圖表**](concepts-twins-graph.md)。 分身圖表可用於表示整個環境。 

若要新增關聯性，請使用 `CreateRelationship` 命令。 指定關聯性的來源分身、要新增的關聯性類型，以及關聯性所連線的分身。 最後，提供關聯性的名稱 (識別碼)。

執行下列程式碼，以將您先前建立的每個 *Floor* 分身與對應 *Room* 的分身新增「包含」關聯性。 請注意，*Floor* 模型上必須已定義「包含」關聯性，才能進行這項操作。

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

這些命令的輸出會確認已成功建立關聯性：

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="CreateRelationship 命令的結果摘要，顯示 relationship0 與 relationship1":::

您也可以使用任何下列命令來驗證關聯性，這些命令會查詢您 Azure Digital Twins 執行個體中的關聯性。
* 查看來自每一個樓層的所有關聯性 (從其中一端檢視關聯性)，
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* 查看抵達每個房間的所有關聯性 (從「其他」端檢視關聯性)，
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* 個別查詢這些關聯性， 
    ```cmd/sh
    GetRelationship floor0 contains relationship0
    GetRelationship floor1 contains relationship1
    ```

您在本教學課程中設定的分身和關聯性會形成以下概念圖表：

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="顯示 floor0 透過 relationship0 連線到 room0，floor1 透過 relationship1 連線到 room1 的圖表" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>查詢分身圖表以回答環境問題

Azure Digital Twins 的主要功能是能夠輕鬆且有效率地[查詢](concepts-query-language.md)分身圖表，以回答有關環境的問題。 請於正在執行的專案主控台中執行下列命令以深入了解。

* **我環境中所有實體在 Azure Digital Twins 中代表什麼？** (查詢全部)

    ```cmd/sh
    Query
    ```

    這可以讓您一眼就能了解環境，確認所有項目的呈現方式都和您希望其在 Azure Digital Twins 中的呈現方式相同。 其結果包含每個數位分身及其詳細資料的輸出。 摘要如下：

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="分身查詢的部分結果，顯示 room0 與 floor1":::

    >[!NOTE]
    >不包含任何其他引數的 `Query` 命令與 `Query SELECT * FROM DIGITALTWINS` 相等。

* **我的環境中有哪些房間？** (依模型查詢)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    您可以將查詢限制在特定類型的分身，以取得其表示項目更具體的資訊。 該結果會顯示 *room0* 與 *room1*，但**不會**顯示 *floor0* 或 *floor1* (因為其為樓層，而非房間)。
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="模型查詢的結果，僅顯示 room0 與 room1":::

* ***floor0* 上有哪些房間？** (依關聯性查詢)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    您可以根據圖表中的關聯性進行查詢，以取得分身連線方式的資訊，或是將您的查詢限制在特定區域。 由於只有 *room0* 位於 *floor0* 上，因此其為結果中唯一的房間。

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="關聯性查詢的結果，顯示 room0":::

* **我的環境中有哪些分身的溫度高於 75？** (依屬性查詢)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    您可以根據屬性查詢圖表來回答各種問題，包括尋找您環境中需要注意的極端值。 也支援其他比較運算子 ( *<* 、 *>* 、 *=* 或 *!=* )。 *room1* 會顯示在此處的結果中，因為其溫度為 80。

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="屬性查詢的結果，僅顯示 room1":::

* ***floor0* 上有哪些房間的溫度高於 75？** (複合查詢)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    您也可以合併使用 `AND`、`OR`、`NOT` 等運算子，如在 SQL 中一樣合併先前的查詢。 此查詢使用 `AND`，讓先前有關分身溫度的查詢更為明確。 結果現在只會包含位於 *floor0* 上溫度高於 75 的房間 (在此案例中，沒有任何房間符合此條件)。 結果集為空白。

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="複合查詢的結果，顯示沒有結果":::

## <a name="clean-up-resources"></a>清除資源

本教學課程中的專案形成下一個教學課程：[教學課程：連線端對端解決方案](tutorial-end-to-end.md)的基礎。 若您希望繼續進行下一個教學課程，您可以保留在此處設定的資源，以繼續使用此 Azure Digital Twins 執行個體以及已設定的範例應用程式。
* 在此情況下，您可以使用範例應用程式的 `DeleteAllTwins` 與 `DeleteAllModels` 命令來分別清除您執行個體中的分身及模型。 如此一來，您就能以全新狀態進行下一個教學課程。

若您不再需要於本教學課程中建立的資源時，請遵循這些步驟將其刪除。

使用 [Azure Cloud Shell](https://shell.azure.com)，您可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令刪除資源群組中的所有 Azure 資源。 這會移除資源群組及 Azure Digital Twins 執行個體。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 

開啟 Azure Cloud Shell 並執行以下命令，以刪除資源群組及其包含的所有內容。

```azurecli-interactive
az group delete --name <your-resource-group>
```

接下來，使用此命令刪除您為用戶端應用程式所建立的 Azure Active Directory 應用程式註冊：

```azurecli
az ad app delete --id <your-application-ID>
```

最後，刪除下載到您本機電腦的專案範例資料夾。

## <a name="next-steps"></a>後續步驟 

在本教學課程中，您已透過設定執行個體及用戶端應用程式與執行個體進行互動，來開始使用 Azure Digital Twins。 您使用了用戶端應用程式來探索 Azure Digital Twins、建立模型、數位分身及關聯性。 您也在解決方案上執行了一些查詢，了解 Azure Digital Twins 可以針對環境回答的問題種類。

請繼續下一個教學課程以搭配其他 Azure 服務使用範例命令列應用程式，來完成資料驅動的端對端案例：

> [!div class="nextstepaction"]
> [教學課程：連線端對端解決方案](tutorial-end-to-end.md)

或者，開始查看概念文件，以深入了解您在本教學課程中使用過的元素：
* [概念：自訂模型](concepts-models.md)

您也可以開始閱讀新的操作指南文章，深入了解本教學課程中的流程：
* [操作說明：使用 Azure Digital Twins CLI](how-to-use-cli.md)
