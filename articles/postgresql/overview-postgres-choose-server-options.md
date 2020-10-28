---
title: 在 Azure 中選擇適當的 PostgreSQL 伺服器選項
description: 提供為您的部署選擇正確 PostgreSQL 伺服器選項的指導方針。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 8bc303f619d145cc280e6caab65781bd42d1b314
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489688"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>在 Azure 中選擇適當的 PostgreSQL 伺服器選項

有了 Azure，您的 PostgreSQL 伺服器工作負載就可以在託管的虛擬機器基礎結構即服務 (IaaS) 中執行，或作為託管的平台即服務 (PaaS)。 PaaS 有多個部署選項，每個都有多個服務層級。 選擇要使用 IaaS 和 PaaS 時，必須判斷您是否想管理資料庫、套用修補檔及進行備份，或是您要將這些作業委派給 Azure。

進行決策時，請考慮 PaaS 的下列三個選項，或在 Azure VM (IaaS) 上執行
- [適用於 PostgreSQL 的 Azure 資料庫單一伺服器](./overview-single-server.md)
- [適用於 PostgreSQL 的 Azure 資料庫彈性伺服器](./flexible-server/overview.md)
- [適用於 PostgreSQL 的超大規模 Azure 資料庫 (Citus)]()

**PostgreSQL on Azure VM** 選項屬於 IaaS 的企業類別。 透過此服務，您可以在 Azure 雲端平台上完全受控的虛擬機器中執行 PostgreSQL 伺服器。 所有最新版本的 PostgreSQL 都可以安裝在 IaaS 虛擬機器上。 PostgreSQL 與適用於 PostgreSQL 的 Azure 資料庫的最大差異在於，PostgreSQL on Azure VM 提供資料庫引擎的控制權。 不過，此控制權是管理 VM 和許多資料庫管理 (DBA) 工作的責任成本。 這些工作包括維護和修補資料庫伺服器、資料庫復原，以及高可用性的設計。

下表列出這些選項之間的主要差異：

| **Attribute** | **Azure VM 上的 PostgreSQL** | **PostgreSQL 作為 PaaS** |
| ----- | ----- | ----- |
| <B>可用性 SLA |- 99.99% 可用性設定組 <br> - 99.95% 具有單一 VM | - 單一伺服器 – 99.99% <br> - 彈性伺服器 - 預覽期間無法使用 <br> - 超大規模 (Citus) - 99.95% (啟用高可用性時)|
| <B> OS 和 PostgreSQL 修補 | - 由客戶管理 | - 單一伺服器 - 自動 <br> - 彈性伺服器 - 自動使用選用的受客戶管理視窗 <br> - 超大規模 (Citus) - 自動 |
| <B> 高可用性 | - 客戶架構、實作、測試和維護高可用性。 功能可能包括叢集、複寫等。 | - 單一伺服器：內建 <br> - 彈性伺服器：內建 <br> - 超大規模 (Citus)：內建為待命 |
| <B> 區域備援 | - 您可以將 Azure VM 設定為在不同的可用性區域中執行。 針對內部部署解決方案，客戶必須建立、管理及維護自己的次要資料中心。 | - 單一伺服器：否 <br> - 彈性伺服器：是 <br> - 超大規模 (Citus)：否 |
| <B> 混合式案例 | - 由客戶管理 |- 單一伺服器：讀取複本 <br> - 彈性伺服器：預覽期間無法使用 <br> - 超大規模 (Citus)：否 |
| <B> 備份與還原 | - 由客戶管理 | - 單一伺服器：內建供本機和異地使用的使用者組態 <br> - 彈性伺服器：內建在區域備援儲存體上的使用者組態 <br> - 超大規模 (Citus)：內建 |
| <B> 監視資料庫作業 | - 由客戶管理 | - 單一伺服器、彈性伺服器和超大規模 (Citus)：均可讓客戶設定資料庫作業的警示，並在達到臨界值時採取行動。 |
| <B> 進階威脅防護 | - 客戶必須自行建置此保護。 |- 單一伺服器：是 <br> - 彈性伺服器：預覽期間無法使用 <br> - 超大規模 (Citus)：否 |
| <B> 災害復原 | - 由客戶管理 | - 單一伺服器：異地備援備份和備份讀取複本 <br> - 彈性伺服器：預覽期間無法使用 <br> - 超大規模 (Citus)：否 |
| <B> 智慧型效能 | - 由客戶管理 | - 單一伺服器：是 <br> - 彈性伺服器：預覽期間無法使用 <br> - 超大規模 (Citus)：否 |

