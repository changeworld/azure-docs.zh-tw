---
title: Contoso 遷移系列 |微軟文件
description: 連結到 Contoso 範例遷移方案,用於遷移到 Azure。
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676317"
---
# <a name="contoso-migration-series"></a>Contoso 移轉系列


我們有一系列文章,展示了虛構的組織 Contoso 如何將其本地基礎結構遷移到[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)雲。 

該系列包括說明如何設置基礎結構遷移以及如何運行不同類型的遷移的方案。 隨著方案的進展,其複雜性會增大。 這些文章顯示了 Contoso 公司如何處理遷移,但始終提供一般說明和指標。

## <a name="migration-articles"></a>移轉文章

下表摘要說明這一系列的文章。  

- 每個遷移方案都由略有不同的業務需求驅動,這些業務需求決定了遷移策略。
- 對於每個部署方案,我們提供有關業務驅動因素和目標、建議的體系結構、執行遷移的步驟以及遷移完成后的清理和後續步驟的資訊。


**文章** | **詳細資料** 
--- | --- 
[文章 1：概觀](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 系列文章的概觀、Contoso 的移轉策略，以及此系列中所使用的應用程式範例。 
[文章 2：部署 Azure 基礎結構](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso 準備其本地基礎結構和 Azure 基礎結構進行遷移。 此系列中的所有文章都使用相同的基礎結構。 
[文章 3：存取內部部署資源以移轉至 Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso 會評估其在 VMware 上執行的內部部署 SmartHotel360 應用程式。 Contoso 會使用 Azure Migrate 服務來評估應用程式 VM，並使用 Database Migration Assistant 來評估應用程式 SQL Server 資料庫。
[文章 4：在 Azure VM 和 SQL Database 受控執行個體上重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso 會為其內部部署 SmartHotel360 應用程式執行隨即移轉至 Azure 的作業。 Contoso 使用[Azure 移遷](https://docs.microsoft.com/azure/migrate/migrate-services-overview)移應用程式前端 VM。 Contoso 使用[Azure 資料庫遷移服務](https://docs.microsoft.com/azure/dms/dms-overview)將應用資料庫遷移到 Azure SQL 資料庫託管實例。
[文章 5：在 Azure VM 上重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso 使用 Azure 遷移服務將其 SmartHotel360 應用 VM 遷移到 Azure VM。 
[文章 6：在 Azure VM 上和 SQL Server AlwaysOn 可用性群組中重新裝載應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso 會移轉 SmartHotel360 應用程式。 Contoso 使用 Azure 遷移遷移應用 VM。 它會使用資料庫移轉服務，將應用程式資料庫遷移至受到 SQL Server 可用性群組保護的 SQL Server 叢集。 
[文章 7：將 Linux 應用程式重新裝載至 Azure VM](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso 使用 Azure 遷移完成其 Linux osTicket 應用到 Azure VM 的提升和行動遷移。
[文章 8：在 Azure VM 和適用於 MySQL 的 Azure 資料庫上重新裝載 Linux 應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso 使用 Azure 遷移將其 Linux osTicket 應用遷移到 Azure VM。 它使用 Azure 資料庫遷移服務(包括使用 MySQL 工作台的替代選項)將應用資料庫遷移到 MySQ 的 Azure 資料庫。
[文章 9：在 Azure Web 應用程式和 Azure SQL Database 中重構應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso 將其 SmartHotel360 應用遷移到 Azure Web 應用,並使用 Azure 資料庫遷移服務將應用資料庫遷移到 Azure SQL Server 實體。
[第 10 條:使用 Azure 應用服務和 SQL 託管實例重構 Windows 應用](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso 將基於 Windows 的本地應用遷移到 Azure Web 應用,並使用 Azure 資料庫遷移服務將應用資料庫遷移到 Azure SQL 託管實例。
[第 11 條:在 Azure Web 應用和 MySQL 的 Azure 資料庫中重構 Linux 應用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso 使用與 GitHub 整合的 Azure 流量管理員將其 Linux osTicket 應用遷移到多個 Azure 區域中的 Azure Web 應用,以便持續傳遞。 Contoso 會將應用程式資料庫遷移至適用於 MySQL 的 Azure 資料庫執行個體。 
[第 12 條:在 Azure DevOps 服務上重構團隊基礎伺服器](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso 會將其內部部署 Team Foundation Server 部署移轉至 Azure 中的 Azure DevOps Services。
[文章 13：在 Azure 中重建應用程式](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso 使用一系列 Azure 功能和服務重建其 SmartHotel 應用,包括 Azure 應用服務、Azure 庫伯奈斯服務 (AKS)、Azure 函數、Azure 認知服務和 Azure 宇宙資料庫。
[文章 14：對 Azure 進行大規模移轉](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Contoso 在試驗完移轉組合後，準備對 Azure 進行完整規模的移轉。



## <a name="next-steps"></a>後續步驟

- [瞭解](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/)雲遷移。
- 在[資料庫遷移指南](https://datamigration.microsoft.com/)中瞭解其他方案(源/目標對)的遷移策略。
