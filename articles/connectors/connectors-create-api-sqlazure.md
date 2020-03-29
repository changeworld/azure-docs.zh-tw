---
title: 連接到 SQL 伺服器或 Azure SQL 資料庫
description: 使用 Azure 邏輯應用自動執行本地或雲中的 SQL 資料庫任務
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789185"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用自動執行 SQL 伺服器或 Azure SQL 資料庫的工作流

本文說明如何使用 SQL Server 連接器，從邏輯應用程式內部存取 SQL 資料庫中的資料。 這樣，您可以通過創建邏輯應用自動執行管理 SQL 資料和資源的任務、流程或工作流。 SQL Server 連接器適用于[本地 SQL 伺服器](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation)和基於雲的[Azure SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。

您可以建立會由 SQL 資料庫或其他系統 (例如，Dynamics CRM Online) 中的事件所觸發而執行的邏輯應用程式。 邏輯應用還可以獲取、插入和刪除資料以及運行 SQL 查詢和預存程序。 例如，可以構建一個邏輯應用，該應用自動檢查 Dynamics CRM Online 中的新記錄，將任何新記錄的專案添加到 SQL 資料庫中，然後發送有關添加項的電子郵件警報。

如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 有關特定于連接器的技術資訊、限制和已知問題，請參閱 SQL [Server 連接器參考頁](https://docs.microsoft.com/connectors/sql/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [SQL 伺服器資料庫](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database)或[Azure SQL 資料庫](../sql-database/sql-database-get-started-portal.md)

  資料表內必須有資料，邏輯應用程式才能在呼叫作業時傳回結果。 如果您建立的是 Azure SQL Database，則可以使用隨附的資料庫範例。

* 伺服器名稱名稱、資料庫名稱、使用者名稱和密碼。 需要有這些認證，才能授權邏輯存取 SQL 伺服器。

  * 如果是 SQL Server，可在連接字串中找到這些詳細資料：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 如果是 Azure SQL Database，可在連接字串中找到這些詳細資料，也可在 Azure 入口網站的 SQL Database 屬性下找到：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 本地電腦上安裝[的本地資料閘道](../logic-apps/logic-apps-gateway-install.md)和[Azure 門戶中為這些方案創建的 Azure 資料閘道資源](../logic-apps/logic-apps-gateway-connection.md)：

  * 邏輯應用不在[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中運行。

  * 邏輯應用*確實*在整合服務環境中運行，但您必須為 SQL Server 連接使用 Windows 身份驗證。 對於此方案，請使用 SQL Server 連接器的非 ISE 版本以及資料閘道，因為 ISE 版本不支援 Windows 身份驗證。

* 需要存取 SQL 資料庫的邏輯應用程式。 若要使用 SQL 觸發程序啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>添加 SQL 觸發器

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次觸發觸發器時，邏輯應用引擎都會創建一個邏輯應用實例，並開始運行邏輯應用的工作流。

1. 在 Azure 入口網站或 Visual Studio 中，建立空白的邏輯應用程式，以開啟 Logic Apps 設計工具。 這個範例會使用 Azure 入口網站。

1. 在設計器上，在搜索框中，輸入"sql 伺服器"作為篩選器。 從觸發程序清單中，選取您想要的 SQL 觸發程序。

   此示例使用 **"創建專案時觸發器**"。

   ![選擇"創建專案時"觸發器](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果系統提示您創建連接，[請立即創建 SQL 連接](#create-connection)。 如果連接存在，請選擇**表名稱**。

   ![選擇所需的表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 設定 [間隔]**** 和 [頻率]**** 屬性，以指定邏輯應用程式檢查資料表的頻率。

   此觸發器僅從所選表中返回一行，沒有其他。 要執行其他任務，添加執行所需任務的其他操作。 例如，要查看此行中的資料，可以添加其他操作，以創建包含返回行中的欄位的檔，然後發送電子郵件警報。 要瞭解此連接器的其他可用操作，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/sql/)。

1. 完成後，在設計器工具列上，選擇 **"保存**"。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>添加 SQL 操作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動，並且會呼叫動作以從 SQL 資料庫取得資料列。

1. 在 Azure 入口網站或 Visual Studio 的邏輯應用程式設計工具中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

1. 在要添加 SQL 操作的觸發器或操作下，選擇 **"新建步驟**"。

   ![向邏輯應用添加新步驟](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜索框中**選擇操作**，在搜索框中輸入"sql Server"作為篩選器。 從動作清單中選擇所需的 SQL 操作。

   此示例使用**Get 行**操作，該操作獲取單個記錄。

   ![查找並選擇 SQL"獲取行"操作](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   此操作僅從所選表中返回一行，沒有其他該行。 要查看此行中的資料，可以添加其他操作，以創建包含返回行中的欄位的檔，並將該檔存儲在雲存儲帳戶中。 要瞭解此連接器的其他可用操作，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/sql/)。

1. 如果系統提示您創建連接，[請立即創建 SQL 連接](#create-connection)。 如果連接存在，請選擇**表名稱**，然後輸入所需記錄的**行 ID。**

   ![輸入資料表名稱和資料列識別碼](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 完成後，在設計器工具列上，選擇 **"保存**"。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>連接到您的資料庫

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>處理批量資料

有時，必須處理結果集，以便連接器不會同時返回所有結果，或者希望更好地控制結果集的大小和結構。 以下是處理此類大型結果集的一些方法：

* 要説明您以較小的集管理結果，請打開*分頁*。 有關詳細資訊，請參閱[使用分頁獲取批量資料、記錄和專案](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 創建預存程序，以您想要的方式組織結果。

  獲取或插入多行時，邏輯應用可以使用這些[限制](../logic-apps/logic-apps-limits-and-config.md)中的[*直到迴圈*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)來反覆運算這些行。 但是，當邏輯應用必須處理如此大的記錄集（例如數千行或數百萬行）時，您希望將調用資料庫所產生的成本降至最低。

  要按所需方式組織結果，可以創建在 SQL 實例中運行並使用**SELECT - ORDER BY**語句的[*預存程序*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)。 此解決方案可讓您對結果的大小和結構擁有更好的控制能力。 邏輯應用程式會使用 SQL Server 連接器的**執行預存程序**動作來呼叫預存程序。

  有關更多解決方案詳細資訊，請參閱以下文章：

  * [可供使用 Logic Apps 傳輸大量資料的 SQL 分頁](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 子句](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>連接器特定的詳細資料

有關此連接器的觸發器、操作和限制的技術資訊，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/sql/)。

## <a name="next-steps"></a>後續步驟

* 瞭解[Azure 邏輯應用的其他連接器](../connectors/apis-list.md)
