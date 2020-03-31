---
title: 連接到 IBM Informix 資料庫
description: 使用 Azure 邏輯應用自動執行管理存儲在 IBM Informix 中的資源的任務和工作流
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757963"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用管理 IBM Informix 資料庫資源

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和[Informix 連接器](/connectors/informix/)，您可以創建自動任務和工作流來管理 IBM Informix 資料庫中的資源。 此連接器包括 Microsoft 用戶端，該用戶端通過 TCP/IP 網路與遠端 Informix 伺服器電腦通信，包括基於雲的資料庫，如在 Azure 虛擬化中運行的 Windows 的 IBM Informix 和本機資料庫，當您使用[本地資料閘道](../logic-apps/logic-apps-gateway-connection.md)時。 如果這些 Informix 平臺和版本配置為支援分散式關係資料庫體系結構 （DRDA） 用戶端連接，則可以連接到這些平臺和版本：

* IBM Informix 12.1
* IBM Informix 11.7

本主題說明如何在邏輯應用程式中使用連接器來處理資料庫作業。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 對於本機資料庫，請在本地電腦上[下載並安裝本地資料閘道](../logic-apps/logic-apps-gateway-install.md)，然後在[Azure 門戶中創建 Azure 資料閘道資源](../logic-apps/logic-apps-gateway-connection.md)。

* 需要訪問 Informix 資料庫的邏輯應用。 此連接器僅提供操作，因此邏輯應用必須從觸發器開始，例如["定期觸發器](../connectors/connectors-native-recurrence.md)"。 

## <a name="add-an-informix-action"></a>添加 Informix 操作

1. 在 [Azure 入口網站](https://portal.azure.com)中，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在要添加 Informix 操作的步驟下，選擇 **"新建步驟**"。

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 `informix` 作為篩選條件。 從動作清單中選擇所需的操作，例如：

   ![選擇要運行的 Informix 操作](./media/connectors-create-api-informix/select-informix-connector-action.png)

   連接器提供以下操作，這些操作運行相應的資料庫操作：

   * 獲取表 - 使用 語句`CALL`列出資料庫表
   * 獲取行 - 使用語句`SELECT *`讀取所有行
   * 獲取行 - 使用語句`SELECT WHERE`讀取行
   * 使用`INSERT`語句添加行
   * 使用`UPDATE`語句編輯行
   * 使用`DELETE`語句刪除行

1. 如果系統提示您為 Informix 資料庫提供連接詳細資訊，請按照[步驟創建連接](#create-connection)，然後繼續執行下一步。

1. 提供所選操作的資訊：

   | 動作 | 描述 | 屬性和說明 |
   |--------|-------------|-----------------------------|
   | **取得資料表** | 通過運行 Informix CALL 語句列出資料庫表。 | None |
   | **取得多個資料列** | 通過運行 Informix`SELECT *`語句獲取指定表中的所有行。 | **表名稱**：所需的 Informix 表的名稱 <p><p>要向此操作添加其他屬性，請從 **"添加新參數**"清單中選擇它們。 有關詳細資訊，請參閱[連接器的參考主題](/connectors/informix/)。 |
   | **取得單一資料列** | 通過運行 Informix`SELECT WHERE`語句從指定表獲取行。 | - **表名稱**：所需的 Informix 表的名稱 <br>- **行 ID**：例如，行的唯一 ID，`9999` |
   | **插入資料列** | 通過運行 Informix 語句將行添加到指定的 Informix`INSERT`表。 | - **表名稱**：所需的 Informix 表的名稱 <br>- **項**： 具有要添加的值的行 |
   | **更新資料列** | 通過運行 Informix 語句更改指定 Informix`UPDATE`表中的行。 | - **表名稱**：所需的 Informix 表的名稱 <br>- **行 ID**：要更新的行的唯一 ID，例如，`9999` <br>- **行**：具有更新值的行，例如，`102` |
   | **刪除行** | 通過運行 Informix 語句從指定的 Informix`DELETE`表中刪除行。 | - **表名稱**：所需的 Informix 表的名稱 <br>- **行 ID**：要刪除的行的唯一 ID，例如，`9999` |
   ||||

1. 儲存您的邏輯應用程式。 現在，[測試邏輯應用](#test-logic-app)或繼續構建邏輯應用。

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>連接到 Informix

1. 如果邏輯應用連接到本機資料庫，請選擇 **"通過本地資料閘道連接**"。

1. 提供此連接資訊，然後選擇 **"創建**"。

   | 屬性 | JSON 屬性 | 必要 | 範例值 | 描述 |
   |----------|---------------|----------|---------------|-------------|
   | 連接名稱 | `name` | 是 | `informix-demo-connection` | 用於連接到 Informix 資料庫的名稱 |
   | 伺服器 | `server` | 是 | - 雲：`informixdemo.cloudapp.net:9089` <br>- 本地：`informixdemo:9089` | 採用 IPv4 或 IPv6 格式的 TCP/IP 位址或別名，後跟冒號和 TCP/IP 埠號 |
   | 資料庫 | `database` | 是 | `nwind` | DRDA 關係資料庫名稱 （RDBNAM） 或 Informix 資料庫名稱 （dbname）。 Informix 接受 128 位元組字串。 |
   | 驗證 | `authentication` | 僅限本地 | **基本**或**視窗**（kerberos） | Informix 資料庫所需的身份驗證類型。 僅當選擇**通過本地資料閘道進行連接**時，才會顯示此屬性。 |
   | 使用者名稱 | `username` | 否 | <*資料庫使用者名*> | 資料庫的使用者名 |
   | 密碼 | `password` | 否 | <*資料庫密碼*> | 資料庫的密碼 |
   | 閘道 | `gateway` | 僅限本地 | - <*Azure 訂閱*> <br>- <*Azure 本地資料閘道資源*> | 在 Azure 門戶中創建的本地資料閘道的 Azure 訂閱和 Azure 資源名稱。 僅當選擇**通過本地資料閘道進行連接**時，才會顯示**閘道**屬性和子屬性。 |
   ||||||

   例如：

   * **雲資料庫**

     ![雲資料庫連接資訊](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **本機資料庫**

     ![本機資料庫連接資訊](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. 儲存您的邏輯應用程式。

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在邏輯應用設計器工具列上，選擇 **"運行**"。 邏輯應用運行後，您可以查看該運行中的輸出。

1. 從邏輯應用的功能表中，選擇 **"概述**"。 在"概述"窗格中，在 **"摘要** > **執行歷程記錄**"下，選擇最近的運行。

1. 在**邏輯應用運行**下，選擇 **"運行詳細資訊**"。

1. 從動作清單中，選擇具有要查看的輸出的操作，例如 **，Get_tables**。

   如果操作成功，則其**狀態**屬性將標記為 **"成功**"。

1. 要查看輸入，請在**輸入連結**下，選擇 URL 連結。 要查看輸出，請在 **"輸出連結"** 下，選擇 URL 連結。 下面是一些示例輸出：

   * **Get_tables**顯示表清單：

     !["獲取表"操作中的輸出](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows**顯示行清單：

     ![來自"獲取行"操作的輸出](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row**顯示指定的行：

     !["獲取行"操作的輸出](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row**顯示新行：

     !["插入行"操作的輸出](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row**顯示更新的行：

     !["更新行"操作的輸出](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row**顯示已刪除的行：

     !["刪除行"操作的輸出](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>連接器特定的詳細資料

有關觸發器、操作和限制的技術詳細資訊（由連接器的 Swagger 描述）描述，請查看[連接器的參考頁](/connectors/informix/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](apis-list.md)
