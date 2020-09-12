---
title: 從檔案大量複製到資料庫
description: 了解如何使用解決方案範本，將資料從 Azure Data Lake Storage Gen2 大量複製到 Azure Synapse Analytics/Azure SQL Database。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: c7f4cba10117efef4099b3524b49cae313593a9a
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442706"
---
# <a name="bulk-copy-from-files-to-database"></a>從檔案大量複製到資料庫

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文描述可用來將資料從 Azure Data Lake Storage Gen2 大量複製到 Azure Synapse Analytics/Azure SQL Database 的解決方案範本。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會從 Azure Data Lake Storage Gen2 來源擷取檔案。 然後，此範本會逐一查看來源中的每個檔案，並將檔案複製到目的地資料存放區。 

此範本目前僅支援複製 **DelimitedText** 格式的資料。 您也可以從來源資料存放區中擷取其他資料格式的檔案，但無法將這些檔案複製到目的地資料存放區。  

範本包含三個活動：
- [取得中繼資料] 活動會從 Azure Data Lake Storage Gen2 擷取檔案，然後將這些檔案傳遞至後續的 [ForEach] 活動。
- [ForEach] 活動會從 [取得中繼資料] 活動取得檔案，並對 [複製] 活動逐一查看每個檔案。
- [複製] 活動位於 [ForEach] 活動中，可將每個檔案從來源資料存放區複製到目的地資料存放區。

範本會定義下列兩個參數：
- *SourceContainer* 是 Azure Data Lake Storage Gen2 中資料複製來源的根容器路徑。 
- *SourceDirectory* 是 Azure Data Lake Storage Gen2 中根容器下資料複製來源的目錄路徑。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 [從檔案大量複製到資料庫] 範本。 建立與來源 Gen2 存放區的**新**連線。 請注意，"GetMetadataDataset" 和 "SourceDataset" 是對來源檔案存放區相同連線的參考。

    ![建立與來源資料存放區的新連線](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 建立與接收資料存放區 (資料複製目標) 的**新**連線。

    ![建立與接收資料存放區的新連線](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 選取 [使用此範本]。

    ![使用此範本](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 您會看到建立的管線，如下列範例所示：

    ![檢閱管線](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果您選擇 **Azure Synapse Analytics (先前的 SQL DW) ** 作為上述 **步驟 2** 中的資料目的地，您必須輸入 Azure Blob 儲存體的連線以進行預備，如 AZURE SYNAPSE ANALYTICS (先前的 Sql 資料倉儲) Polybase 所需。 如下列螢幕擷取畫面所示，此範本會為 Blob 儲存體自動產生「儲存體路徑」。 檢查是否已在管線執行之後建立容器。
        
    ![Polybase 設定](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。

    ![按一下 [偵錯]****](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 當管線執行成功完成時，即會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)