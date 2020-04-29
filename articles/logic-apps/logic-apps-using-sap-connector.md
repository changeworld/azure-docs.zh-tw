---
title: 連接到 SAP 系統
description: 使用 Azure Logic Apps 自動化工作流程，以存取和管理 SAP 資源
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77651010"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>從 Azure Logic Apps 連線至 SAP 系統

> [!IMPORTANT]
> 舊版的 SAP 應用程式伺服器和 SAP 訊息伺服器連接器會在2020年2月29日淘汰。 目前的 SAP 連接器會合並這些先前的 SAP 連接器，讓您不需要變更連線類型、與先前的連接器完全相容、提供許多額外的功能，並繼續使用 SAP .Net 連接器程式庫（SAP NCo）。
>
> 對於使用較舊連接器的邏輯應用程式，請在淘汰日期之前[遷移至最新的連接器](#migrate)。 否則，這些邏輯應用程式會遇到執行失敗，而且無法將訊息傳送至您的 SAP 系統。

本文說明如何使用 SAP 連接器，從邏輯應用程式記憶體取內部部署 SAP 資源。 連接器適用于 SAP 的傳統版本，例如 R/3 和 ECC 系統內部部署。 連接器也可以整合 SAP 的較新 HANA 型 SAP 系統（例如 S/4 HANA），不論它們是裝載于內部部署或雲端。 SAP 連接器支援透過中繼檔（IDoc）、商務應用程式開發介面（BAPI）或遠端函式呼叫（RFC），在 SAP NetWeaver 型系統之間進行訊息或資料整合。

SAP 連接器會使用[sap .Net connector （NCo）程式庫](https://support.sap.com/en/product/connectors/msnet.html)，並提供下列動作：

* **將訊息傳送至 sap**：透過 TRFC 傳送 IDoc、透過 RFC 呼叫 BAPI 函式，或在 SAP 系統中呼叫 RFC/tRFC。
* **從 sap 收到訊息時**：接收 IDoc over tRFC、透過 TRFC 呼叫 BAPI 函式，或在 SAP 系統中呼叫 RFC/tRFC。
* **產生架構**：為 IDOC、BAPI 或 RFC 的 SAP 成品產生架構。

針對這些作業，SAP 連接器支援透過使用者名稱和密碼進行基本驗證。 連接器也支援[安全網路通訊（SNC）](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)。 SNC 可用於 SAP NetWeaver 單一登入（SSO），或用於外部安全性產品所提供的其他安全性功能。

SAP 連接器會透過[內部部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)與內部部署 SAP 系統整合。 例如，在傳送案例中，當訊息從邏輯應用程式傳送到 SAP 系統時，資料閘道會作為 RFC 用戶端，並將接收自邏輯應用程式的要求轉送到 SAP。 同樣地，在接收案例中，資料閘道會作為 RFC 伺服器，以接收來自 SAP 的要求，並將其轉送至邏輯應用程式。

本文說明如何建立會與 SAP 整合的邏輯應用程式範例，同時也會說明先前所述的整合案例。 針對使用舊版 SAP 連接器的邏輯應用程式，本文說明如何將您的邏輯應用程式遷移至最新的 SAP 連接器。

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>先決條件

若要遵循本文中的範例，您需要以下項目：

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要用來存取 SAP 系統的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程序。 如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 您的[sap 應用程式伺服器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或[sap 訊息伺服器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。

* 下載最新的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)，並安裝在任何內部部署電腦中。 請務必先在 Azure 入口網站中設定閘道再繼續。 閘道可協助您安全地存取內部部署資料和資源。 如需詳細資訊，請參閱[安裝適用于 Azure Logic Apps 的內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

* 如果您使用 SNC 搭配 SSO，請確定閘道是以對應于 SAP 使用者的使用者身分執行。 若要變更預設帳戶，請選取 [**變更帳戶**]，然後輸入使用者認證。

  ![變更閘道帳戶](./media/logic-apps-using-sap-connector/gateway-account.png)

* 如果您啟用具有外部安全性產品的 SNC，請將 SNC 程式庫或檔案複製到已安裝閘道的同一部電腦上。 SNC 產品的一些範例包括[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、KERBEROS 和 NTLM。

* 下載並安裝最新的 SAP 用戶端程式庫，這是目前[使用 .NET Framework 4.0-Windows 64 位（x64）編譯的 Sap Connector （NCo 3.0）](https://softwaredownloads.sap.com/file/0020000001000932019)，位於與內部部署資料閘道相同的電腦上。 安裝此版本或更新版本的原因如下：

  * 當同時傳送多個 IDoc 訊息時，較舊的 SAP NCo 版本可能會變得鎖死。 此條件會封鎖傳送至 SAP 目的地的所有後續訊息，這會導致訊息超時。
  
  * 內部部署資料閘道僅能在 64 位元系統上執行。 否則，您會遭遇「映像錯誤」錯誤，因為資料閘道主機服務不支援 32 位元組件。
  
  * 資料閘道主機服務和 Microsoft SAP Adapter 皆使用 .NET Framework 4.5。 適用於 .NET Framework 4.0 的 SAP NCo 可執行採用 .NET runtime 4.0 到 4.7.1 版的流程。 適用于 .NET Framework 2.0 的 SAP NCo 適用于使用 .NET 執行時間2.0 至3.5 的進程，但不再適用于最新的內部部署資料閘道。

* 您可以傳送至 SAP 伺服器的訊息內容（例如範例 IDoc 檔案）必須是 XML 格式，並包含您想要使用之 SAP 動作的命名空間。

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>遷移至目前的連接器

1. 如果您尚未這麼做，請更新您的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)，讓您擁有最新版本。 如需詳細資訊，請參閱[安裝適用于 Azure Logic Apps 的內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

1. 在使用舊版 SAP 連接器的邏輯應用程式中，刪除 [**傳送至 SAP** ] 動作。

1. 從最新的 SAP 連接器中，新增 [**傳送訊息至 SAP** ] 動作。 在您可以使用此動作之前，請先重新建立與 SAP 系統的連線。

1. 完成後，儲存邏輯應用程式。

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>將訊息傳送至 SAP

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 邏輯應用程式會將 IDoc 傳送至 SAP 伺服器，並將回應傳回給呼叫邏輯應用程式的要求者。

### <a name="add-an-http-request-trigger"></a>新增 HTTP 要求觸發程式

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

在此範例中，您可使用 Azure 中的端點建立邏輯應用程式，如此即可將 *HTTP POST 要求*傳送至您的邏輯應用程式。 邏輯應用程式收到這些 HTTP 要求後，觸發程序便會在您的工作流程中引發並執行後續步驟。

1. 在[Azure 入口網站](https://portal.azure.com)中，建立空白邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從**觸發**程式清單中，選取 [**收到 HTTP 要求時**]。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在儲存您的邏輯應用程式，讓您可以產生邏輯應用程式的端點 URL。 在設計工具的工具列上，選取 [儲存]****。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>新增 SAP 動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 如果您尚未新增觸發程序至邏輯應用程式，並且想要遵循此範例進行，請[按照本節所述指示新增觸發程序](#add-trigger)。

1. 在邏輯應用程式設計工具的觸發程式底下，選取 [**新增步驟**]。

   ![將新步驟新增至邏輯應用程式](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [**動作**] 清單中，選取 [**傳送訊息至 SAP**]。
  
   ![選取 [傳送訊息至 SAP] 動作](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   或者，您可以選取 [**企業**] 索引標籤，然後選取 [SAP] 動作。

   ![從 [企業] 索引標籤選取 [傳送訊息至 SAP] 動作](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟，讓您可以設定 SAP 動作。 不過，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連接的名稱。

   1. 在 [**資料閘道**] 區段的 [**訂**用帳戶] 底下，先針對您在閘道安裝的 Azure 入口網站中建立的閘道資源選取 Azure 訂用帳戶。 
   
   1. 在 [連線**閘道**] 底下，選取您的閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [**登入類型**] 屬性，請根據此屬性是否設為 [**應用程式伺服器**] 或 [**群組**] 來遵循此步驟：
   
      * 若為**應用程式伺服器**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 若為**群組**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      根據預設，強式類型是用來透過對架構執行 XML 驗證來檢查是否有不正確值。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 深入瞭解[安全輸入選項](#safe-typing)。

   1. 當您完成時，請選取 [**建立**]。

      Logic Apps 會設定並測試您的連線，以確保連線正常運作。

1. 立即尋找並從 SAP 伺服器選取動作。

   1. 在 [ **SAP 動作**] 方塊中，選取資料夾圖示。 從檔案清單中，尋找並選取您要使用的 SAP 訊息。 若要瀏覽清單，請使用箭號。

      這個範例會選取具有**Orders**類型的 IDoc。

      ![尋找並選取 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到您需要的動作，您可手動輸入路徑，例如：

      ![手動提供路徑給 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 透過運算式編輯器提供**SAP 動作**的值。 如此一來，您就可以針對不同的訊息類型使用相同的動作。

      如需 IDoc 作業的詳細資訊，請參閱[IDoc 作業的訊息架構](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在 [輸入訊息]**** 方塊內按一下，動態內容清單隨即顯示。 在該清單中的 [收到 HTTP 要求時]**** 之下，選取 [內文]**** 欄位。

      此步驟包含來自 HTTP 要求觸發程式的本文內容，並將該輸出傳送至您的 SAP 伺服器。

      ![從觸發程式選取 "Body" 屬性](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      當您完成時，您的 SAP 動作會如下列範例所示：

      ![完成 SAP 動作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>新增 HTTP 回應動作

現在請將回應動作新增至邏輯應用程式的工作流程，並納入 SAP 動作的輸出結果。 如此一來，邏輯應用程式便會從 SAP 伺服器將結果傳回至原始的要求者。

1. 在邏輯應用程式設計工具的 [SAP] 動作底下，選取 [**新增步驟**]。

1. 在搜尋方塊中，輸入 `response` 作為篩選條件。 從 [**動作**] 清單中，選取 [**回應**]。

1. 在 [內文]**** 方塊內按一下，動態內容清單隨即顯示。 從該清單的 [**將訊息傳送至 SAP**] 底下**Body** ，選取 [內文] 欄位。

   ![完整的 SAP 動作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 儲存您的邏輯應用程式。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 如果您的邏輯應用程式尚未啟用，請在邏輯應用程式功能表上，選取 **[總覽**]。 在工具列上，選取 [**啟用**]。

1. 在設計工具工具列上，選取 [**執行**]。 此步驟會手動啟動您的邏輯應用程式。

1. 藉由將 HTTP POST 要求傳送至 HTTP 要求觸發程式中的 URL，來觸發邏輯應用程式。
將您的訊息內容包含在您的要求中。 若要傳送內容，您可使用 [Postman](https://www.getpostman.com/apps) 之類的工具。

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

您現在已建立可與您的 SAP 伺服器通訊的邏輯應用程式。 您現在已為邏輯應用程式設定 SAP 連線，接下來可探索其他可用的 SAP 動作如：BAPI 和 RFC。

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>從 SAP 接收訊息

這個範例會使用應用程式從 SAP 系統接收訊息時觸發的邏輯應用程式。

### <a name="add-an-sap-trigger"></a>新增 SAP 觸發程式

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [**觸發**程式] 清單中，選取 [**從 SAP 收到訊息時**]。

   ![新增 SAP 觸發程序](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   或者，您可以選取 [**企業**] 索引標籤，然後選取觸發程式：

   ![從 [企業] 索引標籤新增 SAP 觸發程式](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟，讓您可以設定 SAP 動作。 不過，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連接的名稱。

   1. 在 [**資料閘道**] 區段的 [**訂**用帳戶] 底下，先針對您在閘道安裝的 Azure 入口網站中建立的閘道資源選取 Azure 訂用帳戶。 

   1. 在 [連線**閘道**] 底下，選取您的閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [**登入類型**] 屬性，請根據此屬性是否設為 [**應用程式伺服器**] 或 [**群組**] 來遵循此步驟：

      * 若為**應用程式伺服器**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 若為**群組**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      根據預設，強式類型是用來透過對架構執行 XML 驗證來檢查是否有不正確值。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 深入瞭解[安全輸入選項](#safe-typing)。

   1. 當您完成時，請選取 [**建立**]。

      Logic Apps 會設定並測試您的連線，以確保連線正常運作。

1. 根據 SAP 系統設定提供必要的參數。

   您可以選擇性地提供一或多個 SAP 動作。 此動作清單會指定觸發程序透過資料閘道從 SAP 伺服器所接收的訊息。 空白清單會指定讓觸發程序接收所有訊息。 如果清單有多個訊息，觸發程序則只會接收清單所指定的訊息。 閘道會拒絕任何其他從 SAP 伺服器傳送的訊息。

   您可以從檔案選擇器選取 SAP 動作：

   ![將 SAP 動作新增至邏輯應用程式](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，您也可以手動指定動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   以下舉例說明當您設定觸發程序來接收多個訊息時，此動作的顯示方式。

   ![接收多個訊息的觸發程式範例](media/logic-apps-using-sap-connector/example-trigger.png)

   如需 SAP 動作的詳細資訊，請參閱[IDOC 作業的訊息架構](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 現在儲存邏輯應用程式，以便可以開始從 SAP 系統接收訊息。 在設計工具的工具列上，選取 [儲存]****。

邏輯應用程式現在已準備好從 SAP 系統接收訊息。

> [!NOTE]
> SAP 觸發程式不是輪詢觸發程式，而是以 webhook 為基礎的觸發程式。 訊息存在時才會從閘道呼叫此觸發程序，所以不必輪詢。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 若要觸發邏輯應用程式，請從 SAP 系統傳送訊息。

1. 在邏輯應用程式功能表上，選取 **[總覽**]。 針對您的邏輯應用程式，檢查**執行歷程記錄**中是否有任何新的執行。

1. 開啟最近的執行，其會在觸發程序的輸出區段中顯示從 SAP 系統傳送的訊息。

## <a name="receive-idoc-packets-from-sap"></a>從 SAP 接收 IDOC 封包

您可以設定 SAP 在封包（也就是 Idoc 的批次或群組）[中傳送 idoc](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)。 若要接收 IDOC 封包，SAP 連接器（尤其是觸發程式）不需要額外的設定。 不過，若要在觸發程式收到封包之後處理 IDOC 封包中的每個專案，則需要執行一些額外的步驟，將封包分割成個別的 Idoc。

以下範例示範如何使用[ `xpath()`函式，從](./workflow-definition-language-functions-reference.md#xpath)封包中解壓縮個別的 idoc：

1. 開始之前，您需要有一個具有 SAP 觸發程式的邏輯應用程式。 如果您還沒有此邏輯應用程式，請遵循本主題中的先前步驟來[設定具有 SAP 觸發程式的邏輯應用程式](#receive-from-sap)。

   例如：

   ![將 SAP 觸發程式新增至邏輯應用程式](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 從您的邏輯應用程式從 SAP 接收的 XML IDOC 取得根命名空間。 若要從 XML 檔解壓縮此命名空間，請新增一個步驟來建立本機字串變數，並使用`xpath()`運算式來儲存該命名空間：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![從 IDOC 取得根命名空間](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 若要將個別 IDOC 解壓縮，請新增一個步驟來建立陣列變數，並使用另一個`xpath()`運算式來儲存 IDOC 集合：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![取得專案的陣列](./media/logic-apps-using-sap-connector/get-array.png)

   陣列變數會透過列舉集合，讓邏輯應用程式可個別處理每個 IDOC。 在此範例中，邏輯應用程式會使用迴圈，將每個 IDOC 傳輸至 SFTP 伺服器：

   ![將 IDOC 傳送至 SFTP 伺服器](./media/logic-apps-using-sap-connector/loop-batch.png)

   每個 IDOC 都必須包含根命名空間，這是在將 IDOC 傳送到下游應用程式`<Receive></Receive`之前，或在此情況下，將檔案內容包裝在元素中的原因，以及根命名空間。

當您建立新的邏輯應用程式時，您可以在邏輯應用程式設計工具中選取此範本，以使用此模式的快速入門範本。

![選取批次邏輯應用程式範本](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>在 SAP 中產生成品的結構描述

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 SAP 動作會將要求傳送至 SAP 系統，以產生指定 IDoc 和 BAPI 的架構。 在回應中傳回的架構，會使用 Azure Resource Manager 連接器上傳至整合帳戶。

### <a name="add-an-http-request-trigger"></a>新增 HTTP 要求觸發程式

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從**觸發**程式清單中，選取 [**收到 HTTP 要求時**]。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在請儲存邏輯應用程式，即可產生邏輯應用程式的端點 URL。
在設計工具的工具列上，選取 [儲存]****。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>新增 SAP 動作以產生架構

1. 在邏輯應用程式設計工具的觸發程式底下，選取 [**新增步驟**]。

   ![將新步驟新增至邏輯應用程式](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [**動作**] 清單中，選取 [**產生架構**]。
  
   ![將 [產生架構] 動作新增至邏輯應用程式](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   或者，您可以選取 [**企業**] 索引標籤，然後選取 [SAP] 動作。

   ![從 [企業] 索引標籤選取 SAP 傳送動作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟，讓您可以設定 SAP 動作。 不過，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連接的名稱。

   1. 在 [**資料閘道**] 區段的 [**訂**用帳戶] 底下，先針對您在閘道安裝的 Azure 入口網站中建立的閘道資源選取 Azure 訂用帳戶。 
   
   1. 在 [連線**閘道**] 底下，選取您的閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [**登入類型**] 屬性，請根據此屬性是否設為 [**應用程式伺服器**] 或 [**群組**] 來遵循此步驟：
   
      * 若為**應用程式伺服器**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 若為**群組**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      根據預設，強式類型是用來透過對架構執行 XML 驗證來檢查是否有不正確值。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 深入瞭解[安全輸入選項](#safe-typing)。

   1. 當您完成時，請選取 [**建立**]。

      Logic Apps 會設定並測試您的連線，以確保連線正常運作。

1. 針對您要產生結構描述的成品提供其路徑。

   您可以從檔案選擇器選取 SAP 動作：

   ![選取 SAP 動作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，您也可以手動輸入動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要針對多個成品產生結構描述，請提供每個成品的 SAP 動作詳細資料，例如：

   ![選取 [新增項目]](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![顯示兩個項目](media/logic-apps-using-sap-connector/schema-generator-example.png)

   如需 SAP 動作的詳細資訊，請參閱[IDOC 作業的訊息架構](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計工具工具列上，選取 [**執行**] 以觸發邏輯應用程式的執行。

1. 開啟執行，並檢查 [**產生架構**] 動作的輸出。

   輸出中會顯示針對所指定訊息清單產生的結構描述。

### <a name="upload-schemas-to-an-integration-account"></a>將架構上傳至整合帳戶

(選擇性) 您可以在存放庫 (例如，Blob、儲存體或企業整合帳戶) 下載或儲存產生的結構描述。 企業整合帳戶搭配其他 XML 動作可提供一流的體驗，因此這個範例會說明如何使用 Azure Resource Manager 連接器，將相同邏輯應用程式的結構描述上傳到企業整合帳戶。

1. 在邏輯應用程式設計工具的觸發程式底下，選取 [**新增步驟**]。

1. 在搜尋方塊中，輸入 `Resource Manager` 作為篩選條件。 選取 [**建立或更新資源**]。

   ![選取 Azure Resource Manager 動作](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 輸入動作的詳細資料，包括您的 Azure 訂用帳戶、Azure 資源群組和整合帳戶。 若要將 SAP 權杖新增至欄位，請在這些欄位的方塊內按一下，然後從顯示的動態內容清單中選取。

   1. 開啟 [**加入新的參數**] 清單，然後選取 [**位置**] 和 [**屬性**] 欄位。

   1. 提供這些新欄位的詳細資料，如下列範例所示。

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

1. 在設計工具工具列上，選取 [**執行**] 以手動觸發邏輯應用程式。

1. 成功執行之後，請移至整合帳戶，並檢查產生的架構是否存在。

## <a name="enable-secure-network-communications"></a>啟用安全網路通訊

開始之前，請確定您符合先前列出的[必要條件](#pre-reqs)：

* 內部部署資料閘道安裝在與您的 SAP 系統位於相同網路中的電腦上。
* 若為 SSO，閘道會以對應至 SAP 使用者的使用者身分執行。
* 提供額外安全性功能的 SNC 程式庫會安裝在與資料閘道相同的電腦上。 其中一些範例包括[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、KERBEROS 和 NTLM。

   若要針對您在 SAP 系統中的要求啟用 SNC，請選取 SAP 連接中的 [**使用 SNC** ] 核取方塊，並提供下列屬性：

   ![在連接中設定 SAP SNC](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 屬性 | 描述 |
   |----------| ------------|
   | **SNC 程式庫路徑** | 相對於 NCo 安裝位置或絕對路徑的 SNC 程式庫名稱或路徑。 範例為`sapsnc.dll`或`.\security\sapsnc.dll`或`c:\security\sapsnc.dll`。 |
   | **SNC SSO** | 當您透過 SNC 連接時，SNC 身分識別通常用來驗證呼叫端。 另一個選項是覆寫，讓使用者和密碼資訊可以用來驗證呼叫者，但這一行仍然是加密的。 |
   | **SNC 我的姓名** | 在大部分的情況下，可以省略這個屬性。 已安裝的 SNC 解決方案通常會知道自己的 SNC 名稱。 僅針對支援多個身分識別的解決方案，您可能需要指定要用於此特定目的地或伺服器的身分識別。 |
   | **SNC 夥伴名稱** | 後端 SNC 的名稱。 |
   | **SNC 保護品質** | 此特定目的地或伺服器的 SNC 通訊所使用的服務品質。 預設值是由後端系統所定義。 最大值是由用於 SNC 的安全性產品所定義。 |
   |||

   > [!NOTE]
   > 請勿在具有資料閘道和 SNC 程式庫的電腦上設定環境變數 SNC_LIB 和 SNC_LIB_64。 如果設定，則會優先于透過連接器傳遞的 SNC 程式庫值。

<a name="safe-typing"></a>

## <a name="safe-typing"></a>安全類型

根據預設，當您建立 SAP 連接時，會使用強型別來檢查是否有不正確值，方法是對架構執行 XML 驗證。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 如果您選擇**安全**型別，SAP 中的 dat 型別和 TIMS 型別會被視為字串，而不是`xs:date`它們`xs:time`的 XML `xmlns:xs="http://www.w3.org/2001/XMLSchema"`對等專案，以及。 安全類型會影響所有架構產生的行為、「已傳送」裝載和「已接收」回應的傳送訊息，以及觸發程式。 

當使用強型別時（未啟用**安全**型別），架構會將 DAT 和 TIMS 型別對應到更簡單的 XML 類型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

當您使用強型別傳送訊息時，DAT 和 TIMS 回應會符合相符的 XML 類型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

啟用**安全類型**時，架構只會將 DAT 和 TIMS 類型對應至長度限制為的 XML 字串欄位，例如：

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

在啟用**安全輸入**的情況下傳送訊息時，DAT 和 TIMS 回應會如下列範例所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>進階案例

### <a name="confirm-transaction-explicitly"></a>明確確認交易

當您從 Logic Apps 將交易傳送至 SAP 時，此交換會以 SAP 檔、[交易式 RFC 伺服器程式](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)中所述的兩個步驟進行。 根據預設，[**傳送至 SAP** ] 動作會在單一呼叫中處理函式傳送和交易確認的步驟。 SAP 連接器可讓您選擇分離這些步驟。 您可以傳送 IDOC，而不是自動確認交易，您可以使用明確的「**確認交易識別碼**」動作。

當您不想要在 SAP 中重複交易時，這項將交易識別碼解除耦合的功能會很有用，例如，在因網路問題而造成失敗的情況下。 藉由單獨確認交易識別碼，交易只會在您的 SAP 系統中完成一次。

以下是顯示此模式的範例：

1. 建立空白邏輯應用程式，並新增 HTTP 觸發程式。

1. 從 SAP 連接器新增 [**傳送 IDOC** ] 動作。 提供您傳送至 SAP 系統的 IDOC 詳細資料。

1. 若要在個別步驟中明確確認交易識別碼，請在 [**確認 TID** ] 欄位中選取 [**否**]。 針對 [選擇性**交易識別碼 GUID** ] 欄位，您可以手動指定值，或讓連接器自動產生，並在 [傳送 IDOC] 動作的回應中傳回此 GUID。

   ![傳送 IDOC 動作屬性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 若要明確確認交易識別碼，請新增 [**確認交易識別碼**] 動作。 按一下 [**交易識別碼**] 方塊內部，讓動態內容清單出現。 從該清單中，選取從 [**傳送 IDOC** ] 動作傳回的 [**交易識別碼**] 值。

   ![確認交易識別碼動作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   執行此步驟之後，在 SAP 連接器端和 SAP 系統端上，目前的交易都會標示為「已完成」。

## <a name="known-issues-and-limitations"></a>已知問題和限制

以下是 SAP 連接器目前的已知問題和限制：

* SAP 觸發程序不支援資料閘道叢集。 在某些容錯移轉案例中，與 SAP 系統通訊的資料閘道節點可能會與使用中節點不同，這會導致非預期的行為。 針對傳送案例，支援資料閘道叢集。

* SAP 連接器目前不支援 SAP 路由器字串。 內部部署資料閘道必須和所要連線的 SAP 系統存在於相同 LAN 內。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，例如連接器的 Swagger 檔案所描述的觸發程式、動作和限制，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sap/)。

> [!NOTE]
> 對於[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式，此連接器的 ise 標記版本會使用[ISE 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 從 Azure Logic Apps[連接到內部部署系統](../logic-apps/logic-apps-gateway-connection.md)。
* 瞭解如何驗證、轉換和使用[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)的其他訊息作業。
* 深入瞭解其他[Logic Apps 連接器](../connectors/apis-list.md)。
