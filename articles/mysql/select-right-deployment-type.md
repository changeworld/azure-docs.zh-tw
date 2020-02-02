---
title: 選取正確的部署類型-適用於 MySQL 的 Azure 資料庫
description: 本文說明將適用於 MySQL 的 Azure 資料庫部署為基礎結構即服務（IaaS）或平臺即服務（PaaS）之前應考慮的因素。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8cff61d547e75b186869b3ab4d57c5eb12e6e2f5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935454"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中選擇正確的 MySQL 伺服器選項

有了 Azure，您的 MySQL 伺服器工作負載就可以在託管的虛擬機器基礎結構即服務（IaaS）中執行，或作為託管的平臺即服務（PaaS）。 PaaS 有多個部署選項，而且每個部署選項中都有服務層級。 當您選擇 IaaS 和 PaaS 時，您必須決定要管理資料庫、套用修補程式，以及進行備份，還是要將這些作業委派給 Azure。

進行決策時，請考慮下列兩個選項：

- **適用於 MySQL 的 Azure 資料庫**。 此選項是完全受控的 MySQL 資料庫引擎，以 MySQL 社區版的穩定版本為基礎。 裝載于 Azure 雲端平臺上的此關係資料庫即服務（DBaaS）屬於 PaaS 的產業類別。

  透過 Azure 上的 MySQL 受控實例，您可以使用內建功能，在 MySQL Server 是內部部署或 Azure VM 時，需要大量設定。

  使用 MySQL 即服務時，您可以使用相應增加或相應放大的選項來進行調整，以進行更好的控制，而不會有任何中斷。 和獨立的 MySQL Server 不同的是，適用於 MySQL 的 Azure 資料庫具有內建高可用性、智慧和管理等其他功能。

- **Azure vm 上的 MySQL**。 此選項屬於 IaaS 的產業類別。 透過此服務，您可以在 Azure 雲端平臺上完全受控的虛擬機器中執行 MySQL 伺服器。 所有最新版本的 MySQL 都可以安裝在 IaaS 虛擬機器上。

  與適用於 MySQL 的 Azure 資料庫的最大差異在於，Azure Vm 上的 MySQL 提供資料庫引擎的控制權。 不過，此控制是管理 Vm 和許多資料庫管理（DBA）工作的責任成本。 這些工作包括維護和修補資料庫伺服器、資料庫復原，以及高可用性的設計。

下表列出這些選項之間的主要差異：

