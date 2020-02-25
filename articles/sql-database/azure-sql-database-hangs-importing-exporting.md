---
title: 匯入/匯出服務需要很長的時間
description: Azure SQL Database 匯入/匯出服務需要很長的時間來匯入或匯出資料庫
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: ed80482147d415ed890bb50ee70be9457c9c5211
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562287"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database 匯入/匯出服務需要很長的時間來匯入或匯出資料庫

當您使用 Azure SQL Database 匯入/匯出服務時，程式可能會花費比預期更長的時間。 本文說明這項延遲的可能原因，以及替代的因應措施方法。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database 匯入/匯出服務

Azure SQL Database 匯入/匯出服務是以 REST 為基礎的 web 服務，會在每個 Azure 資料中心執行。 當您使用 [匯[入資料庫](sql-database-import.md#using-azure-portal)] 或 [[匯出](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal)] 選項來移動 Azure 入口網站中的 SQL 資料庫時，會呼叫此服務。 此服務提供免費的要求佇列和計算服務，可在 Azure SQL database 和 Azure Blob 儲存體之間執行匯入和匯出。

匯入和匯出作業不代表傳統實體資料庫備份，而是使用特殊 BACPAC 格式之資料庫的邏輯備份。 BACPAC 格式可讓您避免必須使用可能會因 Microsoft SQL Server 和 Azure SQL Database 版本不同的實體格式。 因此，您可以使用它，安全地將資料庫還原到 SQL Server 資料庫和 SQL 資料庫。

## <a name="what-causes-delays-in-the-process"></a>造成進程延遲的原因為何？

Azure SQL Database 的匯入/匯出服務會為每個區域提供有限數量的計算虛擬機器（Vm），以處理匯入和匯出作業。 計算 Vm 會每個區域託管，以確保匯入或匯出可避免跨區域頻寬延遲和收費。 如果在同一個區域中同時進行太多要求，則處理作業時可能會發生顯著的延遲。 完成要求所需的時間可能會因數秒到數小時而有所不同。

> [!NOTE]
> 如果要求未在四天內處理，服務就會自動取消要求。

## <a name="recommended-solutions"></a>建議的解決方案

如果您的資料庫匯出僅用於復原意外刪除的資料，所有 Azure SQL Database 版本都會從系統產生的備份中提供自助式還原功能。 但是，如果您因為其他原因而需要這些匯出，而且您需要持續更快或更容易預測的匯入/匯出效能，請考慮下列選項：

* [使用 SQLPackage 公用程式匯出到 BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)檔案。
* [使用 SQL Server Management Studio （SSMS）匯出至 BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)檔案。
* 使用 Microsoft SQL Server 資料層應用程式架構（DacFx） API，直接在您的程式碼中執行 BACPAC 匯入或匯出。 如需相關資訊，請參閱：
  * [匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer Dac 命名空間](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [下載 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>當您匯出或匯入 Azure SQL 資料庫時所要考慮的事項

* 本文中討論的所有方法都會使用資料庫交易單位（DTU）配額，這會導致 Azure SQL Database 服務進行節流。 您可以[在 Azure 入口網站上查看資料庫的 DTU 統計資料](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)。 如果資料庫已達到其資源限制，請[升級服務層](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)來新增更多資源。
* 在理想的情況下，您應該從與 SQL 資料庫相同的區域中的 VM 執行用戶端應用程式（例如 sqlpackage 公用程式或自訂 DAC 應用程式）。 否則，您可能會遇到與網路延遲相關的效能問題。
* 匯出不含叢集索引的大型資料表可能會很緩慢，甚至造成失敗。 之所以會發生這種行為，是因為資料表無法以平行方式分割和匯出。 相反地，它必須在單一交易中匯出，這會導致匯出期間的效能變慢和潛在的失敗，特別是針對大型資料表。


## <a name="related-documents"></a>相關文件

[匯出 Azure SQL 資料庫時的考慮](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
