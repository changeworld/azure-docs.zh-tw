---
title: 智慧合約整合模式 - Azure 區塊鏈工作臺
description: Azure 區塊鏈工作臺預覽中智慧合約整合模式概述。
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325960"
---
# <a name="smart-contract-integration-patterns"></a>智慧合約整合模式

智慧合約常會顯示需要與外部系統和裝置整合的商務工作流程。

這些工作流程的需求，包括需要對包含外部系統、服務或裝置資料的分散式總帳起始交易。 它們也必須具有外部系統，以回應從分散式總帳的智慧合約產生的事件。

REST API 與訊息整合會將外部系統的交易傳送至 Azure Blockchain Workbench 應用程式中包含的智慧合約。 它也會根據應用程式內進行的變更，將事件通知傳送到外部系統。

在資料整合案例中，Azure Blockchain Workbench 包含一組資料庫檢視，其中整合了區塊鏈和中繼資料中與應用程式和智慧合約有關的中交易資料。

此外，在某些情況下 (例如，與供應鏈或媒體有關的情況)，也可能需要文件的整合。 雖然 Azure Blockchain Workbench 未提供可直接處理文件的 API 呼叫，但文件可以納入 Blockchain 應用程式中。 本節也會說明該模式。

本節將說明經認可能夠在您的端對端解決方案中實作各類整合的模式。

## <a name="rest-api-based-integration"></a>以 REST API 為基礎的整合

Azure Blockchain Workbench 產生的 Web 應用程式所包含的功能，可透過 REST API 來公開。 這些功能包括 Azure Blockchain Workbench 上傳、應用程式的設定和管理、將交易傳送至分散式總帳，以及應用程式中繼資料和總帳資料的查詢。

REST API 主要用於互動式用戶端，例如 Web、行動和 Bot 應用程式。

本節探討的模式著重在可將交易傳送至分散式總帳的 REST API 層面，以及可從 Azure Blockchain Workbench 的*鏈結關閉* SQL 資料庫查詢交易相關資料的模式。

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>從外部系統將交易傳送至分散式總帳

Azure Blockchain Workbench REST API 會傳送已驗證的要求，以對分散式總帳執行交易。

![將交易傳送至分散式總帳](./media/integration-patterns/send-transactions-ledger.png)

執行交易是使用前述程序來進行，期間：

-   外部應用程式對已在部署的過程中完成佈建的 Azure Blockchain Workbench 進行驗證。
-   授權使用者接收可隨要求一起傳送至 API 的持有人權杖。
-   外部應用程式使用持有人權杖呼叫 REST API。
-   REST API 將要求封裝為訊息，並將其傳送至服務匯流排。 在此處擷取、簽署訊息，然後傳送至適當的分散式總帳。
-   REST API 對 Azure Blockchain Workbench SQL DB 提出要求，以記錄要求並建立目前的佈建狀態。
-   SQL DB 傳回佈建狀態，而 API 呼叫會將識別碼傳回至呼叫它的外部應用程式。

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>查詢 Blockchain Workbench 中繼資料和分散式總帳交易

Azure Blockchain Workbench REST API 會傳送已驗證的要求，以查詢對分散式總帳執行之智慧合約的相關詳細資料。

![查詢中繼資料](./media/integration-patterns/querying-metadata.png)

查詢是使用前述程序來執行，其間：

1. 外部應用程式對已在部署的過程中完成佈建的 Azure Blockchain Workbench 進行驗證。
2. 授權使用者接收可隨要求一起傳送至 API 的持有人權杖。
3. 外部應用程式使用持有人權杖呼叫 REST API。
4. REST API 會查詢來自 SQL DB 的要求資料，並將其傳回至用戶端。

## <a name="messaging-integration"></a>傳訊整合

傳訊整合有助於與無法使用或不適用互動式登入的系統、服務和裝置進行互動。 傳訊整合著重在兩種類型的訊息：要求對分散式總帳執行交易，且在交易發生時必須由該總帳公開事件的訊息。

傳訊整合著重於執行和監視與使用者建立、合約建立和合約的交易執行有關的交易，且主要由*無周邊*後端系統所使用。

