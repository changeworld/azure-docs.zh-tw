---
title: 適用於 Azure Logic Apps 的連接器
description: 使用 Azure Logic Apps 的連接器將工作流程自動化，例如內建、受控、內部部署、整合帳戶、ISE 和企業連接器
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 8bf91a3b7843d3212b62ced5b6a7c6fa54892ec9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359743"
---
# <a name="connectors-for-azure-logic-apps"></a>適用於 Azure Logic Apps 的連接器

連接器可讓您跨其他應用程式、服務、系統、通訊協定與平台快速從 Azure Logic Apps 存取事件、資料和動作。 在邏輯應用程式中使用連接器，即可擴充雲端和內部部署應用程式的功能，以便對您新建和已有的資料執行工作。

雖然 Logic Apps 提供 [數百個連接器](/connectors)，但是本文會說明數千個應用程式成功使用的 *熱門和較常用* 的連接器，以及用來處理資料和資訊的數百萬次執行。 若要尋找連接器的完整清單和每個連接器的參考資訊，例如觸發程式、動作和限制，請參閱 [連接器總覽](/connectors)下的連接器參考頁面。 此外，深入瞭解 [觸發程式和動作](#triggers-actions)、 [Logic Apps 計價模式](../logic-apps/logic-apps-pricing.md)，以及 [Logic Apps 定價詳細資料](https://azure.microsoft.com/pricing/details/logic-apps/)。

> [!TIP]
> 若要整合沒有連接器的服務或 API，您可以透過通訊協定（例如 HTTP）直接呼叫服務，或建立 [自訂連接器](#custom)。

## <a name="connector-types"></a>連接器類型

連接器可做為內建觸發程式和動作，或是受控連接器。

<a name="built-in"></a>

* [**內建**](#built-ins)：內建的觸發程式和動作都是「原生」的 Azure Logic Apps，可協助您為邏輯應用程式執行下列工作：

  * 在自訂和先進排程上執行。

  * 組織及控制邏輯應用程式的工作流程，例如迴圈和條件，也可以使用變數和資料作業。

  * 與其他端點通訊。

  * 接收和回應要求。

  * 使用 azure API 管理來呼叫 Azure 函式、Azure API Apps (Web Apps) 、您自己的 Api 管理和發佈，以及可接收要求的嵌套邏輯應用程式。

<a name="managed-connectors"></a>

* [**受控連接器**](#managed-api-connectors)：由 Microsoft 部署及管理，這些連接器提供用於存取雲端服務、內部部署系統或兩者（包括 Office 365、Azure Blob 儲存體、SQL Server、Dynamics、Salesforce、SharePoint 等等）的觸發程式和動作。 某些連接器專門支援 B2B) 通訊案例的企業對企業 (，而且需要連結至邏輯應用程式的 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 。 使用特定連接器之前，您可能必須先建立由 Azure Logic Apps 管理的連接。

  例如，如果您使用 Microsoft BizTalk Server，您的邏輯應用程式可以使用 [Biztalk server on-premises connector](#on-premises-connectors)連接至 biztalk server 並與之通訊。 您可以使用[整合帳戶連接器](#integration-account-connectors)，接著在邏輯應用程式中擴充或執行類似 BizTalk 的作業。

  連接器會分類為 Standard 或 Enterprise。 [企業連接器](#enterprise-connectors) 提供企業系統（例如 SAP、ibm MQ 和 IBM 3270）的存取權，以產生額外的費用。 若要判斷連接器是否為標準或企業，請參閱 [連接器總覽](/connectors)下每個連接器參考頁面中的技術詳細資料。

  您也可以使用這些類別來識別連接器，雖然有些連接器可以存在於多個類別中。 例如，SAP 是企業連接器和內部部署連接器：

  | 類別 | 描述 |
  |----------|-------------|
  | [**受控連接器**](#managed-api-connectors) | 建立可使用 Azure Blob 儲存體、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online 等其他服務的邏輯應用程式。 |
  | [**內部部署連接器**](#on-premises-connectors) | 在您安裝並設定[內部部署資料閘道][gateway-doc]之後，這些連接器可協助您的邏輯應用程式存取內部部署系統，例如 SQL Server、SharePoint Server、Oracle DB、檔案共用及其他系統。 |
  | [**整合帳戶連接器**](#integration-account-connectors) | 當您建立並支付整合帳戶時，這些連接器會轉換和驗證 XML、進行一般檔案的編碼和解碼，以及透過 AS2、EDIFACT 和 X12 通訊協定處理企業對企業 (B2B) 訊息。 |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>從整合服務環境 (ISE) 連接

對於需要直接存取 Azure 虛擬網路中資源的邏輯應用程式，您可以建立專用的 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ，您可以在其中建立、部署和執行您在專用資源上的邏輯應用程式。 在邏輯應用程式設計工具中，當您在 ISE 中流覽要用於邏輯應用程式的連接器時，[ **核心** ] 標籤會出現在內建觸發程式和動作中，而 **ISE** 標籤會出現在某些連接器上。

> [!NOTE]
> 在 ISE 與其連接器中執行的邏輯應用程式（無論這些連接器的執行位置為何）都會遵循固定的定價方案與以耗用量為基礎的定價方案。 如需詳細資訊，請參閱 [Logic Apps 計價模式](../logic-apps/logic-apps-pricing.md) 和 [Logic Apps 定價詳細資料](https://azure.microsoft.com/pricing/details/logic-apps/)。

| 標籤 | 範例 | 描述 |
|-------|---------|-------------|
| **核心** | ![核心連接器範例](./media/apis-list/example-core-connector.png) | 使用此標籤的內建觸發程式和動作會在與邏輯應用程式相同的 ISE 中執行。 |
| **ISE** | ![ISE 連接器範例](./media/apis-list/example-ise-connector.png) | 具有此標籤的受控連接器會在與邏輯應用程式相同的 ISE 中執行。 如果您有連線到 Azure 虛擬網路的內部部署系統，ISE 可讓您的邏輯應用程式直接存取該系統，而不需要內部 [部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)。 相反地，您可以使用該系統的 **ISE** 連接器（如果有的話）、HTTP 動作或 [自訂連接器](#custom)。 針對沒有 **ISE** 連接器的內部部署系統，請使用內部部署資料閘道。 若要查看可用的 ISE 連接器，請參閱 [ise 連接器](#ise-connectors)。 |
| 沒有標籤 | ![多租使用者連接器範例](./media/apis-list/example-multi-tenant-connector.png) | 所有其他不具 **核心** 或 **ISE** 標籤的連接器，您可以繼續使用這些連接器，在全域、多租使用者 Logic Apps 服務中執行。 |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>內建

Logic Apps 提供內建的觸發程式和動作，可讓您建立以排程為基礎的工作流程、協助邏輯應用程式與其他應用程式和服務通訊、透過邏輯應用程式控制工作流程，以及管理或運算元據。

| Name | 描述 |
|------|-------------|
| [![排程內建連接器 ][schedule-icon]<br> **排程**][schedule-doc] | -在指定的週期上執行邏輯應用程式，範圍從基本到高階排程，以及 [**週期** 觸發][schedule-recurrence-doc]程式。 <br>-執行邏輯應用程式，該邏輯應用程式需要以 [**滑動視窗** 觸發][schedule-sliding-window-doc]程式來處理連續區塊中的資料。 <br>-使用 [**延遲** 動作][schedule-delay-doc]將邏輯應用程式暫停一段指定的持續時間。 <br>-暫停您的邏輯應用程式，直到指定的日期和時間加上 [**延遲到動作為止**][schedule-delay-until-doc]。 |
| [![Batch 內建連接器 ][batch-icon]<br> **批次**][batch-doc] | - 透過 [批次訊息] 觸發程序，分批處理訊息。 <br>- 透過 [將訊息傳送至批次] 動作，呼叫具有現有批次觸發程序的邏輯應用程式。 |
| [![HTTP 內建連接器 ][http-icon]<br> **HTTP**][http-doc] | 使用 HTTP 的觸發程序和動作呼叫 HTTP 或 HTTPS 端點。 其他 HTTP 內建觸發程式和動作包括 [HTTP + Swagger 內建連接器][http-swagger-doc] 和 [HTTP + Webhook][http-webhook-doc]。 |
| [![要求內建連接器 ][http-request-icon]<br> **要求**][http-request-doc] | - 透過 [要求] 觸發程序，讓邏輯應用程式得以從其他應用程式或服務呼叫、在 Event Grid 資源事件上觸發，或在 Azure 資訊安全中心警示的回應上觸發。 <br>- 透過 [回應] 動作，將回應傳送到應用程式或服務。 |
| [![Azure API 管理內建連接器 ][azure-api-management-icon]<br> **azure api <br> 管理**][azure-api-management-doc] | 呼叫透過 Azure API 管理控管和發佈的自有 API 所定義的觸發程序和動作。 |
| [![Azure App Services 內建連接器 ][azure-app-services-icon]<br> **Azure App <br> 服務**][azure-app-services-doc] | 呼叫 API Apps 或 Web Apps (裝載於 Azure App Service)。 如果包含 Swagger，這些應用程式所定義的動作與觸發程序看起來像是任何其他第一級觸發程序和動作。 |
| [![Azure Logic Apps 內建連接器 ][azure-logic-apps-icon]<br> **Azure 邏輯 <br> 應用程式**][nested-logic-app-doc] | 呼叫以 **要求** 觸發程式開頭的其他邏輯應用程式。 |
|||

### <a name="run-code-from-logic-apps"></a>從邏輯應用程式執行程式碼

Logic Apps 提供在邏輯應用程式的工作流程中執行您自己的程式碼的內建動作：

| Name | 描述 |
|------|-------------|
| [![Azure Functions 內建連接器 ][azure-functions-icon]<br> **Azure Functions**][azure-functions-doc] | 呼叫 Azure 函式，以從邏輯應用程式執行自訂程式碼片段 (C# 或 Node.js)。 |
| [![內嵌程式碼內建連接器 ][inline-code-icon]<br> **內嵌程式碼**][inline-code-doc] | 從邏輯應用程式新增並執行 JavaScript 程式碼片段。 |
|||

### <a name="control-workflow"></a>控制工作流程

Logic Apps 提供在邏輯應用程式工作流程中結構化及控制動作的內建動作：

| Name | 描述 |
|------|-------------|
| [![條件內建動作 ][condition-icon]<br> **條件**][condition-doc] | 根據條件為 true 或 false，評估條件並執行不同的動作。 |
| [![針對 ][for-each-icon]<br> **每個** 的內建動作][for-each-doc] | 對陣列中的每個項目執行相同的動作。 |
| [![範圍內建動作 ][scope-icon]<br> **範圍**][scope-doc] | 將動作分成數個「範圍」，這些範圍會在範圍中的動作執行完成後取得自己的狀態。 |
| [![切換內建動作 ][switch-icon]<br> **參數**][switch-doc] | 將動作分成數個「案例」，這些案例會獲派唯一值 (預設案例除外)。 僅執行其指派值符合運算式、物件或語彙基元結果的案例。 如果沒有相符項目存在，請執行預設案例。 |
| [![終止內建動作 ][terminate-icon]<br> **終止**][terminate-doc] | 停止正在執行的邏輯應用程式工作流程。 |
| [![][until-icon]<br> **Until** 直到內建動作為止][until-doc] | 重複動作，直到符合指定的條件或某些狀態已改變為止。 |
|||

### <a name="manage-or-manipulate-data"></a>管理或操作資料

Logic Apps 提供使用資料輸出及其格式的內建動作：

| Name | 描述 |
|------|-------------|
| [![資料作業內建動作 ][data-operations-icon]<br> **資料作業**][data-operations-doc] | 執行資料相關作業： <p>- **撰寫** ：從多個輸入與各種類型建立單一輸出。 <br>- **建立 CSV 資料表** ：從 JSON 物件陣列建立以逗號分隔 (CSV) 的資料表。 <br>- **建立 HTML 資料表** ：從 JSON 物件陣列建立 HTML 資料表。 <br>- **篩選陣列** ：從符合您準則的另一個陣列建立陣列。 <br>- **加入** ：從陣列中的所有項目建立字串，並使用指定的分隔符號來分隔這些項目。 <br>- **剖析 json** ：從屬性及其在 JSON 內容中的值建立方便使用的標記，以便您可以在工作流程中使用這些屬性。 <br>- **選取** ：轉換另一個陣列中的項目或值，並將這些項目對應至指定的屬性，以建立 JSON 物件陣列。 |
| ![日期時間內建動作][date-time-icon]<br>**日期時間** | 執行時間戳記相關作業： <p>- **新增至時間** ：將指定的單位數新增至時間戳記。 <br>- **轉換時區** ：將時間戳記從來源時區轉換為目標時區。 <br>- **目前時間** ：傳回目前的時間戳記作為字串。 <br>- **取得未來時間** ：傳回目前時間戳記加上指定時間單位的結果。 <br>- **取得過去時間** ：傳回目前時間戳記減去指定時間單位的結果。 <br>- **從時間減去** ：從時間戳記減去時間單位數字。 |
| [![變數內建的動作 ][variables-icon]<br> **變數**][variables-doc] | 執行變數相關作業： <p>- **附加到陣列變數** ：插入一個值，作為變數所儲存陣列中的最後一個項目。 <br>- **附加到字串變數** ：插入一個值，作為變數所儲存字串中的最後一個字元。 <br>- **遞減變數** ：依常數值遞減變數。 <br>- **遞增變數** ：依常數值遞增變數。 <br>- **初始化變數** ：建立變數，並宣告其資料類型和初始值。 <br>- **設定變數** ：將不同的值指派給現有的變數。 |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>受控連接器

Logic Apps 提供這些常用的標準連接器，可讓您使用這些服務或系統將工作、進程和工作流程自動化：

| Name | 描述 |
|------|-------------|
| [![Azure 服務匯流排受控連接器 ][azure-service-bus-icon]<br> **Azure 服務匯流排**][azure-service-bus-doc] | 利用 Logic Apps 中最常用的連接器，管理非同步訊息、工作階段和主題訂用帳戶。 |
| [![SQL Server 受控連接器 ][sql-server-icon]<br> **SQL Server**][sql-server-doc] | 連接到您的 SQL Server 內部部署或雲端中的 Azure SQL Database，讓您可以記錄管理、執行預存程式或執行查詢。 |
| [![Azure Blob 儲存體受控連接器 ][azure-blob-storage-icon]<br> **Azure Blob <br> 儲存體**][azure-blob-storage-doc] | 連接到您的儲存體帳戶，以便建立和管理 blob 內容。 |
| [![Office 365 Outlook managed connector ][office-365-outlook-icon]<br> **office 365 <br> outlook**][office-365-outlook-doc] | 連接到您的公司或學校電子郵件帳戶，讓您可以建立和管理電子郵件、工作、行事曆事件和會議、連絡人、要求等等。 |
| [![SFTP-SSH 管理的連接器 ][sftp-ssh-icon]<br> **SFTP-ssh**][sftp-ssh-doc] | 使用 SSH 連線到可從網際網路存取的 SFTP 伺服器，以便使用您的檔案和資料夾。 |
| [![SharePoint Online 管理的連接器 ][sharepoint-online-icon]<br> **sharepoint <br> online**][sharepoint-online-doc] | 連線到 SharePoint Online，以便管理檔案、附件、資料夾等等。 |
| [![Azure 佇列受控連接器 ][azure-queues-icon]<br> **azure <br> 佇列**][azure-queues-doc] | 連接到您的 Azure 儲存體帳戶，讓您可以建立和管理佇列和訊息。 |
| [![FTP 受控連接器 ][ftp-icon]<br> **ftp**][ftp-doc] | 連接到您可以從網際網路存取的 FTP 伺服器，以便使用您的檔案和資料夾。 |
| [![檔案系統管理的連接器 ][file-system-icon]<br> **檔 <br> 系統**][file-system-doc] | 連接到您的內部部署檔案共用，讓您可以建立和管理檔案。 |
| [![Azure 事件中樞受控連接器 ][azure-event-hubs-icon]<br> **Azure 事件中樞**][azure-event-hubs-doc] | 透過事件中樞取用和發佈事件。 例如，透過事件中樞從邏輯應用程式取得輸出，然後將輸出傳送給即時分析提供者。 |
| [![Azure event grid 受控連接器 ][azure-event-grid-icon]<br> **azure 事件** <br> **方格**][azure-event-grid-doc] | 當 Azure 資源或第三方資源變更時，監視 Event Grid 所發佈的事件。 |
| [![Salesforce managed connector ][salesforce-icon]<br> **Salesforce**][salesforce-doc] | 連接到您的 Salesforce 帳戶，讓您可以建立和記錄管理、作業、物件等專案。 |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>內部部署連接器

您必須先[下載、安裝及設定內部部署資料閘道][gateway-doc]，才可建立內部部署系統連線。 此閘道會提供安全的通訊通道，而不需設定必要的網路基礎結構。 

以下是 *一些* 常用的標準連接器，Logic Apps 可用於存取內部部署系統中的資料和資源。 如需內部部署連接器清單，請參閱 [支援的資料來源](../logic-apps/logic-apps-gateway-connection.md#supported-connections)。

:::row:::
    :::column:::
        [![Biztalk server connector ][biztalk-server-icon]<br> **biztalk** <br> **server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![檔案系統連接器 ][file-system-icon]<br> **檔 <br> 系統**][file-system-doc]
    :::column-end:::
    :::column:::
        [![DB2 連接器 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Informix connector ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL 連接器 ][mysql-icon]<br> **mysql**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB 連接器 ][oracle-db-icon]<br> **Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![于 postgresql 連接器 ][postgre-sql-icon]<br> **于 postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server connector ][sharepoint-server-icon]<br> **sharepoint <br> server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server 連接器 ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata 連接器 ][teradata-icon]<br> **Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>整合帳戶連接器

Logic Apps 提供標準連接器，可讓您在建立及支付 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)時，使用邏輯應用程式建立企業對企業 (B2B) 解決方案，該帳戶可透過 Azure 中的企業整合套件 (EIP) 取得。 使用此帳戶，您可以建立並儲存一些 B2B 構件，例如交易夥伴、合約、地圖、結構描述、憑證等等。 若要使用這些構件，請建立您的邏輯應用程式與整合帳戶的關聯。 如果您目前使用 BizTalk Server，可能已經熟悉這些連接器。

:::row:::
    :::column:::
        [![AS2 解碼動作 ][as2-icon]<br> **as2 <br> 解碼**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 編碼動作 ][as2-icon]<br> **as2 <br> 編碼**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 解碼動作 ][edifact-icon]<br> **edifact <br> 解碼**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 編碼動作 ][edifact-icon]<br> **edifact <br> 編碼**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![一般檔案解碼動作一般檔案 ][flat-file-decode-icon]<br> **<br> 解碼**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![一般檔案編碼動作一般檔案 ][flat-file-encode-icon]<br> **<br> 編碼**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![整合帳戶動作 ][integration-account-icon]<br> **整合 <br> 帳戶**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![液體轉換動作 ][liquid-icon]<br> **液體** <br> **transforms** 轉][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12 解碼動作 ][x12-icon]<br> **x12 <br> 解碼**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 編碼動作 ][x12-icon]<br> **x12 <br> 編碼**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Xml 轉換動作 ][xml-transform-icon]<br> **xml** <br> **轉換**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![XML 驗證動作 ][xml-validate-icon]<br> **xml <br> 驗證**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>企業連接器

Logic Apps 提供這些企業連接器來存取企業系統，例如 SAP 和 IBM MQ：

:::row:::
    :::column:::
        [![IBM 3270 連接器 ][ibm-3270-icon]<br> **ibm 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![MQ connector ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP 連接器 ][sap-icon]<br> **sap**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 連接器

針對您在專用整合服務環境中建立和執行的邏輯應用程式 [ (ISE)](#integration-service-environment)，邏輯應用程式設計工具會使用 **核心** 標籤來識別在您 ISE 中執行的內建觸發程式和動作。 在 ISE 中執行的受控連接器會顯示 **ISE** 標籤，而在全域、多租使用者 Logic Apps 服務中執行的連接器則不會顯示任一標籤。 這份清單會顯示目前有 ISE 版本的連接器：

:::row:::
    :::column:::
        [![AS2 ISE 連接器 ][as2-icon]<br> **as2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure 自動化 ISE 連接器 ][azure-automation-icon]<br> **Azure <br> 自動化**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob 儲存體 ISE 連接器 ][azure-blob-storage-icon]<br> **Azure Blob <br> 儲存體**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE 連接器 ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> DB**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure 事件中樞 ISE 連接器 ][azure-event-hubs-icon]<br> **Azure 事件 <br> 中樞**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE 連接器 ][azure-event-grid-icon]<br> **azure 事件 <br> 方格**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Azure 檔案儲存體 ISE 連接器 ][azure-file-storage-icon]<br> **azure 檔案 <br> 儲存體**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE 連接器 ][azure-key-vault-icon]<br> **Azure 金鑰保存 <br> 庫**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure 監視器記錄 ISE 連接器 ][azure-monitor-logs-icon]<br> **Azure 監視器 <br> 記錄**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure 服務匯流排 ISE 連接器 ][azure-service-bus-icon]<br> **Azure 服務 <br> 匯流排**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE 連接器 ][azure-sql-data-warehouse-icon]<br> **Azure SQL 資料 <br> 倉儲**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Azure 資料表儲存體 ISE 連接器 ][azure-table-storage-icon]<br> **azure 資料表 <br> 儲存體**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure 佇列 ISE 連接器 ][azure-queues-icon]<br> **azure <br> 佇列**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE 連接器 ][edifact-icon]<br> **edifact**][edifact-doc]
    :::column-end:::
    :::column:::
        [![檔案系統 ISE 連接器 ][file-system-icon]<br> **檔 <br> 系統**][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE 連接器 ][ftp-icon]<br> **ftp**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![IBM 3270 ISE 連接器 ][ibm-3270-icon]<br> **ibm 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![DB2 ISE 連接器 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![MQ ISE connector ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE 連接器 ][sap-icon]<br> **sap**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE 連接器 ][sftp-ssh-icon]<br> **SFTP-ssh**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE 連接器 ][smtp-icon]<br> **smtp**][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE 連接器 ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE 連接器 ][x12-icon]<br> **x12**][x12-doc]
    :::column-end:::
