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
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431068"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>在 Azure Synapse Studio 中使用 SQL 腳本（預覽）

Azure Synapse Studio （預覽）提供 SQL 腳本 web 介面，可讓您撰寫 SQL 查詢。 您可以連接到 SQL 集區（預覽）或隨選 SQL （預覽）。 

## <a name="begin-authoring-in-sql-script"></a>開始撰寫 SQL 腳本 

有數種方式可以啟動 SQL 腳本中的撰寫體驗。 您可以透過下列其中一種方法來建立新的 SQL 腳本。

1. 選取 [+] 圖示，然後選擇 [SQL 腳本]。

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. 從 [開發 SQL 腳本] 底下的 [動作] 功能表，選擇 [開發 SQL 腳本] 底下 [動作] 功能表中的 [新增 SQL 腳本]。 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

或 

3. 從 [開發 SQL 腳本] 底下的 [動作] 功能表中選擇 [匯入]，然後從您的本機儲存體選取現有的 SQL 腳本。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>建立您的 SQL 腳本

1. 選取 [屬性] 按鈕，並取代指派給 SQL 腳本的預設名稱，以選擇 SQL 腳本的名稱。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. 從 [連接到] 下拉式功能表選擇特定的 SQL 集區或 SQL 隨選。 或者，如有需要，請從 [使用資料庫] 中選擇資料庫。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. 使用 intellisense 功能開始撰寫 SQL 腳本。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>執行您的 SQL 腳本

選取 [執行] 按鈕來執行您的 SQL 腳本。 根據預設，結果會顯示在資料表中。

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>匯出您的結果

您可以選取 [匯出結果]，然後選擇延伸模組，以不同的格式（包括 CSV、Excel、JSON、XML）將結果匯出至本機儲存體。

您也可以選取 [圖表] 按鈕，在圖表中將 SQL 腳本結果視覺化。 選取 [圖表類型] 和 [分類資料行]。 您可以選取 [另存新檔]，將圖表匯出成圖片。 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>探索 Parquet 檔案中的資料。

您可以使用 SQL 腳本來預覽檔案內容，以探索儲存體帳戶中的 Parquet 檔案。 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 資料表、外部資料表、視圖

藉由選取 [資料] 底下的 [動作] 功能表，您可以選取數個動作，例如：「新增 SQL 腳本」、「選取前1000個數據列」、「建立」、「卸載並建立」。 以滑鼠右鍵按一下 SQL 集區的節點和 [視需要的 SQL]，以探索可用的手勢。

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>後續步驟

如需撰寫 SQL 腳本的詳細資訊，請參閱[Azure Synapse 分析](https://docs.microsoft.com/azure/synapse-analytics)。