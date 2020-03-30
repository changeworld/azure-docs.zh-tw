---
title: 使用 SQL 資料庫 DAC 包和流分析作業與 Azure SQL 資料庫邊緣 |微軟文檔
description: 瞭解如何在 SQL 資料庫邊緣中使用流分析作業
keywords: sql 資料庫邊緣、流分析、sql 包
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384167"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>使用 SQL 資料庫 DAC 包和具有 SQL 資料庫邊緣的流分析作業

Azure SQL 資料庫邊緣預覽是一個優化的關係資料庫引擎，適用于 IoT 和邊緣部署。 它基於最新版本的 Microsoft SQL Server 資料庫引擎，它提供了業界領先的性能、安全性和查詢處理功能。 除了 SQL Server 業界領先的關係資料庫管理功能外，Azure SQL 資料庫邊緣還提供內置流式處理功能，用於即時分析和複雜的事件處理。

Azure SQL 資料庫邊緣還提供 SqlPackage.exe 的本機實現，使您能夠在 SQL 資料庫邊緣部署期間部署[SQL 資料庫 DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications)包。

Azure SQL 資料庫邊緣通過 IoT 邊緣`module twin's desired properties`模組的選項公開兩個可選參數：

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|欄位 | 描述 |
|------|-------------|
| SqlPackage | 包含 SQL 資料庫 DAC 包的 @.ZIP 檔案的 Azure Blob 存儲 URI。
| ASAJobInfo | 用於 ASA 邊緣作業的 Azure Blob 存儲 URI。 有關詳細資訊，請參閱為[SQL 資料庫邊緣發佈 ASA 邊緣作業](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>將 SQL 資料庫 DAC 包與 SQL 資料庫邊緣一起使用

要將 SQL 資料庫 DAC 包 （*.dacpac） 與 SQL 資料庫邊緣一起使用，請執行以下步驟：

1. 創建或提取 SQL 資料庫 DAC 包。 有關如何為現有 SQL Server 資料庫生成 DAC 包的資訊，請參閱[從資料庫中提取 DAC。](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)

2. 壓縮 @.dacpac 並將其上載到 Azure Blob 存儲帳戶。 有關將檔上載到 Azure Blob 存儲的詳細資訊，請參閱[使用 Azure 門戶上載、下載和列出 Blob。](../storage/blobs/storage-quickstart-blobs-portal.md)

3. 使用 Azure 門戶為 ZIP 檔案生成共用訪問簽名。 有關詳細資訊，請參閱[使用共用訪問簽名 （SAS） 委派存取權限](../storage/common/storage-sas-overview.md)。

4. 更新 SQL 資料庫邊緣模組配置以包括 DAC 包的共用訪問 URI。 要更新 SQL 資料庫邊緣模組，請執行以下步驟：

    1. 在 Azure 門戶中，轉到 IoT 中心部署。

    2. 在左側窗格中，選取 [IoT Edge]****。

    3. 在**IoT 邊緣**頁上，查找並選擇部署 SQL 資料庫邊緣模組的 IoT 邊緣。

    4. 在**IoT 邊緣設備**設備頁面上，選擇 **"設置模組**"。

    5. 在 **"設置模組"** 頁上，選擇針對 SQL 資料庫邊緣模組**進行配置**。

    6. 在**IoT 邊緣自訂模組**窗格中，選擇 **"設置模組孿生"所需的屬性**。 更新所需屬性以包括選項的`SQLPackage`URI，如以下示例所示。

        > [!NOTE]
        > 以下 JSON 中的 SAS URI 只是一個示例。 將 URI 替換為部署中的實際 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. 選取 [儲存]****。

    8. 在 **"設置模組"** 頁上，選擇 **"下一步**"。

    9. 在 **"設置模組"** 頁上，選擇 **"下一步**"，然後**提交**。

5. 模組更新後，將下載、解壓縮 DAC 包檔，並針對 SQL 資料庫邊緣實例部署該檔。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>使用具有 SQL 資料庫邊緣的流式處理作業

Azure SQL 資料庫邊緣具有流分析運行時的本機實現。 此實現使您能夠創建 Azure 流分析邊緣作業，並將該作業部署為 SQL 資料庫邊緣流作業。 要創建流分析邊緣作業，請完成以下步驟：

1. 使用預覽[URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)轉到 Azure 門戶。 此預覽 URL 使您能夠為流分析邊緣作業配置 SQL 資料庫輸出。

2. 在**IoT 邊緣作業上創建新的 Azure 流分析**。 選擇面向**邊緣的**託管環境。

3. 為 Azure 流分析作業定義輸入和輸出。 您將在此處設置的每個 SQL 輸出都綁定到資料庫中的單個表。 如果需要將資料流程式傳輸到多個表，則需要創建多個 SQL 資料庫輸出。 您可以將 SQL 輸出配置為指向不同的資料庫。

    **輸入**。 選擇 EdgeHub 作為邊緣作業的輸入，並提供資源資訊。

    **輸出**。 選擇 SQL 資料庫作為輸出。 選擇**手動提供 SQL 資料庫設置**。 提供資料庫和表的配置詳細資訊。

    |欄位      | 描述 |
    |---------------|-------------|
    |輸出別名 | 輸出別名的名稱。|
    |資料庫 | SQL 資料庫的名稱。 它需要是 SQL 資料庫邊緣實例上存在的資料庫的有效名稱。|
    |伺服器名稱 | SQL 實例的名稱（或 IP 位址）和埠號詳細資訊。 對於 SQL 資料庫邊緣部署，可以使用**tcp：.，1433**作為伺服器名稱。|
    |使用者名稱 | SQL 登錄帳戶，該帳戶具有資料讀取器和資料寫入器外掛程式對前面指定的資料庫的存取權限。|
    |密碼 | 您之前指定的 SQL 登錄帳戶的密碼。|
    |Table | 將輸出的流作業的表的名稱。|
    |繼承分區| 啟用繼承上一個查詢步驟或輸入的分區方案。 啟用此選項後，在寫入基於磁片的表並為作業提供完全並行拓撲時，可以預期會看到更好的輸送量。|
    |批次大小| 隨每個批量插入事務一起發送的最大記錄數。|

    下面是一個示例輸入/輸出配置：

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > 有關 Azure 流分析的 SQL 輸出配接器的詳細資訊，請參閱[Azure 流分析輸出到 Azure SQL 資料庫](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 為邊緣作業定義 ASA 作業查詢。 此查詢應使用定義的輸入/輸出別名作為查詢中的輸入和輸出名稱。 有關詳細資訊，請參閱[流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 設置邊緣作業的存儲帳戶設置。 存儲帳戶用作邊緣作業的發佈目標。

6. 在 **"配置**"**下，選擇"發佈**"，然後選擇 **"發佈**"按鈕。 保存 SAS URI 以與 SQL 資料庫邊緣模組一起使用。

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>將流分析邊緣作業部署到 SQL 資料庫邊緣

要將流式處理作業部署到 SQL 資料庫邊緣模組，請更新 SQL 資料庫邊緣模組配置，以包括前面步驟中流作業的 SAS URI。 要更新 SQL 資料庫邊緣模組：

1. 在 Azure 門戶中，轉到 IoT 中心部署。

2. 在左側窗格中，選取 [IoT Edge]****。

3. 在**IoT 邊緣**頁上，查找並選擇部署 SQL 資料庫邊緣模組的 IoT 邊緣。

4. 在**IoT 邊緣設備**設備頁面上，選擇 **"設置模組**"。

5. 在 **"設置模組"** 頁上，選擇針對 SQL 資料庫邊緣模組**進行配置**。

6. 在**IoT 邊緣自訂模組**窗格中，選擇 **"設置模組孿生"所需的屬性**。 更新所需屬性以包括選項的`ASAJobInfo`URI，如以下示例所示。

    > [!NOTE]
    > 以下 JSON 中的 SAS URI 只是一個示例。 將 URI 替換為部署中的實際 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. 選取 [儲存]****。

8. 在 **"設置模組"** 頁上，選擇 **"下一步**"。

9. 在 **"設置模組"** 頁上，選擇 **"下一步**"，然後**提交**。

10. 模組更新後，將下載、解壓縮和部署針對 SQL 資料庫邊緣實例的流分析作業檔。

## <a name="next-steps"></a>後續步驟

- 有關定價和可用性詳細資訊，請參閱[Azure SQL 資料庫邊緣](https://azure.microsoft.com/services/sql-database-edge/)。
- 請求為訂閱啟用 Azure SQL 資料庫邊緣。
- 要開始，請參閱[通過 Azure 門戶部署 SQL 資料庫邊緣](deploy-portal.md)。
