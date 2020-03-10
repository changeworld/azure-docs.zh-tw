---
title: Contoso 遷移系列 |Microsoft Docs
description: 提供 Contoso 將內部部署資料中心移轉至 Azure 時使用的移轉策略和案例概觀。
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939180"
---
# <a name="contoso-migration-series"></a>Contoso 移轉系列


我們有一系列文章，示範虛構的組織 Contoso 如何將內部部署基礎結構遷移至[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)雲端。 

整個系列文章中包含資訊以及一些案例，用以說明如何設定基礎結構移轉，以及執行不同類型的移轉。 案例會隨著進度而變得越來越複雜。 這些文章將會說明 Contoso 公司如何完成移轉任務，而一般閱讀要點和具體指示則會在下文完整提供。

## <a name="migration-articles"></a>移轉文章

下表摘要說明這一系列的文章。  

- 由於每種移轉案例的商務目標都略有不同，因此移轉策略也會有所差異。
- 針對每個部署案例，我們提供的相關資訊包括：商業誘因和目標、提議的架構、執行移轉的步驟，以及清理建議和移轉完成後可採取的後續步驟。

**文章** | **詳細資料** 
--- | --- 
[文章 1：概觀](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 系列文章的概觀、Contoso 的移轉策略，以及此系列中所使用的應用程式範例。 
[文章 2：部署 Azure 基礎結構](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso 會準備其內部部署基礎結構和其 Azure 基礎結構以進行移轉。 此系列中的所有移轉文章都使用相同的基礎結構。 
[文章 3：存取內部部署資源以移轉至 Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso 會評估其在 VMware 上執行的內部部署 SmartHotel360 應用程式。 Contoso 會使用 Azure Migrate 服務來評估應用程式 VM，並使用 Database Migration Assistant 來評估應用程式 SQL Server 資料庫。
[文章 4：在 Azure VM 和 SQL Database 受控執行個體上重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso 會為其內部部署 SmartHotel360 應用程式執行隨即移轉至 Azure 的作業。 Contoso 會使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 來移轉應用程式的前端 VM。 Contoso 會使用 [Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)將應用程式資料庫移轉至 Azure SQL Database 受控執行個體。
[文章 5：在 Azure VM 上重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso 會使用 Site Recovery 服務，將其 SmartHotel360 應用程式 VM 移轉至 Azure VM。 
[文章 6：在 Azure VM 上和 SQL Server AlwaysOn 可用性群組中重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso 會移轉 SmartHotel360 應用程式。 Contoso 會使用 Site Recovery 來遷移應用程式的 VM。 它會使用資料庫移轉服務，將應用程式資料庫遷移至受到 SQL Server 可用性群組保護的 SQL Server 叢集。 
[文章 7：將 Linux 應用程式重新裝載至 Azure VM](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso 會使用 Site Recovery 服務，來完成將其 Linux osTicket 應用程式隨即移轉至 Azure VM 的作業。
[文章 8：在 Azure VM 和適用於 MySQL 的 Azure 資料庫上重新裝載 Linux 應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso 會藉由使用 Site Recovery 將其 Linux osTicket 應用程式遷移至 Azure VM。 它會使用 MySQL Workbench 將應用程式資料庫遷移至適用於 MySQL 的 Azure 資料庫。 
[文章 9：在 Azure Web 應用程式和 Azure SQL Database 中重構應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso 會將其 SmartHotel360 應用程式移轉至 Azure Web 應用程式，以及使用 Database Migration Assistant 將應用程式資料庫移轉至 Azure SQL Server 執行個體。     
[文章 10：在 Azure Web 應用程式和適用於 MySQL 的 Azure 資料庫中重構 Linux 應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso 會使用 Azure 流量管理員，將 Linux osTicket 應用程式移轉至多個 Azure 區域的 Azure Web 應用程式，與 GitHub 整合以進行持續傳遞。 Contoso 會將應用程式資料庫遷移至適用於 MySQL 的 Azure 資料庫執行個體。 
[文章 11：在 Azure DevOps Services 上重構 Team Foundation Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso 會將其內部部署 Team Foundation Server 部署移轉至 Azure 中的 Azure DevOps Services。
[文章 12：在 Azure 容器和 Azure SQL Database 中重新建構應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso 會將其 SmartHotel 應用程式移轉至 Azure。 然後，它會重新建構應用程式 Web 層，作為在 Azure Service Fabric 中執行的 Windows 容器，以及具有 Azure SQL Database 的資料庫。
[文章 13：在 Azure 中重建應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso 會藉由使用各種 Azure 功能和服務 (包括 Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure 認知服務及 Azure Cosmos DB) 重建其 SmartHotel 應用程式。
[文章 14：對 Azure 進行大規模移轉](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Contoso 在試驗完移轉組合後，準備對 Azure 進行完整規模的移轉。

## <a name="next-steps"></a>後續步驟

- [瞭解](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/)雲端遷移。
- 瞭解[資料庫移轉指南](https://datamigration.microsoft.com/)中其他案例（來源/目標群組）的遷移策略。
