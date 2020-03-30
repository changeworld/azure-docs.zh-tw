---
title: 適用於 Azure Logic Apps 的連接器
description: 使用 Azure 邏輯應用的連接器（如內置、託管、本地、集成帳戶、ISE 和企業連接器）自動執行工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247328"
---
# <a name="connectors-for-azure-logic-apps"></a>適用於 Azure Logic Apps 的連接器

連接器可讓您跨其他應用程式、服務、系統、通訊協定與平台快速從 Azure Logic Apps 存取事件、資料和動作。 在邏輯應用程式中使用連接器，即可擴充雲端和內部部署應用程式的功能，以便對您新建和已有的資料執行工作。

雖然邏輯應用提供了[數百個連接器](https://docs.microsoft.com/connectors)，本文描述了*流行的和最常用的*連接器，這些連接器被數千個應用成功使用，數百萬次執行用於處理資料和資訊。 要查找連接器的完整清單和每個連接器的參考資訊（如觸發器、操作和限制），請查看[連接器概述](https://docs.microsoft.com/connectors)下的連接器參考頁。 此外，瞭解有關[觸發器和操作](#triggers-actions)、[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md)和[邏輯應用定價詳細資訊的詳細資訊](https://azure.microsoft.com/pricing/details/logic-apps/)。

> [!TIP]
> 要與沒有連接器的服務或 API 集成，可以通過 HTTP 等協定直接調用服務，也可以創建[自訂連接器](#custom)。

## <a name="connector-types"></a>連接器類型

連接器可作為內置觸發器和操作或託管連接器提供。

<a name="built-in"></a>

* [**內置**](#built-ins)： 內置觸發器和操作是 Azure 邏輯應用的"本機"，可説明您為邏輯應用執行這些任務：

  * 按照自訂和高級計畫運行。

  * 組織和控制邏輯應用的工作流，例如迴圈和條件，以及處理變數和資料操作。

  * 與其他終結點通信。

  * 接收和回應請求。

  * 調用 Azure 函數、Azure API 應用（Web 應用）、使用 Azure API 管理管理和發佈的自己的 API 以及可以接收請求的嵌套邏輯應用。

<a name="managed-connectors"></a>

* [**託管連接器**](#managed-api-connectors)：由 Microsoft 部署和管理，這些連接器提供觸發器和操作，用於訪問雲服務、本地系統或兩者，包括 Office 365、Azure Blob 存儲、SQL Server、動態、銷售力量、SharePoint 等。 某些連接器專門支援企業對企業 （B2B） 通信方案，並且需要連結到邏輯應用的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 在使用某些連接器之前，您可能需要首先創建由 Azure 邏輯應用管理的連接。

  例如，如果您使用的是 Microsoft BizTalk 伺服器，則邏輯應用可以使用[BizTalk 伺服器本地連接器](#on-premises-connectors)連接到 BizTalk 伺服器並與其通信。 您可以使用[整合帳戶連接器](#integration-account-connectors)，接著在邏輯應用程式中擴充或執行類似 BizTalk 的作業。

  連接器分為標準連接器或企業連接器。 [企業連接器](#enterprise-connectors)提供對企業系統（如 SAP、IBM MQ 和 IBM 3270）的訪問，但需額外付費。 要確定連接器是標準連接器還是企業連接器，請參閱[連接器概述](https://docs.microsoft.com/connectors)下的每個連接器參考頁中的技術詳細資訊。

  您還可以使用這些類別標識連接器，儘管某些連接器可以跨多個類別。 例如，SAP 是企業連接器和本地連接器：

  |   |   |
  |---|---|
  | [**受控連接器**](#managed-api-connectors) | 建立可使用 Azure Blob 儲存體、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online 等其他服務的邏輯應用程式。 |
  | [**內部部署連接器**](#on-premises-connectors) | 在您安裝並設定[內部部署資料閘道][gateway-doc]之後，這些連接器可協助您的邏輯應用程式存取內部部署系統，例如 SQL Server、SharePoint Server、Oracle DB、檔案共用及其他系統。 |
  | [**整合帳戶連接器**](#integration-account-connectors) | 當您建立並支付整合帳戶時，這些連接器會轉換和驗證 XML、進行一般檔案的編碼和解碼，以及透過 AS2、EDIFACT 和 X12 通訊協定處理企業對企業 (B2B) 訊息。 |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>從整合服務環境進行連接

對於需要直接存取 Azure 虛擬網路中資源的邏輯應用，您可以創建一個隔離[的整合服務環境 （ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)您可以在其中在專用資源上生成、部署和運行邏輯應用。 在邏輯應用設計器中，當您流覽要用於 ISE 中的邏輯應用的連接器時，內置觸發器和操作上會出現**CORE**標籤，而**ISE**標籤出現在某些連接器上：

* **CORE**： 內置觸發器和操作，具有此標籤，在與邏輯應用相同的 ISE 中運行，例如：

  ![示例 ISE 連接器](./media/apis-list/example-core-connector.png)

* **ISE**： 具有此標籤的託管連接器與邏輯應用在 ISE 中運行，例如：

  ![示例 ISE 連接器](./media/apis-list/example-ise-connector.png)

  如果有連接到 Azure 虛擬網路的本地系統，ISE 允許邏輯應用在沒有[本地資料閘道](../logic-apps/logic-apps-gateway-connection.md)的情況下直接存取該系統。 相反，您可以使用該系統的**ISE**連接器（如果可用）、HTTP 操作或[自訂連接器](#custom)。 對於沒有**ISE**連接器的本地系統，請使用本地資料閘道。 要查看可用的 ISE 連接器，請參閱[ISE 連接器](#ise-connectors)。

* 在全域多租戶邏輯應用服務中運行沒有**CORE**或**ISE**標籤的所有其他連接器（可以繼續使用）：

  ![多租戶連接器示例](./media/apis-list/example-multi-tenant-connector.png)

在 ISE 及其連接器中運行的邏輯應用（無論這些連接器在何處運行）遵循固定定價計畫，而不是基於消耗的定價計畫。 有關詳細資訊，請參閱以下頁面：

* [Logic Apps 的定價模式](../logic-apps/logic-apps-pricing.md)
* [邏輯應用定價詳細資訊](https://azure.microsoft.com/pricing/details/logic-apps/)
* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>內建

邏輯應用提供內置觸發器和操作，以便您可以創建基於計畫的工作流、説明邏輯應用與其他應用和服務通信、通過邏輯應用控制工作流以及管理或運算元據。

|   |   |   |   |
|---|---|---|---|
| [![API][schedule-icon]<br>圖示**計畫**][schedule-doc] | - 在指定的定期事件上運行邏輯應用，從基本計畫到高級計畫（使用[**定期**觸發器][schedule-recurrence-doc]）不等。 <p>- 運行一個邏輯應用程式，需要處理連續塊中的資料與[**滑動視窗**觸發器][schedule-sliding-window-doc]。 <p>- 使用[**"延遲**"操作][schedule-delay-doc]暫停邏輯應用指定持續時間。 <p>- 暫停邏輯應用，直到指定的日期和時間與[**延遲，直到**操作][schedule-delay-until-doc]。 | [![API][batch-icon]<br>圖示**批次處理**][batch-doc] | - 透過 [批次訊息]**** 觸發程序，分批處理訊息。 <p>- 透過 [將訊息傳送至批次]**** 動作，呼叫具有現有批次觸發程序的邏輯應用程式。 |
| [![API][http-icon]<br>圖示**HTTP**][http-doc] | 使用 HTTP 的觸發器和操作調用 HTTP 或 HTTPS 終結點。 其他 HTTP 內置觸發器和操作包括[HTTP + 斯瓦格][http-swagger-doc]和[HTTP = Webhook][http-webhook-doc]。 | [![API][http-request-icon]<br>圖示**請求**][http-request-doc] | - 透過 [要求]**** 觸發程序，讓邏輯應用程式得以從其他應用程式或服務呼叫、在 Event Grid 資源事件上觸發，或在 Azure 資訊安全中心警示的回應上觸發。 <p>- 透過 [回應]**** 動作，將回應傳送到應用程式或服務。 |
| [![API][azure-api-management-icon]<br>圖示**Azure API<br>管理**][azure-api-management-doc] | 呼叫透過 Azure API 管理控管和發佈的自有 API 所定義的觸發程序和動作。 | [![API][azure-app-services-icon]<br>圖示**Azure 應用<br>服務**][azure-app-services-doc] | 呼叫 API Apps 或 Web Apps (裝載於 Azure App Service)。 如果包含 Swagger，這些應用程式所定義的動作與觸發程序看起來像是任何其他第一級觸發程序和動作。|
| [![API][azure-logic-apps-icon]<br>圖示**Azure 邏輯<br>應用**][nested-logic-app-doc] | 調用以 **"請求"** 觸發器開頭的其他邏輯應用。 |
|||||

### <a name="run-code-from-logic-apps"></a>從邏輯應用運行代碼

邏輯應用提供內置操作，用於在邏輯應用的工作流中運行您自己的代碼：

|   |   |   |   |
|---|---|---|---|
| [![API][azure-functions-icon]<br>圖示**Azure 函數**][azure-functions-doc] | 呼叫 Azure 函式，以從邏輯應用程式執行自訂程式碼片段 (C# 或 Node.js)。 | [![API][inline-code-icon]<br>圖示**內聯代碼**][azure-functions-doc] | 從邏輯應用添加和運行 JavaScript 程式碼片段。 |
|||||

### <a name="control-workflow"></a>控制工作流程

邏輯應用提供內置操作，用於構建和控制邏輯應用工作流中的操作：

|   |   |   |   |
|---|---|---|---|
| [![內置圖示][condition-icon]<br>**條件**][condition-doc] | 根據條件為 true 或 false，評估條件並執行不同的動作。 | [![][for-each-icon]<br>**每個**的內置圖示][for-each-doc] | 對陣列中的每個項目執行相同的動作。 |
| [![內置圖示][scope-icon]<br>**範圍**][scope-doc] | 將動作分成數個「範圍」**，這些範圍會在範圍中的動作執行完成後取得自己的狀態。 | [![內置圖示][switch-icon]<br>**開關**][switch-doc] | 將動作分成數個「案例」**，這些案例會獲派唯一值 (預設案例除外)。 僅執行其指派值符合運算式、物件或語彙基元結果的案例。 如果沒有相符項目存在，請執行預設案例。 |
| [![內置圖示][terminate-icon]<br>**終止**][terminate-doc] | 停止正在執行的邏輯應用程式工作流程。 | [![內置圖示][until-icon]<br>**，直到**][until-doc] | 重複動作，直到符合指定的條件或某些狀態已改變為止。 |
|||||

### <a name="manage-or-manipulate-data"></a>管理或操作資料

邏輯應用提供用於處理資料輸出及其格式的內置操作：

|   |   |
|---|---|
| [![內置圖示][data-operations-icon]<br>**資料操作**][data-operations-doc] | 執行資料相關作業： <p>- **撰寫**：從多個輸入與各種類型建立單一輸出。 <br>- **建立 CSV 資料表**：從 JSON 物件陣列建立以逗號分隔 (CSV) 的資料表。 <br>- **建立 HTML 資料表**：從 JSON 物件陣列建立 HTML 資料表。 <br>- **篩選陣列**：從符合您準則的另一個陣列建立陣列。 <br>- **加入**：從陣列中的所有項目建立字串，並使用指定的分隔符號來分隔這些項目。 <br>- **分析 JSON**：從 JSON 內容中的屬性及其值創建方便使用的權杖，以便在工作流中使用這些屬性。 <br>- **選取**：轉換另一個陣列中的項目或值，並將這些項目對應至指定的屬性，以建立 JSON 物件陣列。 |
| ![內建圖示][date-time-icon]<br>**日期時間** | 執行時間戳記相關作業： <p>- **新增至時間**：將指定的單位數新增至時間戳記。 <br>- **轉換時區**：將時間戳記從來源時區轉換為目標時區。 <br>- **目前時間**：傳回目前的時間戳記作為字串。 <br>- **取得未來時間**：傳回目前時間戳記加上指定時間單位的結果。 <br>- **取得過去時間**：傳回目前時間戳記減去指定時間單位的結果。 <br>- **從時間減去**：從時間戳記減去時間單位數字。 |
| [![內置圖示][variables-icon]<br>**變數**][variables-doc] | 執行變數相關作業： <p>- **附加到陣列變數**：插入一個值，作為變數所儲存陣列中的最後一個項目。 <br>- **附加到字串變數**：插入一個值，作為變數所儲存字串中的最後一個字元。 <br>- **遞減變數**：依常數值遞減變數。 <br>- **遞增變數**：依常數值遞增變數。 <br>- **初始化變數**：建立變數，並宣告其資料類型和初始值。 <br>- **設定變數**：將不同的值指派給現有的變數。 |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>受控連接器

邏輯應用提供這些流行的標準連接器，用於通過這些服務或系統自動執行任務、流程和工作流：

|   |   |   |   |
|---|---|---|---|
| [![API][azure-service-bus-icon]<br>圖示**Azure 服務匯流排**][azure-service-bus-doc] | 利用 Logic Apps 中最常用的連接器，管理非同步訊息、工作階段和主題訂用帳戶。 | [![API][sql-server-icon]<br>圖示**SQL 伺服器**][sql-server-doc] | 在內部連接到 SQL Server 或雲中的 Azure SQL 資料庫，以便可以記錄管理、運行預存程序或執行查詢。 |
| [![API][azure-blob-storage-icon]<br>圖示**Azure Blob<br>存儲**][azure-blob-storage-doc] | 連接到存儲帳戶，以便您可以創建和管理 Blob 內容。 | [![API][office-365-outlook-icon]<br>圖示**Office<br>365 展望**][office-365-outlook-doc] | 連接到 Office 365 電子郵件帳戶，以便您可以創建和管理電子郵件、任務、日曆事件和會議、連絡人、請求等。 |
| [![API][sftp-ssh-icon]<br>圖示**SFTP-SSH**][sftp-ssh-doc] | 連接到 SFTP 伺服器，您可以使用 SSH 從 Internet 訪問這些伺服器，以便可以使用檔和資料夾。 | [![API][sharepoint-online-icon]<br>圖示**共用點<br>線上**][sharepoint-online-doc] | 連線連接到 SharePoint，以便可以管理檔、附件、資料夾等。 | 
| [![API][dynamics-365-icon]<br>圖示**動態 365<br> **][dynamics-365-doc] | 連接到 Dynamics 365 帳戶，以便您可以創建和記錄管理、專案等。 | [![API][azure-queues-icon]<br>圖示**Azure<br>佇列**][azure-queues-doc] | 連接到 Azure 存儲帳戶，以便創建和管理佇列和消息 |
| [![API][ftp-icon]<br>圖示**FTP**][ftp-doc] | 連接到 FTP 伺服器，您可以從 Internet 訪問，以便您可以使用檔和資料夾。 | [![API][file-system-icon]<br>**圖示<br>檔案系統**][file-system-doc] | 連接到本地檔共用，以便您可以創建和管理檔。 |
| [![API][azure-event-hubs-icon]<br>圖示**Azure 事件中心**][azure-event-hubs-doc] | 透過事件中樞取用和發佈事件。 例如，透過事件中樞從邏輯應用程式取得輸出，然後將輸出傳送給即時分析提供者。 | [![API][azure-event-grid-icon]<br>圖示**Azure 事件**<br>**網格**][azure-event-grid-doc] | 當 Azure 資源或第三方資源變更時，監視 Event Grid 所發佈的事件。 |
| [![API][salesforce-icon]<br>圖示**銷售隊伍**][salesforce-doc] | 連接到 Salesforce 帳戶，以便您可以創建和記錄管理、作業、物件等專案。 | [![API圖示][twitter-icon]<br>**推特**][twitter-doc] | 連接到您的 Twitter 帳戶，以便您可以管理推文、關注者、時間表等。 將您的推文儲存到 SQL、Excel 或 SharePoint。 |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>內部部署連接器

以下是邏輯應用為訪問本地系統中的資料和資源提供的一些常用標準連接器。 您必須先[下載、安裝及設定內部部署資料閘道][gateway-doc]，才可建立內部部署系統連線。 此閘道會提供安全的通訊通道，而不需設定必要的網路基礎結構。

|   |   |   |   |   |
|---|---|---|---|---|
| [![API][biztalk-server-icon]<br>圖示**BizTalk** <br>**伺服器**][biztalk-server-doc] | [![API][file-system-icon]<br>**圖示<br>檔案系統**][file-system-doc] | [![API][ibm-db2-icon]<br>圖示**IBM DB2**][ibm-db2-doc] | [![API][ibm-informix-icon]<br>圖示**IBM** <br> **Informix**][ibm-informix-doc] | [![API][mysql-icon]<br>圖示**MySQL**][mysql-doc] |
| [![API][oracle-db-icon]<br>圖示**Oracle DB**][oracle-db-doc] | [![API][postgre-sql-icon]<br>圖示**PostgreSQL**][postgre-sql-doc] | [![API][sharepoint-server-icon]<br>圖示**共用<br>點伺服器**][sharepoint-server-doc] | [![API][sql-server-icon]<br>圖示**SQL<br>伺服器**][sql-server-doc] | [![API][teradata-icon]<br>圖示**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>整合帳戶連接器

邏輯應用提供標準連接器，用於在創建和支付[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)時使用邏輯應用構建企業對企業 （B2B） 解決方案，該帳戶可通過 Azure 中的企業集成包 （EIP） 提供。 使用此帳戶，您可以建立並儲存一些 B2B 構件，例如交易夥伴、合約、地圖、結構描述、憑證等等。 若要使用這些構件，請建立您的邏輯應用程式與整合帳戶的關聯。 如果您目前使用 BizTalk Server，可能已經熟悉這些連接器。

|   |   |   |   |
|---|---|---|---|
| [![API][as2-icon]<br>圖示**AS2<br>解碼**][as2-doc] | [![API][as2-icon]<br>圖示**AS2<br>編碼**][as2-doc] | [![API][edifact-icon]<br>圖示**EDIFACT<br>解碼**][edifact-decode-doc] | [![API][edifact-icon]<br>圖示**EDIFACT<br>編碼**][edifact-encode-doc] |
| [![API][flat-file-decode-icon]<br>圖示**一般檔案<br>解碼**][flat-file-decode-doc] | [![API][flat-file-encode-icon]<br>圖示**一般檔案<br>編碼**][flat-file-encode-doc] | [![API][integration-account-icon]<br>**圖示<br>集成帳戶**][integration-account-doc] | [![API][liquid-icon]<br>圖示**液體**<br>**轉換**][json-liquid-transform-doc] |
| [![API][x12-icon]<br>圖示**X12<br>解碼**][x12-decode-doc] | [![API][x12-icon]<br>圖示**X12<br>編碼**][x12-encode-doc] | [![API][xml-transform-icon]<br>圖示**XML** <br>**轉換**][xml-transform-doc] | [![API][xml-validate-icon]<br>圖示**XML<br>驗證**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>企業連接器

邏輯應用提供用於訪問企業系統的企業連接器，例如 SAP 和 IBM MQ：

|   |   |   |
|---|---|---|
| [![API][ibm-3270-icon]<br>圖示**IBM 3270**][ibm-3270-doc] | [![API][ibm-mq-icon]<br>圖示**IBM MQ**][ibm-mq-doc] | [![API][sap-icon]<br>圖示**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 連接器

對於創建和運行隔離[整合服務環境 （ISE）](#integration-service-environment)的邏輯應用，邏輯應用設計器使用**CORE**標籤標識在 ISE 中運行的內置觸發器和操作。 在 ISE 中運行的託管連接器顯示**ISE**標籤，而在全域多租戶邏輯應用服務中運行的連接器不顯示任一標籤。 此清單顯示當前具有 ISE 版本的連接器：

|   |   |   |   |   |
|---|---|---|---|---|
[![API][as2-icon]<br>圖示**AS2**][as2-doc] | [![API][azure-blob-storage-icon]<br>圖示**Azure Blob<br>存儲**][azure-blob-storage-doc] | [![API][azure-cosmos-db-icon]<br>圖示**Azure<br>宇宙 DB**][azure-cosmos-db-doc] | [![API][azure-event-hubs-icon]<br>圖示**Azure 事件<br>中心**][azure-event-hubs-doc] | [![API][azure-file-storage-icon]<br>圖示**Azure 檔<br>存儲**][azure-file-storage-doc] |
| [![API][azure-service-bus-icon]<br>圖示**Azure 服務<br>匯流排**][azure-service-bus-doc] | [![API][azure-sql-data-warehouse-icon]<br>圖示**Azure <br>SQL 資料倉儲**][azure-sql-data-warehouse-doc] | [![API][azure-table-storage-icon]<br>圖示**Azure 表<br>存儲**][azure-table-storage-doc] | [![API][azure-queues-icon]<br>圖示**Azure<br>佇列**][azure-queues-doc] | [![API][edifact-icon]<br>圖示**EDIFACT**][edifact-doc] |
| [![API][file-system-icon]<br>**圖示<br>檔案系統**][file-system-doc] | [![API][ftp-icon]<br>圖示**FTP**][ftp-doc] | [![API][ibm-3270-icon]<br>圖示**IBM 3270**][ibm-3270-doc] | [![API][ibm-db2-icon]<br>圖示**IBM DB2**][ibm-db2-doc] | [![API][ibm-mq-icon]<br>圖示**IBM MQ**][ibm-mq-doc] |
| [![API][sap-icon]<br>圖示**SAP**][sap-connector-doc] | [![API][sftp-ssh-icon]<br>圖示**SFTP-SSH**][sftp-ssh-doc] | [![API][smtp-icon]<br>圖示**SMTP**][smtp-doc] | [![API][sql-server-icon]<br>圖示**SQL<br>伺服器**][sql-server-doc] | [![API][x12-icon]<br>圖示**X12**][x12-doc] |
||||||

如需詳細資訊，請參閱下列主題：

* [從 Azure 邏輯應用訪問 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps 的定價模式](../logic-apps/logic-apps-pricing.md)
* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>觸發器和操作類型

連接器可以提供*觸發器*、*操作*或兩者。 *觸發器*是任何邏輯應用中的第一步，通常指定觸發觸發器並開始運行邏輯應用的事件。 例如，FTP 連接器具有一個觸發器，該觸發器"在添加或修改檔時"啟動邏輯應用。 某些觸發器定期檢查指定的事件或資料，然後在檢測到指定事件或資料時觸發。 其他觸發器等待，但在發生特定事件或有新資料可用時立即觸發。 觸發器還會將任何必需的資料傳遞給邏輯應用。 邏輯應用可以在整個工作流中讀取和使用該資料。 例如，Twitter 連接器具有一個觸發器"當發佈新推文時"，該觸發器將推文的內容傳遞到邏輯應用的工作流中。

觸發觸發器後，Azure 邏輯應用將創建邏輯應用的實例，並開始在邏輯應用的工作流中運行*操作*。 操作是跟隨觸發器並在邏輯應用的工作流中執行任務的步驟。 例如，您可以創建一個邏輯應用，從 SQL 資料庫獲取客戶資料，並在以後的操作中處理該資料。

以下是 Azure 邏輯應用提供的一般觸發器類型：

* *定期觸發器*：此觸發器按指定計劃運行，並且不與特定服務或系統緊密關聯。

* *輪詢觸發器*：此觸發器根據指定的計畫定期輪詢特定服務或系統，檢查新資料或是否發生了特定事件。 如果新資料可用或發生了特定事件，觸發器將創建並運行邏輯應用的新實例，該實例現在可以使用作為輸入傳遞的資料。

* *推送觸發器*：此觸發器等待並偵聽新資料或事件發生。 當新資料可用或事件發生時，觸發器將創建並運行邏輯應用的新實例，該實例現在可以使用作為輸入傳遞的資料。

<a name="connections"></a>

## <a name="connector-configuration"></a>連接器設定

每個連接器的觸發器和操作都提供它們自己的屬性，供您配置。 許多連接器還要求您首先創建到目標服務或系統的*連接*，並提供身份驗證憑據或其他配置詳細資訊，然後才能在邏輯應用中使用觸發器或操作。 例如，您必須授權連接到 Twitter 帳戶以訪問資料或代表您發佈。

對於使用 Azure 活動目錄 （Azure AD） OAuth 的連接器，創建連接意味著登錄到服務，如 Office 365、Salesforce 或 GitHub，其中訪問權杖[已加密](../security/fundamentals/encryption-overview.md)並安全地存儲在 Azure 金鑰存儲中。 其他連接器（如 FTP 和 SQL）需要具有配置詳細資訊的連接，如伺服器位址、使用者名和密碼。 這些連線的組態詳細資料也會加密並安全地儲存。 瞭解有關[Azure 中的加密](../security/fundamentals/encryption-overview.md)的更多詳細資訊。

只要該服務或系統允許，連接就可以訪問目標服務或系統。 對於使用 Azure AD OAuth 連接的服務（如 Office 365 和動態），Azure 邏輯應用將無限期地刷新訪問權杖。 其他服務可能限制 Azure 邏輯應用在不刷新的情況下使用權杖的時間。 通常，某些操作會使所有訪問權杖無效，例如更改密碼。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>自訂 API 和連接器

若要呼叫可執行自訂程式碼或不可作為連接器的 API，您可[建立自訂 API Apps](../logic-apps/logic-apps-create-api-app.md)，以擴充 Logic Apps 平台。 您也可以針對「任何」** 以 REST 或 SOAP 為基礎的 API [建立自訂連接器](../logic-apps/custom-connector-overview.md)，以便將這些 API 提供給 Azure 訂用帳戶中的任何邏輯應用程式使用。 若要對任何人公開自訂 API Apps 或連接器以使用於 Azure，您可以[提交連接器進行 Microsoft 認證](../logic-apps/custom-connector-submit-certification.md)。

> [!NOTE]
> 在[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中部署和運行的邏輯應用可以直接存取 Azure 虛擬網路中的資源。 如果您有需要本地資料閘道的自訂連接器，並且在 ISE 之外創建了這些連接器，則 ISE 中的邏輯應用也可以使用這些連接器。
>
> 在 ISE 中創建的自訂連接器與本地資料閘道不起作用。 但是，這些連接器可以直接存取連接到託管 ISE 的 Azure 虛擬網路的本地資料來源。 因此，在與這些資源通信時，ISE 中的邏輯應用很可能不需要資料閘道。
>
> 有關創建 ISE 的詳細資訊，請參閱[從 Azure 邏輯應用連接到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

## <a name="next-steps"></a>後續步驟

* 查看[完整的連接器清單](https://docs.microsoft.com/connectors)
* [建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [建立邏輯應用程式的自訂連接器](https://docs.microsoft.com/connectors/custom-connectors/)
* [建立邏輯應用程式的自訂 API](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "使用內部部署資料閘道從邏輯應用程式連線至內部部署資料來源"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "建立 Azure API 管理服務執行個體以便管理和發佈您的 API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "整合邏輯應用程式與 App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "將邏輯應用與 Azure 函數集成"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "以群組或批次方式處理訊息"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "根據條件是真還是假評估條件並運行不同的操作"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "對陣列中的每個項執行相同的操作"
[http-doc]: ./connectors-native-http.md "從邏輯應用調用 HTTP 或 HTTPS 終結點"
[http-request-doc]: ./connectors-native-reqres.md "在邏輯應用中接收 HTTP 要求"
[http-response-doc]: ./connectors-native-reqres.md "回應來自邏輯應用的 HTTP 要求"
[http-swagger-doc]: ./connectors-native-http-swagger.md "從邏輯應用調用 REST 終結點"
[http-webhook-doc]: ./connectors-native-webhook.md "等待來自 HTTP 或 HTTPS 終結點的特定事件"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "整合邏輯應用程式與巢狀工作流程"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "透過查詢動作選取和篩選陣列"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "基於計畫運行邏輯應用"
[schedule-delay-doc]: ./connectors-native-delay.md "延遲運行下一個操作"
[schedule-delay-until-doc]: ./connectors-native-delay.md "延遲運行下一個操作"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "定期計畫運行邏輯應用"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "運行需要連續處理資料的邏輯應用"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "將動作組織成群組，這些群組會在群組中的動作執行完成後取得自己的狀態"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "將操作組織到分配給唯一值的案例中。僅運行其值與運算式、物件或權杖的結果匹配的案例。如果不存在匹配項，請運行預設大小寫"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消邏輯應用程式正在執行中的工作流程"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重複操作，直到指定條件為 true 或某些狀態已更改"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "執行如篩選陣列或建立 CSV 和 HTML 資料表等的資料作業"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "執行變數相關作業，例如初始化、設定、遞增、遞減及附加至字串或陣列變數"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 儲存體連接器管理 Blob 容器中的檔案"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "連接到 Azure Cosmos DB，以便可以訪問文檔和預存程序"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "監視事件網格發佈的事件，例如，當 Azure 資源或協力廠商資源發生更改時"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "連接到 Azure 事件中心，以便在邏輯應用和事件中心之間接收和發送事件"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "連接到 Azure 存儲帳戶，以便創建、更新、獲取和刪除檔"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "連接到 Azure 存儲帳戶，以便創建和管理佇列和消息"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "從「服務匯流排佇列和主題」傳送訊息，並接收來自「服務匯流排佇列和訂用帳戶」的訊息"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "連接到 Azure SQL 資料倉儲，以便查看資料"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "連接到 Azure 存儲帳戶，以便創建、更新和查詢表等"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "連接到 BizTalk 伺服器，以便與 Azure 邏輯應用並行運行基於 BizTalk 的應用程式"
[box-doc]: ./connectors-create-api-box.md "連接到框。上傳、獲取、刪除、列出檔等"
[dropbox-doc]: ./connectors-create-api-dropbox.md "連接到放置框。上傳、獲取、刪除、列出檔等"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "連線連接到動態 CRM，以便可以使用 CRM 連線資料"
[facebook-doc]: ./connectors-create-api-facebook.md "連接到 Facebook。發佈到時間表、獲取頁面源等"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "連線至內部部署檔案系統"
[ftp-doc]: ./connectors-create-api-ftp.md "連線至 FTP / FTPS 伺服器以便執行 FTP 工作，像是上傳、取得、刪除檔案等等"
[github-doc]: ./connectors-create-api-github.md "連線至 GitHub 並追蹤問題"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "連接到 Google 日曆，可以管理日曆"
[google-drive-doc]: ./connectors-create-api-googledrive.md "連接到 GoogleDrive，以便您能夠處理您的資料"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "連接到 Google 表格，以便您可以修改工作表"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "連接到 Google 任務，以便您可以管理您的任務"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "連接到 IBM 主機上的 3270 個應用"
[ibm-db2-doc]: ./connectors-create-api-db2.md "在雲或本地連接到 IBM DB2。更新行、獲取表等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "在雲或本地連接到 Informix。閱讀行、列出表等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "連線到 IBM MQ 內部部署或 Azure 以傳送及接收訊息"
[instagram-doc]: ./connectors-create-api-instagram.md "連接到 Instagram。觸發事件或對事件採取行動"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "連接到您的郵件池帳戶。管理和自動化郵件"
[mandrill-doc]: ./connectors-create-api-mandrill.md "連線至 Mandrill 進行通訊"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "連接到本地 MySQL 資料庫，以便讀取和寫入資料"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "連接到 Office 365 帳戶，以便您可以發送和接收電子郵件、管理日曆和連絡人等"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "連接到您的個人 Microsoft OneDrive，以便您可以上傳、刪除、列出檔等"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "連接到您的業務 Microsoft OneDrive，以便您可以上傳、刪除、列出檔等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "連接到 Oracle 資料庫，以便添加、插入、刪除行等"
[outlook.com-doc]: ./connectors-create-api-outlook.md "連接到 Outlook 郵箱，以便管理電子郵件、日曆、連絡人等"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "連接到 PostgreSQL 資料庫，以便從表中讀取資料"
[project-online-doc]: ./connectors-create-api-projectonline.md "連線連接到 Microsoft 專案，以便管理專案、任務、資源等"
[rss-doc]: ./connectors-create-api-rss.md "發佈和檢索源項，將新專案發佈到 RSS 源時觸發操作"
[salesforce-doc]: ./connectors-create-api-salesforce.md "連接到您的 Salesforce 帳戶。管理客戶、潛在客戶、商機等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "連線至內部部署 SAP 系統"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "連接到發送網格。發送電子郵件和管理收件者清單"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "使用 SSH 連接到您的 SFTP 帳戶。上傳、獲取、刪除檔等"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "連接到 SharePoint 本機伺服器。管理文檔、清單項等"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "線上連接到共用點。管理文檔、清單項等"
[slack-doc]: ./connectors-create-api-slack.md "連接到鬆弛並將消息發佈到 Slack 通道"
[smtp-doc]: ./connectors-create-api-smtp.md "連線至 SMTP 伺服器，以及傳送帶有附件的電子郵件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "連線至 SparkPost 進行通訊"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "連接到 Azure SQL 資料庫或 SQL 伺服器。在 SQL 資料庫表中創建、更新、獲取和項目剪除"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "連接到 Teradata 資料庫以從表中讀取資料"
[trello-doc]: ./connectors-create-api-trello.md "連接到特雷洛。管理您的專案，並與任何人組織任何內容"
[twilio-doc]: ./connectors-create-api-twilio.md "連接到特維利奧。發送和獲取消息、獲取可用號碼、管理傳入電話號碼等"
[twitter-doc]: ./connectors-create-api-twitter.md "連接到推特。獲取時間表、發佈推文等"
[yammer-doc]: ./connectors-create-api-yammer.md "連接到 Yammer。發佈郵件、獲取新消息等"
[youtube-doc]: ./connectors-create-api-youtube.md "連接到 YouTube。管理視頻和頻道"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "編碼和解碼使用 AS2 協定的消息"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "編碼和解碼使用 EDIFACT 協定的消息"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "解碼使用 EDIFACT 協定的消息"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "對使用 EDIFACT 協定的消息進行編碼"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "瞭解企業集成一般檔案"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "瞭解企業集成一般檔案"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "管理集成帳戶專案中繼資料"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "使用液體範本轉換 JSON"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "編碼和解碼使用 X12 協定的消息"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "解碼使用 X12 協定的消息"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "對使用 X12 協定的消息進行編碼"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "轉換 XML 消息"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "驗證 XML 消息"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
