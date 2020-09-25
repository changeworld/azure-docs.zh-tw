---
title: 使用 SQL Database DACPAC 和 BACPAC 套件-Azure SQL Edge
description: 瞭解如何在 Azure SQL Edge 中使用 dacpac 和 bacpac
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: e9c8c58c6be8d2c2a85e56690903e6b54f0e4a0d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293895"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>在 SQL Edge 中 SQL Database DACPAC 和 BACPAC 套件

Azure SQL Edge 是專為 IoT 和邊緣部署而準備的最佳化關聯式資料庫引擎。 它是以最新版本的 Microsoft SQL Database 引擎為基礎，提供領先業界的效能、安全性和查詢處理功能。 除了 SQL Server 領先業界的關聯式資料庫管理功能，Azure SQL Edge 還提供內建的串流功能，以進行即時分析和複雜的事件處理。

Azure SQL Edge 提供原生機制，可讓您在部署 SQL Edge 期間或之後部署 [SQL DATABASE DACPAC 和 BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 套件。

您可以使用環境變數，將 SQL Database dacpac 和 bacpac 封裝部署到 SQL Edge `MSSQL_PACKAGE` 。 您可以使用下列任何一項設定環境變數。  
- SQL 容器內包含 dacpac 和 bacpac 檔案的本機資料夾位置。 您可以使用掛接點或資料磁片區容器，將此資料夾對應至主機磁片區。 
- SQL 容器中指向 dacpac 或 bacpac 檔案的本機檔案路徑。 您可以使用掛接點或資料磁片區容器，將此檔案路徑對應到主機磁片區。 
- SQL 容器內的本機檔案路徑，會對應至包含 dacpac 或 bacpac 檔案的 zip 檔案。 您可以使用掛接點或資料磁片區容器，將此檔案路徑對應到主機磁片區。 
- 包含 dacpac 和 bacpac 檔案之 zip 檔案的 Azure Blob SAS URL。
- Dacpac 或 bacpac 檔案的 Azure Blob SAS URL。 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>搭配使用 SQL Database DAC 套件與 SQL Edge

若要 `(*.dacpac)` 使用 Azure Blob 儲存體和 zip 檔案部署 (或匯入) SQL DATABASE DAC 封裝或 BACPAC 檔案 `(*.bacpac)` ，請依照下列步驟執行。 

1. 使用下面所述的機制建立/解壓縮 DAC 封裝或匯出 Bacpac 檔案。 
    - 建立或擷取 SQL Database DAC 套件。 如需如何為現有的 SQL Server 資料庫產生 DAC 套件的詳細資訊，請參閱[從資料庫擷取 DAC](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)。
    - 匯出已部署的 DAC 封裝或資料庫。 如需如何為現有的 SQL Server 資料庫產生 bacpac 檔案的相關資訊，請參閱 [匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) 。

2. 壓縮 `*.dacpac` 或檔案 `*.bacpac` ，並將它上傳至 Azure Blob 儲存體帳戶。 如需將檔案上傳至 Azure Blob 儲存體的詳細資訊，請參閱[使用 Azure 入口網站上傳、下載及列出 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 入口網站產生 zip 檔案的共用存取簽章。 如需詳細資訊，請參閱[使用共用存取簽章 (SAS) 委派存取權](../storage/common/storage-sas-overview.md)。

4. 更新 SQL Edge 模組設定，以包含 DAC 套件的共用存取 URI。 若要更新 SQL Edge 模組，請執行下列步驟：

    1. 在 Azure 入口網站中，移至您的 IoT 中樞部署。

    2. 在左側窗格中，選取 [IoT Edge]。

    3. 在 [IoT Edge] 頁面上，尋找並選取部署 SQL Edge 模組的 IoT Edge。

    4. 在 [IoT Edge 裝置]裝置頁面上，選取 [設定模組]。

    5. 在 [ **設定模組** ] 頁面上，按一下 [Azure SQL Edge 模組]。

    6. 在 [ **更新 IoT Edge 模組** ] 窗格中，選取 [ **環境變數**]。 新增 `MSSQL_PACKAGE` 環境變數，並指定在上述步驟3中產生的 SAS URL 作為環境變數的值。 

    7. 選取 [更新]。

    8. 在 [ **設定模組** ] 頁面上，選取 [ **審核 + 建立**]。

    9. 在 [ **設定模組** ] 頁面上，選取 [ **建立**]。

5. 模組更新之後，會下載、解壓縮套件檔案，並針對 SQL Edge 實例進行部署。

在每次重新開機 Azure SQL Edge 容器時，SQL Edge 都會嘗試下載 zip 壓縮檔案套件，並評估是否有變更。 如果發現新版本的 dacpac 檔案，則會將變更部署到 SQL Edge 中的資料庫。

## <a name="known-issue"></a>已知問題

在某些 DACPAC 或 BACPAC 部署期間，使用者可能會遇到命令逾時，而導致 DACPAC 部署作業失敗。 如果您遇到這個問題，請使用 SQLPackage.exe (或 SQL 用戶端工具) 套用 DACPAC 或 BACPAC maually。 

## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Edge](deploy-portal.md)。
- [串流資料](stream-data.md)
- [SQL Edge 中採用 ONNX 格式的機器學習和 AI](onnx-overview.md)