本節探討的模式著重在可將交易傳送至分散式總帳的訊息型 API 層面，以及可表示基礎分散式總帳所公開事件訊息的模式。

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>從智慧合約到事件取用者的單向事件傳遞 

在此案例中，事件發生於智慧合約內，例如特定事件類型的狀態變更或執行。 此事件會透過事件方格廣播至下游取用者，讓這些取用者採取適當的動作。

此案例的範例之一，是取用者在交易發生時會收到警示並可執行動作，例如在 SQL DB 或 Common Data Service 中記錄資訊。 此案例是 Workbench 填入其「鏈結關閉」** SQL DB 時所依循的相同模式。

另一個範例，是智慧合約轉換為特定狀態時，例如，當合約進入 *OutOfCompliance* 時。 當此狀態變更發生時，可能會觸發警示並傳送至系統管理員的行動電話。

![單向事件傳遞](./media/integration-patterns/one-way-event-delivery.png)

此案例是使用前述程序來執行，其間：

-   智慧合約轉換為新的狀態，並將事件傳送至總帳。
-   總帳接收該事件，並將其傳遞至 Azure Blockchain Workbench。
-   Azure Blockchain Workbench 訂閱來自總帳的事件，並接收事件。
-   Azure Blockchain Workbench 將事件發佈至事件方格的訂閱者。
-   外部系統訂閱事件方格、取用訊息，並採取適當動作。

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>將訊息從外部系統傳至智慧合約的單向事件傳遞

也有某些案例，會以相反方向傳遞事件。 在此情況下，感應器或外部系統所產生的事件以及該事件中的資料，均應傳送至智慧合約。

常見的範例是將來自金融市場的資料 (例如商品、股票或債券的價格) 傳遞至智慧合約。

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Azure Blockchain Workbench 以預期的格式進行直接傳遞

某些應用程式在建置後會與 Azure Blockchain Workbench 整合，並直接以預期的格式產生和傳送訊息。

![直接傳遞](./media/integration-patterns/direct-delivery.png)

此傳遞是使用前述程序來執行，其間：

-   事件發生於外部系統中，而致使系統為 Azure Blockchain Workbench 建立訊息。
-   外部系統撰寫程式碼，以已知的格式建立此訊息，並將其直接傳送至服務匯流排。
-   Azure Blockchain Workbench 訂閱來自服務匯流排的事件，並擷取訊息。
-   Azure Blockchain Workbench 對總帳起始呼叫，將資料從外部系統傳送至特定合約。
-   收到訊息時，合約會轉換為新的狀態。

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>以 Azure Blockchain Workbench 未知的格式傳遞訊息

某些系統無法修改成以 Azure Blockchain Workbench 使用的標準格式傳遞訊息。 在此類情況下，常會使用這些系統的現有機制和訊息格式。 具體而言，這些系統的原生訊息類型可使用 Logic Apps、Azure Functions 或其他自訂程式碼進行轉換，以對應至預期的標準傳訊格式之一。

![未知的訊息格式](./media/integration-patterns/unknown-message-format.png)

此作業是使用前述程序來執行，其間：

-   事件發生於外部系統中，而致使系統建立訊息。
-   使用 Logic App 或自訂程式碼接收該訊息，並將其轉換為標準 Azure Blockchain Workbench 格式化訊息。
-   Logic App 將已轉換的訊息直接傳送至服務匯流排。
-   Azure Blockchain Workbench 訂閱來自服務匯流排的事件，並擷取訊息。
-   Azure Blockchain Workbench 對總帳起始呼叫，將資料從外部系統傳送至合約的特定函式。
-   函式執行時通常會修改狀態。 狀態的變更會移至反映在智慧合約中的商務工作流程，讓其他函式此時能夠正常執行。

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>將控制權轉換至外部程序，並等待完成

在某些情況下，智慧合約必須停止內部執行，而讓外部程序接管。 該外部程序會隨即完成處理、將訊息傳送至智慧合約，然後再讓智慧合約繼續執行。

#### <a name="transition-to-the-external-process"></a>轉換至外部程序

此模式通常使用下列方法來實作：

-   智慧合約轉換至特定狀態。 在此狀態下，將沒有或僅有少數函式可執行，直到外部系統執行所需的動作為止。
-   狀態的變更以事件的形式對下游取用者呈現。
-   下游取用者接收事件，並觸發外部程式碼執行。