:::row-end:::

如需詳細資訊，請參閱下列主題：

* [從 Azure Logic Apps 存取 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps 的定價模式](../logic-apps/logic-apps-pricing.md)
* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>觸發程式和動作類型

連接器可以提供 *觸發* 程式、 *動作* 或兩者。 *觸發* 程式是任何邏輯應用程式中的第一個步驟，通常會指定引發觸發程式的事件，並開始執行您的邏輯應用程式。 例如，FTP 連接器的觸發程式會啟動邏輯應用程式「當新增或修改檔案時」。 有些觸發程式會定期檢查指定的事件或資料，然後在偵測到指定的事件或資料時引發。 其他觸發程式會等候，但會在特定事件發生或有新資料可用時立即引發。 觸發程式也會將任何必要的資料傳遞至您的邏輯應用程式。 您的邏輯應用程式可以在整個工作流程中讀取和使用這些資料。 例如，Office 365 Outlook 連接器有一個觸發程式：「新的電子郵件送達時」，可將該電子郵件中的內容傳遞至邏輯應用程式的工作流程。

觸發程式引發之後，Azure Logic Apps 會建立邏輯應用程式的實例，並開始在邏輯應用程式的工作流程中執行 *動作* 。 動作是遵循觸發程式的步驟，並在邏輯應用程式的工作流程中執行工作。 例如，您可以建立邏輯應用程式，以從 SQL 資料庫取得客戶資料，並在後續的動作中處理該資料。

