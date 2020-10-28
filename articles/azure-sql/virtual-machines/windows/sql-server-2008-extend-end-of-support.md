---
title: 擴充對 SQL Server 2008 和 2008 R2 的支援
description: 若要擴充對 SQL Server 2008 和 SQL Server 2008 R2 的支援，您可以將 SQL Server 執行個體遷移至 Azure，或購買擴充支援以保留內部部署的執行個體。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fbfc4619e8af86a89b82f32ff3bc9a39c92b355a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784859"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>使用 Azure 擴充對 Azure SQL Server 2008 和 SQL Server 2008 R2 的支援
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 和 SQL Server 2008 R2 均已達到[終止支援 (EOS) 生命週期](https://www.microsoft.com/sql-server/sql-server-2008)。 因為許多客戶仍在使用這兩個版本，所以我們會提供數個選項來繼續支援。 您可以將內部部署 SQL Server 執行個體遷移至 Azure 虛擬機器 (VM)、遷移至 Azure SQL Database，或保留內部部署並購買擴充的安全性更新。

與受控執行個體不同的是，遷移至 Azure VM 不需要重新認證應用程式。 與保留內部部署不同的是，遷移至 Azure VM 後，您就可以收到免費的擴充安全性修補程式。

接下來我們會說明，將 SQL Server 執行個體遷移至 Azure VM 時的注意事項。

如需終止支援選項的詳細資訊，請參閱[終止支援](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。

## <a name="provisioning"></a>佈建

在 Azure Marketplace 上提供一個 **在 Windows Server 2008 R2 映像上執行的隨用隨付 SQL Server 2008 R2** 。

使用 SQL Server 2008 的客戶必須自行安裝或升級至 SQL Server 2008 R2。 同樣地，使用 Windows Server 2008 的客戶也必須從自訂 VHD 部署 VM，或升級至 Windows Server 2008 R2。

透過 Azure Marketplace 部署的映像會隨附預先安裝的 SQL IaaS 擴充功能。 SQL IaaS 擴充功能是彈性授權和自動修補功能的必備條件。 部署自行安裝 VM 的客戶必須手動安裝 SQL IaaS 擴充功能。 Windows Server 2008 不支援 SQL IaaS 擴充功能。

> [!NOTE]
> SQL Server 的 [建立] 和 [管理] 刀鋒視窗支援 Azure 入口網站的 SQL Server 2008 R2 映像，但 _不支援_ 下列功能：自動備份、Azure Key Vault 整合、R 服務和儲存體設定。

## <a name="licensing"></a>授權
隨用隨付 SQL Server 2008 R2 部署可以轉換為 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。

若要將以軟體保證 (SA) 為基礎的授權轉換成隨用隨付授權，客戶應向 SQL VM [資源提供者](sql-vm-resource-provider-register.md)註冊。 在完成上述註冊後，即可互換 Azure Hybrid Benefit 與隨用隨付這兩種 SQL 授權類型。

在 Azure VM 上自行安裝的 SQL Server 2008 或 SQL Server 2008 R2 執行個體，可以向 SQL VM 資源提供者註冊，並將其授權類型轉換成隨用隨付。

## <a name="migration"></a>遷移
您可以使用手動備份/還原方法，將 EOS SQL Server 執行個體遷移至 Azure VM。 這是從內部部署遷移到 Azure VM 的最常見方法。

### <a name="azure-site-recovery"></a>Azure Site Recovery

針對大量遷移，我們建議使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 服務。 透過 Azure Site Recovery，客戶可以複寫整個 VM，包括從內部部署到 Azure VM 的 SQL Server。

SQL Server 需要應用程式一致的 Azure Site Recovery 快照集以確保復原。 Azure Site Recovery 支援最少 1 小時間隔的應用程式一致快照集。 使用 Azure Site Recovery 遷移 SQL Server 時，最短復原點目標 (RPO) 為 1 小時。 復原時間目標 (RTO) 是 2 小時加上 SQL Server 復原時間。

### <a name="database-migration-service"></a>Database Migration Service

如果 [客戶是從](../../../dms/dms-overview.md) 內部部署遷移至 azure VM，請將 SQL Server 升級為2012版或更新版本，以供客戶選擇。

## <a name="disaster-recovery"></a>災害復原

Azure VM 上的 EOS SQL Server 的災害復原解決方案如下：

- **SQL Server 備份** ：使用 Azure 備份的 15 分鐘 RPO 和時間點復原的損毀，協助您保護 EOS SQL Server 2008 和 2008 R2 免於遭受勒索軟體的侵害、意外刪除和損毀。 如需詳細資料，請參閱[這篇文章](../../../backup/sql-support-matrix.md#scenario-support)。
- **記錄傳送** ：您可以在具有持續還原的另一個區域或 Azure 區域中，建立記錄傳送複本，以減少 RTO。 您必須手動設定記錄傳送。
- **Azure Site Recovery** ：您可以透過 Azure Site Recovery 複寫，在區域和區域之間複寫 VM。 SQL Server 需要應用程式一致的快照集，以確保在發生災害時能夠復原。 Azure Site Recovery 為 EOS SQL Server 災害復原提供最少 1 小時的 RPO 和 2 小時 (加上 SQL Server 復原時間) 的 RTO。

## <a name="security-patching"></a>安全性修補
在 SQL Server VM 已向 SQL VM [資源提供者](sql-vm-resource-provider-register.md)註冊後，會透過 Microsoft Update 通道傳遞SQL Server VM 的擴充安全性更新。 您可以手動或自動下載修補程式。

*Automated patching* 。 自動修補可讓 Azure 自動修補 SQL Server 和作業系統。 如果已安裝 SQL Server IaaS 擴充功能，您可以指定維護期間的星期幾、時間和持續時間。 Azure 會在此維護期間執行修補。 維護期間排程會使用 VM 地區設定做為時間。 如需詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 自動修補](automated-patching.md)。


## <a name="next-steps"></a>後續步驟

將 SQL Server VM 遷移至 Azure：

* [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](migrate-to-vm-from-sql-server.md)

在 Azure 虛擬機器上開始使用 SQL Server：

* [在 Azure 入口網站中建立 SQL Server VM](sql-vm-create-portal-quickstart.md)

獲得有關 SQL Server VM 常見問題的答案：

* [Azure 虛擬機器上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)

深入瞭解結束支援選項和擴充安全性更新：

* [終止支援](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [擴充安全性更新](/sql/sql-server/end-of-support/sql-server-extended-security-updates)