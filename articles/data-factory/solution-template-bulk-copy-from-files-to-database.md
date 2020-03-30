---
title: 從檔案大量複製到資料庫
description: 瞭解如何使用解決方案範本將資料批量從 Azure 資料存儲第 2 代複製到 Azure 同步分析/Azure SQL 資料庫。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75921146"
---
# <a name="bulk-copy-from-files-to-database"></a>從檔案大量複製到資料庫

本文介紹了一個解決方案範本，可用於將資料批量從 Azure 資料存儲第 2 代複製到 Azure 同步分析/Azure SQL 資料庫。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本從 Azure 資料存儲源第 2 代源檢索檔。 然後，它遍遍了源中的每個檔，並將檔案複製到目標資料存儲。 

目前，此範本僅支援以 **"分隔文本"** 格式複製資料。 也可以從來源資料存儲中檢索其他資料格式的檔，但不能複製到目標資料存儲。  

範本包含三個活動：
- **獲取中繼資料**活動從 Azure 資料存儲第 2 代檢索檔，並將它們傳遞到後續*的 ForEach*活動。
- **ForEach 每個**活動從 *"獲取中繼資料"活動獲取*檔，並將每個檔重新複製到 *"複製"* 活動。
- **複製**活動駐留在*ForEach*活動中，用於將每個檔從來源資料存儲複製到目標資料存儲。

範本定義了以下兩個參數：
- *源容器*是從 Azure 資料湖存儲 Gen2 中複製資料的根容器路徑。 
- *SourceDirectory*是根容器下的目錄路徑，其中從 Azure 資料湖存儲 Gen2 中複製資料。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 轉到**從檔到資料庫範本的批量複製**。 創建到源 Gen2 存儲**的新**連接。 請注意，"GetMetadataDataset"和"源資料集"是原始檔案存儲的相同連接的引用。

    ![創建到來源資料存儲的新連接](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 創建要將資料複製到的接收器資料存儲**的新**連接。

    ![創建到接收器資料存儲的新連接](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 選擇**使用此範本**。

    ![使用此範本](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 您將看到創建管道，如以下示例所示：

    ![檢閱管線](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果在上述**步驟 2**中選擇**Azure 突觸分析（以前為 SQL DW）** 作為資料目標，則必須按照 SQL 資料倉儲庫庫的要求，輸入到 Azure Blob 存儲進行暫存的連接。 如下圖所示，範本將自動為 Blob 存儲生成*存儲路徑*。 檢查容器是否已在管道運行後創建。
        
    ![Polybase 設定](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 選擇 **"調試**"，輸入**參數**，然後選擇 **"完成**"。

    ![按一下 [調試]](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 當管道運行成功完成時，您將看到類似于以下示例的結果：

    ![檢閱結果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)