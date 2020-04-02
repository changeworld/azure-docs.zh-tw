---
title: 擴展對 SQL Server 2008 & 2008 R2 的支援
description: 通過將 SQL Server 實例遷移到 Azure 或購買擴展支援以將實例保留在本地,擴展對 SQL Server 2008 和 SQL Server 2008 R2 的支援。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548397"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>使用 Azure 擴充對 SQL Server 2008 和 SQL Server 2008 R2 的支援

SQL Server 2008 與 SQL Server 2008 R2 都達到了[其支援 (EOS) 生命週期的結束](https://www.microsoft.com/sql-server/sql-server-2008)。 由於許多客戶仍在使用這兩個版本,因此我們提供多個選項來繼續獲得支援。 您可以將本地 SQL Server 實體移到 Azure 虛擬機器 (VM)、移至 Azure SQL 資料庫或留在本地併購的安全更新。

與託管實例不同,遷移到 Azure VM 不需要重新認證應用程式。 與留在本地不同,您將通過遷移到 Azure VM 獲得免費擴充的安全修補程式。

本文的其餘部分提供了將 SQL Server 實例遷移到 Azure VM 的注意事項。

有關支援選項結束的詳細資訊,請參閱[支援結束](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。

## <a name="provisioning"></a>佈建

**Windows Server 2008 R2 映射上有**一個即用即付 SQL Server 2008 R2 映射,可在 Azure 應用商店中使用。

SQL Server 2008 上的客戶需要自行安裝或升級到 SQL Server 2008 R2。 同樣,Windows Server 2008 上的客戶需要從自定義 VHD 部署其 VM 或升級到 Windows Server 2008 R2。

通過 Azure 應用商店部署的圖像附帶預安裝的 SQL IaaS 擴展。 SQL IaaS 擴展是靈活許可和自動修補的要求。 部署自安裝 VM 的客戶需要手動安裝 SQL IaaS 擴展。 Windows 伺服器 2008 不支援 SQL IaaS 擴展。

> [!NOTE]
> 儘管 SQL Server**創建****和管理**邊欄選項卡將使用 Azure 門戶中的 SQL Server 2008 R2 映射,_但不支援_以下功能:自動備份、Azure 密鑰保管庫整合、R 服務和儲存配置。

## <a name="licensing"></a>授權
即用即付 SQL Server 2008 R2 部署可以轉換為[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

要將基於軟體保障 (SA) 的授權轉換為即用即付,客戶應向 SQL VM[資源提供程式](virtual-machines-windows-sql-register-with-resource-provider.md)註冊 。 註冊后,SQL許可證類型將在 Azure 混合權益和即用即付之間互換。

Azure VM 上的自安裝 SQL Server 2008 或 SQL Server 2008 R2 實例可以註冊到 SQL VM 資源提供者,並將其許可證類型轉換為即用即付。

## <a name="migration"></a>遷移
您可以使用手動備份/還原方法將 EOS SQL Server 實例遷移到 Azure VM。 這是從本地到 Azure VM 的最常見遷移方法。

### <a name="azure-site-recovery"></a>Azure Site Recovery

對於批量遷移,我們建議[使用 Azure 網站恢復](/azure/site-recovery/site-recovery-overview)服務。 使用 Azure 網站恢復,客戶可以複製整個 VM,包括從本地複製到 Azure VM 的 SQL 伺服器。

SQL Server 需要應用一致的 Azure 網站恢復快照來保證恢復。 Azure 網站恢復支援至少 1 小時間隔的應用一致的快照。 具有 Azure 網站恢復遷移的 SQL Server 可能的最低復原點目標 (RPO) 為 1 小時。 恢復時間目標 (RTO) 是 2 小時加上 SQL Server 恢復時間。

### <a name="database-migration-service"></a>Database Migration Service

如果客戶透過將 SQL Server 升級到 2012 年或更高版本從本地遷移到 Azure VM,則[資料庫遷移服務](/azure/dms/dms-overview)是一個選項。

## <a name="disaster-recovery"></a>災害復原

Azure VM 上的 EOS SQL Server 災難復原解決方案如下所示:

- **SQL Server 備份**:使用 Azure 備份幫助保護 EOS SQL Server 2008 和 2008 R2,防止勒索軟體、意外刪除和損壞,提供 15 分鐘的 RPO 和時間點恢復。 有關詳細資訊,請參閱[本文](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support)。
- **日誌傳送**:您可以在其他區域或 Azure 區域中創建日誌傳送副本,並具有連續還原以減少 RTO。 您需要手動配置日誌傳送。
- **Azure 網站恢復**:您可以通過 Azure 網站恢復複製在區域和區域之間複製 VM。 SQL Server 需要應用一致的快照,以確保在發生災難時恢復。 Azure 網站恢復提供至少 1 小時的 RPO 和 2 小時(加上 SQL Server 恢復時間)RTO,用於 EOS SQL Server 災難恢復。

## <a name="security-patching"></a>安全修補
SQL Server VM 的擴充安全更新在 SQL Server VM 註冊到 SQL VM[資源提供程式](virtual-machines-windows-sql-register-with-resource-provider.md)後,透過 Microsoft 更新通道傳遞。 修補程式可以手動或自動下載。

*Automated patching* 。 自動修補可讓 Azure 自動修補 SQL Server 和作業系統。 如果安裝了 SQL Server IaaS 擴展,則可以為維護視窗指定星期幾、時間和持續時間。 Azure 會在此維護期間執行修補。 維護期間排程會使用 VM 地區設定做為時間。  有關詳細資訊,請參閱[Azure 虛擬機器上的 SQL Server 自動修補](virtual-machines-windows-sql-automated-patching.md)。


## <a name="next-steps"></a>後續步驟

將 SQL Server VM 移至 Azure:

* [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)

開始在 Azure 虛擬機器上使用 SQL 伺服器:

* [在 Azure 入口網站中建立 SQL Server VM](quickstart-sql-vm-create-portal.md)

取得有關 SQL Server VM 的常見問題的解答:

* [Azure 虛擬機器上的 SQL 伺服器錯誤](virtual-machines-windows-sql-server-iaas-faq.md)

詳細瞭解支援選項結束與擴充的安全更新:

* [支援結束](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [擴充安全更新](/sql/sql-server/end-of-support/sql-server-extended-security-updates)