以下是 Azure Logic Apps 提供的一般觸發程式類型：

* *週期觸發* 程式：此觸發程式會依照指定的排程執行，且不會與特定的服務或系統緊密相關聯。

* *輪詢觸發* 程式：此觸發程式會根據指定的排程定期輪詢特定的服務或系統，檢查是否有新資料，或是否發生特定事件。 如果有新資料可用或發生特定事件，則觸發程式會建立並執行您的邏輯應用程式的新實例，現在可以使用傳遞做為輸入的資料。

* *推送觸發* 程式：此觸發程式會等候並接聽新的資料，或發生事件。 當有新資料可用或發生事件時，觸發程式會建立並執行邏輯應用程式的新實例，現在可以使用傳遞做為輸入的資料。

<a name="connections"></a>

## <a name="connector-configuration"></a>連接器設定

每個連接器的觸發程式和動作都提供自己的屬性供您設定。 許多連接器也會要求您先建立與目標服務或系統的 *連線，並* 提供驗證認證或其他設定詳細資料，您才能在邏輯應用程式中使用觸發程式或動作。 例如，在您可以存取及使用 Office 365 Outlook 電子郵件帳戶之前，您必須先授權該帳戶的連接。

針對使用 Azure Active Directory (Azure AD) OAuth 的連接器，建立連線表示您的存取權杖會 [加密](../security/fundamentals/encryption-overview.md) 並安全地儲存在 Azure 秘密存放區中的服務，例如 Office 365、Salesforce 或 GitHub。 其他連接器（例如 FTP 和 SQL）需要具有設定詳細資料的連接，例如伺服器位址、使用者名稱和密碼。 這些連線的組態詳細資料也會加密並安全地儲存。 深入瞭解 [Azure 中的加密](../security/fundamentals/encryption-overview.md)。

