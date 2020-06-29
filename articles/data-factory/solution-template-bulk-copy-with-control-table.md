---
title: 使用控制資料表從資料庫進行大量複製
description: 深入了解如何使用解決方案範本，以利用 Azure Data Factory 透過外部控制資料表來儲存來源資料表的分割區清單來從資料庫大量複製資料。
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
ms.openlocfilehash: 46e81242c1fba463f547015a244650ae6e574580
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629077"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>使用控制資料表從資料庫進行大量複製

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

若要從 Oracle 伺服器、Netezza、Teradata 或 SQL Server 中的資料倉儲將資料複製到 Azure SQL 資料倉儲時，則必須從多個資料表載入大量資料。 通常，資料必須在每個資料表中進行分割，如此即可從單一資料表使用多個執行緒平行載入資料列。 本文描述要在這些案例中使用的範本。

 >！注意：如果想要從資料量相對較小的少數資料表將資料複製到 SQL 資料倉儲，則使用 [Azure Data Factory 資料複製工具](copy-data-tool.md)會更有效率。 本文中所述範本其範圍遠超出該案例所需的內容。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會擷取要從外部控制資料表複製的來源資料庫分割區清單。 然後，此範本會逐一查看來源資料庫中的每個分割區，並將資料複製到目的地。

範本包含三個活動：
- [查閱] 會從外部控制資料表中擷取確定資料庫分割區的清單。
- [ForEach] 會從 [查閱] 活動取得分割區清單，並針對 [複製] 活動逐一查看每個分割區。
- [複製] 會從來源資料庫存放區將每個分割區複製到目的地存放區。

範本會定義下列參數：
- *Control_Table_Name* 是外部控制資料表，其用來儲存來源資料庫的分割區清單。
- *Control_Table_Schema_PartitionID* 是外部控制資料表中的資料行名稱，其用來儲存每個分割區識別碼。 請確定來源資料庫中每個分割區其分割區識別碼是唯一的。
- *Control_Table_Schema_SourceTableName* 是外部控制資料表，其用來儲存來源資料庫中的每個資料表名稱。
- *Control_Table_Schema_FilterQuery* 是外部控制資料表中的資料行名稱，用來儲存篩選查詢以取得來源資料庫中每個分割區的資料。 例如，若您依年份來分割資料，則每個資料列中儲存的查詢可能類似於 ‘select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''。
- *Data_Destination_Folder_Path* 是將資料複製到目的地存放區的路徑 (適用於所選目的地是「檔案系統」或「Azure Data Lake Storage Gen1」時)。 
- *Data_Destination_Container* 是目的地存放區中資料複製目標的根資料夾路徑。 
- *Data_Destination_Directory* 是根目錄下將資料複製到目的地存放區的目錄路徑。 

最後三個參數會定義目的地存放區的路徑，只有在您所選目的地是檔案型儲存體時，才會顯示這些參數。 如果選擇「Azure Synapse Analytics (先前稱為 SQL DW)」作為目的地存放區，則不需要這些參數。 但是，SQL 資料倉儲中資料表名稱和結構描述必須與來源資料庫中的這些項目相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 在 SQL Server 或 Azure SQL Database 中建立控制資料表，以儲存來源資料庫分割區清單進行大量複製。 在下列範例中，來源資料庫中有五個分割區。 三個分割區用於 *datasource_table*，兩個分割區則用於 *project_table*。 資料行 *LastModifytime* 是用來分割來源資料庫中資料表 *datasource_table* 的資料。 用來讀取第一個分割區的查詢是 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''。 您可使用類似查詢來讀取其他分割區的資料。

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

2. 移至 [從資料庫進行大量複製] 範本。 建立與外部控制資料表 (在步驟 1 中所建立) 的**新**連線。

    ![建立與控制資料表的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 建立與來源資料庫 (資料複製來源) 的**新**連線。

    ![建立與來源資料庫的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 建立與目的地資料存放區 (資料複製目標) 的**新**連線。

    ![建立與目的地存放區的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 選取 [使用此範本]。

6. 您會看到管線，如下列範例所示：

    ![檢閱管線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。

    ![按一下 [偵錯]****](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 您會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (選擇性) 如果選擇「Azure Synapse Analytics (先前稱為 SQL DW)」作為資料目的地，則必須依照 SQL 資料倉儲 Polybase 的需求，輸入與 Azure Blob 儲存體的連線以進行暫存。 此範本會自動為 Blob 儲存體產生容器路徑。 檢查是否已在管線執行之後建立容器。
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
