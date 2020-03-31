---
title: 使用區塊鏈資料管理員更新 Azure Cosmos DB - Azure 區塊鏈服務
description: 使用 Azure 區塊鏈服務的區塊鏈資料管理員將區塊鏈資料傳送至 Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533176"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>教學課程：使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB

在本教學課程中，您將使用 Azure 區塊鏈服務的區塊鏈資料管理員，將區塊鏈交易資料記錄在 Azure Cosmos DB 中。 區塊鏈資料管理員會將區塊鏈總帳資料擷取、轉換和傳遞至 Azure 事件方格主題。 在 Azure 事件方格中，您可以使用 Azure 邏輯應用程式連接器在 Azure Cosmos DB 資料庫中建立文件。 完成本教學課程後，您將可在 Azure Cosmos DB 資料總管中探索區塊鏈交易資料。

[![區塊鏈交易詳細資料](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

在本教學課程中，您：

> [!div class="checklist"]
> * 建立區塊鏈資料管理員執行個體
> * 新增用來解碼交易屬性和事件的區塊鏈應用程式
> * 建立用來儲存交易資料的 Azure Cosmos DB 帳戶和資料庫
> * 建立 Azure 邏輯應用程式，以將 Azure 事件方格主題連線至 Azure Cosmos DB
> * 將交易傳送至區塊鏈總帳
> * 在 Azure Cosmos DB 中檢視解碼的交易資料

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 完成[快速入門：使用 Azure 入口網站建立區塊鏈成員](create-member.md)或[快速入門：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員](create-member-cli.md)
* 完成[快速入門：使用 Visual Studio Code 連線至 Azure 區塊鏈服務聯盟網路](connect-vscode.md)。 此快速入門會引導您安裝[適用於 Ethereum 的 Azure 區塊鏈服務開發套件](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)，並設定您的區塊鏈開發環境。
* 完成[教學課程：使用 Visual Studio Code 建立、建置及部署智慧型合約](send-transaction.md)。 本教學課程將逐步解說如何建立範例智慧合約。
* 建立[事件方格主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>建立執行個體

區塊鏈資料管理員執行個體都連線至 Azure 區塊鏈服務交易節點，並加以監視。 執行個體會從該交易節點中擷取所有的原始區塊和原始交易資料。 輸出連線會將區塊鏈資料傳送至 Azure 事件方格。 您在建立執行個體時，會設定單一輸出連線。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您在下列必要快速入門中建立的 Azure 區塊鏈服務成員：[快速入門：使用 Azure 入口網站建立區塊鏈成員](create-member.md)。 選取 [區塊鏈資料管理員]  。
1. 選取 [新增]  。

    ![新增區塊鏈資料管理員](./media/data-manager-cosmosdb/add-instance.png)

    輸入下列詳細資料：

    設定 | 範例 | 描述
    --------|---------|------------
    名稱 | mywatcher | 為已連線的區塊鏈資料管理員輸入唯一名稱。
    交易節點 | myblockchainmember | 選取您在必要條件中建立的 Azure 區塊鏈服務成員的預設交易節點。
    連接名稱 | cosmosdb | 為用來傳送區塊鏈交易資料的輸出連線輸入唯一名稱。
    事件方格端點 | myTopic | 選擇您在必要條件中建立的事件方格主題。 注意:區塊鏈資料管理員執行個體和事件方格主題必須位於相同的訂用帳戶中。

1. 選取 [確定]  。

    區塊鏈資料管理員執行個體不到一分鐘即可建立。 執行個體在部署後將會自動啟動。 執行中的區塊鏈資料管理員執行個體會從交易節點中擷取區塊鏈事件，並將資料傳送至事件方格。

## <a name="add-application"></a>新增應用程式

新增 **helloblockchain** 區塊鏈應用程式，可讓區塊鏈資料管理員解碼事件和屬性狀態。 區塊鏈資料管理員需要智慧型合約 ABI 和位元組程式碼檔案，才能新增此應用程式。

### <a name="get-contract-abi-and-bytecode"></a>取得合約 ABI 和位元組程式碼

合約 ABI 會定義智慧型合約的介面。 此介面會說明如何與智慧型合約互動。 您可以使用[適用於 Ethereum 的 Azure 區塊鏈服務開發套件擴充功能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)，將合約 ABI 複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開您在下列必要教學課程中建立的 **helloblockchain** Solidity 專案的 **build/contracts** 資料夾：[教學課程：使用 Visual Studio Code 建立、建置及部署智慧型合約](send-transaction.md)。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製合約 ABI]  。

    ![Visual Studio Code 窗格，已選取 [複製合約 ABI]](./media/data-manager-cosmosdb/abi-devkit.png)

    合約 ABI 會複製到剪貼簿中。

1. 將 **abi** 陣列儲存為 JSON 檔案。 例如 *abi.json*。 您在後續步驟中會用到此檔案。

區塊鏈資料管理員需要將已部署的位元組程式碼用於智慧型合約。 已部署的位元組程式碼與智慧型合約位元組程式碼不同。 您可以使用 Azure 區塊鏈開發套件擴充功能，將位元組程式碼複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製交易位元組程式碼]  。

    ![Visual Studio Code 窗格，已選取 [複製交易位元組程式碼]](./media/data-manager-cosmosdb/bytecode-devkit.png)

    位元組程式碼會複製到剪貼簿中。

1. 將**位元組程式碼**值儲存為 JSON 檔案。 例如 *bytecode.json*。 您在後續步驟中會用到此檔案。

下列範例顯示在 VS Code 編輯器中開啟的 *abi.json* 和 *bytecode.json* 檔案。 您的檔案應會與其相似。

![abi.json 和 bytecode.json 檔案的範例](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>建立合約 ABI 和位元組程式碼 URL

區塊鏈資料管理員在新增應用程式時，必須可透過 URL 存取合約 ABI 和位元組程式碼檔案。 您可以使用 Azure 儲存體帳戶來提供可私下存取的 URL。

#### <a name="create-storage-account"></a>建立儲存體帳戶

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上傳合約檔案

1. 為儲存體帳戶建立新的容器。 選取 [容器] > [容器]  。

    ![建立儲存體帳戶容器](./media/data-manager-cosmosdb/create-container.png)

    | 設定 | 描述 |
    |---------|-------------|
    | 名稱  | 為容器命名。 例如 *smartcontract* |
    | 公用存取層級 | 選擇 [私人 (沒有匿名存取)]  |

1. 選取 [確定]  以建立容器。
1. 選取容器，然後選取 [上傳]  。
1. 選擇您在[取得合約 ABI 和位元組程式碼](#get-contract-abi-and-bytecode)一節中所建立的兩個 JSON 檔案。

    ![上傳 Blob](./media/data-manager-cosmosdb/upload-blobs.png)

    選取 [上傳]  。

#### <a name="generate-url"></a>產生 URL

為每個 Blob 分別產生一個共用存取簽章。

1. 選取 ABI JSON Blob。
1. 選取 [產生 SAS] 
1. 設定所需的存取簽章到期日，然後選取 [產生 Blob SAS 權杖和 URL]  。

    ![產生 SAS Token](./media/data-manager-cosmosdb/generate-sas.png)

1. 複製 **Blob SAS URL** 並加以儲存，以供下一節使用。
1. 針對位元組程式碼 JSON Blob，重複[產生 URL](#generate-url) 步驟。

### <a name="add-helloblockchain-application-to-instance"></a>將 helloblockchain 應用程式新增至執行個體

1. 從執行個體清單中，選取您的區塊鏈資料管理員執行個體。
1. 選取 [區塊鏈應用程式]  。
1. 選取 [新增]  。

    ![新增區塊鏈應用程式](./media/data-manager-cosmosdb/add-application.png)

    輸入區塊鏈應用程式的名稱，以及智慧型合約 ABI 和位元組程式碼 URL。

    設定 | 描述
    --------|------------
    名稱 | 輸入要追蹤的區塊鏈應用程式的唯一名稱。
    合約 ABI | 合約 ABI 檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組程式碼 URL](#create-contract-abi-and-bytecode-url)。
    合約位元組程式碼 | 位元組程式碼檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組程式碼 URL](#create-contract-abi-and-bytecode-url)。

1. 選取 [確定]  。

    應用程式建立後，應用程式會出現在區塊鏈應用程式清單中。

    ![區塊鏈應用程式清單](./media/data-manager-cosmosdb/artifact-list.png)

您可以刪除 Azure 儲存體帳戶，或用它來設定更多區塊鏈應用程式。 如果您要刪除 Azure 儲存體帳戶，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

## <a name="create-azure-cosmos-db"></a>建立 Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>新增資料庫和容器

您可以在 Azure 入口網站中使用資料總管，建立資料庫和容器。

1. 在 Azure Cosmos DB 帳戶頁面上的左側導覽中選取 [資料總管]  ，然後選取 [新增容器]  。
1. 在 [新增容器]  窗格上，輸入新容器的設定。

    ![新增容器設定](./media/data-manager-cosmosdb/add-container.png)

    | 設定 | 描述
    |---------|-------------|
    | 資料庫識別碼 | 輸入 **blockchain-data** 作為新資料庫的名稱。 |
    | Throughput | 讓輸送量保持在每秒 **400** 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以擴大輸送量。|
    | 容器識別碼 | 輸入 **Messages** 作為新容器的名稱。 |
    | 資料分割索引鍵 | 使用 **/MessageType** 作為分割區索引鍵。 |

1. 選取 [確定]  。 [資料總管] 會顯示您建立的新資料庫和容器。

## <a name="create-logic-app"></a>建立邏輯應用程式

當您需要整合系統和服務時，Azure Logic Apps 可協助您排程及自動化商務程序和工作流程。 您可以使用邏輯應用程式將事件方格連線至 Azure Cosmos DB。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]   > [整合]   > [邏輯應用程式]  。
1. 提供關於應在何處建立邏輯應用程式的詳細資料。 在完成作業後，選取 [建立]  。

    如需如何建立邏輯應用程式的詳細資訊，請參閱[使用 Azure Logic Apps 來建立自動化工作流程](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在 Azure 部署您的應用程式之後，請選取您的邏輯應用程式資源。
1. 在 Logic Apps 設計工具中的 [範本]  下方，選取 [空白邏輯應用程式]  。

### <a name="add-event-grid-trigger"></a>新增事件方格觸發程序

每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎會建立邏輯應用程式執行個體，啟動並執行您的工作流程。 請使用 Azure 事件方格觸發程序，將區塊鏈交易資料從事件方格傳送至 Cosmos DB。

1. 在 Logic Apps 設計工具中，搜尋並選取 [Azure 事件方格]  連接器。
1. 在 [觸發程序]  索引標籤中，選取 [當資源事件發生時]  。
1. 建立事件方格主題的 API 連線。

    ![事件方格觸發程序設定](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | 設定 | 描述
    |---------|-------------|
    | 訂用帳戶 | 選擇包含事件方格主題的訂用帳戶。 |
    | 資源類型 | 選擇 **Microsoft.EventGrid.Topics**。 |
    | 資源名稱 | 選擇區塊鏈資料管理員要將交易資料訊息傳送到的事件方格主題名稱。 |

### <a name="add-cosmos-db-action"></a>新增 Cosmos DB 動作

新增在 Cosmos DB 中為每個交易建立文件的動作。 請使用交易訊息類型作為分割區索引鍵，為訊息分類。

1. 選取 [新增步驟]  。
1. 在 [選擇動作]  上，搜尋 **Azure Cosmos DB**。
1. 選擇 [Azure Cosmos DB] > [動作] > [建立或更新文件]  。
1. 建立 Cosmos DB 資料庫的 API 連線。

    ![Cosmos DB 連線設定](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | 設定 | 描述
    |---------|-------------|
    | 連線名稱 | 選擇包含事件方格主題的訂用帳戶。 |
    | DocumentDB 帳戶 | 選擇您在[建立 Azure Cosmos DB 帳戶](#create-azure-cosmos-db)一節中建立的 DocumentDB 帳戶。 |

1. 輸入您先前在[新增資料庫和容器](#add-a-database-and-container)一節中建立的 Azure Cosmos DB 的 [資料庫識別碼]  和 [集合識別碼]  。

1. 選取 [文件]  設定。 在 [新增動態內容]  快顯視窗中選取 [運算式]  ，然後複製並貼上下列運算式：

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    此運算式會取得訊息的資料部分，並將識別碼設定為時間戳記值。

1. 選取 [新增參數]  ，並選擇 [分割區索引鍵值]  。
1. 將 [分割區索引鍵值]  設定為 `"@{triggerBody()['data']['MessageType']}"`。 此值必須以雙引號括住。

    ![具有 Cosmos DB 設定的 Logic Apps 設計工具](./media/data-manager-cosmosdb/create-action.png)

    此值會將分割區索引鍵設定為交易訊息類型。

1. 選取 [儲存]  。

邏輯應用程式會監視事件方格主題。 從區塊鏈資料管理員傳送新的交易訊息時，邏輯應用程式會在 Cosmos DB 中建立文件。

## <a name="send-a-transaction"></a>傳送交易

接著，將交易傳送至區塊鏈總帳，以測試您所建立的內容。 使用您在下列必要教學課程中建立的 **HelloBlockchain** 合約的 **SendRequest** 函式：[教學課程：使用 Visual Studio Code 建立、建置及部署智慧型合約](send-transaction.md)。

1. 使用 Azure 區塊鏈服務開發套件智慧型合約互動頁面來呼叫 **SendRequest** 函式。 以滑鼠右鍵按一下 [HelloBlockchain.sol]  ，然後從功能表中選擇 [顯示智慧型合約互動頁面]  。

    ![從功能表選擇 [顯示智慧型合約互動頁面]](./media/data-manager-cosmosdb/contract-interaction.png)

1. 選擇 [SendRequest]  合約動作，然後輸入 **Hello, Blockchain!** 來作為 **requestMessage** 參數。 選取 [執行]  ，以透過交易呼叫 **SendRequest** 函式。

    ![執行 SendRequest 動作](./media/data-manager-cosmosdb/sendrequest-action.png)

SendRequest 函式會設定 [RequestMessage]  和 [狀態]  欄位。 [RequestMessage]  的目前狀態是傳遞了 **Hello, Blockchain** 的引數。 [狀態]  欄位值則仍是 [要求]  。

## <a name="view-transaction-data"></a>檢視交易資料

您已將區塊鏈資料管理員連線至 Azure Cosmos DB，此時您可以在 Cosmos DB 資料總管中檢視區塊鏈交易訊息。

1. 移至 [Cosmos DB 資料總管] 檢視。 例如，移至 [cosmosdb-blockchain] > [資料總管] > [blockchain-data] > [訊息] > [項目]  。

    ![Cosmos DB 資料總管](./media/data-manager-cosmosdb/data-explorer.png)

    資料總管會列出在 Cosmos DB 資料庫中建立的區塊鏈資料訊息。

1. 選取項目識別碼以瀏覽訊息，並尋找具有相符交易雜湊的訊息。

    [![區塊鏈交易詳細資料](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    原始交易訊息會包含交易的相關詳細資料。 不過，屬性資訊會加密處理。

    由於您已將 HelloBlockchain 智慧型合約新增至區塊鏈資料管理員執行個體，因此也會傳送 **ContractProperties** 訊息類型，其中包含已解碼的屬性資訊。

1. 尋找交易的 **ContractProperties** 訊息。 它應該是清單中的下一個訊息。

    [![區塊鏈交易詳細資料](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    **DecodedProperties** 陣列會包含交易的屬性。

恭喜！ 您已使用區塊鏈資料管理員和 Azure Cosmos DB 成功建立交易訊息總管。

## <a name="clean-up-resources"></a>清除資源

不再需要時，您可以刪除在本教學課程中使用的資源和資源群組。 若要刪除資源群組：

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]  ，然後選取您想要刪除的資源群組。
1. 選取 [刪除資源群組]  。 輸入資源群組名稱並選取 [刪除]  ，以確定進行刪除。

## <a name="next-steps"></a>後續步驟

深入了解如何整合區塊鏈總帳。

> [!div class="nextstepaction"]
> [使用 Ethereum 區塊鏈連接器搭配 Azure Logic Apps](ethereum-logic-app.md)
