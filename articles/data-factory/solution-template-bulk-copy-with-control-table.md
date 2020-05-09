---
title: 使用控制資料表從資料庫進行大量複製
description: 瞭解如何使用方案範本，從資料庫複製大量資料，方法是使用外部控制資料表，利用 Azure Data Factory 來儲存來源資料表的分割區清單。
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629077"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>從具有控制資料表的資料庫大量複製

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

若要將資料從 Oracle 伺服器中的資料倉儲、Netezza、Teradata 或 SQL Server 複製到 Azure SQL 資料倉儲，您必須從多個資料表載入大量資料。 通常，資料必須在每個資料表中進行分割，如此一來，您就可以從單一資料表以平行方式載入具有多個執行緒的資料列。 本文說明在這些案例中使用的範本。

 >!注意：如果您想要從少量資料表中複製資料，且資料量相對較小，以 SQL 資料倉儲，使用[Azure Data Factory 資料複製工具](copy-data-tool.md)會更有效率。 這篇文章中所述的範本，不只是您在該案例中所需的內容。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會抓取要從外部控制資料表複製的源資料庫分割區清單。 然後，它會逐一查看源資料庫中的每個分割區，並將資料複製到目的地。

範本包含三個活動：
- **Lookup**會從外部控制資料表中，抓取確定資料庫資料分割的清單。
- **ForEach**會從查閱活動取得分割區清單，並將每個資料分割逐一查看複製活動。
- [**複製**] 會將每個磁碟分割從源資料庫存放區複製到目的地存放區。

範本會定義下列參數：
- *Control_Table_Name*是您的外部控制資料表，它會儲存源資料庫的資料分割清單。
- *Control_Table_Schema_PartitionID*是您的外部控制資料表中，儲存每個資料分割識別碼之資料行名稱的名稱。 請確定源資料庫中每個資料分割的資料分割識別碼是唯一的。
- *Control_Table_Schema_SourceTableName*是您的外部控制資料表，會儲存源資料庫中的每個資料表名稱。
- *Control_Table_Schema_FilterQuery*是您的外部控制資料表中，儲存篩選查詢以從源資料庫中的每個分割區取得資料之資料行的名稱。 例如，如果您依年度分割資料，則儲存在每個資料列中的查詢可能類似于 ' select * from datasource，其中 LastModifytime >= ' ' 2015-01-01 00:00:00 ' '，LastModifytime <= ' ' 2015-12-31 23：59： 59.999 ' ' '。
- *Data_Destination_Folder_Path*是將資料複製到目的地存放區的路徑（當您選擇的目的地是「檔案系統」或「Azure Data Lake Storage Gen1」）時適用。 
- *Data_Destination_Container*是將資料複製到目的地存放區中的根資料夾路徑。 
- *Data_Destination_Directory*是將資料複製到目的地存放區的根目錄下的目錄路徑。 

最後三個參數會定義目的地存放區中的路徑，只有在您選擇的目的地是以檔案為基礎的存放裝置時，才會顯示。 如果您選擇「Azure Synapse 分析（先前稱為 SQL DW）」作為目的地存放區，則不需要這些參數。 但是，SQL 資料倉儲中的資料表名稱和架構必須與源資料庫中的相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 在 SQL Server 或 Azure SQL Database 中建立控制資料表，以儲存大量複製的源資料庫資料分割清單。 在下列範例中，源資料庫中有五個磁碟分割。 三個數據分割適用于*datasource_table*，而兩個數據分割適用于*project_table*。 資料行*LastModifytime*是用來從源資料庫分割資料表*datasource_table*中的資料。 用來讀取第一個分割區的查詢是 ' select * from datasource_table，其中 LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' 和 LastModifytime <= ' ' 2015-12-31 23：59： 59.999 ' ' '。 您可以使用類似的查詢來讀取其他分割區的資料。

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

2. 移至**從資料庫進行大量複製**範本。 建立您在步驟1中建立之外部控制資料表的**新**連接。

    ![建立與控制資料表的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 建立要複製資料的源資料庫的**新**連接。

    ![建立與來源資料庫的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 建立要複製資料的目的地資料存放區的**新**連線。

    ![建立與目的地存放區的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 選取 [**使用此範本**]。

6. 您會看到管線，如下列範例所示：

    ![檢閱管線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 選取 [ **Debug**]，輸入**參數**，然後選取 **[完成]**。

    ![按一下 [Debug] * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 您會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. 選擇性如果您選擇「Azure Synapse 分析（先前稱為 SQL DW）」作為資料目的地，您必須依照 SQL 資料倉儲 Polybase 的要求，輸入與 Azure Blob 儲存體的連線以進行預備。 此範本會自動為您的 Blob 儲存體產生容器路徑。 檢查在管線執行之後是否已建立容器。
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
