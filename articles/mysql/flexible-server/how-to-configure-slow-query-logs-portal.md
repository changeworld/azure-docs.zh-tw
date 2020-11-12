---
title: 設定慢速查詢記錄-Azure 入口網站-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何從 Azure 入口網站設定和存取適用於 MySQL 的 Azure 資料庫彈性伺服器中的慢速查詢記錄。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540058"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 入口網站設定和存取適用於 MySQL 的 Azure 資料庫彈性伺服器的慢速查詢記錄

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

您可以從 Azure 入口網站設定、列出和下載適用於 MySQL 的 Azure 資料庫具彈性的伺服器 [慢速查詢記錄](concepts-slow-query-logs.md) 。

## <a name="prerequisites"></a>必要條件
本文中的步驟需要您擁有有 [彈性的伺服器](quickstart-create-server-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定 MySQL 慢速查詢記錄的存取。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取您的彈性伺服器。

1. 在 [ **設定** ] 區段的側邊欄中，選取 [ **伺服器參數** ]。
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="[伺服器參數] 頁面。":::

1. 將 **slow_query_log** 參數更新為 **ON** 。
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="開啟慢速查詢記錄。":::

1. 變更所需的任何其他參數 (例如 `long_query_time`, `log_slow_admin_statements`). 如需更多參數，請參閱 [慢速查詢記錄](./concepts-slow-query-logs.md#configure-slow-query-logging) 檔。  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="更新慢速查詢記錄檔的相關參數。":::

1. 選取 [儲存]。 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="儲存慢速查詢記錄參數。":::

在 [ **伺服器參數** ] 頁面上，您可以藉由關閉頁面來返回記錄清單。

## <a name="set-up-diagnostics"></a>設定診斷

慢速查詢記錄會與 Azure 監視器診斷設定整合，讓您可以使用管線將記錄傳送至 Azure 監視器記錄、事件中樞或 Azure 儲存體。

1. 在提要欄位的 [ **監視** ] 區段下，選取 [ **診斷設定**  >  **新增診斷設定** ]。

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="診斷設定選項的螢幕擷取畫面":::

1. 提供診斷設定名稱。

1. 指定要將慢速查詢記錄傳送 (儲存體帳戶、事件中樞或 Log Analytics 工作區) 的目的地。

1. 選取 [ **MySqlSlowLogs** ] 做為記錄類型。
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="診斷設定選項的螢幕擷取畫面":::

1. 設定好將慢速查詢記錄輸送至的資料接收器之後，請選取 [ **儲存** ]。
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="[診斷設定] 設定選項的螢幕擷取畫面，其中反白顯示 [儲存]":::

1. 存取慢速查詢記錄，方法是在您設定的資料接收器中加以探索。 記錄最多可能需要10分鐘的時間才會出現。

如果您使用管線將記錄傳送至 Azure 監視器記錄 (Log Analytics) ，請參閱您可以用來進行分析的一些 [範例查詢](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) 。 

## <a name="next-steps"></a>後續步驟
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- 深入瞭解 [查詢記錄緩慢](concepts-slow-query-logs.md)
- 如需有關參數定義和 MySQL 記錄的詳細資訊，請參閱 MySQL 檔中的 [記錄](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。
- 瞭解 [audit 記錄](concepts-audit-logs.md)
