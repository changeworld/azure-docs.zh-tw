---
title: Synapse Studio 中的 SQL 腳本
description: Azure Synapse Analytics 中的 Synapse Studio SQL 腳本簡介。
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118688"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 Synapse Studio SQL 腳本 

Synapse Studio 提供 SQL 腳本 web 介面，可讓您撰寫 SQL 查詢。 

## <a name="begin-authoring-in-sql-script"></a>開始在 SQL 腳本中撰寫 

有幾種方式可以開始撰寫 SQL 腳本中的體驗。 您可以透過下列其中一種方法來建立新的 SQL 腳本。

1. 從 [開發] 功能表選取 [ **+]** 圖示，然後選擇 [ **SQL 腳本**]。

2. 從 [ **動作** ] 功能表中，選擇 [ **新增 SQL 腳本**]。

3. 從 [開發 SQL 腳本] 下的 [**動作**] 功能表中選擇 [匯 **入**]。 從您的本機儲存體選取現有的 SQL 腳本。
![新的 sql 腳本3動作](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>建立您的 SQL 腳本

1. 選取 [ **屬性** ] 按鈕，並取代指派給 sql 腳本的預設名稱，以選擇您的 sql 腳本的名稱。 
![新的 sql 腳本重新命名](media/author-sql-script/new-sql-script-rename.png)

2. 從 [ **連接到]** 下拉式功能表中，選擇特定的專用 sql 集區或無伺服器 SQL 集區。 或者，視需要選擇 [ **使用資料庫** 中的資料庫]。 
![新增 sql 選擇集區](media/author-sql-script/new-sql-choose-pool.png)

3. 使用 intellisense 功能開始撰寫您的 SQL 腳本。

## <a name="run-your-sql-script"></a>執行您的 SQL 腳本

選取 [ **執行** ] 按鈕以執行您的 SQL 腳本。 依預設，結果會顯示在資料表中。

![新的 sql 腳本結果資料表](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>匯出結果

您可以選取 [匯出結果] 並選擇副檔名，將結果匯出到您的本機儲存體（格式不同） (包括 CSV、Excel、JSON、XML) 。

您也可以選取 [ **圖表** ] 按鈕，以視覺化方式呈現圖表中的 SQL 腳本結果。 選取 [圖表類型] 和 [ **類別**] 資料行。 您可以選取 [ **另存** 新檔]，將圖表匯出成圖片。 

![新的 sql 腳本結果圖表](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>探索 Parquet 檔案中的資料

您可以使用 SQL 腳本來預覽檔案內容，以流覽儲存體帳戶中的 Parquet 檔案。

![新的腳本 sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 資料表、外部資料表、視圖

藉由選取 [資料] 下的 [ **動作** ] 功能表，您可以選取數個動作，例如：

- 新增 SQL 腳本
- 選取前1000個數據列
- CREATE
- 卸載並建立 
 
以滑鼠右鍵按一下 SQL 資料庫的節點，以探索可用的手勢。
 
![新的腳本資料庫](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>建立資料夾並將 SQL 腳本移至資料夾

從 [開發 SQL 腳本] 下的 [動作] 功能表中，從 [開發 SQL 腳本] 下的 [動作] 功能表中選擇 [新增資料夾]。 並在快顯視窗中輸入新資料夾的名稱。 

> [!div class="mx-imgBorder"] 
> ![螢幕擷取畫面，顯示已選取 [新增資料夾] 的 SQL 腳本範例。](./media/author-sql-script/new-sql-script-create-folder.png)

若要將 SQL 腳本移至資料夾，您可以選取 sql 腳本，然後從 [動作] 功能表中選擇 [移至]。 然後在新視窗中尋找目的資料夾，然後將 sql 腳本移至選取的資料夾中。您也可以快速拖曳 sql 腳本，並將它放在資料夾中。  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>後續步驟

如需撰寫 SQL 腳本的詳細資訊，請參閱 [Azure Synapse Analytics](../index.yml)。