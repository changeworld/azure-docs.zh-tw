---
title: Azure （大型實例）上 SAP Hana 的支援案例 |Microsoft Docs
description: SAP Hana on Azure (大型執行個體) 的支援案例及其架構詳細資料
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617173"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型執行個體的支援案例
本文說明「HANA 大型實例」（今年）支援的案例和架構詳細資料。

>[!NOTE]
>如果本文未提及您的必要案例，請洽詢 Microsoft 服務管理小組，以評估您的需求。
在您設定：開始之前，請先驗證 SAP 或您的服務實施合作夥伴的設計。

## <a name="terms-and-definitions"></a>詞彙和定義
讓我們來瞭解本文中使用的詞彙和定義：

- **SID**： HANA 系統的系統識別碼
- **B-hli**： Hana 大型實例
- **DR**：嚴重損壞修復
- **一般 dr**：僅供 dr 用途專用資源的系統設定
- 多**用途 dr**：設定為使用非生產環境以及針對 DR 事件設定之生產實例的 dr 網站系統 
- **單一 SID**：已安裝一個實例的系統
- **多重 SID**：已設定多個實例的系統;也稱為 MCOS 環境
- **HSR**： SAP Hana 系統複寫

## <a name="overview"></a>概觀
HANA 大型實例支援各種不同的架構，可協助您達成業務需求。 下列各節涵蓋架構案例和其設定詳細資料。 

衍生的架構設計純粹是從基礎結構的觀點來看，您必須諮詢 SAP 或您的執行合作夥伴以進行 HANA 部署。 如果本文未列出您的案例，請洽詢 Microsoft 帳戶小組來審查架構，並為您衍生解決方案。

> [!NOTE]
> 這些架構完全符合量身打造的資料整合（TDI）設計，並受到 SAP 的支援。

本文說明每個支援的架構中的兩個元件詳細資料：

- 乙太網路
- 儲存體

### <a name="ethernet"></a>乙太網路

每個布建的伺服器都已預先設定一組乙太網路介面。 在每個：每個在每個的一間設定乙太網路介面分為四種類型：

- **A**：用於或用戶端存取。
- **B**：用於節點對節點通訊。 此介面是在所有伺服器上設定（不論要求的拓撲），但僅用於向外延展案例。
- **C**：用於節點對儲存體連線能力。
- **D**：用於 STONITH 設定的節點對 iSCSI 裝置連線。 只有在要求 HSR 安裝程式時，才會設定此介面。  

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點|
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | STONITH |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 節點對節點|
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | STONITH |

您可以根據在的圖上設定的拓撲來選擇介面。 例如，針對節點對節點通訊設定介面 "B"，這在您已設定相應放大拓撲時很有用。 此介面不會用於單一節點、相應增加設定。 如需有關介面使用方式的詳細資訊，請參閱您的必要案例（在本文稍後）。 

如有需要，您可以自行定義額外的 NIC 卡。 不過，您*無法*變更現有 nic 的設定。

>[!NOTE]
>您可能會發現其他介面是實體介面或聯結。 您應該只考慮先前針對您的使用案例所提及的介面。 任何其他人都可以忽略。

具有兩個已指派 IP 位址的單位分佈應如下所示：

- 乙太網路 "A" 應有指派的 IP 位址，其位於您提交給 Microsoft 的伺服器 IP 集區位址範圍內。 此 IP 位址應保留在 OS 的 */etc/hosts*目錄中。

- Ethernet "C" 應具有指派的 IP 位址，以用於與 NFS 的通訊。 此位址不*需要在* *etc/hosts*目錄中進行維護，即可允許租使用者內的實例對實例流量。

對於 HANA 系統複寫或 HANA 向外延展部署，有兩個已指派 IP 位址的分頁設定並不適用。 如果您只有兩個已指派的 IP 位址，而您想要部署這類設定，請在 Azure 服務管理上聯絡 SAP Hana。 他們可以在第三個 VLAN 中指派第三個 IP 位址。 對於三個 NIC 埠上具有三個指派 IP 位址的 HANA 大型實例單位，適用下列使用規則：

- 乙太網路 "A" 應該有指派的 IP 位址，而該位址不在您提交給 Microsoft 的伺服器 IP 集區位址範圍內。 此 IP 位址不應在 OS 的*etc/hosts*目錄中維護。

- 乙太網路 "B" 應專門在*etc/hosts*目錄中進行維護，以供不同實例之間的通訊。 這些是要在向外延展 HANA 設定中維護的 IP 位址，做為 HANA 用於節點間設定的 IP 位址。

- Ethernet "C" 應具有指派的 IP 位址，以用於與 NFS 儲存體的通訊。 此類型的位址不應在*etc/hosts*目錄中維護。

