---
title: 連線至 Oracle Database
description: 使用 Oracle Database REST API 和 Azure Logic Apps 插入及管理記錄
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674818"
---
# <a name="get-started-with-the-oracle-database-connector"></a>開始使用 Oracle Database 連接器

透過 Oracle Database 連接器，您可以使用現有資料庫中的資料來建立組織工作流程。 此連接器可以連線至內部部署 Oracle Database，或是已安裝 Oracle Database 的 Azure 虛擬機器。 您可以使用此連接器來：

* 藉由在客戶資料庫中新增新客戶或在訂單資料庫中更新訂單，以建置工作流程。
* 使用動作來取得一列資料、插入新的資料列，甚至加以刪除。 例如，在 Dynamics CRM Online 中建立記錄時 (觸發程序)，就會在 Oracle Database 中插入資料列 (動作)。 

此連接器不支援下列項目：

* 檢視 
* 包含複合索引鍵的任何資料表
* 資料表中的巢狀物件類型
* 具有非純量值的資料庫函式

本文說明如何在邏輯應用程式中使用 Oracle Database 連接器。

## <a name="prerequisites"></a>Prerequisites

* 支援的 Oracle 版本： 
    * Oracle 9 和更新版本
    * Oracle 用戶端軟體 8.1.7 和更新版本

* 安裝內部部署資料閘道。 [從邏輯應用程式連線至內部部署資料](../logic-apps/logic-apps-gateway-connection.md)中會列出相關步驟。 您需要閘道或是已安裝 Oracle DB 的 Azure VM，才能連線至內部部署 Oracle Database。 

    > [!NOTE]
    > 內部部署資料閘道的角色如同橋接器，在內部部署資料 (不在雲端中的資料) 和邏輯應用程式之間提供安全的資料傳輸。 相同的閘道可以與多個服務，以及多個資料來源搭配使用。  因此，您只需要安裝一次閘道即可。

