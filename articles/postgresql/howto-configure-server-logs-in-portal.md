---
title: 管理日誌 - Azure 門戶 - 用於 PostgreSQL 的 Azure 資料庫 - 單個伺服器
description: 本文介紹如何在 Azure 資料庫中配置和訪問伺服器日誌 （.log 檔）， 用於 PostgreSQL - 從 Azure 門戶獲取單個伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763683"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>為 PostgreSQL 配置和訪問 Azure 資料庫 - 從 Azure 門戶創建單個伺服器日誌

可以從 Azure 門戶配置、列出和下載[用於 PostgreSQL 日誌](concepts-server-logs.md)的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
本文中的步驟要求您具有[用於 PostgreSQL 伺服器的 Azure 資料庫](quickstart-create-server-database-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定查詢記錄和錯誤記錄的存取權。 

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

2. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

3. 在側邊欄中的 **"監視**"部分下，選擇 **"伺服器日誌**"。 

   ![伺服器日誌選項的螢幕截圖](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 要查看伺服器參數，請選擇 **"按一下此處啟用日誌"並配置日誌參數**。

5. 變更您需要調整的參數。 您在此工作階段中所做的所有變更都會以紫色顯示。

   更改參數後，選擇 **"保存**"。 或者，您可以捨棄變更。 

   ![伺服器參數選項的螢幕截圖](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

在 **"伺服器參數"** 頁中，您可以通過關閉頁面返回到日誌清單。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
日誌記錄開始後，您可以查看可用日誌的清單，並下載單個日誌檔。 

1. 開啟 Azure 入口網站。

2. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

3. 在側邊欄中的 **"監視**"部分下，選擇 **"伺服器日誌**"。 該頁顯示日誌檔的清單。

   ![伺服器日誌頁面的螢幕截圖，突出顯示日誌清單](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 記錄的命名慣例是 **postgresql-yyyy-mm-dd_hh0000.log**。 檔案名中使用的日期和時間是頒發日誌的時間。 日誌檔每小時輪換一次或 100 MB，以先到者為准。

4. 如果需要，使用搜索框根據日期和時間快速縮小到特定日誌。 搜尋是根據記錄的名稱進行。

   ![伺服器日誌頁面的螢幕截圖，突出顯示搜索框和結果](./media/howto-configure-server-logs-in-portal/5-search.png)

5. 要下載單個日誌檔，請選擇表行中每個日誌檔旁邊的向下箭頭圖示。

   ![伺服器日誌頁面的螢幕截圖，突出顯示向下箭頭圖示](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>後續步驟
- 請參閱[CLI 中的訪問伺服器日誌](howto-configure-server-logs-using-cli.md)，瞭解如何以程式設計方式下載日誌。
- 瞭解有關 PostgreSQL Azure 資料庫中[伺服器日誌](concepts-server-logs.md)的詳細資訊。 
- 有關參數定義和 PostgreSQL 日誌記錄的詳細資訊，請參閱 PostgreSQL 有關[錯誤報表和日誌記錄](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)的文檔。

