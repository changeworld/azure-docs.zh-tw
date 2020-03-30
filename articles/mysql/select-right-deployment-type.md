---
title: 選擇正確的部署類型 - MySQL 的 Azure 資料庫
description: 本文介紹在將 MySQL Azure 資料庫部署為基礎結構作為服務 （IaaS） 或平臺作為服務 （PaaS） 之前需要考慮哪些因素。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255540"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中選擇正確的 MySQL 伺服器選項

使用 Azure，MySQL 伺服器工作負載可以在託管虛擬機器基礎結構中作為服務 （IaaS） 或作為託管平臺作為服務 （PaaS） 運行。 PaaS 具有多個部署選項，並且每個部署選項中都有服務層。 在 IaaS 和 PaaS 之間進行選擇時，必須決定是否要管理資料庫、應用修補程式和進行備份，或者是否要將這些操作委派給 Azure。

在做決定時，請考慮以下兩個選項：

- **MySQL 的 Azure 資料庫**。 此選項是基於 MySQL 社區版本的穩定版本，是完全託管的 MySQL 資料庫引擎。 此關係資料庫作為服務 （DBaaS） 託管在 Azure 雲平臺上，屬於 PaaS 的行業類別。

  在 Azure 上使用 MySQL 的託管實例，可以使用內置功能，否則當 MySQL 伺服器位於本地或 Azure VM 中時，這些功能需要大量配置。

  使用 MySQL 作為服務時，您需要使用選項進行擴展或橫向擴展，以增強控制，而不會中斷。 與獨立的 MySQL 伺服器不同，MySQL 的 Azure 資料庫具有內置的高可用性、智慧和管理等其他功能。

- **Azure VM 上的 MySQL。** 此選項屬於 IaaS 的行業類別。 使用此服務，您可以在 Azure 雲平臺上的完全託管虛擬機器內運行 MySQL 伺服器。 MySQL 的所有最新版本和版本都可以安裝在 IaaS 虛擬機器上。

  與 MySQL 的 Azure 資料庫最顯著的區別是，Azure VM 上的 MySQL 提供了對資料庫引擎的控制。 但是，此控制項的成本是管理 VM 和許多資料庫管理 （DBA） 任務。 這些任務包括維護和修補資料庫伺服器、資料庫恢復和高可用性設計。

下表列出這些選項之間的主要差異：