* 在您安裝內部部署資料閘道的機器上安裝 Oracle Client。  請務必從 Oracle 安裝 64 位元的 Oracle Data Provider for .NET：  

  [適用於 Windows x64 的 64 位元 ODAC 12c 版本 4 (12.1.0.2.4)](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > 若未安裝 Oracle 用戶端，當您嘗試建立或使用連線時會發生錯誤。 請參閱本文中常見的錯誤。


## <a name="add-the-connector"></a>新增連接器

> [!IMPORTANT]
> 此連接器並沒有任何觸發程序。 只有動作。 因此，當您建立邏輯應用程式時，請新增其他觸發程序以啟動您的邏輯應用程式，例如 **排程 - 循環** 或 **要求/回應 - 回應** 。 

1. 在 [Azure 入口網站](https://portal.azure.com) 中，建立空白的邏輯應用程式。

2. 在邏輯應用程式啟動時，選取 **要求/回應 - 要求** 觸發程序： 

    ![對話方塊中有一個方塊可搜尋所有觸發程式。 另外還有一個顯示 [要求/回應-要求] 的單一觸發程式，以及選取的按鈕。](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. 選取 [儲存]。 當您儲存時，系統會自動生產生一個要求 URL。 

4. 選取 [新增步驟]，再選取 [新增動作]。 輸入 `oracle` 以查看可用的動作： 

    ![搜尋方塊包含「oracle」。 搜尋會產生一個標示為 "Oracle Database" 的點擊。 有一個索引標籤式頁面，一個索引標籤顯示「觸發程式 (0) 」，另一個則顯示「動作 (6) 」。 列出六個動作。 其中第一個是「取得資料列預覽」。](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > 這也是查看連接器可使用的觸發程序和動作最快的方法。 輸入連接器的部分名稱，例如 `oracle`。 設計工具會列出所有觸發程序和動作。 

5. 選取其中一個動作，例如 [Oracle Database - 立即取得]。 選取 [透過內部部署資料閘道連線]。 輸入 Oracle 伺服器名稱、驗證方法、使用者名稱、密碼並選取閘道：

    ![對話方塊的標題為「Oracle Database-Get row」。 有一個方塊已核取，標示為「透過內部部署資料閘道連接」。 以下是五個其他文字方塊。](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. 連線之後，從清單中選取資料表，接著輸入資料表的資料列識別碼。 您必須知道資料表的識別碼。 如果您不知道，請連絡 Oracle DB 管理員，並從 `select * from yourTableName` 取得輸出資料。 這可讓您取得必要的可識別資料以繼續作業。

    在下列範例中，會從人力資源資料庫中傳回作業資料： 

    ![標題為 "Get row (Preview) " 的對話方塊有兩個文字方塊：「資料表名稱」，其中包含「H R 作業」且具有下拉式清單和「資料列 i d」，其中包含「S A _ REP」。](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. 在下一個步驟中，您可以使用其他任何連接器來建置您的工作流程。 如果您想要測試從 Oracle 取得資料，請使用其中一個傳送電子郵件連接器（例如 Office 365 Outlook），將包含 Oracle 資料的電子郵件傳送給自己。 使用 Oracle 資料表中的動態權杖來建置`Subject`以及您電子郵件的 `Body`：

    ![有兩個對話方塊。 [傳送電子郵件] 方塊中有方塊可指定電子郵件的「內文」、「主旨」和「寄件者」位址。 [新增動態內容] 對話方塊可讓您從流程的應用程式和服務搜尋動態內容。](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **儲存** 您的邏輯應用程式，接著選取 [執行]。 關閉設計工具，接著查看狀態的執行歷程記錄。 如果失敗，請選取失敗的訊息資料列。 設計工具隨即開啟，並顯示失敗的步驟，以及錯誤資訊。 如果成功，您應該會收到一封電子郵件，內含您新增的資訊。


### <a name="workflow-ideas"></a>工作流程想法

* 您希望能監視 #oracle 主題標籤，並在資料庫中放置推文以便查詢，並在其他應用程式中使用。 在邏輯應用程式中，新增 `Twitter - When a new tweet is posted` 觸發程序並輸入 **#oracle** 主題標籤。 接著，新增 `Oracle Database - Insert row` 動作，再選取您的資料表：

    ![「當張貼新的推文時」對話方塊會將「主題標籤」顯示為搜尋文字，並可讓您指定檢查頻率。 此對話方塊會導致 [Oracle Database] 對話方塊，讓您選取動作。](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* 訊息已傳送至服務匯流排佇列。 您希望取得這些訊息，並將其放在資料庫中。 在邏輯應用程式中，新增 `Service Bus - when a message is received in a queue` 觸發程序，並選取該佇列。 接著，新增 `Oracle Database - Insert row` 動作，再選取您的資料表：

    ![「收到訊息時 ...」對話方塊會將「訂單」顯示為「佇列名稱」，並可讓您指定檢查頻率。 此方塊會導致 [插入資料列 (預覽) ] 對話方塊，讓您選取 [資料表名稱]。](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>常見錯誤

#### <a name="error-cannot-reach-the-gateway"></a>**錯誤** ：無法連線到閘道

**原因** ︰內部部署資料閘道無法連線至雲端。 

**降低風險** ：確保您的閘道在安裝的內部部署機器中執行，且可連線至網際網路。    我們建議不要在可能關閉或休眠的電腦上安裝閘道。  您也可以重新啟動內部部署資料閘道服務 (PBIEgwService)。

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**錯誤** ：正在使用的提供者已被取代：「System.Data.OracleClient 需有 Oracle 用戶端軟體 version 8.1.7 或以上版本。」 請參閱 [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) 來安裝官方提供者。

**原因** ︰Oracle 用戶端 SDK 並未安裝在內部部署資料閘道執行的機器上。  

**解決方法** ：在與內部部署資料閘道相同的電腦上下載並安裝 Oracle 用戶端 SDK。

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**錯誤** ：資料表 '[Tablename]' 並未定義任何索引鍵資料行

**原因** ︰資料表沒有任何主要索引鍵。  

**解決方法** ：Oracle Database 連接器需要使用主要索引鍵資料行的資料表。
 
## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/oracle/)的所有限制。 

## <a name="get-some-help"></a>取得協助

若要提出問題、回答問題及了解其他 Logic Apps 使用者的近況，[Microsoft 的 Azure Logic Apps 問與答頁面](/answers/topics/azure-logic-apps.html)是最好的地方。 

您可以投票並在 [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) 中提交意見，協助改善 Logic Apps 和連接器。 


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)，並到 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。