---
title: 連接到 SAP 系統
description: 通過使用 Azure 邏輯應用自動化工作流來訪問和管理 SAP 資源
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651010"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>從 Azure Logic Apps 連線至 SAP 系統

> [!IMPORTANT]
> 早期的 SAP 應用程式伺服器和 SAP 消息伺服器連接器于 2020 年 2 月 29 日棄用。 當前的 SAP 連接器整合了這些以前的 SAP 連接器，因此您不必更改連線類型、與以前的連接器完全相容、提供許多其他功能，並繼續使用 SAP .Net 連接器庫（SAP NCo）。
>
> 對於使用舊連接器的邏輯應用，請在棄用日期之前[遷移到最新的連接器](#migrate)。 否則，這些邏輯應用將遇到執行失敗，並且無法向 SAP 系統發送消息。

本文演示如何使用 SAP 連接器從邏輯應用內部訪問本地 SAP 資源。 該連接器適用于 SAP 的經典版本，如本地的 R/3 和 ECC 系統。 該連接器還支援與 SAP 較新的基於 HANA 的 SAP 系統（如 S/4 HANA）集成，無論這些系統是本地託管還是雲中託管。 SAP 連接器支援通過中間文檔 （IDoc）、業務應用程式開發介面 （BAPI） 或遠端函式呼叫 （RFC） 與 SAP NetWeaver 系統進行消息或資料整合。

SAP 連接器使用[SAP .NET 連接器 （NCo） 庫](https://support.sap.com/en/product/connectors/msnet.html)並提供以下操作：

* **向 SAP 發送消息**：通過 tRFC 發送 IDoc，通過 RFC 調用 BAPI 功能，或在 SAP 系統中調用 RFC/tRFC。
* **當從 SAP 收到消息**時：通過 tRFC 接收 IDoc，通過 tRFC 調用 BAPI 功能，或在 SAP 系統中調用 RFC/tRFC。
* **生成架構**：為 IDoc、BAPI 或 RFC 的 SAP 工件生成架構。

對於這些操作，SAP 連接器支援通過使用者名和密碼進行基本驗證。 連接器還支援[安全網路通信 （SNC）。](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) SNC 可用於 SAP NetWeaver 單一登入 （SSO） 或外部安全產品提供的其他安全功能。

SAP 連接器會透過[內部部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)與內部部署 SAP 系統整合。 在發送方案中，例如，當消息從邏輯應用發送到 SAP 系統時，資料閘道充當 RFC 用戶端，並將從邏輯應用接收的請求轉發給 SAP。 同樣，在接收方案中，資料閘道充當 RFC 伺服器，接收來自 SAP 的請求並將其轉發到邏輯應用。

本文說明如何建立會與 SAP 整合的邏輯應用程式範例，同時也會說明先前所述的整合案例。 對於使用較舊的 SAP 連接器的邏輯應用，本文演示如何將邏輯應用遷移到最新的 SAP 連接器。

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Prerequisites

若要遵循本文中的範例，您需要以下項目：

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要用來存取 SAP 系統的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程序。 如果您是邏輯應用的新增功能，請參閱什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)和["快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)"。

* 您的[SAP 應用程式伺服器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或[SAP 消息伺服器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。

* 下載最新的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)，並安裝在任何內部部署電腦中。 請務必先在 Azure 入口網站中設定閘道再繼續。 閘道可説明您安全地訪問本地資料和資源。 有關詳細資訊，請參閱為[Azure 邏輯應用安裝本地資料閘道](../logic-apps/logic-apps-gateway-install.md)。

* 如果將 SNC 與 SSO 一起使用，請確保閘道以映射到 SAP 使用者的使用者身份運行。 要更改預設帳戶，請選擇 **"更改帳戶**"並輸入使用者憑據。

  ![更改閘道帳戶](./media/logic-apps-using-sap-connector/gateway-account.png)

* 如果使用外部安全產品啟用 SNC，請複製安裝閘道的同一台電腦上的 SNC 庫或檔。 SNC 產品的一些示例包括[皂液](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。

* 下載並安裝最新的 SAP 用戶端庫，該庫目前是[Microsoft .NET 3.0.22.0 的 SAP 連接器 （NCo 3.0），該庫使用 .NET 框架 4.0 - Windows 64 位 （x64） 編譯](https://softwaredownloads.sap.com/file/0020000001000932019)，與本地資料閘道位於同一台電腦上。 安裝此版本或更新版本的原因如下：

  * 當同時發送多個 IDoc 消息時，早期 SAP NCo 版本可能會鎖死。 此條件阻止發送到 SAP 目標的所有以後郵件，從而導致消息超時。
  
  * 內部部署資料閘道僅能在 64 位元系統上執行。 否則，您會遭遇「映像錯誤」錯誤，因為資料閘道主機服務不支援 32 位元組件。
  
  * 資料閘道主機服務和 Microsoft SAP Adapter 皆使用 .NET Framework 4.5。 適用於 .NET Framework 4.0 的 SAP NCo 可執行採用 .NET runtime 4.0 到 4.7.1 版的流程。 SAP NCo for .NET Framework 2.0 適用于使用 .NET 運行時 2.0 到 3.5 的進程，但不再適用于最新的本地資料閘道。

* 可以發送到 SAP 伺服器的郵件內容（如示例 IDoc 檔）必須採用 XML 格式，並包含要使用的 SAP 操作的命名空間。

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>遷移到當前連接器

1. 如果尚未這樣做，請更新[本地資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)，以便擁有最新版本。 有關詳細資訊，請參閱為[Azure 邏輯應用安裝本地資料閘道](../logic-apps/logic-apps-gateway-install.md)。

1. 在使用較舊的 SAP 連接器的邏輯應用中，刪除 **"發送到 SAP"** 操作。

1. 從最新的 SAP 連接器中，將 **"發送消息"添加到 SAP**操作。 在使用此操作之前，請重新創建與 SAP 系統的連接。

1. 完成後，儲存邏輯應用程式。

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>將訊息傳送至 SAP

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 邏輯應用將 IDoc 發送到 SAP 伺服器，並向調用邏輯應用的要求者返回回應。

### <a name="add-an-http-request-trigger"></a>添加 HTTP 要求觸發器

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

在此範例中，您可使用 Azure 中的端點建立邏輯應用程式，如此即可將 *HTTP POST 要求*傳送至您的邏輯應用程式。 邏輯應用程式收到這些 HTTP 要求後，觸發程序便會在您的工作流程中引發並執行後續步驟。

1. 在[Azure 門戶](https://portal.azure.com)中 ，創建一個空白邏輯應用，打開邏輯應用設計器。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從**觸發器**清單中，選擇**何時收到 HTTP 要求**。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在保存邏輯應用，以便可以為邏輯應用生成終結點 URL。 在設計工具的工具列上，選取 [儲存]****。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>添加 SAP 操作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 如果您尚未新增觸發程序至邏輯應用程式，並且想要遵循此範例進行，請[按照本節所述指示新增觸發程序](#add-trigger)。

1. 在邏輯應用設計器中，在觸發器下，選擇 **"新步驟**"。

   ![向邏輯應用添加新步驟](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 **"操作"** 清單中，選擇 **"向 SAP 發送消息**"。
  
   ![選擇"向 SAP 發送消息"操作](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   或者，您可以選擇 **"企業"** 選項卡，然後選擇 SAP 操作。

   ![從企業選項卡中選擇"向 SAP 發送消息"操作](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果連接已存在，請繼續執行下一步，以便可以設置 SAP 操作。 但是，如果系統提示您提供連接詳細資訊，請提供資訊，以便立即創建與本地 SAP 伺服器的連接。

   1. 提供連接的名稱。

   1. 在 **"資料閘道**"部分中，在 **"訂閱"** 下，首先為閘道安裝在 Azure 門戶中創建的閘道資源選擇 Azure 訂閱。 
   
   1. 在**連接閘道**下，選擇閘道資源。

   1. 繼續提供有關連接的資訊。 對於 **"登錄類型"** 屬性，請根據該屬性是否設置為**應用程式伺服器**或**組**，執行步驟：
   
      * 對於**應用程式伺服器**，這些通常顯示為可選的屬性是必需的：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 對於**組**，這些通常顯示為可選的屬性是必需的：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      預設情況下，強型別用於通過針對架構執行 XML 驗證來檢查無效值。 此行為可以説明您更早地發現問題。 **"安全鍵入"** 選項可用於向後相容性，並且僅檢查字串長度。 瞭解有關[安全鍵入選項](#safe-typing)的更多內容。

   1. 完成後，選擇 **"創建**"。

      邏輯應用設置和測試連接，以確保連接正常工作。

1. 立即尋找並從 SAP 伺服器選取動作。

   1. 在**SAP 操作**框中，選擇資料夾圖示。 從檔案清單中，尋找並選取您要使用的 SAP 訊息。 若要瀏覽清單，請使用箭號。

      本示例選擇具有**訂單**類型的 IDoc。

      ![尋找並選取 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到您需要的動作，您可手動輸入路徑，例如：

      ![手動提供路徑給 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 通過運算式編輯器為**SAP 操作**提供值。 這樣，您可以對不同的訊息類型使用相同的操作。

      有關 IDoc 操作的詳細資訊，請參閱[IDOC 操作的消息架構](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在 [輸入訊息]**** 方塊內按一下，動態內容清單隨即顯示。 在該清單中的 [收到 HTTP 要求時]**** 之下，選取 [內文]**** 欄位。

      此步驟包括 HTTP 要求觸發器中的正文內容，並將該輸出發送到 SAP 伺服器。

      ![從觸發器中選擇"正文"屬性](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成後，SAP 操作如下所示：

      ![完成 SAP 操作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>添加 HTTP 回應操作

現在請將回應動作新增至邏輯應用程式的工作流程，並納入 SAP 動作的輸出結果。 如此一來，邏輯應用程式便會從 SAP 伺服器將結果傳回至原始的要求者。

1. 在邏輯應用設計器中，在 SAP 操作下，選擇 **"新步驟**"。

1. 在搜尋方塊中，輸入 `response` 作為篩選條件。 從 **"操作"** 清單中，選擇 **"回應**"。

1. 在 [內文]**** 方塊內按一下，動態內容清單隨即顯示。 從該清單中，在 **"向 SAP 發送消息**"下，選擇 **"正文**"欄位。

   ![完整的 SAP 動作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 儲存您的邏輯應用程式。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 如果邏輯應用尚未啟用，請在邏輯應用功能表上選擇 **"概述**"。 在工具列上，選擇 **"啟用**"。

1. 在設計器工具列上，選擇 **"運行**"。 此步驟會手動啟動您的邏輯應用程式。

1. 通過向 HTTP 要求觸發器中的 URL 發送 HTTP POST 請求來觸發邏輯應用。
將郵件內容與您的請求一起包含。 若要傳送內容，您可使用 [Postman](https://www.getpostman.com/apps) 之類的工具。

   在本文中，要求會傳送 IDoc 檔案，其必須為 XML 格式並且包含您要使用的 SAP 動作的命名空間，例如：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. 傳送 HTTP 要求後，請等候來自邏輯應用程式的回應。

   > [!NOTE]
   > 如果回應所需的所有步驟未在[要求逾時期限](./logic-apps-limits-and-config.md)內完成，邏輯應用程式可能會逾時。 如果發生此情況，要求可能就會遭到封鎖。 為協助您診斷問題，請了解如何[檢查和監控邏輯應用程式](../logic-apps/monitor-logic-apps.md)。

現在，您已經創建了一個邏輯應用，可以與您的 SAP 伺服器進行通信。 您現在已為邏輯應用程式設定 SAP 連線，接下來可探索其他可用的 SAP 動作如：BAPI 和 RFC。

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>從 SAP 接收消息

本示例使用邏輯應用，該應用在應用從 SAP 系統接收消息時觸發。

### <a name="add-an-sap-trigger"></a>添加 SAP 觸發器

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從**觸發器**清單中選擇 **"何時從 SAP 收到消息**"。

   ![新增 SAP 觸發程序](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   或者，您可以選擇 **"企業"** 選項卡，然後選擇觸發器：

   ![從企業選項卡添加 SAP 觸發器](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果連接已存在，請繼續執行下一步，以便可以設置 SAP 操作。 但是，如果系統提示您提供連接詳細資訊，請提供資訊，以便立即創建與本地 SAP 伺服器的連接。

   1. 提供連接的名稱。

   1. 在 **"資料閘道**"部分中，在 **"訂閱"** 下，首先為閘道安裝在 Azure 門戶中創建的閘道資源選擇 Azure 訂閱。 

   1. 在**連接閘道**下，選擇閘道資源。

   1. 繼續提供有關連接的資訊。 對於 **"登錄類型"** 屬性，請根據該屬性是否設置為**應用程式伺服器**或**組**，執行步驟：

      * 對於**應用程式伺服器**，這些通常顯示為可選的屬性是必需的：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 對於**組**，這些通常顯示為可選的屬性是必需的：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      預設情況下，強型別用於通過針對架構執行 XML 驗證來檢查無效值。 此行為可以説明您更早地發現問題。 **"安全鍵入"** 選項可用於向後相容性，並且僅檢查字串長度。 瞭解有關[安全鍵入選項](#safe-typing)的更多內容。

   1. 完成後，選擇 **"創建**"。

      邏輯應用設置和測試連接，以確保連接正常工作。

1. 根據 SAP 系統設定提供必要的參數。

   您可以選擇性地提供一或多個 SAP 動作。 此動作清單會指定觸發程序透過資料閘道從 SAP 伺服器所接收的訊息。 空白清單會指定讓觸發程序接收所有訊息。 如果清單有多個訊息，觸發程序則只會接收清單所指定的訊息。 閘道會拒絕任何其他從 SAP 伺服器傳送的訊息。

   您可以從檔案選擇器選取 SAP 動作：

   ![將 SAP 操作添加到邏輯應用](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，您也可以手動指定動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   以下舉例說明當您設定觸發程序來接收多個訊息時，此動作的顯示方式。

   ![接收多條消息的觸發器示例](media/logic-apps-using-sap-connector/example-trigger.png)

   有關 SAP 操作的詳細資訊，請參閱[IDOC 操作的消息架構](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 現在儲存邏輯應用程式，以便可以開始從 SAP 系統接收訊息。 在設計工具的工具列上，選取 [儲存]****。

邏輯應用程式現在已準備好從 SAP 系統接收訊息。

> [!NOTE]
> SAP 觸發器不是輪詢觸發器，而是基於 Webhook 的觸發器。 訊息存在時才會從閘道呼叫此觸發程序，所以不必輪詢。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 若要觸發邏輯應用程式，請從 SAP 系統傳送訊息。

1. 在邏輯應用功能表上，選擇 **"概述**"。 查看邏輯應用的任何新運行**的"執行歷程記錄**"。

1. 開啟最近的執行，其會在觸發程序的輸出區段中顯示從 SAP 系統傳送的訊息。

## <a name="receive-idoc-packets-from-sap"></a>從 SAP 接收 IDOC 資料包

您可以將 SAP 設置為[在資料包中發送 IDOC，](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)資料包是 IDOC 的批次處理或組。 要接收 IDOC 資料包，SAP 連接器，特別是觸發器，不需要額外的配置。 但是，要在觸發器接收資料包後處理 IDOC 資料包中的每個專案，需要執行一些其他步驟才能將資料包拆分為單獨的 IDOC。

下面是一個示例，演示如何使用[`xpath()`函數](./workflow-definition-language-functions-reference.md#xpath)從資料包中提取單個 IDOC：

1. 在開始之前，您需要一個帶有 SAP 觸發器的邏輯應用。 如果尚未具有此邏輯應用，請按照本主題中的上述步驟[設置具有 SAP 觸發器的邏輯應用](#receive-from-sap)。

   例如：

   ![將 SAP 觸發器添加到邏輯應用](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 從邏輯應用從 SAP 接收的 XML IDOC 獲取根命名空間。 要從 XML 文檔中提取此命名空間，添加創建本地字串變數的步驟，並使用`xpath()`運算式存儲命名空間：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![從 IDOC 獲取根命名空間](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 要提取單個 IDOC，添加創建陣列變數的步驟，並使用另一個`xpath()`運算式存儲 IDOC 集合：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![獲取專案陣列](./media/logic-apps-using-sap-connector/get-array.png)

   陣列變數使每個 IDOC 可用於邏輯應用，通過枚舉集合單獨處理。 在此示例中，邏輯應用使用迴圈將每個 IDOC 傳輸到 SFTP 伺服器：

   ![將 IDOC 發送到 SFTP 伺服器](./media/logic-apps-using-sap-connector/loop-batch.png)

   每個 IDOC 都必須包含根命名空間，這是在這種情況下將 IDOC 發送到下游應用或`<Receive></Receive`SFTP 伺服器之前將檔內容與根命名空間一起包裝在元素中的原因。

您可以在創建新的邏輯應用應用時，在邏輯應用設計器中選擇此範本，從而使用此模式的快速入門範本。

![選擇批次處理邏輯應用範本](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>在 SAP 中產生成品的結構描述

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 SAP 操作向 SAP 系統發送請求，以生成指定 IDoc 和 BAPI 的架構。 回應中返回的架構通過使用 Azure 資源管理器連接器上載到集成帳戶。

### <a name="add-an-http-request-trigger"></a>添加 HTTP 要求觸發器

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從**觸發器**清單中，選擇**何時收到 HTTP 要求**。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在請儲存邏輯應用程式，即可產生邏輯應用程式的端點 URL。
在設計工具的工具列上，選取 [儲存]****。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>添加 SAP 操作以生成架構

1. 在邏輯應用設計器中，在觸發器下，選擇 **"新步驟**"。

   ![向邏輯應用添加新步驟](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 **"操作"** 清單中，選擇 **"生成架構**"。
  
   ![將"生成架構"操作添加到邏輯應用](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   或者，您可以選擇 **"企業"** 選項卡，然後選擇 SAP 操作。

   ![從 [企業] 索引標籤選取 SAP 傳送動作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果連接已存在，請繼續執行下一步，以便可以設置 SAP 操作。 但是，如果系統提示您提供連接詳細資訊，請提供資訊，以便立即創建與本地 SAP 伺服器的連接。

   1. 提供連接的名稱。

   1. 在 **"資料閘道**"部分中，在 **"訂閱"** 下，首先為閘道安裝在 Azure 門戶中創建的閘道資源選擇 Azure 訂閱。 
   
   1. 在**連接閘道**下，選擇閘道資源。

   1. 繼續提供有關連接的資訊。 對於 **"登錄類型"** 屬性，請根據該屬性是否設置為**應用程式伺服器**或**組**，執行步驟：
   
      * 對於**應用程式伺服器**，這些通常顯示為可選的屬性是必需的：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 對於**組**，這些通常顯示為可選的屬性是必需的：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      預設情況下，強型別用於通過針對架構執行 XML 驗證來檢查無效值。 此行為可以説明您更早地發現問題。 **"安全鍵入"** 選項可用於向後相容性，並且僅檢查字串長度。 瞭解有關[安全鍵入選項](#safe-typing)的更多內容。

   1. 完成後，選擇 **"創建**"。

      邏輯應用設置和測試連接，以確保連接正常工作。

1. 針對您要產生結構描述的成品提供其路徑。

   您可以從檔案選擇器選取 SAP 動作：

   ![選取 SAP 動作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，您也可以手動輸入動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要針對多個成品產生結構描述，請提供每個成品的 SAP 動作詳細資料，例如：

   ![選取 [新增項目]](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![顯示兩個項目](media/logic-apps-using-sap-connector/schema-generator-example.png)

   有關 SAP 操作的詳細資訊，請參閱[IDOC 操作的消息架構](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計器工具列上，選擇 **"運行"** 以觸發邏輯應用的運行。

1. 打開運行，並檢查"**生成架構**"操作的輸出。

   輸出中會顯示針對所指定訊息清單產生的結構描述。

### <a name="upload-schemas-to-an-integration-account"></a>將架構上載到集成帳戶

(選擇性) 您可以在存放庫 (例如，Blob、儲存體或企業整合帳戶) 下載或儲存產生的結構描述。 企業整合帳戶搭配其他 XML 動作可提供一流的體驗，因此這個範例會說明如何使用 Azure Resource Manager 連接器，將相同邏輯應用程式的結構描述上傳到企業整合帳戶。

1. 在邏輯應用設計器中，在觸發器下，選擇 **"新步驟**"。

1. 在搜尋方塊中，輸入 `Resource Manager` 作為篩選條件。 選擇 **"創建或更新資源**"。

   ![選取 Azure Resource Manager 動作](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 輸入操作的詳細資訊，包括 Azure 訂閱、Azure 資源組和集成帳戶。 要向欄位添加 SAP 權杖，請按一下這些欄位的框內，然後從顯示的動態內容清單中選擇。

   1. 打開"**添加新參數**清單"，然後選擇"**位置**和**屬性**"欄位。

   1. 提供這些新欄位的詳細資訊，如本示例所示。

      ![輸入 Azure Resource Manager 動作的詳細資料](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP 的**產生結構描述**動作會以集合的形式產生結構描述，讓設計工具可以在動作中自動新增 **For each** 迴圈。 以下舉例說明此動作的顯示方式：

   ![Azure Resource Manager 動作與 "for each" 迴圈](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 結構描述會使用 base64 編碼格式。 若要將結構描述上傳到企業整合帳戶，必須先使用 `base64ToString()` 函式將結構描述解碼。 以下舉例說明 `"properties"` 元素的程式碼：
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計器工具列上，選擇 **"運行**"以手動觸發邏輯應用。

1. 成功運行後，轉到集成帳戶，並檢查生成的架構是否存在。

## <a name="enable-secure-network-communications"></a>啟用安全網路通信

在開始之前，請確保您滿足前面列出的[先決條件](#pre-reqs)：

* 本地資料閘道安裝在與 SAP 系統處於同一網路中的電腦上。
* 對於 SSO，閘道以映射到 SAP 使用者的使用者身份運行。
* 提供附加安全功能的 SNC 庫與資料閘道在同一台電腦上安裝。 一些例子包括[皂塞庫利布](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)，Kerberos和NTLM。

   要為您向 SAP 系統或從 SAP 系統請求啟用 SNC，請在 SAP 連接中選擇 **"使用 SNC"** 核取方塊並提供以下屬性：

   ![在連接中配置 SAP SNC](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 屬性 | 描述 |
   |----------| ------------|
   | **SNC 庫路徑** | SNC 庫名稱或路徑相對於 NCo 安裝位置或絕對路徑。 示例是`sapsnc.dll``.\security\sapsnc.dll`或`c:\security\sapsnc.dll`。 |
   | **SNC SSO** | 通過 SNC 連接時，SNC 標識通常用於驗證調用方。 另一個選項是重寫，以便可以使用使用者和密碼資訊對調用方進行身份驗證，但線路仍被加密。 |
   | **SNC 我的名字** | 在大多數情況下，可以省略此屬性。 已安裝的 SNC 解決方案通常知道其自己的 SNC 名稱。 僅對於支援多個識別的解決方案，您可能需要指定用於此特定目標或伺服器的標識。 |
   | **SNC 合作夥伴名稱** | 後端 SNC 的名稱。 |
   | **SNC 保護品質** | 用於此特定目標或伺服器的 SNC 通信的服務品質。 預設值由後端系統定義。 最大值由用於 SNC 的安全產品定義。 |
   |||

   > [!NOTE]
   > 不要在具有資料閘道和 SNC 庫的電腦上設置環境變數SNC_LIB和SNC_LIB_64。 如果設置，它們優先于通過連接器傳遞的 SNC 庫值。

<a name="safe-typing"></a>

## <a name="safe-typing"></a>安全打字

預設情況下，在創建 SAP 連接時，強型別用於通過針對架構執行 XML 驗證來檢查無效值。 此行為可以説明您更早地發現問題。 **"安全鍵入"** 選項可用於向後相容性，並且僅檢查字串長度。 如果選擇**安全鍵入**，SAP 中的 DATS 類型和 TIMS 類型將被視為字串，而不是其 XML 等效項`xs:date`，`xs:time`以及`xmlns:xs="http://www.w3.org/2001/XMLSchema"`。 安全鍵入會影響所有架構生成的行為、"已發送"負載和"已接收"回應的發送消息以及觸發器。 

當使用強型別（未啟用**安全類型）** 時，架構將 DATS 和 TIMS 類型映射到更直接的 XML 類型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

使用強型別發送消息時，DATS 和 TIMS 回應符合匹配的 XML 類型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

啟用**安全鍵入**後，架構將 DATS 和 TIMS 類型映射到僅具有長度限制的 XML 字串欄位，例如：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

啟用**安全鍵入**後發送消息時，DATS 和 TIMS 回應如下所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>進階案例

### <a name="confirm-transaction-explicitly"></a>顯式確認事務

當您從邏輯應用向 SAP 發送事務時，此交換分兩個步驟進行，如 SAP 文檔"[事務性 RFC 伺服器程式](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)"中所述。 預設情況下，"發送到**SAP"** 操作在單個調用中處理函數傳輸和事務確認的步驟。 SAP 連接器為您提供了分離這些步驟的選項。 您可以發送 IDOC，而不是自動確認事務，您可以使用顯式**的"確認事務 ID"** 操作。

當您不想在 SAP 中複製事務時，這種分離事務 ID 確認的功能非常有用，例如，在由於網路問題等原因而可能發生故障的情況下。 通過單獨確認事務 ID，事務僅在 SAP 系統中完成一次。

下面是顯示此模式的示例：

1. 創建空白邏輯應用並添加 HTTP 觸發器。

1. 從 SAP 連接器中，添加 **"發送 IDOC"** 操作。 提供發送到 SAP 系統的 IDOC 的詳細資訊。

1. 要在單獨的步驟中顯式確認事務 ID，請在"**確認 TID"** 欄位中，選擇 **"否**"。 對於可選**的事務 ID GUID**欄位，您可以手動指定值，也可以讓連接器在"發送 IDOC"操作的回應中自動生成並返回此 GUID。

   ![發送 IDOC 操作屬性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 要顯式確認事務 ID，請添加 **"確認事務 ID"** 操作。 按一下 **"事務 ID"** 框，以便顯示動態內容清單。 從該清單中選擇從 **"發送 IDOC"** 操作返回的**事務 ID**值。

   ![確認事務 ID 操作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   運行此步驟後，當前事務在 SAP 連接器端和 SAP 系統端的兩端標記為完成。

## <a name="known-issues-and-limitations"></a>已知問題和限制

以下是 SAP 連接器目前的已知問題和限制：

* SAP 觸發程序不支援資料閘道叢集。 在某些情況下，與 SAP 系統通信的資料閘道節點可能與活動節點不同，從而導致意外行為。 對於發送方案，支援資料閘道群集。

* SAP 連接器目前不支援 SAP 路由器字串。 內部部署資料閘道必須和所要連線的 SAP 系統存在於相同 LAN 內。

## <a name="connector-reference"></a>連接器參考

有關此連接器的更多技術詳細資訊，例如連接器的 Swagger 檔所述的觸發器、操作和限制，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/sap/)。

> [!NOTE]
> 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 從 Azure 邏輯應用[連接到本地系統](../logic-apps/logic-apps-gateway-connection.md)。
* 瞭解如何在[企業集成包](../logic-apps/logic-apps-enterprise-integration-overview.md)中驗證、轉換和使用其他消息操作。
* 瞭解其他[邏輯應用連接器](../connectors/apis-list.md)。
