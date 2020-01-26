---
title: 連接到 IBM Informix 資料庫
description: 使用 Azure Logic Apps，將管理儲存在 IBM Informix 中之資源的工作和工作流程自動化
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76757963"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 管理 IBM Informix 資料庫資源

使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和[Informix 連接器](/connectors/informix/)，您可以建立自動化的工作和工作流程，以管理 IBM Informix 資料庫中的資源。 此連接器包含一個 Microsoft 用戶端，可透過 TCP/IP 網路與遠端 Informix 伺服器電腦通訊，包括雲端式資料庫（例如適用于在 Azure 虛擬化中執行的 IBM Informix for Windows），以及當您使用內部[部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)時的內部部署資料庫。 若已設定為支援分散式關係資料庫架構（DRDA）用戶端連線，您可以連接到這些 Informix 平臺和版本：

* IBM Informix 12.1
* IBM Informix 11.7

本主題說明如何在邏輯應用程式中使用連接器來處理資料庫作業。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 針對內部部署資料庫，請在本機電腦上[下載並安裝內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)，然後[在 Azure 入口網站中建立 Azure 資料閘道資源](../logic-apps/logic-apps-gateway-connection.md)。

* 您需要存取 Informix 資料庫的邏輯應用程式。 此連接器只會提供動作，因此您的邏輯應用程式必須已啟動觸發程式，例如，[週期觸發](../connectors/connectors-native-recurrence.md)程式。 

## <a name="add-an-informix-action"></a>新增 Informix 動作

