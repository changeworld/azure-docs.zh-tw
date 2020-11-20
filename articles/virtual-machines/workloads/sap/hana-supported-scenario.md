---
title: 支援 Azure (大型實例上的 SAP Hana 案例) |Microsoft Docs
description: SAP Hana on Azure (大型執行個體) 的支援案例及其架構詳細資料
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f33ef81282dd8e60eba64cc77cbd95ab8dbc557
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967562"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型執行個體的支援案例
本文說明 () 的 HANA 大型實例支援的案例和架構詳細資料。

>[!NOTE]
>如果本文未提及您所需的案例，請洽詢 Microsoft 服務管理小組以評估您的需求。
設定服務中心單位之前，請先向 SAP 或您的服務實夥伴驗證設計。

## <a name="terms-and-definitions"></a>詞彙和定義
讓我們來瞭解本文中所使用的詞彙和定義：

- **SID**： HANA 系統的系統識別碼
- ！ **： Hana** 大型實例
- **DR**：嚴重損壞修復
- **一般 dr**：僅針對 DR 用途而具有專用資源的系統設定
- 多 **用途 DR**：設定為使用非生產環境的 dr 網站系統，以及針對 DR 事件設定的生產實例 
- **單一 SID**：已安裝一個實例的系統
- **多重 SID**：已設定多個實例的系統;也稱為 MCOS 環境
- **HSR**： SAP Hana 系統複寫

## <a name="overview"></a>概觀
HANA 大型實例支援各種不同的架構，可協助您達成商務需求。 下列各節涵蓋架構案例及其設定詳細資料。 

衍生的架構設計純粹是從基礎結構的觀點來看，您必須查閱 SAP 或您的開發合作夥伴以進行 HANA 部署。 如果本文未列出您的案例，請洽詢 Microsoft 帳戶小組以檢查架構，並為您衍生解決方案。

> [!NOTE]
> 這些架構與量身打造的資料整合 (TDI) 設計及 SAP 所支援的完全相容。

本文描述每個支援的架構中，兩個元件的詳細資料：

- 乙太網路
- 儲存體

### <a name="ethernet"></a>乙太網路

每個布建的伺服器都已預先設定乙太網路介面集。 在每個的每個資訊單位上設定的乙太網路介面分為四種類型：

- **A**：用於或用戶端存取。
- **B**：用於節點對節點通訊。 此介面是在所有伺服器上設定 (無論要求的拓撲) 但僅用於向外延展案例。
- **C**：用於節點對儲存體連線能力。
- **D**：用於 STONITH 設定的節點對 iSCSI 裝置連線。 只有在要求 HSR 設定時，才會設定這個介面。  

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點|
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | STONITH |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 節點對節點|
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

您可以根據在圖上設定的拓撲來選擇介面。 例如，系統會針對節點對節點的通訊設定介面 "B"，這在您已設定相應放大拓撲時很實用。 此介面不會用於單一節點、相應放大設定。 如需介面使用方式的詳細資訊，請參閱本文稍後的 () 。 

如有必要，您可以自行定義額外的 NIC 卡。 但是， *無法* 變更現有 nic 的設定。

>[!NOTE]
>您可能會發現其他介面是實體介面或結合。 針對您的使用案例，您應該只考慮先前提及的介面。 任何其他專案都可以忽略。

有兩個指派之 IP 位址的單位分配應如下所示：

- Ethernet "A" 應該有指派的 IP 位址，其位於您提交給 Microsoft 的伺服器 IP 集區位址範圍內。 此 IP 位址應該保留在 OS 的 */etc/hosts* 目錄中。

- Ethernet "C" 應該有指派的 IP 位址，用來與 NFS 通訊。 此位址 *不* 需要在 *etc/hosts* 目錄中維護，即可允許租使用者內的實例對實例流量。

針對 HANA 系統複寫或 HANA 向外延展部署，有兩個指派之 IP 位址的 blade 設定不適合。 如果您只有兩個指派的 IP 位址，而您想要部署這類設定，請聯絡 Azure 服務管理上的 SAP Hana。 他們可以在第三個 VLAN 中指派第三個 IP 位址。 針對在三個 NIC 埠上有三個指派 IP 位址的 HANA 大型實例單位，適用下列使用規則：

