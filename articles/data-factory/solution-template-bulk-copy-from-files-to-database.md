---
title: 從檔案大量複製到資料庫
description: 瞭解如何使用解決方案範本將資料批量從 Azure 資料儲存第 2 代複製到 Azure 同步分析/Azure SQL 資料庫。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414878"
---
# <a name="bulk-copy-from-files-to-database"></a>從檔案大量複製到資料庫

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹了一個解決方案範本,可用於將資料量產從 Azure 資料儲存第 2 代複製到 Azure 同步分析/Azure SQL 資料庫。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本從 Azure 資料儲存來源第 2 代源檢索檔。 然後,它遍遍了源中的每個檔,並將檔案複製到目標數據存儲。 

目前,此範本僅支援以 **「分隔文字」** 格式複製數據。 也可以從源資料儲存中檢索其他數據格式的檔案,但不能複製到目標數據存儲。  

範本包含三個活動：
- **獲取中繼資料**活動從 Azure 資料儲存第 2 代檢索檔,並將它們傳遞到後續*的 ForEach*活動。
- **ForEach 每個**活動從 *「獲取中繼資料」活動獲取*檔案,並將每個檔重新複製到 *「複製」* 活動。
- **複製**活動駐留在*ForEach*活動中,用於將每個檔從源數據儲存複製到目標數據存儲。

樣本定義了以下兩個參數:
- *源容器*是從 Azure 資料湖儲存 Gen2 中複製數據的根容器路徑。 
- *SourceDirectory*是根容器下的目錄路徑,其中從 Azure 數據湖儲存 Gen2 中複製數據。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至**從檔案的樣本的批次複製**。 建立到源 Gen2 儲存**的新**連接。 請注意,"GetMetadataDataset"和"源數據集"是源檔案存儲的相同連接的引用。

    ![建立來源資料儲存的新連線](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 建立要將資料複製到的接收體資料儲存**的新**連接。

    ![建立到接收器資料儲存的新連線](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 選擇**此樣本**。

    ![使用此範本](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 您將看到建立導管,例如以下範例的範例 :

    ![檢閱管線](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果在上述**步驟 2**中選擇**Azure 突觸分析(以前為 SQL DW)** 作為數據目標,則必須按照 SQL 數據倉庫庫庫的要求,輸入到 Azure Blob 儲存進行暫存的連接。 以下圖示,樣本會自動為 Blob 儲存產生*儲存路徑*。 檢查容器是否已在管道運行後創建。
        
    ![Polybase 設定](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 選擇 **「除錯**」,輸入**參數**,然後選擇 **"完成**"。

    ![按下 [除錯]](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 當導管執行成功完成時,您將看到類似於以下範例的結果:

    ![檢閱結果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)