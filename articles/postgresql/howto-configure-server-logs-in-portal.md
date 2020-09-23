---
title: 記錄管理-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何從 Azure 入口網站設定和存取適用於 PostgreSQL 的 Azure 資料庫單一伺服器中)  ( 記錄檔的伺服器記錄檔。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: cf22a165e9a6eadcd2e5d4cdb0efa301538be7e9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907526"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>從 Azure 入口網站設定和存取適用於 PostgreSQL 的 Azure 資料庫單一伺服器記錄

您可以從 Azure 入口網站設定、列出和下載 [適用於 PostgreSQL 的 Azure 資料庫記錄](concepts-server-logs.md) 。

## <a name="prerequisites"></a>必要條件
本文中的步驟要求您必須有 [適用於 PostgreSQL 的 Azure 資料庫 server](quickstart-create-server-database-portal.md)。

## <a name="configure-logging"></a>設定記錄
設定查詢記錄和錯誤記錄的存取權。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

3. 在提要欄位的 [ **監視** ] 區段下，選取 [ **伺服器記錄**]。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="[伺服器記錄檔] 選項的螢幕擷取畫面":::

4. 若要查看伺服器參數，請選取 [ **按一下這裡啟用記錄] 和 [設定記錄參數**]。

5. 變更您需要調整的參數。 您在此工作階段中所做的所有變更都會以紫色顯示。

   變更參數之後，請選取 [ **儲存**]。 或者，您可以捨棄變更。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="伺服器參數選項的螢幕擷取畫面":::

在 [ **伺服器參數** ] 頁面上，您可以藉由關閉頁面來返回記錄清單。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
開始記錄之後，您可以查看可用記錄檔的清單，並下載個別的記錄檔。 

1. 開啟 Azure 入口網站。

2. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

3. 在提要欄位的 [ **監視** ] 區段下，選取 [ **伺服器記錄**]。 頁面會顯示您的記錄檔清單。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="[伺服器記錄] 頁面的螢幕擷取畫面，其中已醒目提示記錄清單":::

   > [!TIP]
   > 記錄的命名慣例是 **postgresql-yyyy-mm-dd_hh0000.log**。 檔案名中使用的日期和時間是發出記錄檔的時間。 記錄檔會每小時或 100 MB 輪替一次，視何者較早。

4. 如有需要，請使用 [搜尋] 方塊，根據日期和時間快速縮小至特定的記錄。 搜尋是根據記錄的名稱進行。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="[伺服器記錄] 頁面的螢幕擷取畫面，其中已醒目提示搜尋方塊和結果":::

5. 若要下載個別記錄檔，請選取表格資料列中每個記錄檔旁邊的向下箭號圖示。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="[伺服器記錄] 頁面的螢幕擷取畫面，反白顯示向下箭號圖示":::

## <a name="next-steps"></a>下一步
- 請參閱 [CLI 中的存取伺服器記錄](howto-configure-server-logs-using-cli.md) ，以瞭解如何以程式設計方式下載記錄。
- 深入瞭解適用於 PostgreSQL 的 Azure 資料庫中的 [伺服器記錄](concepts-server-logs.md) 。 
- 如需有關參數定義和于 postgresql 記錄的詳細資訊，請參閱有關 [錯誤報表和記錄](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)的于 postgresql 檔。

