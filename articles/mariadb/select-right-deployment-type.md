---
title: 選取正確的部署類型-適用於 MariaDB 的 Azure 資料庫
description: 本文說明在您將適用於 MariaDB 的 Azure 資料庫部署為基礎結構即服務 (IaaS) 或平臺即服務 (PaaS) 之前應考慮的因素。
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 921ad8d187f6c2478bdf92aab0ee0ec3c9e75bce
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664513"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>在 Azure 中選擇正確的適用于 mariadb 伺服器選項

使用 Azure 時，您的適用于 mariadb 伺服器工作負載可以在託管的虛擬機器基礎結構即服務 (IaaS) 或作為託管平臺即服務 (PaaS) 中執行。 PaaS 有多個部署選項，而且每個部署選項中都有服務層級。 選擇要使用 IaaS 和 PaaS 時，必須判斷您是否想管理資料庫、套用修補檔及進行備份，或是您要將這些作業委派給 Azure。

進行決策時，請考慮下列兩個選項：

- **適用於 MariaDB 的 Azure 資料庫：** 此選項是一種完全受控的適用于 mariadb 資料庫引擎，以適用于 mariadb 的穩定版本為基礎。 此關係資料庫即服務 (裝載在 Azure 雲端平臺上的 DBaaS) ，屬於 PaaS 的產業類別。

  使用 Azure 上的適用于 mariadb 受控實例時，您可以使用內建功能，當適用于 mariadb Server 在內部部署或 Azure VM 中時，您也可以使用其他需要大量設定的功能。

  使用適用于 mariadb 做為服務時，您可以選擇相應增加或相應放大的選項，以在不中斷的情況下進行更好的控制。 和獨立適用于 mariadb 伺服器不同的是，適用於 MariaDB 的 Azure 資料庫具有內建高可用性、智慧和管理等其他功能。

- **Azure vm 上的適用于 mariadb：** 此選項落在 IaaS 的產業類別中。 透過此服務，您可以在 Azure 雲端平臺上完全受控的虛擬機器內執行適用于 mariadb 伺服器。 所有最新版本的適用于 mariadb 都可以安裝在 IaaS 虛擬機器上。

  在適用於 MariaDB 的 Azure 資料庫的最大差異，Azure Vm 上的適用于 mariadb 可提供資料庫引擎的控制權。 不過，此控制權是管理 VM 和許多資料庫管理 (DBA) 工作的責任成本。 這些工作包括維護和修補資料庫伺服器、資料庫復原，以及高可用性的設計。

下表列出這些選項之間的主要差異：

