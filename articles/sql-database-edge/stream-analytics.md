---
title: 使用 SQL Database 的 DAC 封裝，以及 Azure SQL Database Edge 串流分析作業 |Microsoft Docs
description: 瞭解如何在 SQL Database Edge 中使用串流分析作業
keywords: sql database 邊緣、串流分析、sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74384167"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>使用 SQL Database 的 DAC 封裝和使用 SQL Database Edge 串流分析作業

Azure SQL Database Edge 預覽是專為 IoT 和 Edge 部署而優化的關係資料庫引擎。 它是以最新版本的 Microsoft SQL Server 資料庫引擎為基礎，可提供領先業界的效能、安全性和查詢處理功能。 隨著 SQL Server 的領先業界關係資料庫管理功能，Azure SQL Database Edge 為即時分析和複雜的事件處理提供了內建的串流功能。

Azure SQL Database Edge 也提供 SqlPackage 的原生執行，可讓您在 SQL Database Edge 部署期間部署[SQL DATABASE DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications)封裝。

Azure SQL Database Edge 會透過 IoT Edge 模組的`module twin's desired properties`選項公開兩個選擇性參數：

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
| SqlPackage | 包含 SQL Database DAC 封裝之 * .zip 檔案的 Azure Blob 儲存體 URI。
| ASAJobInfo | ASA Edge 作業的 Azure Blob 儲存體 URI。 如需詳細資訊，請參閱[發佈 SQL Database 邊緣的 ASA Edge 作業](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>搭配 SQL Database Edge 使用 SQL Database DAC 封裝

若要搭配 SQL Database Edge 使用 SQL Database DAC 封裝（* .dacpac），請執行下列步驟：

1. 建立或解壓縮 SQL Database DAC 封裝。 如需如何為現有的 SQL Server 資料庫產生 DAC 封裝的相關資訊，請參閱[從資料庫解壓縮 dac](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) 。

2. 將 * .dacpac 壓縮，並將其上傳至 Azure Blob 儲存體帳戶。 如需將檔案上傳至 Azure Blob 儲存體的詳細資訊，請參閱[上傳、下載及列出具有 Azure 入口網站的 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 入口網站產生 zip 檔案的共用存取簽章。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）委派存取](../storage/common/storage-sas-overview.md)。

4. 更新 SQL Database Edge 模組設定，以包含 DAC 封裝的共用存取 URI。 若要更新 SQL Database Edge 模組，請執行下列步驟：

    1. 在 Azure 入口網站中，移至您的 IoT 中樞部署。

    2. 在左側窗格中，選取 [IoT Edge]****。

    3. 在 [ **IoT Edge** ] 頁面上，尋找並選取要部署 SQL Database Edge 模組的 IoT Edge。

    4. 在 [ **IoT Edge 裝置**裝置] 頁面上，選取 [**設定模組**]。

    5. 在 [**設定模組**] 頁面上 **，選取 [** 針對 SQL Database Edge 模組進行設定]。

    6. 在 [ **IoT Edge 自訂模組**] 窗格中，選取 [**設定模組對應項的所需屬性**]。 更新所需的屬性，以包含`SQLPackage`選項的 URI，如下列範例所示。

        > [!NOTE]
        > 下列 JSON 中的 SAS URI 只是一個範例。 以您部署中的實際 URI 取代 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. 選取 [儲存]  。

    8. 在 [**設定模組**] 頁面上，選取 **[下一步]**。

    9. 在 [**設定模組**] 頁面上，選取 **[下一步]** 然後**提交**。

5. 模組更新之後，會下載、解壓縮 DAC 封裝檔案，並針對 SQL Database Edge 實例進行部署。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>使用具有 SQL Database 邊緣的串流作業

Azure SQL Database Edge 有串流分析執行時間的原生執行。 此執行可讓您建立 Azure 串流分析 edge 作業，並將該作業部署為 SQL Database Edge 串流作業。 若要建立串流分析 edge 作業，請完成下列步驟：

1. 使用 [預覽[URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)] 移至 [Azure 入口網站]。 此預覽 URL 可讓您設定串流分析 edge 作業的 SQL Database 輸出。

2. 建立新的**Azure IoT Edge 串流分析**作業。 選擇以**Edge**為目標的主控環境。

3. 定義 Azure 串流分析作業的輸入和輸出。 您在這裡設定的每個 SQL 輸出都會系結至資料庫中的單一資料表。 如果您需要將資料串流至多個資料表，您將需要建立多個 SQL Database 輸出。 您可以設定 SQL 輸出以指向不同的資料庫。

    **輸入**。 選擇 [EdgeHub] 作為 edge 作業的輸入，並提供資源資訊。

    **輸出**。 選取 SQL Database 做為輸出。 選取 [**手動提供 SQL Database 設定**]。 提供資料庫和資料表的設定詳細資料。

    |欄位      | 描述 |
    |---------------|-------------|
    |輸出別名 | 輸出別名的名稱。|
    |資料庫 | SQL 資料庫的名稱。 它必須是存在於 SQL Database Edge 實例上之資料庫的有效名稱。|
    |伺服器名稱 | SQL 實例的名稱（或 IP 位址）和埠號碼詳細資料。 針對 SQL Database 邊緣部署，您可以使用**tcp：.，1433**做為伺服器名稱。|
    |使用者名稱 | 具有您稍早指定之資料庫的資料讀取器和資料寫入器存取權的 SQL 登入帳戶。|
    |密碼 | 您稍早指定之 SQL 登入帳戶的密碼。|
    |Table | 將為串流作業輸出之資料表的名稱。|
    |繼承資料分割| 可繼承您先前查詢步驟或輸入的資料分割配置。 啟用此選項時，當您寫入以磁片為基礎的資料表，且具有作業的完全平行拓撲時，您可以預期會看到較佳的輸送量。|
    |批次大小| 每個大量插入交易傳送的記錄數目上限。|

    以下是範例輸入/輸出設定：

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
    > 如需有關 Azure 串流分析 SQL 輸出介面卡的詳細資訊，請參閱[azure 串流分析輸出至 Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 定義 edge 作業的 ASA 工作查詢。 此查詢應該使用定義的輸入/輸出別名做為查詢中的輸入和輸出名稱。 如需詳細資訊，請參閱[串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 設定 edge 作業的儲存體帳戶設定。 儲存體帳戶會用來做為 edge 作業的發佈目標。

6. 在 [**設定**] 底下，選取 [**發佈**]，然後選取 [**發佈**] 按鈕。 儲存用於 SQL Database Edge 模組的 SAS URI。

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>將串流分析 edge 作業部署至 SQL Database Edge

若要將串流作業部署到 SQL Database Edge 模組，請更新 SQL Database Edge 模組設定，以包含先前步驟中串流作業的 SAS URI。 若要更新 SQL Database Edge 模組：

1. 在 Azure 入口網站中，移至您的 IoT 中樞部署。

2. 在左側窗格中，選取 [IoT Edge]****。

3. 在 [ **IoT Edge** ] 頁面上，尋找並選取要部署 SQL Database Edge 模組的 IoT Edge。

4. 在 [ **IoT Edge 裝置**裝置] 頁面上，選取 [**設定模組**]。

5. 在 [**設定模組**] 頁面上 **，選取 [** 針對 SQL Database Edge 模組進行設定]。

6. 在 [ **IoT Edge 自訂模組**] 窗格中，選取 [**設定模組對應項的所需屬性**]。 更新所需的屬性，以包含`ASAJobInfo`選項的 URI，如下列範例所示。

    > [!NOTE]
    > 下列 JSON 中的 SAS URI 只是一個範例。 以您部署中的實際 URI 取代 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. 選取 [儲存]  。

8. 在 [**設定模組**] 頁面上，選取 **[下一步]**。

9. 在 [**設定模組**] 頁面上，選取 **[下一步]** 然後**提交**。

10. 模組更新之後，會下載、解壓縮串流分析作業檔案，並針對 SQL Database Edge 實例進行部署。

## <a name="next-steps"></a>後續步驟

- 如需定價和可用性的詳細資訊，請參閱[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)。
- 要求為您的訂用帳戶啟用 Azure SQL Database Edge。
- 若要開始使用，請參閱[透過 Azure 入口網站部署 SQL Database Edge](deploy-portal.md)。