只要服務或系統允許，連接就可以存取目標服務或系統。 針對使用 Azure AD OAuth 連接的服務（例如 Office 365 和 Dynamics），Azure Logic Apps 無限期地重新整理存取權杖。 其他服務可能會限制 Azure Logic Apps 可使用權杖的時間長度，而不需要重新整理。 一般而言，某些動作會使所有存取權杖失效，例如變更您的密碼。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>自訂 API 和連接器

若要呼叫可執行自訂程式碼或不可作為連接器的 API，您可[建立自訂 API Apps](../logic-apps/logic-apps-create-api-app.md)，以擴充 Logic Apps 平台。 您也可以針對「任何」以 REST 或 SOAP 為基礎的 API [建立自訂連接器](../logic-apps/custom-connector-overview.md)，以便將這些 API 提供給 Azure 訂用帳戶中的任何邏輯應用程式使用。 若要對任何人公開自訂 API Apps 或連接器以使用於 Azure，您可以[提交連接器進行 Microsoft 認證](/connectors/custom-connectors/submit-certification)。

> [!NOTE]
> 您在 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中部署和執行的邏輯應用程式，可以直接存取 Azure 虛擬網路中的資源。 如果您的自訂連接器需要內部部署資料閘道，而且您在 ISE 以外建立了這些連接器，ISE 中的邏輯應用程式也可以使用這些連接器。
>
> 在 ISE 內建立的自訂連接器無法與內部部署資料閘道搭配使用。 不過，這些連接器可以直接存取連線到裝載 ISE 之 Azure 虛擬網路的內部部署資料來源。 因此，ISE 中的邏輯應用程式在與這些資源進行通訊時，最可能不需要資料閘道。
>
> 如需建立 Ise 的詳細資訊，請參閱 [從 Azure Logic Apps 連接到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>封鎖建立連接

如果您的組織不允許使用 Azure Logic Apps 中的連接器來連線到特定資源，您可以在邏輯應用程式工作流程中，使用[Azure 原則](../governance/policy/overview.md)來[封鎖為特定連接器建立這些連接的功能](../logic-apps/block-connections-connectors.md)。 如需詳細資訊，請參閱 [在 Azure Logic Apps 中封鎖特定連接器所建立的連接](../logic-apps/block-connections-connectors.md)。

## <a name="get-ready-for-deployment"></a>準備開始部署

雖然您會從邏輯應用程式內建立連線，但連線是個別的 Azure 資源與其本身的資源定義。 若要檢查這些連線資源定義，請將 [您的邏輯應用程式從 Azure 下載至 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)，這是最簡單的方法，可讓您輕鬆地建立可供部署的有效參數化邏輯應用程式範本。

## <a name="next-steps"></a>後續步驟

* 查看 [完整的連接器清單](/connectors)
* [建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [建立邏輯應用程式的自訂連接器](/connectors/custom-connectors/)
* [建立適用於邏輯應用程式的自訂 API](../logic-apps/logic-apps-create-api-app.md)

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
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
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
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
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
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
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
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
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

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "使用內部部署資料閘道從邏輯應用程式連線至內部部署資料來源"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "建立 Azure API 管理服務執行個體以便管理和發佈您的 API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "整合邏輯應用程式與 App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "整合邏輯應用程式與 Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "以群組或批次方式處理訊息"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "根據條件是否為 true 或 false，評估條件並執行不同的動作"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "針對陣列中的每個專案執行相同的動作"
[http-doc]: ./connectors-native-http.md "從邏輯應用程式呼叫 HTTP 或 HTTPS 端點"
[http-request-doc]: ./connectors-native-reqres.md "在您的邏輯應用程式中接收 HTTP 要求"
[http-response-doc]: ./connectors-native-reqres.md "回應來自您邏輯應用程式的 HTTP 要求"
[http-swagger-doc]: ./connectors-native-http-swagger.md "從邏輯應用程式呼叫 REST 端點"
[http-webhook-doc]: ./connectors-native-webhook.md "等候來自 HTTP 或 HTTPS 端點的特定事件"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "從邏輯應用程式新增並執行 JavaScript 程式碼片段"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "整合邏輯應用程式與巢狀工作流程"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "透過查詢動作選取和篩選陣列"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "根據排程執行邏輯應用程式"
[schedule-delay-doc]: ./connectors-native-delay.md "順延強制下一個動作"
[schedule-delay-until-doc]: ./connectors-native-delay.md "順延強制下一個動作"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "依週期性排程執行邏輯應用程式"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "執行需要以連續區塊處理資料的邏輯應用程式"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "將動作組織成群組，這些群組會在群組中的動作執行完成後取得自己的狀態"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "將動作組織成案例，並將其指派為唯一值。只執行其值符合運算式、物件或標記結果的案例。如果沒有相符專案存在，請執行預設案例"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消邏輯應用程式正在執行中的工作流程"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重複動作，直到指定的條件為 true 或某些狀態已變更"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "執行如篩選陣列或建立 CSV 和 HTML 資料表等的資料作業"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "執行變數相關作業，例如初始化、設定、遞增、遞減及附加至字串或陣列變數"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "建立及管理雲端和內部部署基礎結構的自動化作業"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 儲存體連接器管理 Blob 容器中的檔案"
[azure-cosmos-db-doc]: /connectors/documentdb/ "連接到 Azure Cosmos DB，讓您可以存取檔和預存程式"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "監視事件方格所發佈的事件，例如，當 Azure 資源或協力廠商資源變更時"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "連線到 Azure 事件中樞，讓您可以在邏輯應用程式與事件中樞之間接收和傳送事件"
[azure-file-storage-doc]: /connectors/azurefile/ "連接到您的 Azure 儲存體帳戶，讓您可以建立、更新、取得和刪除檔案"
[azure-key-vault-doc]: /connectors/keyvault/ "連接到您的 Azure Key Vault，讓您可以管理您的秘密和金鑰"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "對 Log Analytics 工作區和 Application Insights 元件的 Azure 監視器記錄執行查詢"
[azure-queues-doc]: /connectors/azurequeues/ "連接到您的 Azure 儲存體帳戶，讓您可以建立和管理佇列和訊息"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "從「服務匯流排佇列和主題」傳送訊息，並接收來自「服務匯流排佇列和訂用帳戶」的訊息"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "連接到 Azure Synapse Analytics，以便您可以查看您的資料"
[azure-table-storage-doc]: /connectors/azuretables/ "連接到您的 Azure 儲存體帳戶，讓您可以建立、更新和查詢資料表等等"
[biztalk-server-doc]: /connectors/biztalk/ "連接到您的 BizTalk Server，讓您可以並存執行 BizTalk 應用程式與 Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "連線至內部部署檔案系統"
[ftp-doc]: ./connectors-create-api-ftp.md "連線至 FTP / FTPS 伺服器以便執行 FTP 工作，像是上傳、取得、刪除檔案等等"
[github-doc]: ./connectors-create-api-github.md "連線至 GitHub 並追蹤問題"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "連接至 Google 行事曆並可以管理行事曆"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "連接至 Google 試算表，讓您可以修改您的工作表"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "連接至 Google 工作，讓您可以管理您的工作"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "連接到 IBM 大型主機上的3270應用程式"
[ibm-db2-doc]: ./connectors-create-api-db2.md "連接到雲端或內部部署中的 IBM DB2。更新資料列、取得資料表等等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "連接到雲端或內部部署中的 Informix。讀取資料列、列出資料表等等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "連線到 IBM MQ 內部部署或 Azure 以傳送及接收訊息"
[instagram-doc]: ./connectors-create-api-instagram.md "連接到 Instagram。觸發或對事件採取動作"
[mandrill-doc]: ./connectors-create-api-mandrill.md "連線至 Mandrill 進行通訊"
[mysql-doc]: /connectors/mysql/ "連接到您的內部部署 MySQL 資料庫，讓您可以讀取和寫入資料"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "連接到您的公司或學校帳戶，讓您可以傳送和接收電子郵件、管理行事曆和連絡人等等"
[onedrive-doc]: ./connectors-create-api-onedrive.md "連接到您的個人 Microsoft OneDrive，讓您可以上傳、刪除、列出檔案等等"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "連接到您的商務 Microsoft OneDrive，讓您可以上傳、刪除、列出您的檔案等等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "連接到 Oracle 資料庫，讓您可以新增、插入、刪除資料列等"
[outlook.com-doc]: ./connectors-create-api-outlook.md "連接到您的 Outlook 信箱，讓您可以管理您的電子郵件、行事曆、連絡人等"
[postgre-sql-doc]: /connectors/postgresql/ "連接到您的于 postgresql 資料庫，讓您可以從資料表讀取資料"
[salesforce-doc]: ./connectors-create-api-salesforce.md "連接到您的 Salesforce 帳戶。管理帳戶、潛在客戶、商機等等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "連線至內部部署 SAP 系統"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "連接到 SendGrid。傳送電子郵件及管理收件者清單"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "使用 SSH 連接到您的 SFTP 帳戶。上傳、取得、刪除檔案等等"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "連接到 SharePoint 內部部署伺服器。管理檔、清單專案等等"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "連接至 SharePoint Online。管理檔、清單專案等等"
[slack-doc]: ./connectors-create-api-slack.md "連線至時差並將訊息張貼至可寬比的頻道"
[smtp-doc]: ./connectors-create-api-smtp.md "連線至 SMTP 伺服器，以及傳送帶有附件的電子郵件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "連線至 SparkPost 進行通訊"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "連接到 Azure SQL Database 或 SQL Server。建立、更新、取得和刪除 SQL 資料庫資料表中的專案"
[teradata-doc]: /connectors/teradata/ "連線至您的 Teradata 資料庫以從資料表讀取資料"
[twilio-doc]: ./connectors-create-api-twilio.md "連接到 Twilio。傳送和取得訊息、取得可用的號碼、管理撥入的電話號碼等等"
[youtube-doc]: ./connectors-create-api-youtube.md "連接至 YouTube。管理您的影片和頻道"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "編碼和解碼使用 AS2 通訊協定的訊息"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "編碼和解碼使用 EDIFACT 通訊協定的訊息"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "解碼使用 EDIFACT 通訊協定的訊息"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "編碼使用 EDIFACT 通訊協定的訊息"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "深入瞭解企業整合一般檔案"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "深入瞭解企業整合一般檔案"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "管理整合帳戶構件的中繼資料"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "使用液體範本轉換 JSON"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "編碼和解碼使用 X12 通訊協定的訊息"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "解碼使用 X12 通訊協定的訊息"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "編碼使用 X12 通訊協定的訊息"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "轉換 XML 訊息"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "驗證 XML 訊息"
