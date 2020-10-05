---
title: 單一資料庫快速入門內容參考
description: 尋找可協助您快速開始使用 Azure SQL Database 中之單一資料庫的所有快速入門內容參考。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: cd1880f58d4ef457cdc411e065588204a5ad6672
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448765"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>開始使用 Azure SQL Database 中的單一資料庫
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[單一資料庫](../index.yml)是完全受控的平台即服務 (PaaS) 資料庫即服務 (DbaaS)，可供新式雲端應用程式作為理想的儲存引擎。 在此節中，您將了解如何快速地在 Azure SQL Database 中設定及建立單一資料庫。

## <a name="quickstart-overview"></a>快速入門概觀

在此節中，您將能查看可協助您快速開始使用單一資料庫的可用文章概觀。 下列快速入門可讓您快速地建立單一資料庫、設定伺服器層級防火牆規則，然後使用 `.bacpac` 檔案將資料庫匯入到新的單一資料庫中：

- [使用 Azure 入口網站建立單一資料庫](single-database-create-quickstart.md)。
- 在建立資料庫之後，您將需要[設定防火牆規則來保護資料庫](firewall-create-server-level-portal-quickstart.md)。
- 若您在 SQL Server 上有想要移轉到 Azure SQL Database 的現有資料庫，您應該安裝 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，其會分析您位於 SQL Server 上的資料庫，並找出可能封鎖移轉的所有問題。 如果您沒有找到任何問題，便可以將資料庫匯出為 `.bacpac` 檔案，然後[使用 Azure 入口網站或 SqlPackage 匯入它](database-import.md)。


## <a name="automating-management-operations"></a>將管理作業自動化

您可以使用 PowerShell 或 Azure CLI 來建立、設定及調整資料庫。

- [使用 PowerShell 建立和設定單一資料庫](scripts/create-and-configure-database-powershell.md)或 [Azure CLI](scripts/create-and-configure-database-cli.md)
- [使用 PowerShell](scripts/monitor-and-scale-database-powershell.md) 或 [Azure CLI](scripts/monitor-and-scale-database-cli.md) 來更新單一資料庫並調整資源

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>以最短停機時間移轉到單一資料庫

這些快速入門可讓您快速建立資料庫，或使用 `.bacpac` 檔案將資料庫匯入至 Azure。 不過，`.bacpac` 與 `.dacpac` 檔案是為了在不同版本的 SQL Server 之間與 Azure SQL 內快速地移動資料庫所設計，或是在 DevOps 管線中實作持續整合。 此方法的設計宗旨並非是要以最短的停機時間移轉您的生產資料庫，因為您必須停止新增資料、等候來源資料庫匯出至 `.bacpac` 檔案的作業完成，並等候匯入到 Azure SQL Database 的作業完成。 這類等候會導致您的應用程式停止運作，特別是在資料庫較大時。 若要移動生產資料庫，您必須以更好的方法進行移轉，以確保最短的停機時間。 為此，請使用[資料移轉服務 (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) \(部分機器翻譯\) 以最短的停機時間移轉資料庫。 DMS 會以累加方式將來源資料庫中的變更推送到正在還原的單一資料庫，藉以完成移轉。 透過此方法，您可以在最短停機時間的前提下，快速地將應用程式從來源資料庫切換到目標資料庫。

## <a name="hands-on-learning-modules"></a>實作學習模組

下列 Microsoft Learn 模組可協助您免費學習 Azure SQL Database。

- [佈建 SQL Database 資料庫以儲存應用程式資料](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [開發和設定可查詢 Azure SQL Database 中之資料庫的 ASP.NET 應用程式](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [保護 Azure SQL Database 中的資料庫](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>後續步驟

- 在[這裡取得 Azure SQL Database 中已支援功能的高階清單](features-comparison.md)。
- 了解如何讓您的[資料庫更安全](secure-database-tutorial.md)。
- 如需更進階的操作說明，請參閱[如何在 Azure SQL Database 中使用單一資料庫](how-to-content-reference-guide.md)。
- 尋找更多使用 [PowerShell](powershell-script-content-guide.md) 與 [Azure CLI](az-cli-script-samples-content-guide.md) 撰寫的範例指令碼。
- 深入了解可用來設定資料庫的[管理 API](single-database-manage.md)。
- [為您的內部部署資料庫識別正確的 Azure SQL Database 或 Azure SQL 受控執行個體 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