| 屬性          | 適用於 MariaDB 的 Azure 資料庫 | Azure Vm 上的適用于 mariadb    |
|:-------------------|:-----------------------------|:--------------------|
| 服務等級協定 (SLA)                | 提供99.99% 可用性的 SLA| 同一個可用性設定組中的兩個或多個實例最高可達99.95% 的可用性。<br/><br/>使用 premium 儲存體的單一實例 VM 可用99.9% 的可用性。<br/><br/>使用可用性區域具有多個可用性設定組中的多個實例時，99.99%。<br/><br/>請參閱 [虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 |
| 作業系統修補        | 自動  | 由客戶管理 |
| 適用于 mariadb 修補     | 自動  | 由客戶管理 |
| 高可用性 | 高可用性 (HA) 模型是根據發生節點層級中斷時的內建容錯移轉機制。 在這種情況下，服務會自動建立新的實例，並將儲存體附加到這個實例。 | 客戶設計師、實行、測試及維護高可用性。 功能可能包括 always on 容錯移轉叢集、always on 群組複寫、記錄傳送或異動複寫。|
| 區域備援 | 目前不支援 | 您可以將 Azure Vm 設定為在不同的可用性區域中執行。 針對內部部署解決方案，客戶必須建立、管理及維護自己的次要資料中心。|
| 混合式案例 | 使用 [資料輸入複寫](concepts-data-in-replication.md)，您可以將外部適用于 mariadb 伺服器的資料同步處理到適用於 MariaDB 的 Azure 資料庫服務。 外部伺服器可以位於內部部署環境、虛擬機器中或其他雲端提供者所裝載的資料庫服務內。<br/><br/> 使用 [讀取複本](concepts-read-replicas.md) 功能時，您可以將適用於 MariaDB 的 Azure 資料庫來源伺服器的資料複寫到最多五個唯讀複本伺服器。 這些複本位於相同的 Azure 區域內或跨區域。 唯讀複本會使用 binlog 複寫技術以非同步方式更新。<br/><br/>跨區域讀取複寫目前為公開預覽狀態。| 由客戶管理
| 備份與還原 | 會自動建立 [伺服器備份](concepts-backup.md#backups) ，並將它們儲存在使用者設定的存放裝置中，該儲存體可能會在本機或地理位置重複。 服務會取得完整、差異及交易記錄備份 | 由客戶管理 |
| 監視資料庫作業 | 提供客戶在資料庫作業上 [設定警示](concepts-monitoring.md) 的能力，並在達到閾值時採取行動。 | 由客戶管理 |
| 進階威脅防護 | 提供 [先進的威脅防護](howto-database-threat-protection-portal.md)。 這種保護會偵測異常活動，指出存取或惡意探索資料庫時不尋常且可能有害的嘗試。<br/><br/>Advanced 威脅防護目前處於公開預覽狀態。| 客戶必須自行建立此保護。
| 災害復原 | 將自動備份儲存在使用者設定的 [本機冗余或地理位置多餘的儲存體](howto-restore-server-portal.md)中。 備份也可以將伺服器還原至某個時間點。 保留期限為7到35天的任何時間。 使用 Azure 入口網站可以完成還原。 | 完全受客戶管理。 責任包括但不限於排程、測試、封存、儲存和保留。 另一個選項是使用 Azure 復原服務保存庫來備份 Vm 上的 Azure Vm 和資料庫。 此選項目前為預覽狀態。 |
| 效能建議 | 根據系統產生的使用量記錄檔，提供客戶 [效能建議](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) 。 這些建議可協助您將工作負載優化。<br/><br/>效能建議目前處於公開預覽狀態。 | 由客戶管理 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>選擇 PaaS 或 IaaS 的商業動機

有幾個因素會影響您的決定選擇 PaaS 或 IaaS 來裝載您的適用于 mariadb 資料庫。

### <a name="cost"></a>成本

有限的資金通常是決定裝載資料庫最佳解決方案的主要考慮。 無論是只有極少現金的新創公司，或是在有規模公司中受到嚴格的預算限制下運作的小組，都會以 TCO 作為主要考量。 本節說明 Azure 中適用于 Azure Vm 上適用於 MariaDB 的 Azure 資料庫和適用于 mariadb 的計費和授權基本概念。

#### <a name="billing"></a>計費

適用於 MariaDB 的 Azure 資料庫目前以服務的形式提供給數個層級，且資源的價格不同。 所有資源均會以固定費率按小時計費。 如需目前支援的服務層級、計算大小和儲存體數量的最新資訊，請參閱以 [vCore 為基礎的購買模型](concepts-pricing-tiers.md)。 您可以動態調整服務層級和計算大小，以符合您應用程式的各種輸送量需求。 傳出的網際網路流量也會以一般[資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

使用適用於 MariaDB 的 Azure 資料庫，Microsoft 會自動設定、修補及升級資料庫軟體。 這些自動化動作會降低您的管理成本。 此外，適用於 MariaDB 的 Azure 資料庫也有 [內建的備份](concepts-backup.md) 功能。 這些功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。 相反地，在 Azure Vm 上使用適用于 mariadb，您可以選擇並執行任何適用于 mariadb 版本。 無論您使用的適用于 mariadb 版本為何，您需要支付已布建 VM 的費用，以及所用特定適用于 mariadb 授權類型的成本。

適用於 MariaDB 的 Azure 資料庫針對任何類型的節點層級中斷提供內建的高可用性，同時仍維持服務的 99.99% SLA 保證。 不過，若要讓 Vm 內的資料庫高可用性，客戶應該使用高可用性選項，例如適用于 mariadb 資料庫上可用的 [適用于 mariadb](https://mariadb.com/kb/en/library/setting-up-replication/) 複寫。 使用支援的高可用性選項並不提供額外的 SLA， 但在支付額外成本和管理負荷的情況下，可讓您達成優於 99.99% 的資料庫可用性。

如需價格的詳細資訊，請參閱下列文章：
* [適用於 MariaDB 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/MariaDB/)
* [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度，因為這直接影響到成本。 使用 IaaS 和 PaaS，Microsoft：

- 管理基礎結構。
- 自動複製所有資料以提供嚴重損壞修復。
- 設定及升級資料庫軟體。
- 管理負載平衡。
- 如果伺服器失敗，則會進行透明的容錯移轉。

下列清單說明每個選項的系統管理考慮：

* 使用適用於 MariaDB 的 Azure 資料庫，您可以繼續管理您的資料庫。 但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例如下：

  - 資料庫
  - 登入
  - 索引微調
  - 查詢微調
  - 稽核
  - 安全性

  此外，設定其他資料中心的高可用性時，至少需要進行的最小設定或系統管理。

* 有了 Azure Vm 上的適用于 mariadb，您就可以完全掌控作業系統和適用于 mariadb 伺服器實例設定。 使用 VM 時，您可以決定何時更新或升級作業系統和資料庫軟體。 您也可以決定何時安裝任何其他軟體，例如防毒應用程式。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 您還可以控制 VM 的大小、磁碟數目及其儲存體組態。 如需詳細資訊，請參閱[Azure 的虛擬機器和雲端服務大小](../virtual-machines/sizes.md)。

### <a name="time-to-move-to-azure"></a>移至 Azure 的時間

* 當新解決方案的開發人員生產力和快速上市時間很重要時，適用於 MariaDB 的 Azure 資料庫是適合雲端設計應用程式的解決方案。 有了類似 DBA 的程式設計功能，此服務非常適合雲端架構設計人員和開發人員，因為其會降低管理基礎作業系統和資料庫的需求。

* 當您想要避免取得新內部部署硬體的時間和費用時，在 Azure Vm 上適用于 mariadb 是適用于需要適用于 mariadb 資料庫或存取 Windows 或 Linux 上適用于 mariadb 功能的應用程式的正確解決方案。 此解決方案也適用于將現有的內部部署應用程式和資料庫移轉至 Azure，以因應適用於 MariaDB 的 Azure 資料庫大小不佳的情況。

  因為不需要變更簡報、應用程式和資料層，所以您可以節省重新架構現有解決方案的時間和預算。 相反地，您可以專注于將所有解決方案遷移至 Azure，並解決 Azure 平臺可能需要的一些效能優化。

## <a name="next-steps"></a>下一步

* 請參閱 [適用於 MariaDB 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/MariaDB/)。
* 從[建立您的第一部伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)開始。
