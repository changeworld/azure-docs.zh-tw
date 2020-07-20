---
title: 使用控制資料表從資料庫進行差異複製
description: 深入了解如何使用解決方案範本，使用 Azure Data Factory 以增量方式僅從資料庫複製新的或更新的資料列。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 4da54318bea21daf9ec363be61bea18adaa2ce63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82629026"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>從具有控制資料表的資料庫進行差異複製

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文描述的範本可讓您使用儲存高水位線值的外部控制資料表，以累加方式將新的或更新的資料列從資料庫資料表載入至 Azure。

此範本需要源資料庫的架構包含 timestamp 資料行或遞增索引鍵，以識別新的或更新的資料列。

>[!NOTE]
> 如果您的源資料庫中有時間戳記資料行來識別新的或更新的資料列，但您不想要建立用於差異複製的外部控制資料表，您可以改為使用[Azure Data Factory 資料複製工具](copy-data-tool.md)來取得管線。 該工具會使用觸發程式排程時間做為變數，從源資料庫讀取新的資料列。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會先抓取舊的浮水印值，並將它與目前的浮水印值進行比較。 之後，它會根據兩個水位線值之間的比較，只複製源資料庫中的變更。 最後，它會將新的高水位線值儲存到外部控制資料表，以供下一次進行差異資料載入。

範本包含四個活動：
- **Lookup**會抓取舊的高水位線值，這會儲存在外部控制資料表中。
- 另一個**查閱**活動會從源資料庫抓取目前的高水位線值。
- **複製**只會將源資料庫的變更複製到目的地存放區。 識別源資料庫中之變更的查詢類似于 ' SELECT * FROM Data_Source_Table，其中 TIMESTAMP_Column > 「上一個高水位線」和 TIMESTAMP_Column <= "目前的高水位線" '。
- **SqlServerStoredProcedure**會將目前的高水位線值寫入至外部控制資料表，以供下一次進行差異複製。

範本會定義下列參數：
- *Data_Source_Table_Name*是源資料庫中您想要從中載入資料的資料表。
- *Data_Source_WaterMarkColumn*是來源資料表中用來識別新的或更新的資料列之資料行的名稱。 此資料行的類型通常是*datetime*、 *INT*或類似。
- *Data_Destination_Container*是將資料複製到目的地存放區中之位置的根路徑。
- *Data_Destination_Directory*是在目的地存放區中將資料複製到其中的位置根目錄下的目錄路徑。
- *Data_Destination_Table_Name*是將資料複製到目的地存放區中的位置（適用于選取 [Azure Synapse Analytics （先前為 SQL DW）] 做為 [資料目的地] 時）。
- *Data_Destination_Folder_Path*是將資料複製到目的地存放區中的位置（適用于選取 [檔案系統] 或 [Azure Data Lake Storage Gen1] 做為資料目的地時）。
- *Control_Table_Table_Name*是儲存高水位線值的外部控制資料表。
- *Control_Table_Column_Name*是外部控制資料表中儲存高水位線值的資料行。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 探索您想要載入的來源資料表，並定義可用來識別新的或更新資料列的高水位線資料行。 此資料行的類型可以是*datetime*、 *INT*或類似。 此資料行的值會隨著新的資料列加入而增加。 在下列範例來源資料表（data_source_table）中，我們可以使用*LastModifytime*資料行做為高水位線資料行。

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. 在 SQL Server 或 Azure SQL Database 中建立控制資料表，以儲存差異資料載入的高水位線值。 在下列範例中，控制資料表的名稱是*watermarktable 作為資料表*。 在此表中， *WatermarkValue*是儲存高水位線值的資料行，而其類型為*datetime*。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 在您用來建立控制資料表的相同 SQL Server 或 Azure SQL Database 實例中，建立預存程式。 預存程式是用來將新的高水位線值寫入至外部控制資料表，以供下一次進行差異資料載入。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 前往 [**從資料庫複製差異複本**] 範本。 建立要從中複製資料的源資料庫的**新**連線。

    ![建立與來源資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 建立要複製資料的目的地資料存放區的**新**連接。

    ![建立與目的地資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 針對您在步驟2和3中建立的外部控制資料表和預存程式，建立**新**的連接。

    ![建立與控制資料表資料存放區的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 選取 [使用此範本]。
    
8. 您會看到可用的管線，如下列範例所示：
  
    ![檢閱管線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 選取 [**預存**程式]。 針對 [**預存**程式名稱 **]，選擇 [dbo]. [update_watermark]**。 選取 [匯**入參數**]，然後選取 [**新增動態內容**]。  

    ![設定預存程式活動](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. 寫入內容** \@ {activity （' LookupCurrentWaterMark '）. FirstRow. NewWatermarkValue}**，然後選取 **[完成]**。  

    ![撰寫預存程式之參數的內容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。

    ![選取 * * Debug * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 會顯示類似下列範例的結果：

    ![檢閱結果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 您可以在您的來源資料表中建立新的資料列。 以下是用來建立新資料列的範例 SQL 語言：

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. 若要再次執行管線，請選取 [ **Debug**]，輸入**參數**，然後選取 **[完成]**。

    您會看到只有新的資料列會複製到目的地。

15. 選擇性如果您選取 [Azure Synapse 分析] （先前稱為 [SQL DW]）做為資料目的地，則也必須提供 Azure Blob 儲存體的連線以進行預備，這是 SQL 資料倉儲 Polybase 所需的連線。 範本會為您產生容器路徑。 執行管線之後，請檢查容器是否已建立于 Blob 儲存體中。
    
    ![設定 Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>後續步驟

- [使用具有 Azure Data Factory 的控制資料表，從資料庫進行大量複製](solution-template-bulk-copy-with-control-table.md)
- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
