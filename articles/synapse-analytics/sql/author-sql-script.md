---
title: Azure 同步工作室中的 SQL 文稿(預覽版)
description: 簡介 Azure Synapse 工作室(預覽)SQL 腳本
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431068"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>在 Azure 同步工作室中使用 SQL 文稿(預覽版)

Azure Synapse 工作室(預覽版)提供了一個 SQL 文本 Web 介面,供您創作 SQL 查詢。 您可以連接到 SQL 池(預覽)或 SQL 按需(預覽)。 

## <a name="begin-authoring-in-sql-script"></a>開始在 SQL 文稿中創作 

有幾種方法可以在 SQL 腳本中開始創作體驗。 您可以通過以下方法之一創建新的 SQL 文稿。

1. 選擇「+」圖示並選擇 SQL 腳本。

    > [!div class="mx-imgBorder"] 
    >![新文稿](./media/author-sql-script/newsqlscript.png)

2. 從開發 SQL 腳本下的「操作」功能表中從開發 SQL 腳本下的「操作」功能表中選擇「新 SQL 文本」。 

    > [!div class="mx-imgBorder"] 
    > ![新文稿](./media/author-sql-script/newsqlscript2actions.png)

或 

3. 從開發 SQL 文稿下的「操作」功能表中選擇「導入」,並從本地存儲中選擇現有 SQL 腳本。

    > [!div class="mx-imgBorder"] 
    > ![新文稿](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>建立 SQL 文稿

1. 通過選擇"屬性"按鈕並替換分配給 SQL 文本的預設名稱,為 SQL 腳本選擇名稱。

    > [!div class="mx-imgBorder"] 
    > ![新文稿](./media/author-sql-script/newsqlscriptrename.png)

1. 從"連接到"下拉菜單中選擇特定的 SQL 池或按需 SQL。 或者,如有必要,從"使用資料庫"中選擇資料庫。

    > [!div class="mx-imgBorder"] 
    > ![新文稿](./media/author-sql-script/newsqlchoosepool.png)

1. 使用"無意義"功能開始創作 SQL 腳本。

    > [!div class="mx-imgBorder"] 
    > ![新文稿](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>執行 SQL 文稿

選擇「執行」按鈕以執行 SQL 文稿。 默認情況下,結果將顯示在表中。

> [!div class="mx-imgBorder"] 
> ![新文稿](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>匯出結果

您可以通過選擇「匯出結果」並選擇擴展,以不同格式(包括 CSV、Excel、JSON、XML)將結果匯出到本地存儲。

您還可以通過選擇「圖表」按鈕在圖表中可視化 SQL 腳本結果。 選擇「圖表類型」 和「類別欄」。 您可以通過選擇「保存為影像」 將圖表匯出到圖片中。 

> [!div class="mx-imgBorder"] 
> ![新文稿](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>從鑲木地板文件中瀏覽數據。

您可以使用 SQL 文本瀏覽儲存帳戶中的 Parquet 檔案來預覽檔內容。 

> [!div class="mx-imgBorder"] 
> ![新文稿](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 表、外部表、檢視

通過選擇資料下的「操作」功能表,您可以選擇多個操作,如:「新 SQL 文本」、「選擇前 1000 行」、「創建」、「創建」和「創建」。。 通過右鍵按下 SQL 池和 SQL 按需節點來流覽可用的手勢。

> [!div class="mx-imgBorder"] 
> ![新文稿](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>後續步驟

有關創作 SQL 文稿的詳細資訊,請參閱[Azure 同步分析](https://docs.microsoft.com/azure/synapse-analytics)。