![將控制權轉換至外部程序](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>從智慧合約交回控制權

根據自訂外部系統的能力，該系統有可能無法以 Azure Blockchain Workbench 預期的標準格式之一來傳遞訊息。 外部系統產生其中一種訊息的能力，可決定應採取下列兩個傳回路徑中的哪一個。

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Azure Blockchain Workbench 以預期的格式進行直接傳遞

![](./media/integration-patterns/direct-delivery.png)

在此模型中，與合約的通訊和後續的狀態變更將會在先前程序之後發生，其間：

-   在外部程式碼執行完成或達到特定里程碑時，就會傳送事件到連線至 Azure Blockchain Workbench 的服務匯流排。

-   系統若無法直接調整而撰寫符合 API 預期的訊息，就會轉換訊息。

-   訊息的內容會在封裝後傳送至智慧合約的特定函式。 此傳遞會代表與外部系統相關聯的使用者執行。

-   函式執行時通常會修改狀態。 狀態的變更會移至反映在智慧合約中的商務工作流程，讓其他函式此時能夠正常執行。

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>以 Azure Blockchain Workbench 未知的格式傳遞訊息

![未知的訊息格式](./media/integration-patterns/unknown-message-format.png)

在此模型中，採用標準格式的訊息無法直接傳送，與合約的通訊和後續的狀態變更將會在先前程序之後發生，其間：

1.  在外部程式碼執行完成或達到特定里程碑時，就會傳送事件到連線至 Azure Blockchain Workbench 的服務匯流排。
2.  使用 Logic App 或自訂程式碼接收該訊息，並將其轉換為標準 Azure Blockchain Workbench 格式化訊息。
3.  Logic App 將已轉換的訊息直接傳送至服務匯流排。
4.  Azure Blockchain Workbench 訂閱來自服務匯流排的事件，並擷取訊息。
5.  Azure Blockchain Workbench 對總帳起始呼叫，將資料從外部系統傳送至特定合約。
6. 訊息的內容會在封裝後傳送至智慧合約的特定函式。 此傳遞會代表與外部系統相關聯的使用者執行。
7.  函式執行時通常會修改狀態。 狀態的變更會移至反映在智慧合約中的商務工作流程，讓其他函式此時能夠正常執行。

## <a name="iot-integration"></a>IoT 整合

常見的整合案例是在智慧合約納入擷取自感應器的遙測資料。 根據感應器所傳遞的資料，智慧合約可以採取正確的動作，並改變合約的狀態。

例如，如果運送藥品的貨車溫度飆升至 110 度，則可能會影響到藥品的藥效，且若未偵測到此問題而從供應鏈中移除，可能會造成公共安全問題。 如果司機將汽車加速到每小時 100 英里，則生成的感應器資訊可能會觸發其保險供應商取消保險。 對於租用的車輛，GPS 資料可以在駕駛的行車路徑超出其租用合約所涵蓋的地理位置時加以指出，而收取罰金。

其困難之處在於感應器是否能穩定持續地傳遞資料，且將所有資料傳送至智慧合約，也並不妥當。 常見的方法是，限定傳送至區塊鏈的訊息數量，而將所有訊息傳遞至次要存放區。 例如，傳遞僅依固定間隔接收的訊息，例如每小時一次，以及在限定值超出智慧合約的協議範圍時傳遞。 檢查超出容許範圍的值，可確保能夠接收及執行合約商務邏輯的相關資料。 定期檢查值，可確認感應器仍在進行報告。 所有資料都會傳送至次要報告存放區，以便進行更廣泛的報告、分析和機器學習。 例如，雖然智慧合約不一定需要每分鐘都取得 GPS 感應器的數據，但對於報告或製圖路徑，這或許可提供有價值的資料。

在 Azure 平台上，與裝置的整合通常透過 IoT 中樞來完成。 IoT 中樞可提供根據內容的訊息路由，並啟用前述的功能類型。

![IoT 訊息](./media/integration-patterns/iot.png)

此程序描述一種模式：

-   裝置直接或透過現場閘道與 IoT 中樞通訊。
-   IoT 中樞接收訊息，並根據已建立而會檢查訊息內容 (舉例而言) 的路由來評估訊息。 *感應器報告的溫度是否超過 50 度？*
-   IoT 中樞將符合條件的訊息傳送至已針對路由而定義的服務匯流排。
-   Logic App 或其他程式碼接聽 IoT 中樞已為路由建立的服務匯流排。
-   Logic App 或其他程式碼擷取訊息，並將其轉換為已知的格式。
-   轉換後的訊息現已採用標準格式，會傳送至 Azure Blockchain Workbench 的服務匯流排。
-   Azure Blockchain Workbench 訂閱來自服務匯流排的事件，並擷取訊息。
-   Azure Blockchain Workbench 對總帳起始呼叫，將資料從外部系統傳送至特定合約。
-   收到訊息時，合約會評估資料，並且可根據該評估的結果變更狀態，例如，在溫度過高時，將狀態變更為*不合規*。

## <a name="data-integration"></a>資料整合

除了 REST 和訊息型 API 以外，Azure Blockchain Workbench 也可讓您存取 SQL DB，讓您使用其中已填入的應用程式和合約中繼資料，以及來自分散式總帳的交易資料。

![資料整合](./media/integration-patterns/data-integration.png)

資料整合十分常見：

-   Azure Blockchain Workbench 會儲存關於應用程式、工作流程、合約和交易的中繼資料，這是其正常作業行為的一部分。
-   外部系統或工具會提供一或多個對話方塊，以利收集資料庫的相關資訊，例如資料庫伺服器名稱、資料庫名稱、驗證類型、登入認證，以及所要使用的資料庫檢視。
-   系統會根據 SQL 資料庫檢視撰寫查詢，以方便外部系統、服務、報告、開發人員工具和企業生產力工具的下游使用。

## <a name="storage-integration"></a>儲存體整合

在許多情況下，都可能需要納入 attestable 檔案。 基於許多原因，將檔案放在區塊鏈上並不適當。 常見的方法是對檔案執行密碼編譯雜湊 (例如 SHA-256)，並在分散式總帳上共用該雜湊。 後續再隨時執行雜湊，應該會傳回相同的結果。 如果檔案已修改，即使只是在影像中修改了一個像素，雜湊也會傳回不同的值。

![儲存體整合](./media/integration-patterns/storage-integration.png)

在下列情況下可實作此模式：

-   外部系統以某種儲存機制保存檔案，例如 Azure 儲存體。
-   使用檔案，或檔案和相關聯的中繼資料 (例如擁有者的識別碼、檔案所在的 URL 等) 產生雜湊。
-   雜湊和任何中繼資料傳送至智慧合約的函式，例如 *FileAdded*
-   在未來，將可再次使用檔案和中繼資料產生雜湊，並將其與總帳上儲存的值進行比較。

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>使用 REST 和訊息 API 實作整合模式的必要條件

若要提升外部系統或裝置使用 REST 或訊息 API 與智慧合約進行互動的能力，必須符合下列條件：

1. 在協會的 Azure Active Directory 中，已建立代表外部系統或裝置的帳戶。
2. Azure Blockchain Workbench 應用程式的一或多份適當智慧合約已定義函式，以接受來自外部系統或裝置的事件。
3. 智慧合約的應用程式組態檔包含指派給系統或裝置的角色。
4. 智慧合約的應用程式組態檔可識別已定義的角色在哪些狀態下會呼叫此函式。
5. 應用程式組態檔及其智慧合約已上傳至 Azure Blockchain Workbench。

應用程式上傳後，外部系統的 Azure Active Directory 帳戶即會指派給合約和相關聯的角色。

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>在撰寫整合程式碼之前測試外部系統整合流程 

與外部系統整合是許多案例中的關鍵需求之一。 能夠在開發與外部系統整合的程式碼之前或期間驗證智慧合約，會比較妥當。

使用 Azure Active Directory (Azure AD) 可大幅增進開發人員生產力和獲利時程。 具體來說，與外部系統的程式碼整合，可能需要相當程度的時間。 使用 Azure AD 和 Azure Blockchain Workbench 的 UX 自動產生功能，可讓開發人員從外部系統登入 Workbench，並透過 UX 填入來自外部系統的值。 您可在為外部系統撰寫整合程式碼之前，快速開發和驗證概念證明環境中的概念。
