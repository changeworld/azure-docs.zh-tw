---
title: 匯入和匯出資料庫需要很長的時間
description: Azure SQL Database 和 Azure SQL 受控執行個體匯入/匯出服務需要很長的時間才能匯入或匯出資料庫
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: f98cfcd49806061a969a9227f9ade05f70ce79ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85982305"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database 和受控執行個體匯入/匯出服務需要很長的時間才能匯入或匯出資料庫

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

當您使用匯入/匯出服務時，進程可能會花費比預期更長的時間。 本文說明此延遲和替代解決方法的可能原因。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database 匯入/匯出服務

Azure SQL Database 匯入/匯出服務是以 REST 為基礎的 web 服務，可在每個 Azure 資料中心執行。 當您使用 [匯 [入資料庫](database-import.md#using-azure-portal) ] 或 [ [匯出](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) ] 選項，在 Azure 入口網站中移動您的資料庫時，就會呼叫此服務。 此服務提供免費的要求佇列和計算服務，可在 Azure SQL Database 和 Azure Blob 儲存體之間執行匯入和匯出。

匯入和匯出作業不代表傳統的實體資料庫備份，而是使用特殊 BACPAC 格式之資料庫的邏輯備份。 BACPAC 格式可讓您避免使用在 Microsoft SQL Server、Azure SQL Database 和 Azure SQL 受控執行個體版本之間可能不同的實體格式。

## <a name="what-causes-delays-in-the-process"></a>造成進程延遲的原因為何？

Azure SQL Database 匯入/匯出服務會提供有限數量的計算虛擬機器 (Vm) 每個區域以處理匯入和匯出作業。 計算 Vm 會依區域託管，以確保匯入或匯出可避免跨區域的頻寬延遲和費用。 如果在相同區域中同時發出太多要求，處理作業時可能會發生嚴重的延遲。 完成要求所需的時間可能會因幾秒鐘而異。

> [!NOTE]
> 如果未在四天內處理要求，服務會自動取消要求。

## <a name="recommended-solutions"></a>建議的解決方案

如果您的資料庫匯出只用于從意外刪除資料的復原，則所有的 Azure SQL Database 版本都會提供系統產生之備份的自助式還原功能。 但是，如果您因為其他原因需要這些匯出，而且您需要持續更快或更容易預測的匯入/匯出效能，請考慮下列選項：

* [使用 SQLPackage 公用程式匯出至 BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)檔案。
* [使用 SQL Server Management Studio (SSMS) 匯出至 BACPAC ](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)檔案。
* 使用 Microsoft SQL Server Data-Tier 應用程式架構 (DacFx) API，直接在您的程式碼中執行 BACPAC 匯入或匯出。 如需相關資訊，請參閱：
  * [匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer. Dac 命名空間](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [下載 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>匯出或匯入資料庫時應考慮的事項

* 本文中討論的所有方法都會使用 (DTU) 配額的資料庫交易單位，這會導致 Azure SQL Database 服務進行節流。 您可以 [在 Azure 入口網站上查看資料庫的 DTU 統計資料](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring)。 如果資料庫已達到其資源限制，請 [升級服務層級](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) ，以新增更多資源。
* 在理想情況下，您應該執行用戶端應用程式， (像是 sqlpackage 公用程式，或自訂 DAC 應用程式) 從與您資料庫相同區域中的 VM。 否則，您可能會遇到與網路延遲相關的效能問題。
* 匯出沒有叢集索引的大型資料表可能會很慢或甚至導致失敗。 發生此行為的原因是無法以平行方式分割和匯出資料表。 相反地，它必須在單一交易中匯出，並且會在匯出期間導致效能變慢和潛在失敗，特別是針對大型資料表。


## <a name="related-documents"></a>相關文件

[匯出資料庫時的考慮](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
