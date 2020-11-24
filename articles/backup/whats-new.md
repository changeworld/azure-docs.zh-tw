---
title: Azure 備份的新功能
description: 瞭解 Azure 備份中的新功能。
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: e48a7df1e42591843c30b77026a4d8656773a57d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511444"
---
# <a name="whats-new-in-azure-backup"></a>Azure 備份的新功能

Azure 備份不斷地改進和釋出新功能，以增強 Azure 中資料的保護。 這些新功能會將您的資料保護擴充到新的工作負載類型、增強安全性，並改善備份資料的可用性。 它們也加入了新的管理、監視和自動化功能。

若要深入瞭解新版本，您可以將此頁面加入書簽，或在 [這裡訂閱更新](https://azure.microsoft.com/updates/?query=backup)。

## <a name="updates-summary"></a>更新摘要

- 2020 年 11 月
  - [Azure Vm 上的 SAP Hana 資料庫增量備份](#incremental-backups-for-sap-hana-databases)
- 2020 年 9 月
  - [備份中心](#backup-center)
  - [備份適用於 PostgreSQL 的 Azure 資料庫](#backup-azure-database-for-postgresql)
  - [選擇性磁片備份和還原](#selective-disk-backup-and-restore)
  - [Azure Vm 上 SQL Server 和 SAP Hana 資料庫的跨區域還原](#cross-region-restore-for-sql-server-and-sap-hana)
  - [支援最多32個磁片的 Vm 備份](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Azure Vm 中 SQL 的簡化備份設定體驗](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL Azure 虛擬機器中的備份 SAP Hana](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [適用于備份資料的區域冗余儲存體 (ZRS) ](#zone-redundant-storage-zrs-for-backup-data)
  - [Azure Vm 中 SQL Server 和 SAP Hana 工作負載的虛刪除](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="incremental-backups-for-sap-hana-databases"></a>SAP Hana 資料庫的增量備份

Azure 備份現在針對裝載于 Azure Vm 上的 SAP Hana 資料庫支援增量備份。 這可讓您更快速且更符合成本效益的方式備份您的 SAP Hana 資料。

如需詳細資訊，請參閱 [建立備份原則期間的各種可用選項](sap-hana-faq-backup-azure-vm.md#policy) ，以及 [如何建立 SAP Hana 資料庫的備份原則](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)。

## <a name="backup-center"></a>備份中心

Azure 備份已啟用新的原生管理功能，可從中央主控台管理整個備份資產。 備份中心可讓您以一致的方式，以一致的方式監視、操作、管理及優化資料保護，以符合 Azure 的原生管理體驗。

使用備份中心，您可以在訂用帳戶、位置、資源群組、保存庫，甚至是使用 Azure Lighthouse 的租使用者中，取得清查的匯總觀點。 備份中心也是一種控制中心，可讓您從單一位置觸發備份相關的活動，例如設定備份、還原、建立原則或保存庫。 此外，透過與 Azure 原則的緊密整合，您現在可以管理您的環境，並從備份的觀點來追蹤合規性。 Azure 備份特定的內建 Azure 原則也可讓您大規模設定備份。

如需詳細資訊，請參閱 [備份中心的總覽](backup-center-overview.md)。

## <a name="backup-azure-database-for-postgresql"></a>備份適用於 PostgreSQL 的 Azure 資料庫

Azure 備份和 Azure 資料庫服務已建立 Azure 于 postgresql (的企業級備份解決方案，現已推出預覽) 。 現在您可以使用客戶控制的備份原則來滿足您的資料保護和合規性需求，讓備份保留最多10年的時間。 如此一來，您就可以更細微地控制在個別資料庫層級上管理備份和還原作業。 同樣地，您可以輕鬆地在於 postgresql 版本或 blob 儲存體間還原。

如需詳細資訊，請參閱 [適用於 PostgreSQL 的 Azure 資料庫備份](backup-azure-database-postgresql.md)。

## <a name="selective-disk-backup-and-restore"></a>選擇性磁片備份和還原

Azure 備份支援使用虛擬機器備份解決方案，同時備份 VM 中的所有磁片 (作業系統和資料) 。 現在，您可以使用選擇性磁片備份和還原功能，在 VM 中備份資料磁片的子集。 如此可提供有效率且符合成本效益的解決方案，以滿足您的備份和還原需求。 每個復原點只包含備份作業中包含的磁片。

如需詳細資訊，請參閱 [Azure 虛擬機器的選擇性磁片備份和還原](selective-disk-backup-restore.md)。

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>SQL Server 和 SAP Hana 的跨區域還原

隨著跨區域還原的推出，您現在可以在次要區域中起始還原，以降低環境主要區域中的即時停機問題。 這會讓次要區域還原完全由客戶控制。 Azure 備份使用複寫至次要區域的備份資料進行這類還原。

現在，除了支援 Azure 虛擬機器的跨區域還原之外，也已擴充功能來還原 Azure 虛擬機器中的 SQL 和 SAP Hana 資料庫。

如需詳細資訊，請參閱 [SQL 資料庫的跨區域還原](restore-sql-database-azure-vm.md#cross-region-restore) 和 [SAP Hana 資料庫的跨區域還原](sap-hana-db-restore.md#cross-region-restore)。

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>支援最多32個磁片的 Vm 備份

到目前為止，Azure 備份支援每個 VM 16 個受控磁片。 現在，Azure 備份可針對每個 VM 支援最多32個受控磁片的備份。

如需詳細資訊，請參閱 [VM 儲存體支援矩陣](backup-support-matrix-iaas.md#vm-storage-support)。

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Azure Vm 中的 SQL 較簡單的備份設定

在 Azure Vm 中設定 SQL Server 的備份現在更容易，因為整合到 Azure 入口網站的 VM 窗格中的內嵌備份設定更容易。 只要幾個步驟，您就可以啟用 SQL Server 的備份，以保護所有現有的資料庫，以及未來新增的資料庫。

如需詳細資訊，請參閱 [[VM] 窗格中的 [備份 SQL Server]](backup-sql-server-vm-from-vm-pane.md)。

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>RHEL Azure 虛擬機器中的備份 SAP Hana

Azure 備份是適用于 Azure 的原生備份解決方案，而且是由 SAP 認證的 BackInt。 Azure 備份現已新增 Red Hat Enterprise Linux (RHEL) 的支援，這是執行 SAP Hana 最普遍使用的 Linux 作業系統之一。

如需詳細資訊，請參閱 [SAP Hana 資料庫備份案例支援矩陣](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>適用于備份資料的區域冗余儲存體 (ZRS) 

Azure 儲存體可提供高效能、高可用性和高資料復原的絕佳平衡，以及其不同的冗余選項。 Azure 備份也可讓您將這些優點延伸至備份資料，以及將備份儲存在本機冗余儲存體的選項， (LRS) 和異地複寫儲存體 (GRS) 。 現在有額外的持久性選項，其中新增了區域冗余儲存體 (ZRS) 的支援。

如需詳細資訊，請參閱 [設定復原服務保存庫的儲存體冗余](backup-create-rs-vault.md#set-storage-redundancy)。

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server 和 SAP Hana 工作負載的虛刪除

現在越來越重視安全性問題，例如惡意程式碼、勒索軟體和入侵。 這些安全性問題在成本和資料方面付出的代價很高。 為了防範這類攻擊，Azure 備份提供安全性功能來協助保護備份資料，甚至是在刪除之後。

其中一項功能是虛刪除。 使用虛刪除時，即使惡意執行者刪除備份 (或不小心刪除備份資料) ，備份資料仍會保留14天，以允許復原該備份專案而不會遺失資料。 在「虛刪除」狀態中，備份資料保留的額外14天不會產生任何費用。

現在，除了 Azure Vm 的虛刪除支援之外，Azure Vm 中的 SQL Server 和 SAP Hana 工作負載也會透過虛刪除來保護。

如需詳細資訊，請參閱 [AZURE vm 中的 SQL server 虛刪除，以及 AZURE vm 工作負載中的 SAP Hana](soft-delete-sql-saphana-in-azure-vm.md)。

## <a name="next-steps"></a>後續步驟

- [Azure 備份指導方針和最佳作法](guidance-best-practices.md)