- Ethernet "A" 應該有指派的 IP 位址，而該位址位於您提交給 Microsoft 的伺服器 IP 集區位址範圍之外。 此 IP 位址不應該保留在 OS 的 *etc/hosts* 目錄中。

- 乙太網路 "B" 應專門在 *etc/hosts* 目錄中維護，才能在不同的實例之間進行通訊。 這些 IP 位址會在相應放大 HANA 設定中維護，作為 HANA 用於節點間設定的 IP 位址。

- Ethernet "C" 應該有指派的 IP 位址，用來與 NFS 儲存體通訊。 這種類型的位址不能在 *etc/hosts* 目錄中保留。

- Ethernet "D" 應專門用來存取 STONITH 裝置以進行 Pacemaker。 當您設定 HANA 系統複寫，並且想要使用 SBD 型裝置來達成作業系統自動容錯移轉時，就需要此介面。


### <a name="storage"></a>儲存體
儲存體會根據要求的拓撲進行預先設定。 磁片區大小和掛接點會依伺服器數目、Sku 數目和已設定的拓撲而有所不同。 如需詳細資訊，請參閱本文稍後的 < 必要案例 () 。 如果您需要更多儲存空間，您可以用 1 TB 的增量來購買。

>[!NOTE]
>掛接點/usr/sap/ \<SID> 是/hana/shared 掛接點的符號連結。


## <a name="supported-scenarios"></a>支援的案例

下一節中的架構圖表會使用下列標記法：

