---
title: Contoso 遷移系列 |Microsoft Docs
description: Contoso 範例遷移案例的連結，可供遷移至 Azure。
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676317"
---
# <a name="contoso-migration-series"></a>Contoso 移轉系列


我們有一系列文章，示範虛構的組織 Contoso 如何將其內部部署基礎結構遷移至[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)雲端。 

此系列包含的案例說明如何設定基礎結構遷移，以及如何執行不同類型的遷移。 案例會隨著進度而變得越來越複雜。 本文說明 Contoso 公司如何處理遷移，但會提供一般指示和指標。

## <a name="migration-articles"></a>移轉文章

下表摘要說明這一系列的文章。  

- 每個遷移案例都是由稍微不同的商務需求所驅動，這會決定遷移策略。
- 針對每個部署案例，我們供應商務驅動程式和目標的相關資訊、提議的架構、執行遷移的步驟，以及完成遷移後的清除和後續步驟的建議。


**篇** | **詳細資料** 
--- | --- 
[文章 1：概觀](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 系列文章的概觀、Contoso 的移轉策略，以及此系列中所使用的應用程式範例。 
[文章 2：部署 Azure 基礎結構](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso 會準備其內部部署基礎結構和 Azure 基礎結構以進行遷移。 此系列中的所有文章都使用相同的基礎結構。 
[文章 3：存取內部部署資源以移轉至 Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso 會評估其在 VMware 上執行的內部部署 SmartHotel360 應用程式。 Contoso 會使用 Azure Migrate 服務來評估應用程式 VM，並使用 Database Migration Assistant 來評估應用程式 SQL Server 資料庫。
[文章 4：在 Azure VM 和 SQL Database 受控執行個體上重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso 會為其內部部署 SmartHotel360 應用程式執行隨即移轉至 Azure 的作業。 Contoso 會使用[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview)來遷移應用程式前端 VM。 Contoso 會使用[Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)，將應用程式資料庫移轉至 Azure SQL Database 受控執行個體。
[文章 5：在 Azure VM 上重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso 會使用 Azure Migrate 服務，將其 SmartHotel360 應用程式 Vm 遷移至 Azure Vm。 
[文章 6：在 Azure VM 上和 SQL Server AlwaysOn 可用性群組中重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso 會移轉 SmartHotel360 應用程式。 Contoso 會使用 Azure Migrate 來遷移應用程式 Vm。 它會使用資料庫移轉服務，將應用程式資料庫遷移至受到 SQL Server 可用性群組保護的 SQL Server 叢集。 
[文章 7：將 Linux 應用程式重新裝載至 Azure VM](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso 會使用 Azure Migrate，完成將其 Linux osTicket 應用程式隨即轉移至 Azure Vm 的動作。
[文章 8：在 Azure VM 和適用於 MySQL 的 Azure 資料庫上重新裝載 Linux 應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso 會使用 Azure Migrate 將其 Linux osTicket 應用程式遷移至 Azure Vm。 它會使用 Azure 資料庫移轉服務（包括使用 MySQL 工作臺的替代選項），將應用程式資料庫移轉至 Azure Database for MySQ。
[文章 9：在 Azure Web 應用程式和 Azure SQL Database 中重構應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso 會使用 Azure 資料庫移轉服務，將其 SmartHotel360 應用程式遷移至 Azure web 應用程式，並將應用程式資料庫移轉至 Azure SQL Server 實例。
[文章10：使用 Azure App 服務和 SQL 受控執行個體重構 Windows 應用程式](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso 會使用 Azure 資料庫移轉服務，將內部部署 Windows 應用程式遷移至 Azure web 應用程式，並將應用程式資料庫移轉至 Azure SQL 受控執行個體。
[文章11：在 Azure web 應用程式中重構 Linux 應用程式並適用於 MySQL 的 Azure 資料庫](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso 會使用與 GitHub 整合的 Azure 流量管理員，將其 Linux osTicket 應用程式遷移至多個 Azure 區域中的 Azure web 應用程式以進行持續傳遞。 Contoso 會將應用程式資料庫遷移至適用於 MySQL 的 Azure 資料庫執行個體。 
[文章12：重構 Azure DevOps Services 上的 Team Foundation Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso 會將其內部部署 Team Foundation Server 部署移轉至 Azure 中的 Azure DevOps Services。
[文章 13：在 Azure 中重建應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso 會使用各種 Azure 功能和服務（包括 Azure App Service、Azure Kubernetes Service （AKS）、Azure Functions、Azure 認知服務和 Azure Cosmos DB）重建其 SmartHotel 應用程式。
[文章 14：對 Azure 進行大規模移轉](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Contoso 在試驗完移轉組合後，準備對 Azure 進行完整規模的移轉。



## <a name="next-steps"></a>後續步驟

- [瞭解](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/)雲端遷移。
- 深入瞭解[資料庫移轉指南](https://datamigration.microsoft.com/)中其他案例（來源/目標群組）的遷移策略。
