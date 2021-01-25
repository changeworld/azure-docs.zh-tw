---
title: 復原服務保存庫概觀
description: 復原服務保存庫的總覽。
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755122"
---
# <a name="recovery-services-vaults-overview"></a>復原服務保存庫概觀

本文說明復原服務保存庫的功能。 復原服務保存庫是 Azure 中裝載資料的儲存體實體。 資料通常是資料的副本，或是虛擬機器 (VM)、工作負載、伺服器或工作站的設定資訊。 您可以使用復原服務保存庫來保存各種 Azure 服務 (例如 IaaS VM (Linux 或 Windows) 和 Azure SQL 資料庫) 的備份資料。 復原服務保存庫支援 System Center DPM、Windows Server、Azure 備份伺服器等等。 復原服務保存庫可輕鬆地組織您的備份資料，同時可減輕管理負擔。 復原服務保存庫是以 Azure 的 Azure Resource Manager 模型為基礎，可提供下列功能：

- **可協助保護備份資料安全的增強功能**︰透過復原服務保存庫，Azure 備份能提供安全性功能來保護雲端備份。 這些安全性功能可確保您能保護您的備份，並安全地將資料復原，即使生產和備份伺服器遭到入侵也一樣。 [深入了解](backup-azure-security-feature.md)

- **將您的混合式 IT 環境集中監視**︰透過復原服務保存庫，您不只可以監視 [Azure IaaS VM](backup-azure-manage-vms.md)，還可以從中央入口網站監視 [內部部署資產](backup-azure-manage-windows-server.md#manage-backup-items)。 [深入了解](backup-azure-monitoring-built-in-monitor.md)

- Azure **角色型存取控制 (AZURE rbac)**： azure rbac 可在 azure 中提供更細緻的存取管理控制。 [Azure 提供各種內建角色](../role-based-access-control/built-in-roles.md)，且 Azure Backup 有三個[內建的角色可用來管理復原點](backup-rbac-rs-vault.md)。 復原服務保存庫與 Azure RBAC 相容，這會限制對所定義使用者角色集的備份和還原存取權。 [深入了解](backup-rbac-rs-vault.md)

- 虛 **刪除**：透過虛刪除，即使惡意執行者刪除備份 (或不小心刪除備份資料) ，備份資料仍會保留14天，以允許復原該備份專案，而不會遺失資料。 在「虛刪除」狀態中，備份資料保留的額外14天不會產生任何費用。 [深入了解](backup-azure-security-feature-cloud.md)。

- **跨區域還原**：跨區域還原 (CRR) 可讓您在次要區域（即 azure 配對的區域）中還原 azure vm。 藉由在保存 [庫層級](backup-create-rs-vault.md#set-cross-region-restore)啟用這項功能，您可以在選擇時隨時還原次要區域中複寫的資料。 這可讓您在發生嚴重損壞狀況時還原次要區域資料，而不需要等待 Azure 宣告嚴重損壞 (，與保存庫) 的 GRS 設定不同。 [深入了解](backup-azure-arm-restore-vms.md#cross-region-restore)。

## <a name="storage-settings-in-the-recovery-services-vault"></a>復原服務保存庫中的儲存體設定

復原服務保存庫是一個實體，可儲存一段時間以來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

- Azure 備份會自動處理保存庫的儲存體。 查看 [儲存設定的變更](./backup-create-rs-vault.md#set-storage-redundancy)方式。

- 若要深入瞭解儲存體冗余，請參閱這些有關[地理](../storage/common/storage-redundancy.md#geo-zone-redundant-storage)位置、區域[和](../storage/common/storage-redundancy.md#zone-redundant-storage)[區域](../storage/common/storage-redundancy.md#locally-redundant-storage)冗余的文章。

## <a name="encryption-settings-in-the-recovery-services-vault"></a>復原服務保存庫中的加密設定

本節討論可用來將儲存在復原服務保存庫中的備份資料加密的選項。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平臺管理的金鑰來加密備份資料

根據預設，您的所有資料都會使用平臺管理的金鑰進行加密。 您不需要從您的一端採取任何明確的動作，就能啟用此加密。 其適用於備份到復原服務保存庫的所有工作負載。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客戶管理的金鑰來加密備份資料

您可以選擇使用由您所擁有和管理的加密金鑰來加密您的資料。 Azure 備份可讓您使用儲存在 Azure Key Vault 中的 RSA 金鑰來加密您的備份。 用於加密備份的加密金鑰可能與用於來源的加密金鑰不同。 使用 AES 256 型資料加密金鑰來保護資料 (DEK) ，也就是使用您的金鑰加以保護。 這可讓您完整控制資料和金鑰。 若要允許加密，必須將 Azure Key Vault 中的加密金鑰存取權授與復原服務保存庫。 您可以在需要時停用金鑰或撤銷存取權。 不過，在您嘗試保護任何專案至保存庫之前，必須先使用您的金鑰啟用加密。

深入瞭解如何 [使用客戶管理的金鑰](encryption-at-rest-with-cmk.md)來加密您的備份資料。

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) 是個人化的雲端顧問，可協助您優化 Azure 的使用。 它會分析您的 Azure 使用量並提供及時的建議，以協助優化及保護您的部署。 它提供四種類別的建議：高可用性、安全性、效能和成本。

Azure Advisor 針對未備份的 Vm 提供每小時 [建議](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) ，因此您永遠不會錯過備份重要的 vm。 您也可以藉由正在延遲來控制建議。  您可以選取建議並在 Vm 上啟用備份，方法是指定保存備份的保存庫 () 和備份原則 (排程備份和保留備份複本) 。

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>其他資源

- [保存庫支援和不支援的案例](backup-support-matrix.md#vault-support)
- [保存庫的常見問題](backup-azure-backup-faq.md)

## <a name="next-steps"></a>後續步驟

使用下列文章進行：

- [備份 IaaS VM](backup-azure-arm-vms-prepare.md)
- [備份 Azure 備份伺服器](backup-azure-microsoft-azure-backup.md)
- [備份 Windows Server](backup-windows-with-mars-agent.md)