## <a name="total-cost-of-ownership-tco"></a>擁有權總成本 (TCO)

TCO 通常是決定裝載資料庫最佳解決方案的主要考量。 無論是只有極少現金的新創公司，或是在有規模公司中受到嚴格的預算限制下運作的小組，都會以 TCO 作為主要考量。 本節說明 Azure 中的計費和授權基本概念，這些概念也適用於適用於 PostgreSQL 的 Azure 資料庫和 PostgreSQL on Azure VM。

## <a name="billing"></a>計費

適用於 PostgreSQL 的 Azure 資料庫目前以服務的形式提供給數個層級使用，且資源的價格不同。 所有資源均會以固定費率按小時計費。 如需目前支援的服務層級、計算大小和儲存體數量的最新資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/server/) 您可以動態調整服務層級和計算大小，以符合您應用程式的各種輸送量需求。 傳出的網際網路流量也會以一般[資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

透過適用於 PostgreSQL 的 Azure 資料庫，Microsoft 會自動設定、修補和升級資料庫軟體。 這些自動化動作會降低您的管理成本。 此外，適用於 PostgreSQL 的 Azure 資料庫具備[自動備份連結]()功能。 這些功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。 相反地，透過 PostgreSQL on Azure VM，您可以選擇並執行任何 PostgreSQL 版本。 不過，您需要支付已佈建的 VM、與資料相關聯的儲存體成本、備份、監視資料和記錄儲存體的費用，以及所使用之特定 PostgreSQL 授權類型的成本 (如果有)。

適用於 PostgreSQL 的 Azure 資料庫提供內建的高可用性，提供可在任何種類的節點層級中斷時，仍保證提供 99.99% 服務的 SLA。 不過，針對 VM 內的資料庫高可用性，您可以使用高可用性選項，例如 PostgreSQL 資料庫中可用的[串流複寫](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION)。 使用支援的高可用性選項並不提供額外的 SLA， 但在支付額外成本和管理負荷的情況下，可讓您達成優於 99.99% 的資料庫可用性。

如需價格的詳細資訊，請參閱下列文章：
- [適用於 PostgreSQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度，因為這直接影響到成本。

Microsoft 可以透過 IaaS：

- 管理基礎結構。
- 自動修補基礎硬體和作業系統

Microsoft 可以透過 PaaS：

- 管理基礎結構。
- 自動修補基礎硬體、作業系統和資料庫引擎。
- 管理資料庫的高可用性。
- 自動執行備份並複寫所有資料，以提供災害復原之用。
- 預設加密待用和移動中的資料。
- 監視您的伺服器，並提供查詢效能深入解析和效能建議的功能。

使用適用於 PostgreSQL 的 Azure 資料庫，您可以繼續管理資料庫。 但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例如下：

- 資料庫
- 登入
- 索引微調
- 查詢微調
- 稽核
- 安全性

此外，設定其他資料中心的高可用性時，至少需要進行的最小設定或系統管理。

- 有了 PostgreSQL on Azure VM，您可以完全掌控作業系統和 PostgreSQL 伺服器執行個體組態。 有了 VM，您可以決定何時更新或升級作業系統與資料庫軟體，以及要套用哪些修補檔。 您也可以決定何時安裝任何其他軟體，例如防毒應用程式。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 您還可以控制 VM 的大小、磁碟數目及其儲存體組態。 如需詳細資訊，請參閱[Azure 的虛擬機器和雲端服務大小](../virtual-machines/sizes.md)。

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>移至 Azure PostgreSQL Service (PaaS) 的時機

- 當新解決方案的開發人員生產力和快速上市時間很重要時，適用於 PostgreSQL 的 Azure 資料庫是雲端式設計應用程式的理想解決方案。 有了類似 DBA 的程式設計功能，此服務非常適合雲端架構設計人員和開發人員，因為其會降低管理基礎作業系統和資料庫的需求。

- 若不想在新內部部署硬體上花費過多時間和費用，對於服務不支援需要細部控管和自訂 PostgreSQL 引擎的功能，或需要基礎作業系統存取權時，PostgreSQL on Azure VM 是適當的應用程式解決方案。

## <a name="next-steps"></a>後續步驟

- 請參閱[適用於 PostgreSQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/postgresql/server/)。
- 從建立您的第一部伺服器開始。