|            | 適用於 MySQL 的 Azure 資料庫 | Azure Vm 上的 MySQL    |
|:-------------------|:-----------------------------|:--------------------|
| 服務等級協定 (SLA)                | 提供99.99% 可用性的 SLA| 同一個可用性設定組中有兩個或多個實例，最高可達99.95% 的可用性。<br/><br/>使用 premium 儲存體的單一實例 VM，99.9% 的可用性。<br/><br/>99.99% 搭配多個可用性設定組中的多個實例使用可用性區域。<br/><br/>請參閱[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 |
| 作業系統修補        | 自動  | 由客戶管理 |
| MySQL 修補     | 自動  | 由客戶管理 |
| 高可用性 | 高可用性（HA）模型是根據發生節點層級中斷時的內建容錯移轉機制。 在這種情況下，服務會自動建立新的實例，並將儲存體附加至這個實例。 | 客戶架構設計、實施、測試及維護高可用性。 功能可能包括叢集、複寫等。|
| 區域備援 | 目前不支援 | Azure Vm 可設定為在不同的可用性區域中執行。 針對內部部署解決方案，客戶必須建立、管理及維護自己的次要資料中心。|
| 混合式案例 | 使用[資料](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)輸入複寫時，您可以將外部 MySQL 伺服器的資料同步處理到適用於 MySQL 的 Azure 資料庫服務。 外部伺服器可以位於內部部署環境、虛擬機器中或其他雲端提供者所裝載的資料庫服務內。<br/><br/> 使用 [[讀取複本](https://docs.microsoft.com/azure/mysql/concepts-read-replicas)] 功能，您可以將資料從適用於 MySQL 的 Azure 資料庫主伺服器複寫到最多五部唯讀複本伺服器。 複本位於相同的 Azure 區域內或跨區域。 唯讀複本會使用 binlog 複寫技術以非同步方式更新。| 由客戶管理
| 備份與還原 | 會自動建立[伺服器備份](https://docs.microsoft.com/azure/mysql/concepts-backup#backups)，並將它們儲存在使用者設定的儲存體中，這可能是本機的冗余或異地多餘的。 此服務會取得完整、差異和交易記錄備份 | 由客戶管理 |
| 監視資料庫作業 | 讓客戶能夠設定資料庫作業的[警示](https://docs.microsoft.com/azure/mysql/concepts-monitoring)，並在達到臨界值時採取行動。 | 由客戶管理 |
| 進階威脅防護 | 提供[先進的威脅防護](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal)。 這種保護會偵測異常活動，指出不尋常且可能有害的存取或惡意探索資料庫嘗試。 | 客戶必須自行建立此保護。
| 災害復原 | 會將自動備份儲存在使用者設定的[本機多餘或異地多餘儲存體](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)中。 備份也可以將伺服器還原至某個時間點。 保留期限為7到35天的任何時間。 您可以使用 Azure 入口網站來進行還原。 | 完全由客戶管理。 責任包括但不限於排程、測試、封存、儲存和保留。 另一個選項是使用 Azure 復原服務保存庫來備份 Vm 上的 Azure Vm 和資料庫。 此選項目前為預覽狀態。 |
| 效能建議 | 根據系統產生的使用量記錄檔，為客戶提供[效能建議](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)。 建議可協助優化工作負載。 | 由客戶管理 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>選擇 PaaS 或 IaaS 的商業動機

有數個因素會影響您的決定選擇 PaaS 或 IaaS 來裝載您的 MySQL 資料庫。

### <a name="cost"></a>成本

有限的資金通常是決定裝載資料庫最佳解決方案的主要考慮。 無論您是在已建立的公司中，只有極少現金或小組，且在嚴格的預算限制之下運作，都是如此。 本節說明 Azure 中適用于 Azure Vm 上適用於 MySQL 的 Azure 資料庫和 MySQL 的計費和授權基本概念。

#### <a name="billing"></a>計費

適用於 MySQL 的 Azure 資料庫目前以服務的形式提供給數個層級，資源的價格不同。 所有資源會以固定費率按小時計費。 如需目前支援的服務層級、計算大小和儲存體數量的最新資訊，請參閱[vCore 為基礎的購買模型](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)。 您可以動態調整服務層級和計算大小，以符合您應用程式的不同輸送量需求。 以一般[資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)向您收取連出網際網路流量的費用。

透過適用於 MySQL 的 Azure 資料庫，Microsoft 會自動設定、修補和升級資料庫軟體。 這些自動化動作會降低您的系統管理成本。 此外，適用於 MySQL 的 Azure 資料庫具有[內建的備份](https://docs.microsoft.com/azure/mysql/concepts-backup)功能。 這些功能可協助您大幅節省成本，特別是當您有大量資料庫時。 相反地，透過 Azure Vm 上的 MySQL，您可以選擇並執行任何 MySQL 版本。 無論您使用的是什麼 MySQL 版本，您都必須支付已布建 VM 的費用，以及所使用之特定 MySQL 授權類型的成本。

適用於 MySQL 的 Azure 資料庫為任何種類的節點層級中斷提供內建的高可用性，同時仍保有服務的 99.99% SLA 保證。 不過，針對 Vm 內的資料庫高可用性，客戶應使用可在 MySQL 資料庫上取得的高可用性選項，例如[mysql](https://dev.mysql.com/doc/refman/8.0/en/replication.html)複寫。 使用支援的高可用性選項不會提供額外的 SLA。 但它可以讓您以額外的成本和管理額外負荷達到超過99.99% 的資料庫可用性。

如需價格的詳細資訊，請參閱下列文章：
* [適用於 MySQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/mysql/)
* [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>系統管理

對於許多企業而言，轉換至雲端服務的決策，與卸載系統管理的複雜性有關，因為這是關於成本。 有了 IaaS 和 PaaS，Microsoft：

- 管理基礎結構。
- 會自動複寫所有資料，以提供嚴重損壞修復。
- 設定和升級資料庫軟體。
- 管理負載平衡。
- 如果伺服器失敗，則會進行透明故障切換。

下列清單說明每個選項的系統管理考慮：

* 使用適用於 MySQL 的 Azure 資料庫，您可以繼續管理您的資料庫。 但是您不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的專案範例包括：

  - 資料庫
  - 登入
  - 索引微調
  - 查詢微調
  - 稽核
  - 安全性

  此外，將高可用性設定為另一個資料中心，幾乎不需要進行任何設定或管理。

* 透過 Azure Vm 上的 MySQL，您可以完全掌控作業系統和 MySQL 伺服器實例設定。 有了 VM，您可以決定何時更新或升級作業系統與資料庫軟體。 您也可以決定何時安裝任何其他軟體，例如防病毒應用程式。 提供一些自動化功能，以大幅簡化修補、備份和高可用性。 您可以控制 VM 的大小、磁片數目和其存放裝置設定。 如需詳細資訊，請參閱[Azure 的虛擬機器和雲端服務大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

### <a name="time-to-move-to-azure"></a>移至 Azure 的時間

* 針對新解決方案的開發人員生產力和快速上市時間很重要，適用於 MySQL 的 Azure 資料庫是雲端設計應用程式的正確解決方案。 有了 DBA 之類的程式設計功能，服務適用于雲端架構設計師和開發人員，因為它可降低管理基礎作業系統和資料庫的需求。

* 當您想要避免取得新內部部署硬體的時間和費用時，Azure Vm 上的 MySQL 是適用于需要 MySQL 資料庫或存取 Windows 或 Linux 上 MySQL 功能之應用程式的正確解決方案。 此解決方案也適用于將現有的內部部署應用程式和資料庫移轉至 Azure，以因應適用於 MySQL 的 Azure 資料庫不適合的情況。

  由於不需要變更簡報、應用程式和資料層，因此您可以節省重新架構現有解決方案的時間和預算。 相反地，您可以專注于將所有解決方案遷移至 Azure，並解決 Azure 平臺可能需要的一些效能優化。

## <a name="next-steps"></a>後續步驟

* 請參閱[適用於 MySQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/MySQL/)。
* 從[建立您的第一部伺服器](https://review.docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)開始。
