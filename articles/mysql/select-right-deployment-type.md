---
title: 選取正確的部署類型-適用於 MySQL 的 Azure 資料庫
description: 本文說明在您將適用於 MySQL 的 Azure 資料庫部署為基礎結構即服務 (IaaS) 或平臺即服務 (PaaS) 之前應考慮的因素。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a1b66528bee63fb123271e4277e122603ced2e75
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906508"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中選擇正確的 MySQL 伺服器選項

使用 Azure 時，您的 MySQL 伺服器工作負載可以在託管的虛擬機器基礎結構即服務 (IaaS) 或作為託管平臺即服務 (PaaS) 中執行。 PaaS 有多個部署選項，而且每個部署選項中都有服務層級。 選擇要使用 IaaS 和 PaaS 時，必須判斷您是否想管理資料庫、套用修補檔及進行備份，或是您要將這些作業委派給 Azure。

進行決策時，請考慮下列兩個選項：

- **適用於 MySQL 的 Azure 資料庫**。 此選項是一種完全受控的 MySQL 資料庫引擎，以 MySQL 社區版的穩定版本為基礎。 此關係資料庫即服務 (裝載在 Azure 雲端平臺上的 DBaaS) ，屬於 PaaS 的產業類別。

  使用 Azure 上的 MySQL 受控實例，您可以使用內建功能視覺效果自動修補、高可用性、自動備份、彈性調整、企業級安全性、合規性和治理、監視及警示，當 MySQL 伺服器在內部部署或 Azure VM 中時，則需要進行廣泛的設定。 使用 MySQL 做為服務時，您隨用隨付的選項可擴大或縮小，以提供更好的控制而不會中斷。 
  
  [適用於 MySQL 的 Azure 資料庫](overview.md)（由 MySQL 社區版提供）提供兩種部署模式：
    - [單一伺服器](single-server-overview.md) 是完全受控的資料庫服務，最基本的資料庫自訂需求。 單一伺服器平台的設計訴求是處理大部分的資料庫管理功能，例如修補、備份、高可用性，並透過最少的使用者組態和控制帶來安全性。 架構已最佳化，可在單一可用性區域上提供 99.99% 的可用性。 雲端原生應用程式最適合使用單一伺服器處理自動修補，無需細部控管修補排程和自訂 MySQL 組態設定。 
    
    - [彈性的伺服器 (預覽版) ](flexible-server/overview.md) 是一項完全受控的資料庫服務，其設計目的是要針對資料庫管理功能和設定，提供更細微的控制和彈性。 一般情況下，服務會根據使用者需求，提供更多的彈性和伺服器設定自訂，相較于單一伺服器部署。 彈性的伺服器架構可讓使用者在單一可用性區域內，以及跨多個可用性區域，選擇高可用性。 彈性伺服器也提供更好的成本優化控制項，讓您能夠啟動/停止您的伺服器和高載 Sku，適用于不需要持續完整計算容量的工作負載。 
    彈性伺服器最適合：
     
      - 應用程式開發需要更完善的 MySQL 引擎控制和自訂。
      - 區域備援高可用性
      - 可管理的維護期間

- **Azure vm 上的 MySQL**。 此選項落在 IaaS 的產業類別中。 透過此服務，您可以在 Azure 雲端平臺上的受控虛擬機器中執行 MySQL Server。 所有最新版本的 MySQL 都可以安裝在虛擬機器中。

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>比較 Azure 中的 MySQL 部署選項

下表列出這些選項之間的主要差異：


| 屬性          | 適用於 MySQL 的 Azure 資料庫<br/>單一伺服器 |適用於 MySQL 的 Azure 資料庫<br/>彈性伺服器  |Azure Vm 上的 MySQL                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| MySQL 版本支援 | 5.6、5.7 & 8。0| 5.7 | 任何版本|
| 計算調整 | 不支援從和到基本層的支援 (調整) | 支援 | 支援|
| 儲存體大小 | 5 GiB 至 16 TiB| 5 GiB 至 16 TiB | 32 GiB 至 32767 GiB|
| 線上儲存體調整 | 支援| 支援| 不支援|
| 自動儲存體調整 | 支援| 預覽中不支援| 不支援|
| 網路連線 | -具有伺服器防火牆的公用端點。<br/> -Private Link 支援的私用存取。|-具有伺服器防火牆的公用端點。<br/> -使用虛擬網路整合進行私用存取。| -具有伺服器防火牆的公用端點。<br/> -Private Link 支援的私用存取。|
| 服務等級協定 (SLA)  | 99.99% 可用性 SLA |預覽中沒有 SLA| 使用可用性區域的99.99%|
| 作業系統修補| 自動  | 自動使用自訂維護視窗控制項 | 由終端使用者管理 |
| MySQL 修補     | 自動  | 自動使用自訂維護視窗控制項 | 由終端使用者管理 |
| 高可用性 | 單一可用性區域內的內建 HA| 可用性區域內和跨可用性區域的內建 HA | 使用群集、複寫等自訂管理|
| 區域備援 | 不支援 | 支援 | 支援|
| 混合式案例 | 支援 [資料輸入複寫](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)| 預覽版中並未提供 | 由終端使用者管理 |
| 讀取複本 | 支援| 支援 | 由終端使用者管理 |
| 備份 | 自動保留7-35 天 | 自動保留1-35 天 | 由終端使用者管理 |
| 監視資料庫作業 | 支援 | 支援 | 由終端使用者管理 |
| 災害復原 | 支援異地冗余備份儲存體和跨區域讀取複本 | 預覽中不支援| 使用複寫技術進行自訂管理 |
| 查詢效能深入解析 | 支援 | 預覽版中並未提供| 由終端使用者管理 |
| 保留執行個體定價 | 支援 | 預覽版中並未提供 | 支援 |
| Azure AD 驗證 | 支援 | 預覽版中並未提供 | 不支援|
| 待用資料加密 | 支援客戶管理的金鑰 | 支援服務管理的金鑰 | 不支援|
| SSL/TLS | 預設為啟用，並支援 TLS 1.2、1.1 和1。0 | 使用 TLS v 1.2 強制執行 | 支援 TLS v 1.2、1.1 和1。0 | 
| 車隊管理 | 支援 Azure CLI、PowerShell、REST 和 Azure Resource Manager | 支援 Azure CLI、PowerShell、REST 和 Azure Resource Manager  | 支援 Vm Azure CLI、PowerShell、REST 和 Azure Resource Manager |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>選擇 PaaS 或 IaaS 的商業動機

