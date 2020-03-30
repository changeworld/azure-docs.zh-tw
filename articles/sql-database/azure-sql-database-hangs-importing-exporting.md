---
title: 導入/匯出服務需要很長時間
description: Azure SQL 資料庫導入/匯出服務需要很長時間才能導入或匯出資料庫
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535760"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL 資料庫導入/匯出服務需要很長時間才能導入或匯出資料庫

使用 Azure SQL 資料庫導入/匯出服務時，該過程可能需要比預期更長的時間。 本文介紹了此延遲的潛在原因和替代替代方法。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL 資料庫導入/匯出服務

Azure SQL 資料庫導入/匯出服務是基於 REST 的 Web 服務，在每個 Azure 資料中心中運行。 當您使用["導入資料庫](sql-database-import.md#using-azure-portal)"或["匯出"](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal)選項在 Azure 門戶中移動 SQL 資料庫時，將調用此服務。 該服務提供免費請求佇列和計算服務，用於在 Azure SQL 資料庫和 Azure Blob 存儲之間執行導入和匯出。

導入和匯出操作不代表傳統的物理資料庫備份，而是表示使用特殊 BACPAC 格式的資料庫的邏輯備份。 通過 BACPAC 格式，您不必使用物理格式，該格式可能因 Microsoft SQL 伺服器和 Azure SQL 資料庫的版本而異。 因此，您可以使用它安全地將資料庫還原到 SQL Server 資料庫和 SQL 資料庫。

## <a name="what-causes-delays-in-the-process"></a>是什麼導致了流程延遲？

Azure SQL 資料庫導入/匯出服務提供數量有限的計算虛擬機器 （VM） 每個區域來處理導入和匯出操作。 計算 VM 按區域託管，以確保導入或匯出避免跨區域頻寬延遲和費用。 如果在同一區域同時發出過多請求，則在處理操作時可能會出現重大延遲。 完成請求所需的時間可能從幾秒鐘到數小時不等。

> [!NOTE]
> 如果在四天內未處理請求，服務將自動取消請求。

## <a name="recommended-solutions"></a>推薦的解決方案

如果資料庫匯出僅用於從意外資料刪除中恢復，則所有 Azure SQL 資料庫版本都提供系統生成的備份的自助服務恢復功能。 但是，如果您出於其他原因需要這些匯出，並且需要始終更快速或更可預測的導入/匯出性能，請考慮以下選項：

* [使用 SQLPackage 實用程式匯出到 BACPAC 檔](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)。
* [使用 SQL 伺服器管理工作室 （SSMS） 匯出到 BACPAC 檔](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)。
* 使用 Microsoft SQL 伺服器資料層應用程式框架 （DacFx） API直接在代碼中運行 BACPAC 導入或匯出。 如需相關資訊，請參閱：
  * [匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [微軟.SqlServer.Dac 命名空間](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [下載 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>匯出或導入 Azure SQL 資料庫時需要考慮的事項

* 本文討論的所有方法都使用資料庫事務單元 （DTU） 配額，這將導致 Azure SQL 資料庫服務限制。 您可以在[Azure 門戶上查看資料庫的 DTU 統計資訊](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring)。 如果資料庫已達到其資源限制，[則升級服務層](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)以添加更多資源。
* 理想情況下，您應該從與 SQL 資料庫位於同一區域的 VM 上運行用戶端應用程式（如 sql 包實用程式或自訂 DAC 應用程式）。 否則，您可能會遇到與網路延遲相關的性能問題。
* 匯出沒有群集索引的大型表可能非常緩慢，甚至會導致失敗。 出現此行為是因為表無法並行拆分和匯出。 相反，它必須在單個事務中匯出它，這會導致匯出期間性能緩慢和潛在失敗，尤其是對於大型表。


## <a name="related-documents"></a>相關文件

[匯出 Azure SQL 資料庫時的考量](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
