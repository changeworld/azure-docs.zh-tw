---
title: 使用 SQL Database DAC 套件 - Azure SQL Edge (預覽)
description: 了解如何在 Azure SQL Edge (預覽) 中使用 DACPAC
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0ddd1544c6a51ff1e2f98a28e40d9eb2ee0b47c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233271"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>SQL Edge 中的 SQL Database DAC 套件

Azure SQL Edge (預覽) 是專為 IoT 和邊緣部署而準備的最佳化關聯式資料庫引擎。 這以最新版本的 Microsoft SQL Server 資料庫引擎為基礎所建置的，提供領先業界的效能、安全性和查詢處理功能。 除了 SQL Server 領先業界的關聯式資料庫管理功能，Azure SQL Edge 還提供內建的串流功能，以進行即時分析和複雜的事件處理。

Azure SQL Edge 也提供 SqlPackage.exe 的原生實作，可讓您在部署 SQL Edge 期間部署 [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 套件。 您可以使用透過 SQL Edge 模組的 `module twin's desired properties` 選項公開的 SqlPackage 參數，將 SQL Database DACPAC 部署至 SQL Edge：

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
| SqlPackage | 包含 SQL Database DAC 套件之 *.zip 檔案的 Azure Blob 儲存體 URI。
| ASAJobInfo | ASA Edge 作業的 Azure Blob 儲存體 URI。

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>搭配使用 SQL Database DAC 套件與 SQL Edge

若要搭配使用 SQL Database DAC 套件 (*.dacpac) 與 SQL Edge，請遵循下列步驟：

1. 建立或擷取 SQL Database DAC 套件。 如需如何為現有的 SQL Server 資料庫產生 DAC 套件的詳細資訊，請參閱[從資料庫擷取 DAC](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)。

2. 壓縮 *.dacpac，並將其上傳至 Azure Blob 儲存體帳戶。 如需將檔案上傳至 Azure Blob 儲存體的詳細資訊，請參閱[使用 Azure 入口網站上傳、下載及列出 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 入口網站產生 zip 檔案的共用存取簽章。 如需詳細資訊，請參閱[使用共用存取簽章 (SAS) 委派存取權](../storage/common/storage-sas-overview.md)。

4. 更新 SQL Edge 模組設定，以包含 DAC 套件的共用存取 URI。 若要更新 SQL Edge 模組，請執行下列步驟：

    1. 在 Azure 入口網站中，移至您的 IoT 中樞部署。

    2. 在左側窗格中，選取 [IoT Edge]。

    3. 在 [IoT Edge] 頁面上，尋找並選取部署 SQL Edge 模組的 IoT Edge。

    4. 在 [IoT Edge 裝置] 裝置頁面上，選取 [設定模組]。

    5. 在 [設定模組] 頁面上，針對 SQL Edge 模組選取 [設定]。

    6. 在 [IoT Edge 自訂模組] 窗格中，選取 [設定模組對應項所需的屬性]。 更新所需的屬性以包含 `SQLPackage` 選項的 URI，如下列範例所示。

        > [!NOTE]
        > 下列 JSON 中的 SAS URI 只是參考範例。 請以您部署中的實際 URI 取代 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. 選取 [儲存]。

    8. 在 [設定模組] 頁面上，選取 [下一步]。

    9. 在 [設定模組] 頁面上，選取 [下一步]，接著選取 [提交]。

5. 模組更新之後，系統會下載、解壓縮 DAC 套件檔案，並且對 SQL Edge 執行個體進行部署。

每次重新啟動 Azure SQL Edge 容器時，會下載 *.dacpac 檔案套件，並評估是否有變更。 如果發現新版本的 dacpac 檔案，則會將變更部署到 SQL Edge 中的資料庫。

## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Edge](deploy-portal.md)。
- [串流資料](stream-data.md)
- [SQL Edge (預覽) 中採用 ONNX 的機器學習和 AI](onnx-overview.md)
