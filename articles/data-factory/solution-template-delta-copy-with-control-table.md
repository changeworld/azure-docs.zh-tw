---
title: 使用控制表從資料庫進行增量複製
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
ms.openlocfilehash: 01a6d796a9a8306da5bb707111b07786136a66cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414791"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>具有控制表的資料庫的增量複本
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹一個範本,該範本可以使用存儲高水印值的外部控制表,以增量方式將新的或更新的行從資料庫表載入Azure。

此範本要求源資料庫的架構包含時間戳列或增量鍵,以標識新的或更新的行。

>[!NOTE]
> 如果在源資料庫中有一個時間戳列來標識新的或更新的行,但不希望創建用於增量複製的外部控制表,則可以使用[Azure 數據工廠複製數據工具](copy-data-tool.md)獲取管道。 該工具使用觸發器計畫的時間作為變數從源資料庫中讀取新行。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本首先檢索舊浮浮水印值,並將其與當前浮浮水印值進行比較。 之後,它僅根據兩個浮浮水印值之間的比較複製源資料庫的更改。 最後,它將新的高水位線值存儲到外部控制表,以便下次載入增量數據。

範本包含四個活動：
- **查找**檢索存儲在外部控制表中的舊高水位線值。
- 另一個**查找**活動從源資料庫檢索當前高水位線值。
- **僅複製**從源資料庫到目標存儲的更改副本。 標識源資料庫中更改的查詢類似於"選擇 + 從 Data_Source_Table">"最後高水位線"TIMESTAMP_Column,TIMESTAMP_Column<= "當前高水位線"。
- **SqlServer儲存過程**將當前高水位線值寫入外部控制表,以便下次進行增量複製。

樣本定義以下參數:
- *Data_Source_Table_Name*是要從中載入數據的源資料庫中的表。
- *Data_Source_WaterMarkColumn*是源表中用於標識新行或更新行的列的名稱。 此列的類型通常是*日期時間* *、INT*或類似。
- *Data_Destination_Container*是數據複製到目標存儲中的位置的根路徑。
- *Data_Destination_Directory*是數據複製到目標存儲中位置的根目錄路徑。
- *Data_Destination_Table_Name*是將數據複製到目標存儲中的位置(在選擇"Azure 突觸分析(以前的 SQL DW)"作為數據目標時適用)。
- *Data_Destination_Folder_Path*是將資料複製到目標存儲中的位置(當選擇"檔案系統"或"Azure 資料存儲第 1 代"作為數據目標時,適用)。
- *Control_Table_Table_Name*是存儲高水位值的外部控制表。
- *Control_Table_Column_Name*是外部控制表中存儲高水位值的列。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 瀏覽要載入的源表,並定義可用於標識新行或更新行的高水位線列。 此列的類型可以是*日期時間**、INT*或類似。 隨著添加新行,此列的值會增加。 從以下範例源表(data_source_table),我們可以使用 *「上次修改時間」* 列作為高水位線列。

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
    
2. 在 SQL Server 或 Azure SQL 資料庫中創建控制表,以儲存增量數據載入的高水位線值。 在下面的示例中,控制表的名稱是*可浮浮水印*的。 此表中,*水印數*值是儲存一個的印數值的欄,其類型為*日期時間*。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 在用於建立控制表的同一 SQL Server 或 Azure SQL 資料庫實例中創建儲存過程。 存儲過程用於將新的高水位線值寫入外部控制表,以便下次載入增量數據。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 從資料庫樣本移至**增量複本**。 創建要從中複製的源資料庫**的新**連接。

    ![建立與來源資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 創建要將資料複製到的目標資料儲存**的新**連接。

    ![建立與目的地資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 創建到外部控制表和在步驟 2 和 3 中創建的儲存過程**的新**連接。

    ![建立與控制資料表資料存放區的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 選擇**此樣本**。
    
8. 您將看到可用的導管,例如以下範例的方式:
  
    ![檢閱管線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 選擇**儲存程序**。 對於**儲存過程名稱**,請選擇 **[dbo]。update_watermark]**. 選擇 **「導入」參數**,然後選擇 **「添加動態內容**」 。。  

    ![設定預存程序活動](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. 編寫內容**\@[活動("尋找目前水標記")。輸出.firstRow.NewWatermarkValue],** 然後選擇 **"完成**"。  

    ![為儲存程序的參數編寫內容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. 選擇 **「除錯**」,輸入**參數**,然後選擇 **"完成**"。

    ![選擇 [除錯]](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 顯示類似於以下範例的結果:

    ![檢閱結果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 您可以在您的來源資料表中建立新的資料列。 下面是建立新行的範例 SQL 語言:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. 要再次運行管道,請選擇 **「調試**」,輸入**參數**,然後選擇 **"完成**"。

    您將看到只有新行複製到目標。

15. (選擇:)如果選擇 Azure 突觸分析(以前為 SQL DW)作為數據目標,則還必須提供到 Azure Blob 儲存的連接,以便進行暫存,這是 SQL 數據倉庫庫庫所需的。 該範本將為您生成容器路徑。 管道運行後,檢查容器是否已在 Blob 儲存中創建。
    
    ![設定 Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>後續步驟

- [使用 Azure 資料工廠控制表從資料庫批次複製](solution-template-bulk-copy-with-control-table.md)
- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