- Ethernet "D" 應專門用來存取 STONITH 裝置以進行 Pacemaker。 當您設定 HANA 系統複寫，而且想要使用以 SBD 為基礎的裝置來達到作業系統的自動容錯移轉時，需要此介面。


### <a name="storage"></a>儲存體
儲存體會根據要求的拓撲預先設定。 磁片區大小和掛接點會根據伺服器數目、Sku 數目和設定的拓撲而有所不同。 如需詳細資訊，請參閱您的必要案例（在本文稍後）。 如果您需要更多儲存空間，您可以在 1 TB 的增量中購買。

>[!NOTE]
>掛接點/usr/sap/\<SID > 是/hana/shared 掛接點的符號連結。


## <a name="supported-scenarios"></a>支援的案例

下一節中的架構圖表會使用下列標記法：

[架構圖表 ![資料表](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

以下是支援的案例：

* 具有一個 SID 的單一節點
* 單一節點 MCOS
* 具有 DR 的單一節點（一般）
* 具有 DR （多用途）的單一節點
* 具有 STONITH 的 HSR
* 具有 DR 的 HSR （一般/多用途） 
* 主機自動容錯移轉 (1+1) 
* 具待命相應放大
* 不具待命相應放大
* 具有 DR 的相應放大

## <a name="single-node-with-one-sid"></a>具有一個 SID 的單一節點

此拓撲支援具有一個 SID 的相應增加設定中的一個節點。

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
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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

此拓撲支援具有多個 Sid 的相應增加設定中的一個節點。

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
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID1 | 適用于 SID1 的 HANA 安裝 | 
|/hana/data/SID1/mnt00001 | 適用于 SID1 的資料檔案安裝 | 
|/hana/log/SID1/mnt00001 | SID1 的記錄檔安裝 | 
|/hana/logbackups/SID1 | 適用於 SID1 的重做記錄 |
|/hana/shared/SID2 | 適用于 SID2 的 HANA 安裝 | 
|/hana/data/SID2/mnt00001 | 適用于 SID2 的資料檔案安裝 | 
|/hana/log/SID2/mnt00001 | SID2 的記錄檔安裝 | 
|/hana/logbackups/SID2 | 適用於 SID2 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。

## <a name="single-node-with-dr-using-storage-replication"></a>具有使用儲存體複寫之 DR 的單一節點
 
此拓撲支援相應增加設定中具有一或多個 Sid 的一個節點，並以儲存體為基礎的複寫至主要 SID 的 DR 網站。 在此圖中，只有單一 SID 系統會在主要網站上說明，但也支援 MCOS 系統。

### <a name="architecture-diagram"></a>架構圖  

![具有使用儲存體複寫之 DR 的單一節點](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
- 針對 MCOS：磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 網站上：磁片區和掛接點已設定（標示為「HANA 安裝所需」），以便在 DR 的 HANA 單位上安裝生產 HANA 實例。 
- 在 DR 網站上：資料、記錄備份和共用磁片區（標示為「儲存體複寫」）是透過生產網站的快照集來複寫。 這些磁片區只會在容錯移轉期間掛接。 如需詳細資訊，請參閱[災難修復容錯移轉](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)程式。
- *SKU 類型 I 類別*的開機磁碟區已複寫到 DR 節點。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>具有 DR （多用途）的單一節點（使用儲存體複寫）
 
此拓撲支援相應增加設定中具有一或多個 Sid 的一個節點，並以儲存體為基礎的複寫至主要 SID 的 DR 網站。 在此圖中，只有單一 SID 系統會在主要網站上說明，但也支援多重 SID （MCOS）系統。 在 DR 網站上，當生產作業從主要網站執行時，會在 QA 實例中使用：的單位。 在 DR 容錯移轉（或容錯移轉測試）期間，會使 DR 網站上的 QA 實例停止執行。

### <a name="architecture-diagram"></a>架構圖  

![具有 DR （多用途）的單一節點（使用儲存體複寫）](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
|/hana/log/QA-SID/mnt00001 | QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 網站上：磁片區和掛接點已設定（標示為「HANA 安裝所需」），以便在 DR 的 HANA 單位上安裝生產 HANA 實例。 
- 在 DR 網站上：資料、記錄備份和共用磁片區（標示為「儲存體複寫」）是透過生產網站的快照集來複寫。 這些磁片區只會在容錯移轉期間掛接。 如需詳細資訊，請參閱[災難修復容錯移轉](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)程式。 
- 在 DR 網站上：已針對 qa 實例安裝設定適用于 QA 的資料、記錄備份、記錄和共用磁片區（標示為「QA 實例安裝」）。
- *SKU 類型 I 類別*的開機磁碟區已複寫到 DR 節點。

## <a name="hsr-with-stonith-for-high-availability"></a>具有 STONITH 的 HSR 以提供高可用性
 
此拓撲支援 HANA 系統複寫設定的兩個節點。 此設定僅支援節點上的單一 HANA 實例。 這表示*不*支援 MCOS 案例。

> [!NOTE]
> 從2019年12月起，只有 SUSE 作業系統才支援此架構。


### <a name="architecture-diagram"></a>架構圖  

![具有 STONITH 的 HSR 以提供高可用性](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 用於 STONITH |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 用於 STONITH |

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
- 針對 MCOS：磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：已針對 STONITH 安裝程式設定 SBD。 不過，使用 STONITH 是選擇性的。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>具有儲存體複寫的 HSR 和 DR 高可用性
 
此拓撲支援 HANA 系統複寫設定的兩個節點。 一般和多用途 DRs 都受到支援。 只有節點上的單一 HANA 實例支援這些設定。 這表示這些設定*不*支援 MCOS 案例。

在此圖中，多用途案例會在 DR 網站上描述，其中，在生產環境作業從主要網站執行時，會在 QA 實例中使用： 在 DR 容錯移轉（或容錯移轉測試）期間，會使 DR 網站上的 QA 實例停止執行。 

### <a name="architecture-diagram"></a>架構圖  

![具有儲存體複寫的 HSR 和 DR 高可用性](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 用於 STONITH |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 用於 STONITH |

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
|/hana/log/QA-SID/mnt00001 | QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：已針對 STONITH 安裝程式設定 SBD。 不過，使用 STONITH 是選擇性的。
- 在 DR 網站上：主要和次要節點複寫*需要兩組儲存體磁片區*。
- 在 DR 網站上：磁片區和掛接點已設定（標示為「HANA 安裝所需」），以便在 DR 的 HANA 單位上安裝生產 HANA 實例。 
- 在 DR 網站上：資料、記錄備份和共用磁片區（標示為「儲存體複寫」）是透過生產網站的快照集來複寫。 這些磁片區只會在容錯移轉期間掛接。 如需詳細資訊，請參閱[災難修復容錯移轉](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)程式。 
- 在 DR 網站上：已針對 qa 實例安裝設定適用于 QA 的資料、記錄備份、記錄和共用磁片區（標示為「QA 實例安裝」）。
- *SKU 類型 I 類別*的開機磁碟區已複寫到 DR 節點。


## <a name="host-auto-failover-11"></a>主機自動容錯移轉 (1+1)
 
此拓撲支援主機自動容錯移轉設定中的兩個節點。 有一個節點具有主要/背景工作角色，另一個則作為待命。 *SAP 僅針對 S/4 HANA 支援此案例。* 如需詳細資訊，請參閱[OSS 附注 2408419-SAP S/4HANA-多節點支援](https://launchpad.support.sap.com/#/notes/2408419)。



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
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
- 待命：磁片區和掛接點已設定（標示為「HANA 安裝所需」），以便在待命單位安裝 HANA 實例。
 

## <a name="scale-out-with-standby"></a>具待命相應放大
 
此拓撲支援相應放大組態中的多個節點。 有一個節點具有主要角色、一或多個具有背景工作角色的節點，以及一或多個節點做為待命。 不過，在任何單一時間點都只能有一個主要節點。


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
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
 
此拓撲支援相應放大組態中的多個節點。 有一個節點具有主要角色，以及一或多個具有背景工作角色的節點。 不過，在任何單一時間點都只能有一個主要節點。


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
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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

## <a name="scale-out-with-dr-using-storage-replication"></a>使用儲存體複寫以 DR 進行相應放大
 
此拓撲支援具 DR 相應放大中的多個節點。 一般和多用途 DRs 都受到支援。 圖表中僅描繪單一用途 DR。 您可以要求此拓撲，不論是否具有待命節點。


### <a name="architecture-diagram"></a>架構圖  

![使用儲存體複寫以 DR 進行相應放大](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端對 |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端對 |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
-  在 DR 網站上：磁片區和掛接點已設定（標示為「HANA 安裝所需」），以便在 DR 的 HANA 單位上安裝生產 HANA 實例。 
- 在 DR 網站上：資料、記錄備份和共用磁片區（標示為「儲存體複寫」）是透過生產網站的快照集來複寫。 這些磁片區只會在容錯移轉期間掛接。 如需詳細資訊，請參閱[災難修復容錯移轉](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)程式。 
- *SKU 類型 I 類別*的開機磁碟區已複寫到 DR 節點。


## <a name="single-node-with-dr-using-hsr"></a>具有使用 HSR 之 DR 的單一節點
 
此拓撲支援具有一個 SID 的相應增加設定中的一個節點，並將 HANA 系統複寫至主要 SID 的 DR 網站。 在此圖中，只有單一 SID 系統會在主要網站上說明，但也支援多重 SID （MCOS）系統。

### <a name="architecture-diagram"></a>架構圖  

![具有使用 HSR 之 DR 的單一節點](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端到 HSR |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>儲存體
下列掛接點已預先設定于：（主要和 DR）上：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | 適用于 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 適用于 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | SID 的記錄檔安裝 | 
|/hana/logbackups/SID | 適用於 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 主要節點會使用 HANA 系統複寫與 DR 節點同步。 
- [全球範圍](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用來將 ExpressRoute 線路連結在一起，以在您的地區網路之間建立私人網路。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>單一節點 HSR 至 DR （成本優化） 
 
 此拓撲支援具有一個 SID 的相應增加設定中的一個節點，並將 HANA 系統複寫至主要 SID 的 DR 網站。 在此圖中，只有單一 SID 系統會在主要網站上說明，但也支援多重 SID （MCOS）系統。 在 DR 網站上，當生產作業從主要網站執行時，會在 QA 實例中使用一單位。 在 DR 容錯移轉（或容錯移轉測試）期間，會使 DR 網站上的 QA 實例停止執行。

### <a name="architecture-diagram"></a>架構圖  

![單一節點 HSR 至 DR （成本優化）](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端到 HSR |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
|/hana/log/QA-SID/mnt00001 | QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 針對 MCOS：磁片區大小散發是以記憶體中的資料庫大小為基礎。 若要瞭解多個 SID 環境中的記憶體支援哪些資料庫大小，請參閱[總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 網站上：磁片區和掛接點已設定（標示為「DR 網站的生產實例」），以用於 DR 的 HANA 實例安裝。 
- 在 DR 網站上：已針對 qa 實例安裝設定適用于 QA 的資料、記錄備份、記錄和共用磁片區（標示為「QA 實例安裝」）。
- 主要節點會使用 HANA 系統複寫與 DR 節點同步。 
- [全球範圍](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用來將 ExpressRoute 線路連結在一起，以在您的地區網路之間建立私人網路。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>使用 HSR 的高可用性和嚴重損壞修復 
 
 此拓撲支援兩個節點，適用于本地區域高可用性的 HANA 系統複寫設定。 針對 DR，DR 區域的第三個節點會使用 HSR （非同步模式）與主要網站同步。 

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR 的高可用性和嚴重損壞修復](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端到 HSR |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
- 在 DR 網站上：磁片區和掛接點已設定（標示為「生產 DR 實例」），以供在 DR 的 HANA 單位上安裝生產 HANA 實例。 
- 主要網站節點會使用 HANA 系統複寫與 DR 節點同步。 
- [全球範圍](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用來將 ExpressRoute 線路連結在一起，以在您的地區網路之間建立私人網路。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>使用 HSR 進行高可用性和嚴重損壞修復（成本優化）
 
 此拓撲支援兩個節點，適用于本地區域高可用性的 HANA 系統複寫設定。 針對 DR，DR 區域的第三個節點會使用 HSR （非同步模式）與主要網站同步，而另一個實例（例如 QA）則已從 DR 節點執行。 

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR 進行高可用性和嚴重損壞修復（成本優化）](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端到 HSR |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
|/hana/log/QA-SID/mnt00001 | QA SID 的記錄檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站上：磁片區和掛接點已設定（標示為「生產 DR 實例」），以供在 DR 的 HANA 單位上安裝生產 HANA 實例。 
- 在 DR 網站上：已針對 qa 實例安裝設定適用于 QA 的資料、記錄備份、記錄和共用磁片區（標示為「QA 實例安裝」）。
- 主要網站節點會使用 HANA 系統複寫與 DR 節點同步。 
- [全球範圍](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用來將 ExpressRoute 線路連結在一起，以在您的地區網路之間建立私人網路。

## <a name="scale-out-with-dr-using-hsr"></a>使用 HSR 相應放大 DR
 
此拓撲支援具 DR 相應放大中的多個節點。 您可以要求此拓撲，不論是否具有待命節點。 主要網站節點會使用 HANA 系統複寫（非同步模式）與 DR 網站節點同步。


### <a name="architecture-diagram"></a>架構圖  

[使用 HSR 向外延展 ![的 DR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點對節點通訊 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點對儲存體 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<tenantNo > | team0.tenant | 用戶端到 HSR |
| B | 類型二 | vlan\<tenantNo + 2 > | team0.tenant+2 | 節點對節點通訊 |
| C | 類型二 | vlan\<tenantNo + 1 > | team0.tenant+1 | 節點對儲存體 |
| D | 類型二 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已設定但未使用 |

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
- 主要網站節點會使用 HANA 系統複寫與 DR 節點同步。 
- [全球範圍](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用來將 ExpressRoute 線路連結在一起，以在您的地區網路之間建立私人網路。


## <a name="next-steps"></a>後續步驟
- 適用于 HANA 大型實例的[基礎結構和連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- HANA 大型實例的[高可用性和嚴重損壞修復](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