|            | 適用於 MySQL 的 Azure 資料庫 | Azure VM 上的 MySQL    |
|:-------------------|:-----------------------------|:--------------------|
| 服務等級協定 （SLA）                | 提供 99.99% 可用性的 SLA| 在同一可用性集中具有兩個或多個實例，可用性高達 99.95%。<br/><br/>使用高級存儲的單個實例 VM 提供 99.9% 的可用性。<br/><br/>99.99% 使用具有多個實例的可用區域，在多個可用性集中。<br/><br/>請參閱[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 |
| 作業系統修補        | 自動  | 由客戶管理 |
| MySQL 修補     | 自動  | 由客戶管理 |
| 高可用性 | 高可用性 （HA） 模型基於節點級中斷時的內置容錯移轉機制。 在這種情況下，服務會自動創建一個新實例並將存儲附加到此實例。 | 客戶設計、實現、測試和維護高可用性。 功能可能包括群集、複製等。|
| 區域備援 | 目前不支援 | 可以將 Azure VM 設置為在不同的可用性區域中運行。 對於本地解決方案，客戶必須創建、管理和維護自己的輔助資料中心。|
| 混合式案例 | 使用[資料輸入複製](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)，可以將來自外部 MySQL 伺服器的資料同步到 MySQL 服務的 Azure 資料庫。 外部伺服器可以位於內部部署環境、虛擬機器中或其他雲端提供者所裝載的資料庫服務內。<br/><br/> 使用[讀取副本](https://docs.microsoft.com/azure/mysql/concepts-read-replicas)功能，可以將 MySQL 主伺服器的 Azure 資料庫資料複製到最多五個唯讀副本伺服器。 副本位於同一 Azure 區域或跨區域。 使用 binlog 複製技術非同步更新唯讀副本。| 由客戶管理
| 備份和恢復 | 自動創建[伺服器備份](https://docs.microsoft.com/azure/mysql/concepts-backup#backups)並將其存儲在使用者配置的存儲中，這些存儲是本地冗余的或異地冗余的。 該服務需要完整、差異和事務記錄備份 | 由客戶管理 |
| 監視資料庫操作 | 為客戶提供在資料庫操作[上設置警報](https://docs.microsoft.com/azure/mysql/concepts-monitoring)並在達到閾值時採取行動的能力。 | 由客戶管理 |
| 進階威脅防護 | 提供[高級威脅防護](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal)。 此保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。 | 客戶必須為自己構建這種保護。
| 災害復原 | 將自動備份存儲在使用者配置的[本地冗余或異地冗余存儲](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)中。 備份還可以將伺服器還原到某個時間點。 保留期為 7 到 35 天。 還原通過使用 Azure 門戶完成。 | 完全由客戶管理。 職責包括但不限於計畫、測試、歸檔、存儲和保留。 另一個選項是使用 Azure 恢復服務保存庫備份 VM 上的 Azure VM 和資料庫。 此選項處於預覽狀態。 |
| 效能建議 | 根據系統生成的使用方式日誌檔為客戶提供[性能建議](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)。 這些建議有助於優化工作負載。 | 由客戶管理 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>選擇 PaaS 或 IaaS 的業務動機

有幾個因素會影響您選擇 PaaS 或 IaaS 來託管 MySQL 資料庫的決定。

### <a name="cost"></a>成本

有限的資金通常是確定託管資料庫的最佳解決方案的首要考慮因素。 無論您是現金很少的初創公司，還是在預算緊張約束下運營的老牌公司中的團隊，都是如此。 本節介紹 Azure 中應用於 Azure VM 上的 MySQL 和 MySQL 的 Azure 資料庫的計費和許可基礎知識。

#### <a name="billing"></a>計費

MySQL 的 Azure 資料庫目前作為服務在資源價格不同的多個層中提供。 所有資源都以固定費率每小時計費。 有關當前受支援的服務層、計算大小和存儲量的最新資訊，請參閱[基於 vCore 的採購模型](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)。 您可以動態調整服務層和計算大小，以滿足應用程式的各種輸送量需求。 您會以常規[資料傳輸速率](https://azure.microsoft.com/pricing/details/data-transfers/)為傳出的互聯網流量收費。

使用 MySQL 的 Azure 資料庫，Microsoft 會自動設定、修補和升級資料庫軟體。 這些自動化操作可降低您的管理成本。 此外，MySQL 的 Azure 資料庫具有[內置備份](https://docs.microsoft.com/azure/mysql/concepts-backup)功能。 這些功能可説明您顯著節約成本，尤其是在擁有大量資料庫時。 相反，使用 Azure VM 上的 MySQL，您可以選擇並運行任何 MySQL 版本。 無論您使用哪種 MySQL 版本，您都為預配的 VM 以及所使用的特定 MySQL 許可證類型的成本付費。

MySQL 的 Azure 資料庫為任何類型的節點級中斷提供內置的高可用性，同時仍維護服務的 99.99% SLA 保證。 但是，對於 VM 中的資料庫高可用性，客戶應使用 MySQL 資料庫中可用的高可用性選項，如 MySQL 資料庫上可用的[MySQL 複製](https://dev.mysql.com/doc/refman/8.0/en/replication.html)。 使用受支援的高可用性選項不會提供額外的 SLA。 但它確實允許您以額外的成本和管理開銷實現超過 99.99% 的資料庫可用性。

有關定價的詳細資訊，請參閱以下文章：
* [用於 MySQL 定價的 Azure 資料庫](https://azure.microsoft.com/pricing/details/mysql/)
* [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>系統管理

對於許多企業來說，向雲服務過渡的決定與成本一樣關上管理的複雜性。 使用 IaaS 和 PaaS，微軟：

- 管理底層基礎結構。
- 自動複製所有資料以提供災害復原。
- 配置和升級資料庫軟體。
- 管理負載平衡。
- 如果伺服器出現故障，則執行透明容錯移轉。

以下清單描述了每個選項的管理注意事項：

* 使用 MySQL 的 Azure 資料庫，可以繼續管理資料庫。 但是，您不再需要管理資料庫引擎、作業系統或硬體。 可以繼續管理的專案示例包括：

  - 資料庫
  - 登入
  - 索引調整
  - 查詢調優
  - 稽核
  - 安全性

  此外，將高可用性配置為其他資料中心需要最少到沒有配置或管理。

* 借助 Azure VM 上的 MySQL，您可以完全控制作業系統和 MySQL 伺服器實例配置。 使用 VM，您可以決定何時更新或升級作業系統和資料庫軟體。 您還可以決定何時安裝任何其他軟體（如防病毒應用程式）。 提供了一些自動化功能，可大大簡化修補、備份和高可用性。 您可以控制 VM 的大小、磁片數及其存儲配置。 有關詳細資訊，請參閱[Azure 的虛擬機器和雲服務大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

### <a name="time-to-move-to-azure"></a>移至 Azure 的時間

* MySQL 的 Azure 資料庫是雲設計應用程式的正確解決方案，因為開發人員的工作效率和新解決方案的快速上市時間至關重要。 使用類似于 DBA 的程式設計功能，該服務適用于雲架構師和開發人員，因為它降低了管理底層作業系統和資料庫的需求。

* 當您希望避免獲取新的本地硬體的時間和費用時，Azure VM 上的 MySQL 是需要 MySQL 資料庫或訪問 Windows 或 Linux 上的 MySQL 功能的應用程式的正確解決方案。 此解決方案還適用于將現有的本地應用程式和資料庫完整地遷移到 Azure，適用于 MySQL 的 Azure 資料庫不適合的情況。

  由於無需更改演示文稿、應用程式和資料層，因此您可以節省重新構建現有解決方案的時間和預算。 相反，您可以專注于將所有解決方案遷移到 Azure 並解決 Azure 平臺可能需要的某些性能優化。

## <a name="next-steps"></a>後續步驟

* 有關[MySQL 定價，](https://azure.microsoft.com/pricing/details/MySQL/)請參閱 Azure 資料庫。
* 從[建立您的第一部伺服器](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)開始。
