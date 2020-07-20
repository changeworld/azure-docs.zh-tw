---
title: 搭配 Azure Logic Apps 使用乙太坊區塊鏈連接器-Azure 區塊鏈服務
description: 使用 Ethereum 區塊鏈連接器搭配 Azure Logic Apps，觸發智慧型合約函式及回應智慧型合約事件。
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: chrisseg
ms.openlocfilehash: c7b45134e8b5c3e33e5d05d59c006abe103e5bda
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200727"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>使用 Ethereum 區塊鏈連接器搭配 Azure Logic Apps

使用 [Ethereum 區塊鏈連接器](https://docs.microsoft.com/connectors/blockchainethereum/)搭配 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)，執行智慧型合約動作及回應智慧型合約事件。 例如，假設您想要建立以 REST 為基礎的微服務，以傳回區塊鏈總帳中的資訊。 使用邏輯應用程式時，您可以接受所查詢的資訊儲存在區塊鏈總帳中的 HTTP 要求。

## <a name="prerequisites"></a>必要條件

完成選用的必要條件[快速入門：使用 Visual Studio Code 連接到 Azure 區塊鏈 Service 聯盟網路](connect-vscode.md)。 此快速入門會引導您安裝[適用於 Ethereum 的 Azure 區塊鏈服務開發套件](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)，並設定您的區塊鏈開發環境。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

當您需要整合系統和服務時，Azure Logic Apps 可協助您排程及自動化商務程序和工作流程。 首先，您必須建立使用 Ethereum 區塊鏈連接器的邏輯。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]   > [整合]   > [邏輯應用程式]  。
1. 在 [建立邏輯應用程式]**** 底下，提供關於應在何處建立邏輯應用程式的詳細資訊。 在完成作業後，選取 [建立]****。

    如需如何建立邏輯應用程式的詳細資訊，請參閱[使用 Azure Logic Apps 來建立自動化工作流程](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在 Azure 部署您的應用程式之後，請選取您的邏輯應用程式資源。
1. 在 Logic Apps 設計工具中的 [範本]**** 下方，選取 [空白邏輯應用程式]****。

每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎會建立邏輯應用程式執行個體，啟動並執行您的工作流程。

Ethereum 區塊鏈連接器有一個觸發程序和數個動作。 您應使用的觸發程序或動作視您的案例而定。

如果您的工作流程：

* 在區塊鏈上發生事件時觸發，請[使用事件觸發](#use-the-event-trigger)程式。
* 查詢或部署智慧合約，[使用動作](#use-actions)。
* 屬於常見的案例，請[使用開發人員套件來產生工作流程](#generate-a-workflow)。

## <a name="use-the-event-trigger"></a>使用事件觸發程序

如果您想要讓邏輯應用程式在智慧型合約事件發生之後執行，請使用 Ethereum 區塊鏈事件觸發程序。 例如，您想要在呼叫智慧型合約函式時傳送電子郵件。

1. 在 Logic Apps 設計工具中，選取 Ethereum 區塊鏈連接器。
1. 在 [觸發程序]**** 索引標籤中，選取 [當智慧型合約事件發生時]****。
1. 對 Azure 區塊鏈服務變更或[建立 API 連線](#create-an-api-connection)。
1. 輸入您想要檢查事件之智慧型合約的相關詳細資料。

    ![具有事件觸發程式屬性的 Logic Apps 設計工具](./media/ethereum-logic-app/event-properties.png)

    | 屬性 | 說明 |
    |----------|-------------|
    | **合約 ABI** | 合約應用程式二進位介面 (ABI) 會定義智慧型合約介面。 如需詳細資訊，請參閱[取得合約 ABI](#get-the-contract-abi)。 |
    | **智慧型合約位址** | 合約位址是 Ethereum 區塊鏈上的智慧型合約目的地位址。 如需詳細資訊，請參閱[取得合約位址](#get-the-contract-address)。 |
    | **事件名稱** | 選取要檢查的智慧型合約事件。 此事件會觸發邏輯應用程式。 |
    | **間隔**和**頻率** | 選取您要檢查事件的頻率。 |

1. 選取 [儲存]。

若要完成您的邏輯應用程式，您可以新增一個根據 Ethereum 區塊鏈事件觸發程序執行動作的步驟。 例如，傳送電子郵件。

## <a name="use-actions"></a>使用動作

當想要讓邏輯應用程式對區塊鏈總帳執行動作時，請使用 Ethereum 區塊鏈動作。 例如，您想要建立以 REST 為基礎的微服務，在對邏輯應用程式發出 HTTP 要求時呼叫智慧型合約函式。

連接器動作需要觸發程序。 您可以使用 Ethereum 區塊鏈連接器動作來作為觸發程序之後的下一個步驟，例如微服務的 HTTP 要求觸發程序。

1. 在 Logic Apps 設計工具中，選取 [新增步驟]**** (在觸發程序之後)。
1. 選取 Ethereum 區塊鏈連接器。
1. 在 [動作]**** 索引標籤中，選取其中一個可用的動作。

    ![具有動作屬性的 Logic Apps 設計工具](./media/ethereum-logic-app/action-properties.png)

1. 對 Azure 區塊鏈服務變更或[建立 API 連線](#create-an-api-connection)。
1. 根據您選擇的動作，提供下列關於智慧型合約函式的詳細資料。

    | 屬性 | 說明 |
    |----------|-------------|
    | **合約 ABI** | 合約 ABI 會定義智慧型合約的介面。 如需詳細資訊，請參閱[取得合約 ABI](#get-the-contract-abi)。 |
    | **合約位元組碼** | 已編譯的智慧型合約位元組程式碼。 如需詳細資訊，請參閱[取得合約位元組程式碼](#get-the-contract-bytecode)。 |
    | **智慧型合約位址** | 合約位址是 Ethereum 區塊鏈上的智慧型合約目的地位址。 如需詳細資訊，請參閱[取得合約位址](#get-the-contract-address)。 |
    | **智慧型合約函式名稱** | 選取動作的智慧型合約函式名稱。 清單中會填入合約 ABI 中的詳細資料。 |

    選取智慧型合約函式名稱之後，您可能會看到函式參數的必要欄位。 輸入您的案例所需的值或動態內容。

您現在可以使用邏輯應用程式。 邏輯應用程式事件觸發時，會執行 Ethereum 區塊鏈動作。 例如，HTTP 要求觸發程序會執行 Ethereum 區塊鏈動作來查詢智慧型合約的狀態值。 此查詢會產生 HTTP 回應以傳回值。

## <a name="generate-a-workflow"></a>產生工作流程

Ethereum Visual Studio Code 延伸模組的 Azure 區塊鏈服務開發套件可產生適用於常見案例的邏輯應用程式工作流程。 可用的案例有四種：

* 發佈至 Azure SQL Database 執行個體的資料
* 發佈至 Azure 事件方格或 Azure 服務匯流排執行個體的事件
* 報告發佈
* 以 REST 為基礎的微服務

 Azure 區塊鏈服務開發套件會使用 Truffle 來簡化區塊鏈開發。 若要根據智慧型合約產生邏輯應用程式，您需要智慧型合約的 Truffle 解決方案。 您也需要連線至您的 Azure 區塊鏈服務聯盟網路。 如需詳細資訊，請參閱[使用 Visual Studio Code 連接到 Azure 區塊鏈 Service 聯盟網路快速入門](connect-vscode.md)。

例如，下列步驟會根據快速入門 **HelloBlockchain** 智慧型合約，產生以 REST 為基礎的微服務邏輯應用程式：

1. 在 Visual Studio Code 總管的提要欄位中，展開解決方案中的 [合約]**** 資料夾。
1. 對 [HelloBlockchain.sol]**** 按一下滑鼠右鍵，然後從功能表中選取 [產生智慧型合約的微服務]****。

    ![Visual Studio Code 窗格，已選取 [產生智慧型合約的微服務]](./media/ethereum-logic-app/generate-logic-app.png)

1. 在命令選擇區中，選取 [邏輯應用程式]****。
1. 輸入**合約位址**。 如需詳細資訊，請參閱[取得合約位址](#get-the-contract-address)。
1. 選取邏輯應用程式的 Azure 訂用帳戶和資源群組。

    邏輯應用程式組態和程式碼檔案會在 **generatedLogicApp** 目錄中產生。

1. 檢視 **generatedLogicApp/HelloBlockchain** 目錄。 每個智慧型合約函式、事件和屬性都有一個邏輯應用程式 JSON 檔案。
1. 開啟 **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** 檔案並複製內容。

    ![JSON 檔案，具有要複製的程式碼](./media/ethereum-logic-app/requestmessage.png)

1. 在您的邏輯應用程式中，選取 [邏輯應用程式程式碼檢視]****。 將現有的 JSON 取代為產生的邏輯應用程式 JSON。

    ![邏輯應用程式程式碼檢視，具有已取代的新應用程式程式碼](./media/ethereum-logic-app/code-view.png)

1. 選取 [設計工具]**** 以切換至設計工具檢視。
1. 邏輯應用程式包含案例的基本步驟。 不過，您必須更新 Ethereum 區塊鏈連接器的組態詳細資料。
1. 選取**連線**步驟，並對 Azure 區塊鏈服務變更或[建立 API 連線](#create-an-api-connection)。

    ![設計工具檢視，已選取 [連線]](./media/ethereum-logic-app/microservice-logic-app.png)

1. 您現在可以使用邏輯應用程式。 若要測試以 REST 為基礎的微服務，請對邏輯應用程式要求 URL 發出 HTTP POST 要求。 複製**收到 HTTP 要求時**步驟中的 **HTTP POST URL** 內容。

    ![具有 HTTP POST URL 的 Logic Apps 設計工具窗格](./media/ethereum-logic-app/post-url.png)

1. 使用 cURL 建立 HTTP POST 要求。 *\<HTTP POST URL\>* 使用上一個步驟中的 URL 取代預留位置文字。

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    cURL 命令會傳回邏輯應用程式的回應。 在此案例中，回應是來自 **RequestMessage** 智慧型合約函式的輸出。

    ![來自 RequestMessage 智慧型合約函式的程式碼輸出](./media/ethereum-logic-app/curl.png)

如需使用開發套件的詳細資訊，請參閱[適用於 Ethereum 的 Azure 區塊鏈服務開發套件](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) Wiki 頁面。

## <a name="create-an-api-connection"></a>建立 API 連線

Ethereum 區塊鏈連接器需要與區塊鏈的 API 連線。 您可以對多個邏輯應用程式使用 API 連接器。 有些屬性是必要的，有些則取決於您的案例。

> [!IMPORTANT]
> 必須要有私密金鑰或帳戶位址和密碼，才能在區塊鏈上建立交易。 驗證只需要一種形式。 您不需要同時提供私密金鑰和帳戶詳細資料。 查詢合約時不需要交易。 如果您使用會查詢合約狀態的動作，則不需要私密金鑰或帳戶位址和密碼。

為了協助您設定 Azure 區塊鏈服務成員的連線，視案例之不同，您可能會需要下列清單中的屬性。

| 屬性 | 說明 |
|----------|-------------|
|**連接名稱** | API 連線的名稱。 必要。 |
|**以太坊 RPC 端點** | Azure 區塊鏈服務交易節點的 HTTP 位址。 必要。 如需詳細資訊，請參閱[取得 RPC 端點](#get-the-rpc-endpoint)。 |
|**私密金鑰** | Ethereum 帳戶私密金鑰。 交易必須要有私密金鑰或帳戶位址和密碼。 如需詳細資訊，請參閱[取得私密金鑰](#get-the-private-key)。 |
|**帳戶位址** | Azure 區塊鏈服務成員帳戶位址。 交易必須要有私密金鑰或帳戶位址和密碼。 如需詳細資訊，請參閱[取得帳戶位址](#get-the-account-address)。 |
|**帳戶密碼** | 帳戶密碼會在您建立成員時設定。 如需重設密碼的詳細資訊，請參閱 [Ethereum 帳戶](consortium.md#ethereum-account)。|

## <a name="get-the-rpc-endpoint"></a>取得 RPC 端點

必須要有 Azure 區塊鏈服務 RPC 端點位址，才能連線至區塊鏈網路。 您可以使用適用於 Ethereum 的 Azure 區塊鏈服務開發套件或 Azure 入口網站來取得端點位址。

**若要使用開發套件：**

1. 在 Visual Studio Code 的 [Azure 區塊鏈服務]**** 下方，以滑鼠右鍵按一下聯盟。
1. 選取 [複製 RPC 端點位址]****。

    ![Visual Studio Code 窗格，會顯示已選取 [複製 RPC 端點位址] 的聯盟](./media/ethereum-logic-app/devkit-rpc.png)

    RPC 端點會複製到您的剪貼簿。

**使用 Azure 入口網站：**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的 Azure 區塊鏈服務成員。 選取 [交易節點] 和預設交易節點連結。

    ![交易節點頁面，已選取 [(預設節點)]](./media/ethereum-logic-app/transaction-nodes.png)

1. 選取 [**連接字串**] [  >  **存取金鑰**]。
1. 從 [HTTPS (存取金鑰 1)]**** 或 [HTTPS (存取金鑰 2)]**** 複製端點位址。

    ![具有連接字串存取金鑰的 Azure 入口網站](./media/ethereum-logic-app/connection-string.png)

    RPC 端點是 HTTPS URL，其包含 Azure 區塊鏈服務成員交易節點的位址和存取金鑰。

## <a name="get-the-private-key"></a>取得私密金鑰

您可以在將交易傳送至區塊鏈時，使用 Ethereum 帳戶的私密金鑰來進行驗證。 您的 Ethereum 帳戶公開和私密金鑰是由 12 個字的助憶鍵產生的。 當您連線至 Azure 區塊鏈服務聯盟成員時，適用於 Ethereum 的 Azure 區塊鏈服務開發套件就會產生助憶鍵。 您可以使用開發套件延伸模組來取得端點位址。

1. 在 Visual Studio Code 中，開啟命令選擇區 (F1)。
1. 選取 [ **Azure 區塊鏈：取出私密金鑰**]。
1. 選取您連線至聯盟成員時所儲存的助憶鍵。

    ![命令選擇區，具有可選取助憶鍵的選項](./media/ethereum-logic-app/private-key.png)

    私密金鑰會複製到您的剪貼簿。

## <a name="get-the-account-address"></a>取得帳戶位址

您可以在將交易傳送至區塊鏈時，使用成員帳戶和密碼來進行驗證。 密碼會在您建立成員時設定。

1. 在 Azure 入口網站中，移至您 Azure 區塊鏈服務的概觀頁面。
1. 複製**成員帳戶**位址。

    ![[概觀] 頁面，具有成員帳戶位址](./media/ethereum-logic-app/member-account.png)

如需帳戶位址和密碼的詳細資訊，請參閱 [Ethereum 帳戶](consortium.md#ethereum-account)。

## <a name="get-the-contract-abi"></a>取得合約 ABI

合約 ABI 會定義智慧型合約的介面。 此介面會說明如何與智慧型合約互動。 您可以使用適用於 Ethereum 的 Azure 區塊鏈服務開發套件來取得合約 ABI。 您也可以從 Solidity 編譯器所建立的合約中繼資料檔案中取得合約 ABI。

**若要使用開發套件：**

如果您使用開發套件或 Truffle 建置智慧型合約，您可以使用延伸模組將合約 ABI 複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製合約 ABI]****。

    ![Visual Studio Code 窗格，已選取 [複製合約 ABI]](./media/ethereum-logic-app/abi-devkit.png)

    合約 ABI 會複製到剪貼簿中。

**若要使用合約中繼資料檔案：**

1. 開啟您 Solidity 專案的 **build/contracts** 資料夾中包含的合約中繼資料檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 尋找 JSON 檔案中的 **abi** 區段。
1. 複製 **abi** JSON 陣列。

    ![合約中繼資料檔案中的 ABI 程式碼](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>取得合約位元組程式碼

合約位元組程式碼是由 Ethereum 虛擬機器所執行的已編譯智慧型合約。 您可以使用適用於 Ethereum 的 Azure 區塊鏈服務開發套件來取得合約位元組程式碼。 您也可以從 Solidity 編譯器來取得合約位元組程式碼。

**若要使用開發套件：**

如果您使用開發套件或 Truffle 建置智慧型合約，您可以使用延伸模組將合約位元組程式碼複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製合約位元組程式碼]****。

    ![Visual Studio Code 窗格，已選取 [複製合約位元組程式碼]](./media/ethereum-logic-app/bytecode-devkit.png)

    合約位元組程式碼會複製到剪貼簿中。

**若要使用合約中繼資料檔案：**

1. 開啟您 Solidity 專案的 **build/contracts** 資料夾中包含的合約中繼資料檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 尋找 JSON 檔案中的**位元組程式碼**元素。
1. 複製**位元組程式碼**值。

    ![Visual Studio Code 窗格，具有中繼資料內的位元組程式碼](./media/ethereum-logic-app/bytecode-metadata.png)

**若要使用 Solidity 編譯器：**

使用命令 `solc --bin <smart contract>.sol` 產生合約位元組程式碼。

## <a name="get-the-contract-address"></a>取得合約位址

合約位址是 Ethereum 區塊鏈上的智慧型合約目的地位址。 您可以使用此位址來傳送智慧型合約的交易或查詢狀態。 您可以從 Truffle 移轉輸出或合約中繼資料檔案取得合約位址。

**若要使用 Truffle 遷移輸出：**

Truffle 會在智慧型合約部署後顯示合約位址。 從輸出複製**合約位址**。

![在 Visual Studio Code 中具有合約位址的 Truffle 移轉輸出](./media/ethereum-logic-app/contract-address-truffle.png)

**若要使用合約中繼資料檔案：**

1. 開啟您 Solidity 專案的 **build/contracts** 資料夾中包含的合約中繼資料檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 尋找 JSON 檔案中的**網路**區段。
1. 私人網路會以整數網路識別碼來識別。 尋找網路區段內的位址值。
1. 複製**位址**值。

![在 Visual Studio Code 中具有位址值的中繼資料](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>後續步驟

請在 [Logic Apps 的其他功用](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)影片中觀看常見案例。
