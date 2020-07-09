---
title: 存取緩慢查詢記錄-Azure 入口網站適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何在 Azure 入口網站的適用於 MariaDB 的 Azure 資料庫中設定和存取緩慢查詢記錄。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: a2642ebbad7c8d7a2e092a6e00929e32b72b52d8
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104974"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>從 Azure 入口網站設定和存取適用于 Maria DB 緩慢查詢記錄的 Azure 資料庫

您可以從 Azure 入口網站設定、列出和下載[適用於 MariaDB 的 Azure 資料庫慢速查詢記錄](concepts-server-logs.md)。

## <a name="prerequisites"></a>必要條件
本文中的步驟需要您擁有[適用於 MariaDB 的 Azure 資料庫 server](quickstart-create-mariadb-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定慢速查詢記錄的存取。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

3. 在提要欄位的 [**監視**] 區段下，選取 [**伺服器記錄**]。 
   ![伺服器記錄選項的螢幕擷取畫面](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. 若要查看伺服器參數，請選取 [**按一下這裡以啟用記錄] 和 [設定記錄參數**]。

5. 將**slow_query_log**開啟至 [**開啟**]。

6. 選取要將記錄輸出到何處，使用**log_output**。 若要將記錄傳送到本機儲存體和 Azure 監視器診斷記錄，**請選取 [** 檔案]。 

7. 變更所需的任何其他參數。 

8. 選取 [儲存]。 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="慢速查詢記錄參數和儲存的螢幕擷取畫面。":::

在 [**伺服器參數**] 頁面上，您可以藉由關閉頁面來返回記錄清單。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
記錄開始之後，您可以查看可用的緩慢查詢記錄清單，並下載個別的記錄檔。 

1. 開啟 Azure 入口網站。

2. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

3. 在提要欄位的 [**監視**] 區段下，選取 [**伺服器記錄**]。 此頁面會顯示您的記錄檔清單。

   ![[伺服器記錄] 頁面的螢幕擷取畫面，其中顯示已醒目提示的記錄清單](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > 記錄的命名慣例為 **mysql-slow-< your server name>-yyyymmddhh.log**。 檔案名中使用的日期和時間是發出記錄的時間。 記錄檔會每24小時或 7.5 GB 旋轉一次，以先發生者為准。

4. 如有需要，請使用 [搜尋] 方塊，根據日期和時間快速縮小為特定的記錄檔。 搜尋是根據記錄的名稱進行。

5. 若要下載個別記錄檔，請選取資料表資料列中每個記錄檔旁邊的向下箭號圖示。

   ![[伺服器記錄] 頁面的螢幕擷取畫面，並反白顯示向下箭號的圖示](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在提要欄位的 [**監視**] 區段下，選取 [**診斷設定**] [  >  **新增診斷設定**]。

   ![診斷設定選項的螢幕擷取畫面](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. 提供診斷設定名稱。

1. 指定要傳送慢速查詢記錄（儲存體帳戶、事件中樞或 Log Analytics 工作區）的資料接收。

1. 選取 [ **MySqlSlowLogs** ] 做為記錄類型。
![診斷設定設定選項的螢幕擷取畫面](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. 設定資料接收以管道傳送緩慢查詢記錄檔之後，請選取 [**儲存**]。
![診斷設定選項的螢幕擷取畫面，反白顯示 [儲存]](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. 藉由在您設定的資料接收器中探索緩慢查詢記錄來加以存取。 最多可能需要10分鐘的時間，記錄才會出現。

## <a name="next-steps"></a>後續步驟
- 若要瞭解如何以程式設計方式下載緩慢查詢記錄，請參閱[在 CLI 中存取緩慢查詢記錄](howto-configure-server-logs-cli.md)。
- 深入瞭解適用於 MariaDB 的 Azure 資料庫中的[緩慢查詢記錄](concepts-server-logs.md)。
- 如需參數定義和記錄的詳細資訊，請參閱[記錄](https://mariadb.com/kb/en/library/slow-query-log-overview/)上的適用于 mariadb 檔。