1. 在 [Azure 入口網站](https://portal.azure.com)中，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在您要新增 Informix 動作的步驟中，選取 [**新增步驟**]。

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選取顯示的加號（ **+** ），然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入 `informix` 作為篩選條件。 從 [動作] 清單中，選取您想要的動作，例如：

   ![選取要執行的 Informix 動作](./media/connectors-create-api-informix/select-informix-connector-action.png)

   連接器會提供這些動作，以執行對應的資料庫作業：

   * 取得資料表-使用 `CALL` 語句列出資料庫資料表
   * 取得資料列-使用 `SELECT *` 語句讀取所有資料列
   * 使用 `SELECT WHERE` 語句取得資料列讀取資料列
   * 使用 `INSERT` 語句加入資料列
   * 使用 `UPDATE` 語句來編輯資料列
   * 使用 `DELETE` 語句來刪除資料列

1. 如果系統提示您提供 Informix 資料庫的連線詳細資料，請遵循建立連線的[步驟](#create-connection)，然後繼續下一個步驟。

1. 提供所選動作的資訊：

   | 行動 | 說明 | 屬性和描述 |
   |--------|-------------|-----------------------------|
   | **取得資料表** | 藉由執行 Informix CALL 語句來列出資料庫資料表。 | 無 |
   | **取得資料列** | 藉由執行 Informix `SELECT *` 語句，提取指定之資料表中的所有資料列。 | **資料表名稱**：您想要的 Informix 資料表名稱 <p><p>若要將其他屬性新增至此動作，請從 [**加入新的參數**] 清單中選取它們。 如需詳細資訊，請參閱[連接器的參考主題](/connectors/informix/)。 |
   | **取得資料列** | 藉由執行 Informix `SELECT WHERE` 語句，從指定的資料表提取一個資料列。 | - **資料表名稱**：您想要的 Informix 資料表名稱 <br>- 資料**列識別碼**：資料列的唯一識別碼，例如，`9999` |
   | **插入資料列** | 藉由執行 Informix `INSERT` 語句，將資料列加入至指定的 Informix 資料表。 | - **資料表名稱**：您想要的 Informix 資料表名稱 <br>- **專案**：包含要加入之值的資料列 |
   | **更新資料列** | 藉由執行 Informix `UPDATE` 語句來變更指定的 Informix 資料表中的資料列。 | - **資料表名稱**：您想要的 Informix 資料表名稱 <br>- 資料**列識別碼**：要更新之資料列的唯一識別碼，例如，`9999` <br>- 的資料**列**：具有更新值的資料列，例如，`102` |
   | **刪除資料列** | 藉由執行 Informix `DELETE` 語句，從指定的 Informix 資料表中移除資料列。 | - **資料表名稱**：您想要的 Informix 資料表名稱 <br>- 資料**列識別碼**：要刪除之資料列的唯一識別碼，例如，`9999` |
   ||||

1. 儲存您的邏輯應用程式。 現在，請[測試您的邏輯應用程式](#test-logic-app)，或繼續建立邏輯應用程式。

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>連接到 Informix

1. 如果您的邏輯應用程式連接到內部部署資料庫，請選取 [透過內部**部署資料網**關聯機]。

1. 提供此連接資訊，然後選取 [**建立**]。

   | 屬性 | JSON 屬性 | 必要項 | 範例值 | 說明 |
   |----------|---------------|----------|---------------|-------------|
   | 連接名稱 | `name` | 是 | `informix-demo-connection` | 要用於連線至 Informix 資料庫的名稱 |
   | 伺服器 | `server` | 是 | -雲端： `informixdemo.cloudapp.net:9089` <br>-內部部署： `informixdemo:9089` | 採用 IPv4 或 IPv6 格式的 TCP/IP 位址或別名，後面接著冒號和 TCP/IP 埠號碼 |
   | 資料庫 | `database` | 是 | `nwind` | DRDA 關係資料庫名稱（RDBNAM）或 Informix 資料庫名稱（dbname）。 Informix 接受128位元組的字串。 |
   | 驗證 | `authentication` | 僅內部部署 | **基本**或**Windows** （kerberos） | 您的 Informix 資料庫所需的驗證類型。 只有當您選取 [透過內部**部署資料閘道**進行連線] 時，才會出現此屬性。 |
   | 使用者名稱 | `username` | 否 | <*資料庫-使用者名稱*> | 資料庫的使用者名稱 |
   | 密碼 | `password` | 否 | <*資料庫-密碼*> | 資料庫的密碼 |
   | 閘道 | `gateway` | 僅內部部署 | -<*Azure-訂*用帳戶> <br>-<*Azure-內部部署-資料閘道-資源*> | 您在 Azure 入口網站中建立之內部部署資料閘道的 Azure 訂用帳戶和 Azure 資源名稱。 只有當您選取 [透過內部**部署資料閘道連線]** 時，才會顯示**閘道**屬性和子屬性。 |
   ||||||

   例如：

   * **雲端資料庫**

     ![雲端資料庫連接資訊](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **內部部署資料庫**

     ![內部部署資料庫連接資訊](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. 儲存您的邏輯應用程式。

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在邏輯應用程式設計工具工具列上，選取 [**執行**]。 執行邏輯應用程式之後，您可以從該執行中查看輸出。

1. 從邏輯應用程式的功能表中，選取 **[總覽**]。 在 [總覽] 窗格的 [**摘要** > **執行歷程記錄**] 底下，選取最新的執行。

1. 在 [**邏輯應用程式執行**] 底下，選取 [**執行詳細資料**]。

1. 從 [動作] 清單中，選取包含您想要查看之輸出的動作，例如**Get_tables**。

   如果動作成功，其 [**狀態**] 屬性會標示為 [**成功**]。

1. 若要查看輸入，請在 [**輸入連結**] 底下，選取 [URL] 連結。 若要查看輸出，請在 [**輸出連結**連結] 底下，選取 [URL] 連結。 以下是一些範例輸出：

   * **Get_tables**會顯示資料表清單：

     ![來自「取得資料表」動作的輸出](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows**顯示資料列清單：

     ![[取得資料列] 動作的輸出](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row**顯示指定的資料列：

     ![從「取得資料列」動作輸出](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row**會顯示新的資料列：

     ![[插入資料列] 動作的輸出](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row**會顯示更新的資料列：

     ![[更新資料列] 動作的輸出](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row**會顯示已刪除的資料列：

     ![[刪除資料列] 動作的輸出](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需有關觸發程式、動作和限制的技術詳細資訊（由連接器的 Swagger 描述所描述），請參閱[連接器的參考頁面](/connectors/informix/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](apis-list.md)
