---
title: 使用控制表從資料庫批次複製
description: 瞭解如何使用解決方案範本使用 Azure 數據工廠使用外部控制表存儲源表的分區清單,從資料庫複製批量數據。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414833"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>從具有控制表的資料庫批次複製
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

要將資料從 Oracle 伺服器、Netezza、Teradata 或 SQL Server 中的數據倉庫複製到 Azure SQL 資料倉庫,必須從多個表中載入大量資料。 通常,數據必須在每個表中進行分區,以便可以從單個表並行載入具有多個線程的行。 本文介紹要在這些情況下使用的範本。

 >!注意 如果要將數據從資料量相對較小的少數表中複製到 SQL 數據倉庫,則使用[Azure 數據工廠複製數據工具](copy-data-tool.md)會更有效。 本文中描述的範本比您所需的範本更需要。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本檢索源資料庫分區的清單,以便從外部控制件表複製。 然後,它遍遍了源資料庫中的每個分區,並將數據複製到目標。

範本包含三個活動：
- **尋找**從外部控制表檢索確定資料庫分區的清單。
- **ForEach**從"尋找"活動獲取分區清單,並將每個分區重新添加到"複製"活動。
- **將**每個分區從源資料庫存儲複製到目標存儲。

樣本定義以下參數:
- *Control_Table_Name*是外部控制表,用於存儲源資料庫的分區清單。
- *Control_Table_Schema_PartitionID*是存儲每個分區 ID 的外部控制元件表中的列名稱的名稱。 確保分區 ID 對於源資料庫中的每個分區都是唯一的。
- *Control_Table_Schema_SourceTableName*是外部控制項表,用於從源資料庫存儲每個表名稱。
- *Control_Table_Schema_FilterQuery*是外部控制元件表中的列的名稱,該列儲存篩選器查詢以從源資料庫中的每個分區獲取資料。 例如,如果按年對數據進行分區,則存儲在每行中的查詢可能與"從數據源中選擇 * 從數據源中選擇*"最後修改時間>=『2015-01-01 00:00:00』"和"最後修改時間<""2015-12-31 23:59:59.999'"。
- *Data_Destination_Folder_Path*是將資料複製到目標儲存的路徑(當您選擇的目標是"檔案系統"或"Azure 資料存儲第 1 代"時適用)。 
- *Data_Destination_Container*是將數據複製到目標存儲中的根資料夾路徑。 
- *Data_Destination_Directory*是根目錄下的目錄路徑,其中將數據複製到目標存儲中。 

最後三個參數(定義目標存儲中的路徑)僅在您選擇的目標基於檔案的儲存時可見。 如果選擇「Azure 突觸分析(以前的 SQL DW))」作為目標存儲,則不需要這些參數。 但是,SQL 數據倉庫中的表名稱和架構必須與源資料庫中的表名稱和架構相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 在 SQL Server 或 Azure SQL 資料庫中創建控制項表,以儲存用於批量複製的源資料庫分區清單。 在下面的示例中,源資料庫中有五個分區。 三個分區用於*datasource_table,* 兩個分區用於*project_table。* 列 *「上次修改時間*」用於從源資料庫對表中*的資料進行分區datasource_table。* 用於讀取第一個分區的查詢是"從datasource_table選擇 ",其中"最後修改時間>""2015-01-01 00:00:00"和"最後修改時間<""2015-12-31 23:59:59.999'"。 您可以使用類似的查詢從其他分區讀取數據。

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. 轉到 **「從資料庫範本批量複製**」。 創建到步驟 1 中創建的外部控制表**的新**連接。

    ![建立與控制資料表的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 建立到要從中複製數據的源資料庫**的新**連接。

    ![建立與來源資料庫的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 建立到要將資料複製到的目標資料儲存**的新**連接。

    ![建立與目的地存放區的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 選擇**此樣本**。

6. 您將看到導管,例如以下範例所示:

    ![檢閱管線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 選擇 **「除錯**」,輸入**參數**,然後選擇 **"完成**"。

    ![按下 [除錯]](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 您將看到類似於以下範例的結果:

    ![檢閱結果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. ( 選擇性的 )如果選擇「Azure 突觸分析(以前為 SQL DW))」作為資料目標,則必須按照 SQL 資料庫庫庫的要求,輸入到 Azure Blob 儲存進行暫存的連接。 該範本將自動為 Blob 儲存生成容器路徑。 檢查容器是否已在管道運行後創建。
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
