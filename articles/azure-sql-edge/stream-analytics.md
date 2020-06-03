---
title: 使用 Azure 串流分析 Edge 作業搭配 Azure SQL Edge (預覽)
description: 了解如何在 Azure SQL Edge (預覽) 中使用串流分析作業
keywords: SQL Edge, 串流分析,
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7c15312b48e7118517894d8ffd4807e4892e03a3
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233150"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>使用 Azure 串流分析作業搭配 SQL Edge

Azure SQL Edge (預覽) 是專為 IoT 和邊緣部署而準備的最佳化關聯式資料庫引擎。 這以最新版本的 Microsoft SQL Server 資料庫引擎為基礎所建置的，提供領先業界的效能、安全性和查詢處理功能。 除了 SQL Server 領先業界的關聯式資料庫管理功能，Azure SQL Edge 還提供內建的串流功能，以進行即時分析和複雜的事件處理。

Azure SQL Edge 具備串流分析執行階段的原生實作。 此實作可讓您建立 Azure 串流分析邊緣作業，並將該作業部署為 SQL Edge 串流作業。 您可以使用透過 SQL Edge 模組之 `module twin's desired properties` 選項公開的 ASAJobInfo 參數，將 Azure 串流分析作業部署到 SQL Edge：

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
| SqlPackage | 包含 SQL Database DAC 套件之 * .zip 檔案的 Azure Blob 儲存體 URI。
| ASAJobInfo | ASA Edge 作業的 Azure Blob 儲存體 URI。

## <a name="create-an-azure-stream-analytics-edge-job"></a>建立 Azure 串流分析 Edge 作業

1. 移至 Azure 入口網站。

2. 建立新的 **IoT Edge Azure 串流分析**作業。 選擇以 **Edge** 為目標的裝載環境。

3. 定義 Azure 串流分析作業的輸入和輸出。 您在這裡設定的每個 SQL 輸出都會繫結至資料庫中的單一資料表。 如果您需要將資料串流至多個資料表，則需要建立多個 SQL Database 輸出。 您可以設定 SQL 輸出以指向不同的資料庫。

    **輸入**。 選擇 EdgeHub 作為邊線作業的輸入，並提供資源資訊。

    **輸出**。 選取 SQL Database 作為輸出。 選取 [手動提供 SQL Database 設定]。 提供資料庫和資料表的組態詳細資料。

    |欄位      | 描述 |
    |---------------|-------------|
    |輸出別名 | 輸出別名的名稱。|
    |資料庫 | SQL 資料庫的名稱。 必須是存在於 SQL Edge 執行個體中資料庫的有效名稱。|
    |伺服器名稱 | SQL 執行個體的名稱 (或 IP 位址) 和連接埠號碼詳細資料。 針對 SQL Edge 部署，您可以使用 **tcp:.,1433** 作為伺服器名稱。|
    |使用者名稱 | 具有您稍早指定之資料庫的資料讀取器和資料寫入器存取權的 SQL 登入帳戶。|
    |密碼 | 您稍早指定之 SQL 登入帳戶的密碼。|
    |Table | 將作為串流作業輸出的資料表名稱。|
    |繼承資料分割| 可繼承您先前查詢步驟或輸入的資料分割配置。 啟用此選項時，寫入磁碟式資料表及針對您的作業採用完全平行拓撲時，可預期取得更佳的輸送量。|
    |批次大小| 每一次大量插入交易中傳送的記錄數上限。|

    以下是範例輸入/輸出組態：

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
    > 如需有關 Azure 串流分析 SQL 輸出配接器的詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 定義邊緣作業的 ASA 作業查詢。 此查詢應該使用定義的輸入/輸出別名作為查詢中的輸入和輸出名稱。 如需詳細資訊，請參閱[串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 設定邊線設定的儲存體帳戶設定。 儲存體帳戶會用來作為邊線作業的發佈目標。

6. 在**設定**下，選取 [發佈]，然後選取 [發佈] 按鈕。 儲存用於 SQL Edge 模組的 SAS URI。

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>將 Azure 串流分析 Edge 作業部署到 SQL Edge

若要將串流作業部署到 SQL Edge 模組，請更新 SQL Edge 模組組態，以包含先前步驟中串流作業的 SAS URI。 若要更新 SQL Edge 模組：

1. 在 Azure 入口網站中，移至您的 IoT 中樞部署。

2. 在左側窗格中，選取 [IoT Edge]。

3. 在 [IoT Edge] 頁面上，尋找並選取部署 SQL Edge 模組的 IoT Edge。

4. 在 [IoT Edge 裝置]裝置頁面上，選取 [設定模組]。

5. 在 [設定模組] 頁面上，針對 SQL Edge 模組選取 [設定]。

6. 在 [IoT Edge 自訂模組] 窗格中，選取 [設定模組對應項的所需屬性]。 更新所需的屬性，以包含 `ASAJobInfo` 選項的 URI，如下列範例所示。

    > [!NOTE]
    > 下列 JSON 中的 SAS URI 只是參考範例。 請以您部署中的實際 URI 取代 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. 選取 [儲存]。

8. 在 [設定模組] 頁面上，選取 [下一步]。

9. 在 [設定模組] 頁面上，選取 [下一步]，接著選取 [提交]。

10. 模組更新之後，系統會下載、解壓縮串流分析作業檔案，並針對 SQL Edge 執行個體進行部署。

## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Edge](deploy-portal.md)。

- [串流資料](stream-data.md)

- [SQL Edge (預覽) 中採用 ONNX 的機器學習和 AI ](onnx-overview.md)
