---
title: 訪問慢速查詢日誌 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 本文介紹如何從 Azure 門戶配置和訪問 MariaDB Azure 資料庫中的慢速查詢日誌。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89bdd209315445519c35f3ef2c2f1ad2555106ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531391"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>配置和訪問來自 Azure 門戶的慢速查詢日誌

可以從 Azure 門戶配置、列出和下載[MariaDB 慢速查詢日誌](concepts-server-logs.md)的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
本文中的步驟要求您具有[MariaDB 伺服器的 Azure 資料庫](quickstart-create-mariadb-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定慢速查詢記錄的存取。 

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

2. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

3. 在側邊欄中的 **"監視**"部分下，選擇 **"伺服器日誌**"。 
   ![伺服器日誌選項的螢幕截圖](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. 要查看伺服器參數，請選擇 **"按一下此處啟用日誌"並配置日誌參數**。

5. 更改需要調整的參數，包括**將slow_query_log**轉向**ON**。 您在此工作階段中所做的所有變更都會以紫色顯示。 

   更改參數後，選擇 **"保存**"。 或者，您可以捨棄變更。

   ![伺服器參數選項的螢幕截圖](./media/howto-configure-server-logs-portal/3-save-discard.png)

在 **"伺服器參數"** 頁中，您可以通過關閉頁面返回到日誌清單。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
日誌記錄開始後，您可以查看可用慢速查詢日誌的清單，並下載單個日誌檔。 

1. 開啟 Azure 入口網站。

2. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

3. 在側邊欄中的 **"監視**"部分下，選擇 **"伺服器日誌**"。 該頁顯示日誌檔的清單。

   ![伺服器日誌頁面的螢幕截圖，突出顯示日誌清單](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > 記錄的命名慣例為 **mysql-slow-< your server name>-yyyymmddhh.log**。 檔案名中使用的日期和時間是頒發日誌的時間。 日誌檔每 24 小時或 7.5 GB 輪換一次，以先到者為准。

4. 如果需要，使用搜索框根據日期和時間快速縮小到特定日誌。 搜尋是根據記錄的名稱進行。

5. 要下載單個日誌檔，請選擇表行中每個日誌檔旁邊的向下箭頭圖示。

   ![伺服器日誌頁面的螢幕截圖，突出顯示向下箭頭圖示](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在側邊欄中的 **"監視**"部分下，選擇 **"診斷設置** > **添加診斷設置**"。

   ![診斷設置選項的螢幕截圖](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. 提供診斷設置名稱。

1. 指定要發送慢速查詢日誌（存儲帳戶、事件中心或日誌分析工作區）的資料接收器。

1. 選擇**MySqlSlowLogs**作為日誌類型。
![診斷設置配置選項的螢幕截圖](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. 將資料接收器配置為將慢速查詢日誌管道到之後，選擇 **"保存**"。
![診斷設置配置選項的螢幕截圖，突出顯示保存](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. 通過在配置的資料接收器中流覽這些日誌來訪問慢速查詢日誌。 日誌最多可能需要 10 分鐘才能顯示。

## <a name="next-steps"></a>後續步驟
- 請參閱[CLI 中訪問慢速查詢日誌](howto-configure-server-logs-cli.md)，瞭解如何以程式設計方式下載慢速查詢日誌。
- 瞭解有關 MariaDB Azure 資料庫中[查詢日誌速度慢](concepts-server-logs.md)的詳細資訊。
- 有關參數定義和日誌記錄的詳細資訊，請參閱[日誌](https://mariadb.com/kb/en/library/slow-query-log-overview/)上的 MariaDB 文檔。