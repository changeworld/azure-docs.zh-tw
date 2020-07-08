---
title: Azure Synapse Studio 中的 SQL 腳本（預覽）
description: 簡介 Azure Synapse Studio （預覽） SQL 腳本
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921382"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>在 Azure Synapse Studio 中使用 SQL 腳本（預覽）

Azure Synapse Studio （預覽）提供 SQL 腳本 web 介面，可讓您撰寫 SQL 查詢。 您可以連接到 SQL 集區（預覽）或隨選 SQL （預覽）。 

## <a name="begin-authoring-in-sql-script"></a>開始撰寫 SQL 腳本 

有數種方式可以啟動 SQL 腳本中的撰寫體驗。 您可以透過下列其中一種方法來建立新的 SQL 腳本。

1. 從 [開發] 功能表中，選取 [ **+]** 圖示，然後選擇 [ **SQL 腳本**]。

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. 從 [**動作**] 功能表中，選擇 [**新增 SQL 腳本**]。
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

或者，您可以： 

3. 從 [開發 SQL 腳本] 底下的 [**動作**] 功能表中選擇 [匯**入**]，然後從您的本機儲存體選取現有的 SQL 腳本。
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>建立您的 SQL 腳本

1. 選取 [**屬性**] 按鈕，並取代指派給 sql 腳本的預設名稱，以選擇 sql 腳本的名稱。
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. 從 [**連接到]** 下拉式功能表選擇特定的 sql 集區或 sql 隨選。 或者，如有必要，請選擇 [**使用資料庫**] 中的資料庫。
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. 使用 intellisense 功能開始撰寫 SQL 腳本。

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>執行您的 SQL 腳本

選取 [**執行**] 按鈕以執行您的 SQL 腳本。 根據預設，結果會顯示在資料表中。

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>匯出您的結果

您可以選取 [匯出結果]，然後選擇延伸模組，以不同的格式（包括 CSV、Excel、JSON、XML）將結果匯出至本機儲存體。

您也可以選取 [**圖表**] 按鈕，將圖表中的 SQL 腳本結果視覺化。 選取 [圖表類型] 和 [**類別目錄**] 資料行。 您可以選取 [**另存**新檔]，將圖表匯出成圖片。 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>探索 Parquet 檔案中的資料

您可以使用 SQL 腳本來預覽檔案內容，以探索儲存體帳戶中的 Parquet 檔案。

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 資料表、外部資料表、視圖

藉由選取 [資料] 底下的 [**動作**] 功能表，您可以選取數個動作，例如：

- 新的 SQL 腳本
- 選取前1000個數據列
- CREATE
- 卸載並建立 
 
以滑鼠右鍵按一下 SQL 集區的節點和 [視需要的 SQL]，以探索可用的手勢。
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>後續步驟

如需撰寫 SQL 腳本的詳細資訊，請參閱[Azure Synapse 分析](https://docs.microsoft.com/azure/synapse-analytics)。
