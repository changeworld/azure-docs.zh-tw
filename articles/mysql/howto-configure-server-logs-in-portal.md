---
title: 存取慢速查詢記錄-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何從 Azure 入口網站設定和存取適用於 MySQL 的 Azure 資料庫中的慢速記錄。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: 5ad4ffa99a7af592e3e93e53673d254956807c40
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541618"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>從 Azure 入口網站設定及存取慢速查詢記錄

您可以從 Azure 入口網站設定、列出和下載 [適用於 MySQL 的 Azure 資料庫慢速查詢記錄](concepts-server-logs.md) 。

## <a name="prerequisites"></a>必要條件
本文中的步驟要求您必須有 [適用於 MySQL 的 Azure 資料庫 server](quickstart-create-mysql-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定 MySQL 慢速查詢記錄的存取。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取適用於 MySQL 的 Azure 資料庫伺服器。

3. 在提要欄位的 [ **監視** ] 區段下，選取 [ **伺服器記錄** ]。 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="[伺服器記錄檔] 選項的螢幕擷取畫面":::

4. 若要查看伺服器參數，請選取 [ **按一下這裡啟用記錄] 和 [設定記錄參數** ]。

5. 開啟 **slow_query_log** ] **。**

6. 使用 **log_output** 選取要輸出記錄檔的位置。 若要將記錄傳送至本機儲存體和 Azure 監視器診斷記錄， **請選取 [** 檔案]。 

7. 變更所需的任何其他參數。 

8. 選取 [儲存]。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="慢速查詢記錄參數和儲存的螢幕擷取畫面。":::

在 [ **伺服器參數** ] 頁面上，您可以藉由關閉頁面來返回記錄清單。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
開始記錄之後，您可以查看可用的慢速查詢記錄清單，並下載個別的記錄檔。

1. 開啟 Azure 入口網站。

2. 選取適用於 MySQL 的 Azure 資料庫伺服器。

3. 在提要欄位的 [ **監視** ] 區段下，選取 [ **伺服器記錄** ]。 頁面會顯示您的記錄檔清單。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="[伺服器記錄] 頁面的螢幕擷取畫面，其中已醒目提示記錄清單":::

   > [!TIP]
   > 記錄的命名慣例為 **mysql-slow-< your server name>-yyyymmddhh.log** 。 檔案名中使用的日期和時間是發出記錄檔的時間。 記錄檔會每24小時或 7.5 GB 旋轉一次，視何者較早。 

4. 如有需要，請使用 [搜尋] 方塊，根據日期和時間快速縮小至特定的記錄。 搜尋是根據記錄的名稱進行。

5. 若要下載個別記錄檔，請選取表格資料列中每個記錄檔旁邊的向下箭號圖示。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="[伺服器記錄] 頁面的螢幕擷取畫面，反白顯示向下箭號圖示":::

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在提要欄位的 [ **監視** ] 區段下，選取 [ **診斷設定**  >  **新增診斷設定** ]。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="診斷設定選項的螢幕擷取畫面":::

1. 提供診斷設定名稱。

1. 指定要將慢速查詢記錄傳送 (儲存體帳戶、事件中樞或 Log Analytics 工作區) 的資料接收。

1. 選取 [ **MySqlSlowLogs** ] 做為記錄類型。
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="診斷設定選項的螢幕擷取畫面":::

1. 設定好將慢速查詢記錄輸送至的資料接收器之後，請選取 [ **儲存** ]。
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="[診斷設定] 設定選項的螢幕擷取畫面，其中反白顯示 [儲存]":::

1. 存取慢速查詢記錄，方法是在您設定的資料接收器中加以探索。 記錄最多可能需要10分鐘的時間才會出現。

## <a name="next-steps"></a>後續步驟
- 請參閱 [在 CLI 中存取慢速查詢記錄](howto-configure-server-logs-in-cli.md) ，以瞭解如何以程式設計方式下載慢速查詢記錄。
- 深入瞭解適用於 MySQL 的 Azure 資料庫中的 [慢速查詢記錄](concepts-server-logs.md) 。
- 如需有關參數定義和 MySQL 記錄的詳細資訊，請參閱 MySQL 檔中的 [記錄](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。