[![架構圖表的表格](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

以下是支援的案例：

* 具有一個 SID 的單一節點
* 單一節點 MCOS
* 具有 DR (一般) 的單一節點
* 具有 DR (多用途) 的單一節點
* 具有 STONITH 的 HSR
* 使用 DR (一般/多用途) 的 HSR 
* 主機自動容錯移轉 (1+1) 
* 具待命相應放大
* 不具待命相應放大
* 具有 DR 的相應放大

## <a name="single-node-with-one-sid"></a>具有一個 SID 的單一節點

此拓撲支援相應增加設定中有一個 SID 的一個節點。

### <a name="architecture-diagram"></a>架構圖  

![具有一個 SID 的單一節點](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | HANA 安裝 | 
|/hana/data/SID/mnt00001 | 資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 記錄檔安裝 | 
|/hana/logbackups/SID | 重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。

## <a name="single-node-mcos"></a>單一節點 MCOS

此拓撲支援在具有多個 Sid 的擴大設定中使用一個節點。

### <a name="architecture-diagram"></a>架構圖  

![單一節點 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID1 | 適用于適用于 SID1 的 HANA 安裝 | 
|/hana/data/SID1/mnt00001 | 適用于適用于 SID1 的資料檔案安裝 | 
|/hana/log/SID1/mnt00001 | 適用于 SID1 的記錄檔安裝 | 
|/hana/logbackups/SID1 | 適用於 SID1 的重做記錄 |
|/hana/shared/SID2 | 適用于 SID2 的 HANA 安裝 | 
|/hana/data/SID2/mnt00001 | 適用于 SID2 的資料檔案安裝 | 
|/hana/log/SID2/mnt00001 | SID2 的記錄檔安裝 | 
|/hana/logbackups/SID2 | 適用於 SID2 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。

## <a name="single-node-with-dr-using-storage-replication"></a>使用儲存體複寫進行 DR 的單一節點
 
此拓撲支援在具有一個或多個 Sid 的相應增加設定中使用一個節點，並以儲存體為基礎的複寫至 DR 網站的主要 SID。 在此圖中，主要網站只會描述單一 SID 系統，但也支援 MCOS 系統。

### <a name="architecture-diagram"></a>架構圖  

![使用儲存體複寫進行 DR 的單一節點](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | 適用于 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。
- 在 DR 網站上：系統會將磁片區和掛接點設定 (標示為「HANA 安裝的必要」 ) 適用于 DR 的生產 HANA 實例安裝。 
- 在 DR 網站上， (標示為「儲存體複寫」的資料、記錄備份和共用磁片區 ) 會從生產網站透過快照集複寫。 這些磁片區只會在容錯移轉期間裝載。 如需詳細資訊，請參閱嚴重損壞 [修復容錯移轉](./hana-overview-high-availability-disaster-recovery.md)程式。
- *SKU 類型 I 類別* 的開機磁碟區已複寫到 DR 節點。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>具有 DR (多用途) 使用儲存體複寫的單一節點
 
此拓撲支援在具有一個或多個 Sid 的相應增加設定中使用一個節點，並以儲存體為基礎的複寫至 DR 網站的主要 SID。 在此圖中，主要網站只會描述單一 SID 系統，但也支援多 SID (MCOS) 系統。 在 DR 網站上，從主要網站執行生產作業時，將會使用速度為 QA 的實例。 在 DR 容錯移轉 (或容錯移轉測試) 期間，DR 網站上的 QA 實例會停止運作。

### <a name="architecture-diagram"></a>架構圖  

![具有 DR (多用途) 使用儲存體複寫的單一節點](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/shared/QA-SID | 適用于 QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | 適用于 QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | 適用于 QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。
- 在 DR 網站上：系統會將磁片區和掛接點設定 (標示為「HANA 安裝的必要」 ) 適用于 DR 的生產 HANA 實例安裝。 
- 在 DR 網站上， (標示為「儲存體複寫」的資料、記錄備份和共用磁片區 ) 會從生產網站透過快照集複寫。 這些磁片區只會在容錯移轉期間裝載。 如需詳細資訊，請參閱嚴重損壞 [修復容錯移轉](./hana-overview-high-availability-disaster-recovery.md)程式。 
- 在 DR 網站上： QA (標示為「QA 實例安裝」的資料、記錄備份、記錄檔和共用磁片區 ) 已針對 QA 實例安裝進行設定。
- *SKU 類型 I 類別* 的開機磁碟區已複寫到 DR 節點。

## <a name="hsr-with-stonith-for-high-availability"></a>具有高可用性 STONITH 的 HSR
 
此拓撲支援 HANA 系統複寫設定的兩個節點。 只有節點上的單一 HANA 實例才支援此設定。 這表示 *不* 支援 MCOS 案例。

> [!NOTE]
> 從2019年12月起，只有 SUSE 作業系統支援此架構。


### <a name="architecture-diagram"></a>架構圖  

![具有高可用性 STONITH 的 HSR](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 用於 STONITH |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 用於 STONITH |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要節點上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在次要節點上**|
|/hana/shared/SID | 適用于次要 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 次要 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 次要 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於次要 SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。
- STONITH：已針對 STONITH 安裝程式設定 SBD。 不過，使用 STONITH 是選擇性的。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>具有 HSR 和 DR 的高可用性與儲存體複寫
 
此拓撲支援 HANA 系統複寫設定的兩個節點。 標準和多用途 DRs 都受到支援。 只有節點上的單一 HANA 實例支援這些設定。 這表示這些設定 *不* 支援 MCOS 案例。

在此圖中，DR 網站上會說明多用途案例，其中，當生產作業從主要網站執行時，會在該網站上使用的是 [中]。 在 DR 容錯移轉 (或容錯移轉測試) 期間，DR 網站上的 QA 實例會停止運作。 

### <a name="architecture-diagram"></a>架構圖  

![具有 HSR 和 DR 的高可用性與儲存體複寫](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 用於 STONITH |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 用於 STONITH |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站的主要節點上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在主要網站的次要節點上**|
|/hana/shared/SID | 適用于次要 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 次要 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 次要 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於次要 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/shared/QA-SID | 適用于 QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | 適用于 QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | 適用于 QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。
- STONITH：已針對 STONITH 安裝程式設定 SBD。 不過，使用 STONITH 是選擇性的。
- 在 DR 網站上：主要和次要節點複寫 *都需要兩組儲存體磁片區* 。
- 在 DR 網站上：系統會將磁片區和掛接點設定 (標示為「HANA 安裝的必要」 ) 適用于 DR 的生產 HANA 實例安裝。 
- 在 DR 網站上， (標示為「儲存體複寫」的資料、記錄備份和共用磁片區 ) 會從生產網站透過快照集複寫。 這些磁片區只會在容錯移轉期間裝載。 如需詳細資訊，請參閱嚴重損壞 [修復容錯移轉](./hana-overview-high-availability-disaster-recovery.md)程式。 
- 在 DR 網站上： QA (標示為「QA 實例安裝」的資料、記錄備份、記錄檔和共用磁片區 ) 已針對 QA 實例安裝進行設定。
- *SKU 類型 I 類別* 的開機磁碟區已複寫到 DR 節點。


## <a name="host-auto-failover-11"></a>主機自動容錯移轉 (1+1)
 
此拓撲支援主機自動容錯移轉設定中的兩個節點。 有一個節點具有主要/背景工作角色，另一個節點作為待命。 *SAP 僅支援 S/4 HANA 的這個案例。* 如需詳細資訊，請參閱 [OSS 附注 2408419-SAP S/4HANA-多節點支援](https://launchpad.support.sap.com/#/notes/2408419)。



### <a name="architecture-diagram"></a>架構圖  

![主機自動容錯移轉 (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要和待命節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |



### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 待命：已將磁片區和掛接點設定 (在待命單位上針對 HANA 實例安裝標示為「HANA 安裝的必要」 ) 。
 

## <a name="scale-out-with-standby"></a>具待命相應放大
 
此拓撲支援相應放大組態中的多個節點。 有一個節點具有主要角色、一或多個節點具有背景工作角色，以及一或多個節點作為待命。 不過，在任何一個時間點都只能有一個主要節點。


### <a name="architecture-diagram"></a>架構圖  

![具待命相應放大](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要、背景工作角色和待命節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


## <a name="scale-out-without-standby"></a>不具待命相應放大
 
此拓撲支援相應放大組態中的多個節點。 有一個節點具有主要角色，以及一個或多個具有背景工作角色的節點。 不過，在任何一個時間點都只能有一個主要節點。


### <a name="architecture-diagram"></a>架構圖  

![不具待命相應放大](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要和背景工作角色節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。

## <a name="scale-out-with-dr-using-storage-replication"></a>使用儲存體複寫來向外擴充 DR
 
此拓撲支援具 DR 相應放大中的多個節點。 標準和多用途 DRs 都受到支援。 圖表中僅描繪單一用途 DR。 您可以要求此拓撲，不論是否具有待命節點。


### <a name="architecture-diagram"></a>架構圖  

![使用儲存體複寫來向外擴充 DR](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
-  在 DR 網站上：系統會將磁片區和掛接點設定 (標示為「HANA 安裝的必要」 ) 適用于 DR 的生產 HANA 實例安裝。 
- 在 DR 網站上， (標示為「儲存體複寫」的資料、記錄備份和共用磁片區 ) 會從生產網站透過快照集複寫。 這些磁片區只會在容錯移轉期間裝載。 如需詳細資訊，請參閱嚴重損壞 [修復容錯移轉](./hana-overview-high-availability-disaster-recovery.md)程式。 
- *SKU 類型 I 類別* 的開機磁碟區已複寫到 DR 節點。


## <a name="single-node-with-dr-using-hsr"></a>使用 HSR 進行 DR 的單一節點
 
此拓撲支援在具有一個 SID 的相應增加設定中設定一個節點，並將 HANA 系統複寫至主要 SID 的 DR 網站。 在此圖中，主要網站只會描述單一 SID 系統，但也支援多 SID (MCOS) 系統。

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR 進行 DR 的單一節點](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對等/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對等/HSR |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點預先設定于 (主要和 DR) 的每個的執行單位：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | 適用于 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。
- 主要節點會使用 HANA 系統複寫與 DR 節點進行同步處理。 
- 「[全球接觸](../../../expressroute/expressroute-global-reach.md)」可用來將 ExpressRoute 線路連結在一起，以在您的區域網路之間建立私人網路。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>單一節點 HSR 至 DR (成本優化)  
 
 此拓撲支援在具有一個 SID 的相應增加設定中設定一個節點，並將 HANA 系統複寫至主要 SID 的 DR 網站。 在此圖中，主要網站只會描述單一 SID 系統，但也支援多 SID (MCOS) 系統。 在 DR 網站上，當生產作業從主要網站執行時，就會在 QA 實例中使用一單位。 在 DR 容錯移轉 (或容錯移轉測試) 期間，DR 網站上的 QA 實例會停止運作。

### <a name="architecture-diagram"></a>架構圖  

![單一節點 HSR 至 DR (成本優化) ](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對等/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對等/HSR |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|/hana/shared/QA-SID | 適用于 QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | 適用于 QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | 適用于 QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小分佈是根據記憶體中的資料庫大小而定。 若要瞭解多 SID 環境中支援的記憶體資料庫大小，請參閱 [總覽和架構](./hana-overview-architecture.md)。
- 在 DR 網站上：已將磁片區和掛接點設定 (標示為「DR 網站上的生產實例」 ) 適用于 DR 的生產 HANA 實例安裝。 
- 在 DR 網站上： QA (標示為「QA 實例安裝」的資料、記錄備份、記錄檔和共用磁片區 ) 已針對 QA 實例安裝進行設定。
- 主要節點會使用 HANA 系統複寫與 DR 節點進行同步處理。 
- 「[全球接觸](../../../expressroute/expressroute-global-reach.md)」可用來將 ExpressRoute 線路連結在一起，以在您的區域網路之間建立私人網路。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>使用 HSR 的高可用性和嚴重損壞修復 
 
 此拓撲支援兩個節點，以用於本機區域高可用性的 HANA 系統複寫設定。 針對 DR，DR 區域中的第三個節點會使用 HSR (async 模式) ，與主要網站同步處理。 

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR 的高可用性和嚴重損壞修復](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對等/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對等/HSR |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站上：系統會將磁片區和掛接點設定 (標示為「生產 DR ) 實例」，以在 DR 的單位安裝生產 HANA 實例。 
- 主要網站節點會使用 HANA 系統複寫與 DR 節點進行同步處理。 
- 「[全球接觸](../../../expressroute/expressroute-global-reach.md)」可用來將 ExpressRoute 線路連結在一起，以在您的區域網路之間建立私人網路。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>使用 HSR (成本優化) 的高可用性和嚴重損壞修復
 
 針對本機區域的高可用性，此拓撲支援將 HANA 系統複寫設定的兩個節點。 針對 DR，DR 區域中的第三個節點會使用 HSR (async) 模式來與主要網站同步處理，而另一個實例 (例如，QA) 已從 DR 節點中執行。 

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR (成本優化) 的高可用性和嚴重損壞修復](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對等/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對等/HSR |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|/hana/shared/QA-SID | 適用于 QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | 適用于 QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | 適用于 QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站上：系統會將磁片區和掛接點設定 (標示為「生產 DR ) 實例」，以在 DR 的單位安裝生產 HANA 實例。 
- 在 DR 網站上： QA (標示為「QA 實例安裝」的資料、記錄備份、記錄檔和共用磁片區 ) 已針對 QA 實例安裝進行設定。
- 主要網站節點會使用 HANA 系統複寫與 DR 節點進行同步處理。 
- 「[全球接觸](../../../expressroute/expressroute-global-reach.md)」可用來將 ExpressRoute 線路連結在一起，以在您的區域網路之間建立私人網路。

## <a name="scale-out-with-dr-using-hsr"></a>使用 HSR 向外擴充 DR
 
此拓撲支援具 DR 相應放大中的多個節點。 您可以要求此拓撲，不論是否具有待命節點。 使用 HANA 系統複寫 (非同步模式) ，主要網站節點會與 DR 網站節點進行同步處理。


### <a name="architecture-diagram"></a>架構圖  

[![使用 HSR 向外擴充 DR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對等/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo> | team0.tenant | 用戶端對等/HSR |
| B | 類型二 | vlan\<tenantNo+2> | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo+1> | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 節點上**|
|HANA/shared | 適用于生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 適用于生產 SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站上：磁片區和掛接點是針對在 DR 的生產 HANA 實例安裝進行設定。 
- 主要網站節點會使用 HANA 系統複寫與 DR 節點進行同步處理。 
- 「[全球接觸](../../../expressroute/expressroute-global-reach.md)」可用來將 ExpressRoute 線路連結在一起，以在您的區域網路之間建立私人網路。


## <a name="next-steps"></a>後續步驟
- 適用于 HANA 大型實例的[基礎結構和連線能力](./hana-overview-infrastructure-connectivity.md)
- 適用于 HANA 大型實例的[高可用性和嚴重損壞修復](./hana-overview-high-availability-disaster-recovery.md)
