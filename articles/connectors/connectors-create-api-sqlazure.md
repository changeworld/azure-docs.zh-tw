---
title: 連線至 SQL Server 或 Azure SQL Database
description: 使用 Azure Logic Apps 將內部部署或雲端中的 SQL 資料庫工作自動化
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: c32e17aaf83c233ad77bbbf607c30cc526253352
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402587"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 將 SQL Server 或 Azure SQL Database 的工作流程自動化

本文說明如何使用 SQL Server 連接器，從邏輯應用程式內部存取 SQL 資料庫中的資料。 如此您即可建立邏輯應用程式，以自動化用來管理 SQL 資料和資源的工作、流程或工作流程。 SQL Server 連接器適用於[內部部署 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) 和[雲端式 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。

您可以建立會由 SQL 資料庫或其他系統 (例如，Dynamics CRM Online) 中的事件所觸發而執行的邏輯應用程式。 邏輯應用程式也可以取得、插入及刪除資料，還可執行 SQL 查詢或已儲存的程序。 例如，您可以建置邏輯應用程式，以自動檢查 Dynamics CRM Online 中是否有新記錄、將新記錄的項目新增至 SQL 資料庫，然後傳送有關新增項目的電子郵件警示。

如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需了解連接器專屬的技術資訊、限制和已知問題，請參閱 [SQL Server 連接器參考頁面](https://docs.microsoft.com/connectors/sql/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [SQL Server 資料庫](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database)或 [Azure SQL 資料庫](../sql-database/sql-database-get-started-portal.md)

  資料表內必須有資料，邏輯應用程式才能在呼叫作業時傳回結果。 如果您建立的是 Azure SQL Database，則可以使用隨附的資料庫範例。

* 伺服器名稱名稱、資料庫名稱、使用者名稱和密碼。 需要有這些認證，才能授權邏輯存取 SQL 伺服器。

  * 如果是 SQL Server，可在連接字串中找到這些詳細資料：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 如果是 Azure SQL Database，可在連接字串中找到這些詳細資料，也可在 Azure 入口網站的 SQL Database 屬性下找到：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 安裝在本機電腦上的[內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)，以及[在 Azure 入口網站中建立的 Azure 資料閘道資源](../logic-apps/logic-apps-gateway-connection.md)，下列案例中會用到：

  * 您的邏輯應用程式不會在[整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中執行。

  * 您的邏輯應用程式「會」在整合服務環境中執行，但您必須將 Windows 驗證用於您的 SQL Server 連線。 針對此案例，請使用 SQL Server 連接器的非 ISE 版本以及資料閘道，因為 ISE 版本不支援 Windows 驗證。

* 需要存取 SQL 資料庫的邏輯應用程式。 若要使用 SQL 觸發程序啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>新增 SQL 觸發程序

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行邏輯應用程式的工作流程。

1. 在 Azure 入口網站或 Visual Studio 中，建立空白的邏輯應用程式，以開啟 Logic Apps 設計工具。 這個範例會使用 Azure 入口網站。

1. 在設計工具的搜尋方塊中，輸入 "sql server" 作為篩選條件。 從觸發程序清單中，選取您想要的 SQL 觸發程序。

   此範例會使用「**建立項目時**」觸發程序。

   ![選取「建立項目時」觸發程序](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果系統提示您建立連線，請[立即建立 SQL 連線](#create-connection)。 如果您的連線已存在，請選取**資料表名稱**。

   ![選取想要的資料表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 設定 [間隔] 和 [頻率] 屬性，以指定邏輯應用程式檢查資料表的頻率。

   此觸發程序只會傳回所選資料表的一個資料列而已。 若要執行其他工作，請新增其他動作來執行您想要的工作。 例如，若要檢視此資料列中的資料，您可以新增其他動作來建立檔案，使其包含傳回資料列中的欄位，然後傳送電子郵件警示。 若要深入了解此連接器的其他可用動作，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sql/)。

1. 當您完成時，請在設計工具的工具列上，選取 [儲存]。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>新增 SQL 動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動，並且會呼叫動作以從 SQL 資料庫取得資料列。

1. 在 Azure 入口網站或 Visual Studio 的邏輯應用程式設計工具中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

1. 在您要新增 SQL 動作的觸發程序或動作底下，選取 [新增步驟]。

   ![在邏輯應用程式中新增步驟](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 "sql server" 作為篩選條件。 從動作清單中，選取您想要的任何 SQL 動作。

   此範例會使用**取得資料列**動作來取得單一記錄。

   ![尋找並選取 SQL 的「取得資料列」動作](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   此動作只會傳回所選資料表的一個資料列而已。 若要檢視此資料列中的資料，請新增其他動作來建立包含傳回資料列中欄位的檔案，並將該檔案儲存在雲端儲存體帳戶中。 若要深入了解此連接器的其他可用動作，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sql/)。

1. 如果系統提示您建立連線，請[立即建立 SQL 連線](#create-connection)。 如果連線已存在，請選取 [資料表名稱]，並輸入您所要記錄的 [資料列識別碼]。

   ![輸入資料表名稱和資料列識別碼](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 當您完成時，請在設計工具的工具列上，選取 [儲存]。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>連接到您的資料庫

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>處理大量資料

有時候，您必須處理的結果集很大，導致連接器無法一次傳回所有結果，或是您想要對結果集的大小和結構擁有更好的控制能力。 以下是用於處理這類大型結果集的一些方法：

* 為協助您將結果分成較小的集合來管理，請開啟 [分頁]。 如需詳細資訊，請參閱[使用分頁取得大量資料、記錄和項目](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 建立預存程序，以您想要的方式來組織結果。

  在取得或插入多個資料列時，邏輯應用程式可在這些[限制](../logic-apps/logic-apps-limits-and-config.md)內使用 [*until 迴圈*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)，來逐一查看這些資料列。 但是，當邏輯應用程式必須處理的記錄集很大時 (例如，數千或數百萬個資料列)，您應將資料庫的呼叫成本降至最低。

  為了以所需方式組織結果，您可以轉而建立[*預存程序*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)，其可在 SQL 執行個體中執行，並使用 **SELECT - ORDER BY** 陳述式。 此解決方案可讓您對結果的大小和結構擁有更好的控制能力。 邏輯應用程式會使用 SQL Server 連接器的**執行預存程序**動作來呼叫預存程序。

  如需解決方案的詳細資訊，請參閱下列文章：

  * [可供使用 Logic Apps 傳輸大量資料的 SQL 分頁](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 子句](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>處理動態大量資料

有時候，當您在 SQL Server 連接器中呼叫預存程序時，傳回的輸出會是動態的。 在此情況下，請遵循下列步驟：

1. 開啟 [Logic Apps 設計工具]。
1. 執行邏輯應用程式的測試回合，以查看輸出格式。 複製您的輸出範例。
1. 在設計工具中，於您呼叫預存程序的動作底下，選取 [新增步驟]。
1. 在 [選擇動作] 中，搜尋並選取[**剖析 JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 動作。
1. 在**剖析 JSON** 動作中，選取 [使用範例承載來產生結構描述]。
1. 在 [輸入或貼上 JSON 承載範例] 視窗中，貼上您的輸出範例，然後選取 [完成]。
1. 如果您收到 Logic Apps 無法產生結構描述的錯誤，請檢查您輸出範例的語法格式是否正確。 如果您仍然無法產生結構描述，請在 [結構描述] 方塊中手動輸入結構描述。
1. 在設計工具的工具列上，選取 [儲存]。
1. 若要存取 JSON 內容屬性，請使用資料權杖，其會顯示在[**剖析 JSON** 動作](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)底下的動態內容清單中。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需此連接器的觸發程序、動作和限制的技術資訊，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sql/)。

## <a name="next-steps"></a>後續步驟

* 了解 [Azure Logic Apps 的其他連接器](../connectors/apis-list.md)