有幾個因素會影響您的決定選擇 PaaS 或 IaaS 來裝載 MySQL 資料庫。

### <a name="cost"></a>Cost

降低成本通常是決定裝載資料庫最佳解決方案的主要考慮。 無論是只有極少現金的新創公司，或是在有規模公司中受到嚴格的預算限制下運作的小組，都會以 TCO 作為主要考量。 本節說明 Azure 中適用于 Azure Vm 上適用於 MySQL 的 Azure 資料庫和 MySQL 的計費和授權基本概念。

#### <a name="billing"></a>計費

適用於 MySQL 的 Azure 資料庫目前以服務的形式提供給數個層級，且資源的價格不同。 所有資源均會以固定費率按小時計費。 如需目前支援的服務層級、計算大小和儲存體數量的最新資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/mysql/)。 您可以動態調整服務層級和計算大小，以符合您應用程式的各種輸送量需求。 傳出的網際網路流量也會以一般[資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

使用適用於 MySQL 的 Azure 資料庫，Microsoft 會自動設定、修補及升級資料庫軟體。 這些自動化動作會降低您的管理成本。 此外，適用於 MySQL 的 Azure 資料庫也有 [自動化的備份](https://docs.microsoft.com/azure/mysql/concepts-backup) 功能。 這些功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。 相反地，在 Azure Vm 上使用 MySQL，您可以選擇並執行任何 MySQL 版本。 無論您使用的是什麼 MySQL 版本，您都需要支付已布建的 VM、與資料相關聯的儲存體成本、備份、監視資料和記錄儲存體，以及在任何) 中使用 (特定 MySQL 授權類型的成本。

適用於 MySQL 的 Azure 資料庫針對任何類型的節點層級中斷提供內建的高可用性，同時仍維持服務的 99.99% SLA 保證。 不過，若要讓 Vm 內的資料庫高可用性，您可以使用 MySQL 資料庫上可用的高可用性選項，例如 [mysql](https://dev.mysql.com/doc/refman/8.0/en/replication.html) 複寫。 使用支援的高可用性選項並不提供額外的 SLA， 但在支付額外成本和管理負荷的情況下，可讓您達成優於 99.99% 的資料庫可用性。

如需價格的詳細資訊，請參閱下列文章：
* [適用於 MySQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/mysql/)
* [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度，因為這直接影響到成本。 

Microsoft 可以透過 IaaS：

- 管理基礎結構。
- 提供基礎硬體和作業系統的自動修補。
  
Microsoft 可以透過 PaaS：

- 管理基礎結構。
- 自動修補基礎硬體、作業系統和資料庫引擎。
- 管理資料庫的高可用性。
- 自動執行備份並複寫所有資料，以提供災害復原之用。
- 預設加密待用和移動中的資料。
- 監視您的伺服器，並提供查詢效能深入解析和效能建議的功能

下列清單說明每個選項的系統管理考慮：

* 使用適用於 MySQL 的 Azure 資料庫，您可以繼續管理您的資料庫。 但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例如下：

  - 資料庫
  - 登入
  - 索引微調
  - 查詢微調
  - 稽核
  - 安全性

  此外，設定其他資料中心的高可用性時，至少需要進行的最小設定或系統管理。

* 使用 Azure Vm 上的 MySQL，您可以完全掌控作業系統和 MySQL 伺服器實例設定。 有了 VM，您可以決定何時更新或升級作業系統與資料庫軟體，以及要套用哪些修補檔。 您也可以決定何時安裝任何其他軟體，例如防毒應用程式。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 您還可以控制 VM 的大小、磁碟數目及其儲存體組態。 如需詳細資訊，請參閱[Azure 的虛擬機器和雲端服務大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

### <a name="time-to-move-to-azure"></a>移至 Azure 的時間

* 當新解決方案的開發人員生產力和快速上市時間很重要時，適用於 MySQL 的 Azure 資料庫是適合雲端設計應用程式的解決方案。 有了類似 DBA 的程式設計功能，此服務非常適合雲端架構設計人員和開發人員，因為其會降低管理基礎作業系統和資料庫的需求。

* 當您想要避免取得新內部部署硬體的時間和費用時，Azure Vm 上的 MySQL 是適用于應用程式的正確解決方案，需要更細微的控制和自訂不受服務支援的 MySQL 引擎，或需要存取基礎 OS。 此解決方案也適用于將現有的內部部署應用程式和資料庫移轉至 Azure，以因應適用於 MySQL 的 Azure 資料庫大小不佳的情況。

因為不需要變更簡報、應用程式和資料層，所以您可以節省重新架構現有解決方案的時間和預算。 相反地，您可以專注于將所有解決方案遷移至 Azure，並解決 Azure 平臺可能需要的一些效能優化。

## <a name="next-steps"></a>下一步

* 請參閱 [適用於 MySQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/MySQL/)。
* [建立您的第一部伺服器](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)，開始使用。
