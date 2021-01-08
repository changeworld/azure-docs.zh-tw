---
title: 連接到 SQL Server、Azure SQL Database 或 Azure SQL 受控執行個體
description: 使用 Azure Logic Apps 將內部部署或雲端中的 SQL 資料庫工作自動化
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 75c657236b6e06a7e0f6c717d746bcc8c034d423
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013442"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 將 SQL database 的工作流程自動化

本文說明如何使用 SQL Server 連接器，從邏輯應用程式內部存取 SQL 資料庫中的資料。 如此您即可建立邏輯應用程式，以自動化用來管理 SQL 資料和資源的工作、流程或工作流程。 SQL Server 連接器適用于 [SQL Server](/sql/sql-server/sql-server-technical-documentation) 以及 [AZURE SQL DATABASE](../azure-sql/database/sql-database-paas-overview.md) 和 [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。

您可以建立會由 SQL 資料庫或其他系統 (例如，Dynamics CRM Online) 中的事件所觸發而執行的邏輯應用程式。 邏輯應用程式也可以取得、插入及刪除資料，還可執行 SQL 查詢或已儲存的程序。 例如，您可以建置邏輯應用程式，以自動檢查 Dynamics CRM Online 中是否有新記錄、將新記錄的項目新增至 SQL 資料庫，然後傳送有關新增項目的電子郵件警示。

如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需了解連接器專屬的技術資訊、限制和已知問題，請參閱 [SQL Server 連接器參考頁面](/connectors/sql/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [SQL Server 資料庫](/sql/relational-databases/databases/create-a-database)、 [AZURE SQL DATABASE](../azure-sql/database/single-database-create-quickstart.md)或[Azure SQL 受控執行個體](../azure-sql/managed-instance/instance-create-quickstart.md)。

  資料表內必須有資料，邏輯應用程式才能在呼叫作業時傳回結果。 如果您使用 Azure SQL Database，可以使用包含的範例資料庫。

* 伺服器名稱名稱、資料庫名稱、使用者名稱和密碼。 需要有這些認證，才能授權邏輯存取 SQL 伺服器。

  * 針對內部部署 SQL Server，您可以在連接字串中找到這些詳細資料：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 針對 Azure SQL Database，您可以在連接字串中找到這些詳細資料。
  
    例如，若要在 Azure 入口網站中尋找這個字串，請開啟您的資料庫。 在 [資料庫] 功能表上，選取 [ **連接字串** ] 或 [ **屬性**]：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* 根據您的邏輯應用程式是否要在全域、多租使用者 Azure 或 [整合服務環境中執行 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，以下是連接到內部部署 SQL Server 的其他需求：

  * 針對全域、多租使用者 Azure 中連線到內部部署 SQL Server 的邏輯應用程式，您需要在本機電腦上安裝內部 [部署資料閘道](../logic-apps/logic-apps-gateway-install.md) ，並在 [Azure 中建立資料閘道資源](../logic-apps/logic-apps-gateway-connection.md)。

  * 針對 ISE 中連接至內部部署 SQL Server 並使用 Windows 驗證的邏輯應用程式，ISE 版本設定的 SQL Server 連接器不支援 Windows 驗證。 因此，您仍然需要使用資料閘道和非 ISE SQL Server 連接器。 針對其他驗證類型，您不需要使用資料閘道，而且可以使用 ISE 版本設定的連接器。

* 需要存取 SQL 資料庫的邏輯應用程式。 若要使用 SQL 觸發程序啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>連接到您的資料庫

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

現在，請繼續進行下列步驟：

* [連接到雲端式 Azure SQL Database 或受控執行個體](#connect-azure-sql-db)
* [連接至內部部署 SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>連接到 Azure SQL Database 或受控執行個體

若要在不使用內部部署資料閘道或整合服務環境的情況下存取 Azure SQL 受控執行個體，您必須 [在 AZURE SQL 受控執行個體上設定公用端點](../azure-sql/managed-instance/public-endpoint-configure.md)。 公用端點會使用埠3342，因此當您從邏輯應用程式建立連線時，請確定您指定此埠號碼。


當您第一次新增 [sql 觸發](#add-sql-trigger) 程式或 [sql 動作](#add-sql-action)，但您尚未建立與資料庫的連線時，系統會提示您完成這些步驟：

1. 在 [ **驗證類型**] 中，選取您的資料庫在 Azure SQL Database 或 Azure SQL 受控執行個體中所需和啟用的驗證：

   | 驗證 | 描述 |
   |----------------|-------------|
   | [**Azure AD 整合式**](../azure-sql/database/authentication-aad-overview.md) | -支援非 ISE 和 ISE SQL Server 連接器。 <p><p>-需要具有資料庫存取權的 Azure Active Directory (Azure AD) 中的有效身分識別。 <p>如需詳細資訊，請參閱下列主題： <p>- [Azure SQL 安全性概觀-驗證](../azure-sql/database/security-overview.md#authentication) <br>- [授權資料庫存取 Azure SQL-驗證和授權](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL-Azure AD 整合式驗證](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server Authentication**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -支援非 ISE 和 ISE SQL Server 連接器。 <p><p>-需要在您的資料庫中建立並儲存有效的使用者名稱和強式密碼。 <p>如需詳細資訊，請參閱下列主題： <p>- [Azure SQL 安全性概觀-驗證](../azure-sql/database/security-overview.md#authentication) <br>- [授權資料庫存取 Azure SQL-驗證和授權](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   此範例會繼續 **Azure AD 整合** 式：

   ![顯示 [SQL Server] 連線視窗的螢幕擷取畫面，其中已選取 [驗證類型] 清單和 [Azure AD 整合]。](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. 選取 [ **Azure AD 整合**] 之後，請選取 [登 **入**]。 根據您是否使用 Azure SQL Database 或 Azure SQL 受控執行個體，請選取您的使用者認證以進行驗證。

1. 為您的資料庫選取下列值：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **伺服器名稱** | 是 | SQL server 的位址，例如 `Fabrikam-Azure-SQL.database.windows.net` |
   | **資料庫名稱** | 是 | SQL 資料庫的名稱，例如， `Fabrikam-Azure-SQL-DB` |
   | **資料表名稱** | 是 | 您要使用的資料表，例如 `SalesLT.Customer` |
   ||||

   > [!TIP]
   > 若要提供您的資料庫和資料表資訊，您可以使用下列選項：
   > 
   > * 在資料庫的連接字串中尋找此資訊。 例如，在 Azure 入口網站中，尋找並開啟您的資料庫。 在 [資料庫] 功能表上，選取 [ **連接字串** ] 或 [ **屬性**]，您可以在其中找到這個字串：
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * 根據預設，系統資料庫中的資料表會被篩選掉，因此當您選取系統資料庫時，可能不會自動顯示它們。 或者，您可以在從資料庫清單中選取 [ **輸入自訂值** ] 之後，手動輸入資料表名稱。
   >

   這個範例會顯示這些值的外觀：

   ![建立 SQL database 的連接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 現在，請繼續進行下列步驟： [新增 sql 觸發](#add-sql-trigger) 程式或 [新增 sql 動作](#add-sql-action)。

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>連接至內部部署 SQL Server

當您第一次新增 [sql 觸發](#add-sql-trigger) 程式或 [sql 動作](#add-sql-action)，但您尚未建立與資料庫的連線時，系統會提示您完成這些步驟：

1. 若要連接到需要內部部署資料閘道的內部部署 SQL server，請確定您已 [完成這些必要條件](#multi-tenant-or-ise)。

   否則，當您建立連線時，您的資料閘道資源將不會出現在連線 **閘道** 清單中。

1. 針對 [ **驗證類型**]，選取您的 SQL Server 所需且已啟用的驗證：

   | 驗證 | 描述 |
   |----------------|-------------|
   | [**Windows 驗證**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -僅支援非 ISE SQL Server 連接器，此連接器需要先前在 Azure 中為您的連線建立的資料閘道資源，無論您是使用多租使用者 Azure 或 ISE。 <p><p>-需要有效的 Windows 使用者名稱和密碼，才能透過您的 Windows 帳戶確認您的身分識別。 <p>如需詳細資訊，請參閱 [Windows 驗證](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server Authentication**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -支援非 ISE 和 ISE SQL Server 連接器。 <p><p>-需要在您的 SQL Server 中建立並儲存有效的使用者名稱和強式密碼。 <p>如需詳細資訊，請參閱 [SQL Server Authentication](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)。 |
   |||

   此範例會繼續進行 **Windows 驗證**：

   ![選取要使用的驗證類型](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. 為您的 SQL 資料庫選取或提供下列值：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **SQL server 名稱** | 是 | SQL server 的位址，例如 `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL 資料庫名稱** | 是 | SQL Server 資料庫的名稱，例如 `Fabrikam-Azure-SQL-DB` |
   | **使用者名稱** | 是 | SQL server 和資料庫的使用者名稱 |
   | **密碼** | 是 | SQL server 和資料庫的密碼 |
   | **訂用帳戶** |  是，適用于 Windows 驗證 | 您先前在 Azure 中建立之資料閘道資源的 Azure 訂用帳戶 |
   | **連接閘道** | 是，適用于 Windows 驗證 | 您先前在 Azure 中建立之資料閘道資源的名稱 <p><p>**秘訣**：如果您的閘道未出現在清單中，請檢查您是否已正確 [設定閘道](../logic-apps/logic-apps-gateway-connection.md)。 |
   |||

   > [!TIP]
   > 您可以在資料庫的連接字串中找到這項資訊：
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   這個範例會顯示這些值的外觀：

   ![建立 SQL Server 連接已完成](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 當您準備好時，選取 [建立]。

1. 現在，請繼續進行下列步驟： [新增 sql 觸發](#add-sql-trigger) 程式或 [新增 sql 動作](#add-sql-action)。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>新增 SQL 觸發程序

1. 在 [Azure 入口網站](https://portal.azure.com) 或 Visual Studio 中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。 此範例會繼續進行 Azure 入口網站。

1. 在設計工具的搜尋方塊中，輸入 `sql server` 。 從觸發程序清單中，選取您想要的 SQL 觸發程序。 此範例會在 **建立專案觸發程式時** 使用。

   ![選取「建立項目時」觸發程序](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果您是第一次連線到 SQL database，系統會提示您 [立即建立 sql database 連接](#create-connection)。 建立此連接之後，您可以繼續進行下一個步驟。

1. 在觸發程式中，指定觸發程式檢查資料表的間隔和頻率。

1. 若要加入此觸發程式的其他可用屬性，請開啟 [ **加入新的參數** ] 清單。

   此觸發程式只會傳回所選資料表中的一個資料列，不會傳回任何其他資料列。 若要執行其他工作，請在邏輯應用程式工作流程中新增 [SQL connector 動作](#add-sql-action) 或執行下一個工作的 [另一個動作](../connectors/apis-list.md) ，以繼續執行。

   例如，若要檢視此資料列中的資料，您可以新增其他動作來建立檔案，使其包含傳回資料列中的欄位，然後傳送電子郵件警示。 若要深入了解此連接器的其他可用動作，請參閱[連接器的參考頁面](/connectors/sql/)。

1. 在設計工具的工具列上，選取 [儲存]  。

   雖然這個步驟會在 Azure 中自動啟用併發布您的邏輯應用程式，但您的邏輯應用程式目前所採用的唯一動作，就是根據指定的間隔和頻率來檢查您的資料庫。

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>觸發迴圈移位和漂移

以連線為基礎的觸發程式，您必須先建立連接，例如 SQL 觸發程式，與在 Azure Logic Apps 中以原生方式執行的內建觸發程式不同，例如 [迴圈觸發](../connectors/connectors-native-recurrence.md)程式。 在週期性的以連線為基礎的觸發程式中，週期排程不是控制執行的唯一驅動程式，而時區只會決定初始開始時間。 後續的執行取決於週期排程、最後一個觸發程式執行， *以及* 可能造成執行時間漂移或產生非預期行為的其他因素，例如，當日光節約時間 (DST) 開始和結束時，不會維持指定的排程。 為了確保在 DST 生效時，週期時間不會轉移，請手動調整週期，讓邏輯應用程式在預期的時間繼續執行。 否則，開始時間會在 DST 開始時向前移動一小時，而當 DST 結束時，則會向後移動一小時。 如需詳細資訊，請參閱 [以連接為基礎之觸發程式的週期](../connectors/apis-list.md#recurrence-connection-based)。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>新增 SQL 動作

在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動，並且會呼叫動作以從 SQL 資料庫取得資料列。

1. 在 [Azure 入口網站](https://portal.azure.com) 或 Visual Studio 中，在邏輯應用程式設計工具中開啟邏輯應用程式。 此範例會繼續 Azure 入口網站。

1. 在您要新增 SQL 動作的觸發程序或動作底下，選取 [新增步驟]。

   ![將動作新增至邏輯應用程式](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   或者，若要在現有步驟之間新增動作，請將滑鼠移至連接箭號上方。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `sql server`。 從動作清單中，選取您想要的任何 SQL 動作。 此範例會使用 **取得資料列** 動作來取得單一記錄。

   ![選取 SQL 「取得資料列」動作](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. 如果您是第一次連線到 SQL database，系統會提示您 [立即建立 sql database 連接](#create-connection)。 建立此連接之後，您可以繼續進行下一個步驟。

1. 選取此範例中的 **資料表名稱** `SalesLT.Customer` 。 輸入您想要的記錄的資料 **列識別碼** 。

   ![選取資料表名稱並指定資料列識別碼](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   此動作只會傳回所選資料表的一個資料列而已。 因此，若要查看此資料列中的資料，您可以新增其他動作來建立包含所傳回資料列中欄位的檔案，並將該檔案儲存在雲端儲存體帳戶中。 若要深入了解此連接器的其他可用動作，請參閱[連接器的參考頁面](/connectors/sql/)。

1. 當您完成時，請在設計工具的工具列上，選取 [儲存]。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>處理大量資料

有時候，您必須處理的結果集很大，導致連接器無法一次傳回所有結果，或是您想要對結果集的大小和結構擁有更好的控制能力。 以下是用於處理這類大型結果集的一些方法：

* 為協助您將結果分成較小的集合來管理，請開啟 [分頁]。 如需詳細資訊，請參閱[使用分頁取得大量資料、記錄和項目](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。 如需詳細資訊，請參閱 [使用 Logic Apps 進行大量資料傳輸的 SQL 分頁](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)。

* 建立 [*預存*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 程式，以您想要的方式組織結果。 SQL connector 提供許多可利用 Azure Logic Apps 存取的後端功能，讓您可以更輕鬆地將使用 SQL database 資料表的商務工作自動化。

  在取得或插入多個資料列時，邏輯應用程式可在這些 [限制](../logic-apps/logic-apps-limits-and-config.md)內使用 [*until 迴圈*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)，來逐一查看這些資料列。 但是，當邏輯應用程式必須處理的記錄集很大時 (例如，數千或數百萬個資料列)，您應將資料庫的呼叫成本降至最低。

  為了以所需方式組織結果，您可以轉而建立預存程序，其可在 SQL 執行個體中執行，並使用 **SELECT - ORDER BY** 陳述式。 此解決方案可讓您對結果的大小和結構擁有更好的控制能力。 邏輯應用程式會使用 SQL Server 連接器的 **執行預存程序** 動作來呼叫預存程序。 如需詳細資訊，請參閱 [SELECT-ORDER By 子句](/sql/t-sql/queries/select-order-by-clause-transact-sql)。

  > [!NOTE]
  > SQL 連接器的預存程式超時限制 [小於2分鐘](/connectors/sql/#known-issues-and-limitations)。 某些預存程式可能需要超過此限制才能完成，因而導致 `504 Timeout` 錯誤。 您可以使用 SQL 完成觸發程式、原生 SQL 傳遞查詢、狀態資料表和伺服器端作業來解決此問題。
  > 
  > 針對這項工作，您可以使用適用于[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)的[Azure 彈性作業代理程式](../azure-sql/database/elastic-jobs-overview.md)。 針對 [SQL Server 內部部署](/sql/sql-server/sql-server-technical-documentation) 和 [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)，您可以使用 [SQL Server Agent](/sql/ssms/agent/sql-server-agent)。 若要深入瞭解，請參閱在 [SQL connector 中處理長時間執行的預存程式超時以進行 Azure Logic Apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md)。

### <a name="handle-dynamic-bulk-data"></a>處理動態大量資料

當您使用 SQL Server 連接器來呼叫預存程式時，傳回的輸出有時是動態的。 在此情況下，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 執行測試回合以查看輸出格式。 複製並儲存您的範例輸出。

1. 在設計工具中，於您呼叫預存程序的動作底下，選取 [新增步驟]。

1. 在 **[選擇動作**] 底下，尋找並選取 [ [**剖析 JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) ] 動作。

1. 在 **剖析 JSON** 動作中，選取 [使用範例承載來產生結構描述]。

1. 在 [ **輸入或貼上範例 JSON** 承載] 方塊中，貼上您的範例輸出，然後選取 [ **完成**]。

   > [!NOTE]
   > 如果您收到 Logic Apps 無法產生結構描述的錯誤，請檢查您輸出範例的語法格式是否正確。 如果您仍然無法產生架構，請在 [ **架構** ] 方塊中，手動輸入架構。

1. 在設計工具的工具列上，選取 [儲存]  。

1. 若要參考 JSON 內容屬性，請在您想要參考這些屬性的編輯方塊內按一下，以顯示動態內容清單。 在清單中的 [ [**剖析 json**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) ] 標題下，選取您要的 JSON 內容屬性的資料標記。

## <a name="troubleshoot-problems"></a>問題疑難排解

<a name="connection-problems"></a>

### <a name="connection-problems"></a>連線問題

連接問題通常可能發生，因此若要疑難排解和解決這類問題，請參閱 [解決連線錯誤以 SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)。 這裡有一些範例：

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需有關此連接器觸發程式、動作和限制的技術資訊，請參閱從 Swagger 描述產生的 [連接器參考頁面](/connectors/sql/)。

## <a name="next-steps"></a>後續步驟

* 了解 [Azure Logic Apps 的其他連接器](../connectors/apis-list.md)
