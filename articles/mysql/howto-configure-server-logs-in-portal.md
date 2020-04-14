---
title: 存取慢速查詢紀錄 ─ Azure 門戶 - MySQL 的 Azure 資料庫
description: 本文介紹如何從 Azure 門戶配置和訪問 MySQL Azure 資料庫中的慢速日誌。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 59faf63312bd7cc657f8b96ca3110707ea997c02
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273594"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>設定與存取來自 Azure 門戶的慢速查詢紀錄

可以從 Azure 門戶配置、列出和下載[MySQL 慢速查詢日誌](concepts-server-logs.md)的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
本文中的步驟要求您具有[MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定 MySQL 慢速查詢記錄的存取。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取適用於 MySQL 的 Azure 資料庫伺服器。

3. 在側邊欄中的 **「監視**」部分下,選擇 **「伺服器日誌**」。。 
   ![伺服器紀錄選項的螢幕截圖](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 要檢視伺服器參數,請選擇 **「按下此處啟用紀錄」並設定日誌參數**。

5. 將**slow_query_log**slow_query_log**ON**轉向 。

6. 選擇將日誌輸出到使用**log_output**的位置。 要將紀錄傳送到本地儲存和 Azure 監視器診斷紀錄,請選擇「**檔案**」。 

7. 更改所需的任何其他參數。 

8. 選取 [儲存]  。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="慢速查詢日誌參數的螢幕截圖和保存。":::

在 **「伺服器參數」** 頁中,您可以透過關閉頁面傳回到紀錄清單。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
記錄記錄開始後,您可以查看可用慢速查詢紀錄的清單,並下載單個紀錄檔。

1. 開啟 Azure 入口網站。

2. 選取適用於 MySQL 的 Azure 資料庫伺服器。

3. 在側邊欄中的 **「監視**」部分下,選擇 **「伺服器日誌**」。。 該頁顯示日誌檔的清單。

   ![伺服器記錄頁面的螢幕截圖,突顯紀錄清單](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 記錄的命名慣例為 **mysql-slow-< your server name>-yyyymmddhh.log**。 檔名中使用的日期和時間是頒發日誌的時間。 日誌檔每 24 小時或 7.5 GB 輪換一次,以先到者為準。 

4. 如果需要,使用搜尋框根據日期和時間快速縮小到特定日誌。 搜尋是根據記錄的名稱進行。

5. 要下載單個日誌檔,請選擇表行中每個日誌檔旁邊的向下箭頭圖示。

   ![伺服器記錄頁面的螢幕截圖,突顯下箭頭圖示](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在側邊欄中的 **「監視**」部分下,選擇 **「診斷設置** > **添加診斷設置**」。

   ![診斷設定選項的螢幕擷取](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. 提供診斷設置名稱。

1. 指定要發送慢速查詢日誌(存儲帳戶、事件中心或日誌分析工作區)的數據接收器。

1. 選擇**MySqlSlowLogs**作為日誌類型。
![診斷設定設定選項的螢幕擷取](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. 將資料接收器配置為將慢速查詢日誌管道到之後,選擇 **「保存**」。
![診斷設定設定選項的螢幕截圖,突顯儲存](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. 通過在配置的數據接收器中流覽這些日誌來訪問慢速查詢日誌。 日誌最多可能需要 10 分鐘才能顯示。

## <a name="next-steps"></a>後續步驟
- 請參閱[CLI 中訪問慢速查詢日誌](howto-configure-server-logs-in-cli.md),瞭解如何以程式設計方式下載慢速查詢日誌。
- 瞭解有關 MySQL Azure 資料庫中[查詢日誌速度慢](concepts-server-logs.md)的詳細資訊。
- 有關參數定義和 MySQL 日誌記錄的詳細資訊,請參閱[日誌](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)上的 MySQL 文件。