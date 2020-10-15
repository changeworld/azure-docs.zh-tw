---
title: 'Azure Synapse Studio 中的 SQL 腳本 (預覽版) '
description: 簡介 Azure Synapse Studio (preview) SQL 腳本
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: cd00c5033d0435b24d4cfb9f413b5afe74da6774
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089004"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>使用 Azure Synapse Studio 中的 SQL 腳本 (預覽版) 

Azure Synapse Studio (preview) 提供可讓您撰寫 SQL 查詢的 SQL 腳本 web 介面。 您可以 (preview) 或 SQL 隨選 (預覽) 連接到 SQL 集區。 

## <a name="begin-authoring-in-sql-script"></a>開始在 SQL 腳本中撰寫 

有幾種方式可以開始撰寫 SQL 腳本中的體驗。 您可以透過下列其中一種方法來建立新的 SQL 腳本。

1. 從 [開發] 功能表選取 [ **+]** 圖示，然後選擇 [ **SQL 腳本**]。

![新增 sql 腳本](media/author-sql-script/newsqlscript.png)

2. 從 [ **動作** ] 功能表中，選擇 [ **新增 SQL 腳本**]。
> [!div class="mx-imgBorder"]
> ![新的 sql script 2 動作](media/author-sql-script/newsqlscript2actions.png)

或者，您可以： 

3. 從 [開發 SQL 腳本] 下的 [**動作**] 功能表中選擇 [匯**入**]。 從您的本機儲存體選取現有的 SQL 腳本。
![新的 sql 腳本3動作](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>建立您的 SQL 腳本

1. 選取 [ **屬性** ] 按鈕，並取代指派給 sql 腳本的預設名稱，以選擇您的 sql 腳本的名稱。 
![新的 sql 腳本重新命名](media/author-sql-script/newsqlscriptrename.png)

2. 從 [ **連接到]** 下拉式功能表選擇特定的 sql 集區或 sql 隨選。 或者，視需要選擇 [ **使用資料庫**中的資料庫]。 
![新增 sql 選擇集區](media/author-sql-script/newsqlchoosepool.png)

3. 使用 intellisense 功能開始撰寫您的 SQL 腳本。
![新的 sql intellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>執行您的 SQL 腳本

選取 [ **執行** ] 按鈕以執行您的 SQL 腳本。 依預設，結果會顯示在資料表中。

![新的 sql 腳本結果資料表](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>匯出結果

您可以選取 [匯出結果] 並選擇副檔名，將結果匯出到您的本機儲存體（格式不同） (包括 CSV、Excel、JSON、XML) 。

您也可以選取 [ **圖表** ] 按鈕，以視覺化方式呈現圖表中的 SQL 腳本結果。 選取 [圖表類型] 和 [ **類別**] 資料行。 您可以選取 [ **另存**新檔]，將圖表匯出成圖片。 

![新的 sql 腳本結果圖表](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>探索 Parquet 檔案中的資料

您可以使用 SQL 腳本來預覽檔案內容，以流覽儲存體帳戶中的 Parquet 檔案。

![新的腳本 sqlod parquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 資料表、外部資料表、視圖

藉由選取 [資料] 下的 [ **動作** ] 功能表，您可以選取數個動作，例如：

- 新增 SQL 腳本
- 選取前1000個數據列
- CREATE
- 卸載並建立 
 
以滑鼠右鍵按一下 SQL 集區的節點和 SQL 隨選節點，以探索可用的手勢。
 
![新的腳本資料庫](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>後續步驟

如需撰寫 SQL 腳本的詳細資訊，請參閱 [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)。
