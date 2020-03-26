---
title: 快速入門 - 單一資料庫
description: 了解如何快速地開始使用 Azure SQL Database 中的單一資料庫
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 6070b53e5f906bc378402d98275b8f798f57b505
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674437"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>開始使用 Azure SQL Database 中的單一資料庫

[單一資料庫](sql-database-single-index.yml)是完全受控的 PaaS 資料庫即服務 (DbaaS)，可供新式雲端型應用程式作為理想的儲存引擎。 在本節中，您將了解如何快速地在 SQL Database 中設定和建立單一資料庫。

## <a name="quickstart-overview"></a>快速入門概觀

在本節中，您將能查看可協助您快速開始使用單一資料庫的可用文章概觀。 下列快速入門可讓您快速建立單一資料庫、設定資料庫伺服器防火牆規則，然後使用 `.bacpac` 檔案將資料庫匯入到新的單一資料庫中：

- [使用 Azure 入口網站建立單一資料庫](sql-database-single-database-get-started.md)。
- 在建立資料庫之後，您將需要[設定防火牆規則來保護資料庫](sql-database-server-level-firewall-rule.md)。
- 如果您在 SQL Server 上有想要移轉到 Azure 的現有資料庫，則應安裝 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，它會分析您在 SQL Server 上的資料庫，並找出任何可能導致移轉至單一資料庫部署的選項無法運作的問題。 如果您沒有找到任何問題，便可以將資料庫匯出為 `.bacpac` 檔案，然後[使用 Azure 入口網站或 SqlPackage 匯入它](sql-database-import.md)。

## <a name="automating-management-operations"></a>將管理作業自動化

您可以使用 PowerShell 或 Azure CLI 來建立、設定及調整資料庫。

- [使用 PowerShell 建立和設定單一資料庫](scripts/sql-database-create-and-configure-database-powershell.md)或 [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
- [使用 PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) 或 [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) 來更新單一資料庫並調整資源

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>以最短停機時間移轉到單一資料庫

這些快速入門可讓您快速建立資料庫，或使用 `.bacpac` 檔案將資料庫匯入至 Azure。 不過，`.bacpac` 和 `.dacpac` 檔案的用途是要在不同版本的 SQL Server 之間快速移動資料庫和 Azure SQL Database 內的部署選項，或是在 DevOps 管線中實作持續整合。 此方法的設計宗旨並非是要以最短的停機時間移轉您的生產資料庫，因為您必須停止新增資料、等候來源資料庫匯出至 `.bacpac` 檔案的作業完成，並等候匯入到 Azure SQL Database 的作業完成。 這類等候會導致您的應用程式停止運作，特別是在資料庫較大時。 若要移動生產資料庫，您必須以更好的方法進行移轉，以確保最短的停機時間。 為此，請使用[資料移轉服務 (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) 以最短的停機時間移轉資料庫。 DMS 會以累加方式將來源資料庫中的變更推送到正在還原的單一資料庫，藉以完成移轉。 透過此方法，您可以在最短停機時間的前提下，快速地將應用程式從來源資料庫切換到目標資料庫。

## <a name="hands-on-learning-modules"></a>實作學習模組

下列 Microsoft Learn 模組可協助您免費學習 Azure SQL Database。

- [佈建 Azure SQL 資料庫以存放應用程式資料](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [開發和設定可查詢 Azure SQL Database 的 ASP.NET 應用程式](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [保護 Azure SQL Database](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>後續步驟

- 在[這裡取得 Azure SQL Database 中已支援功能的高階清單](sql-database-features.md)。
- 了解如何讓您的[資料庫更安全](sql-database-security-tutorial.md)。
- 如需更進階的操作說明，請參閱[如何在 Azure SQL Database 中使用單一資料庫](sql-database-howto-single-database.md)。
- 尋找更多在 [PowerShell](sql-database-powershell-samples.md) 和 [Azure CLI](sql-database-cli-samples.md) 中撰寫的範例指令碼。
- 深入了解可用來設定資料庫的[管理 API](sql-database-single-databases-manage.md)。
- [為您的內部部署資料庫識別正確的 Azure SQL Database/受控執行個體 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
