---
title: 從檔案大量複製到資料庫
description: 瞭解如何使用解決方案範本，從 Azure Data Lake Storage Gen2 大量將資料複製到 Azure Synapse 分析/Azure SQL Database。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414878"
---
# <a name="bulk-copy-from-files-to-database"></a>從檔案大量複製到資料庫

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明可用來將大量資料從 Azure Data Lake Storage Gen2 複製到 Azure Synapse 分析/Azure SQL Database 的解決方案範本。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會從 Azure Data Lake Storage Gen2 來源抓取檔案。 然後，它會逐一查看來源中的每個檔案，並將檔案複製到目的地資料存放區。 

此範本目前僅支援以**DelimitedText**格式複製資料。 您也可以從來源資料存放區中抓取其他資料格式的檔案，但無法將檔案複製到目的地資料存放區。  

範本包含三個活動：
- **取得中繼資料**活動會從 Azure Data Lake Storage Gen2 抓取檔案，然後將它們傳遞至後續的*ForEach*活動。
- **ForEach**活動會從*取得中繼資料*活動取得檔案，並將每個檔案逐一查看*複製*活動。
- **複製**活動位於*ForEach*活動中，可將每個檔案從來源資料存放區複製到目的地資料存放區。

此範本會定義下列兩個參數：
- *Sourceaccount*是從您的 Azure Data Lake Storage Gen2 複製資料的根容器路徑。 
- *SourceDirectory*是根容器下的目錄路徑，其中的資料會從您的 Azure Data Lake Storage Gen2 中複製到該處。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 [**從檔案大量複製到資料庫**] 範本。 建立連至來源 Gen2 存放區的**新**連線。 請注意，"GetMetadataDataset" 和 "SourceDataset" 是與來源檔案存放區相同連線的參考。

    ![建立與來源資料存放區的新連線](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 建立要複製資料的接收資料存放區的**新**連線。

    ![建立連至接收資料存放區的新連線](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 選取 [**使用此範本**]。

    ![使用此範本](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 您會看到建立的管線，如下列範例所示：

    ![檢閱管線](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果您選擇**Azure Synapse Analytics （先前稱為 SQL DW）** 做為上述**步驟 2**中的資料目的地，您必須依照 SQL 資料倉儲 Polybase 的要求，輸入與 azure Blob 儲存體的連線以進行預備。 如下列螢幕擷取畫面所示，此範本會自動為您的 Blob 儲存體產生*儲存體路徑*。 檢查在管線執行之後是否已建立容器。
        
    ![Polybase 設定](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 選取 [ **Debug**]，輸入**參數**，然後選取 **[完成]**。

    ![按一下 [Debug] * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 當管線執行成功完成時，您會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)