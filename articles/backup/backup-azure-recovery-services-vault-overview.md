---
title: 復原服務保存庫概觀
description: 復原服務保存庫和 Azure 備份保存庫之間的概觀與比較。
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e1d061c6baf31fad2e937a604098f0baff6086d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041896"
---
# <a name="recovery-services-vaults-overview"></a>復原服務保存庫概觀

本文說明復原服務保存庫的功能。 復原服務保存庫是 Azure 中裝載資料的儲存體實體。 資料通常是資料的副本，或是虛擬機器 (VM)、工作負載、伺服器或工作站的設定資訊。 您可以使用復原服務保存庫來保存各種 Azure 服務 (例如 IaaS VM (Linux 或 Windows) 和 Azure SQL 資料庫) 的備份資料。 復原服務保存庫支援 System Center DPM、Windows Server、Azure 備份伺服器等等。 復原服務保存庫可輕鬆地組織您的備份資料，同時可減輕管理負擔。 復原服務保存庫是以 Azure 的 Azure Resource Manager 模型為基礎，提供下列功能：

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>比較復原服務保存庫與備份保存庫

- **可協助保護備份資料安全的增強功能**︰透過復原服務保存庫，Azure 備份能提供安全性功能來保護雲端備份。 這些安全性功能可確保您能保護您的備份，並安全地將資料復原，即使生產和備份伺服器遭到入侵也一樣。 [深入了解](backup-azure-security-feature.md)

- **將您的混合式 IT 環境集中監視**︰透過復原服務保存庫，您不只可以監視 [Azure IaaS VM](backup-azure-manage-vms.md)，還可以從中央入口網站監視[內部部署資產](backup-azure-manage-windows-server.md#manage-backup-items)。 [深入了解](backup-azure-monitoring-built-in-monitor.md)

- **角色型存取控制 (RBAC)**：RBAC 提供 Azure 中的更細緻存取權管理。 [Azure 提供各種內建角色](../role-based-access-control/built-in-roles.md)，且 Azure Backup 有三個[內建的角色可用來管理復原點](backup-rbac-rs-vault.md)。 復原服務保存庫與 RBAC 相容，且會對一組定義之使用者角色的備份和還原存取權限加以限制。 [深入了解](backup-rbac-rs-vault.md)

- 虛**刪除**：透過虛刪除，即使惡意執行者刪除備份 (或不小心刪除備份資料) ，備份資料會保留14個額外的天數，允許復原該備份專案，而不會遺失資料。 在「虛刪除」狀態中，備份資料的額外14天保留期不會對客戶產生任何費用。 [深入了解](backup-azure-security-feature-cloud.md)。

- **跨區域還原**：跨區域還原 (的 CRR) 可讓您在次要區域（也就是 azure 配對的區域）中還原 azure vm。 如果 Azure 在主要區域中宣告了嚴重損壞，次要區域中複寫的資料可以在次要區域中進行還原，以減輕其環境主要區域中的即時停機嚴重損壞。 [深入了解](backup-azure-arm-restore-vms.md#cross-region-restore)。

## <a name="storage-settings-in-the-recovery-services-vault"></a>復原服務保存庫中的儲存體設定

復原服務保存庫是一個實體，可儲存一段時間以來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

- Azure 備份會自動處理保存庫的儲存體。 瞭解如何[變更存放裝置設定](./backup-create-rs-vault.md#set-storage-redundancy)。

- 若要深入瞭解儲存體冗余，請參閱關於[異地](../storage/common/storage-redundancy.md)和[本機](../storage/common/storage-redundancy.md)冗余的文章。

### <a name="additional-resources"></a>其他資源

- [保存庫支援和不支援的案例](backup-support-matrix.md#vault-support)
- [保存庫常見問題](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml)是個人化的雲端顧問，可協助優化 Azure 的使用。 它會分析您的 Azure 使用量並提供及時建議，以協助優化和保護您的部署。 它提供四種類別的建議：高可用性、安全性、效能和成本。

Azure Advisor 針對未備份的 Vm 提供每小時的[建議](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion)，因此您永遠不會錯過備份重要的 vm。 您也可以藉由 snoozing 來控制建議。  您可以藉由指定要儲存備份的保存庫 (來選取建議，並在 Vm 上啟用備份) 而備份原則會 (備份和保留備份複本的排程) 。

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>後續步驟

使用下列文章進行：</br>
[備份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[備份 Azure 備份伺服器](backup-azure-microsoft-azure-backup.md)</br>
[備份 Windows Server](backup-windows-with-mars-agent.md)
