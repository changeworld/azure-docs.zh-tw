---
title: Azure 上 SAP HANA 支援的方案（大型實例）*微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617173"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型執行個體的支援案例
本文介紹了 HANA 大型實例 （HLI） 支援的方案和體系結構詳細資訊。

>[!NOTE]
>如果本文中未提及所需的方案，請與 Microsoft 服務管理團隊聯繫以評估您的要求。
在設置 HLI 單元之前，請與 SAP 或服務實施合作夥伴驗證設計。

## <a name="terms-and-definitions"></a>詞彙和定義
讓我們瞭解本文中使用的術語和定義：

- **SID**：HANA 系統的系統識別碼
- **HLI**： 哈納大實例
- **DR**： 災害復原
- **普通 DR**：僅出於 DR 目的使用專用資源的系統設置
- **多用途 DR**： 配置為將非生產環境與為 DR 事件配置的生產實例一起使用的 DR 網站系統 
- **單 SID**：安裝一個實例的系統
- **多 SID**：配置多個實例的系統;也稱為 MCOS 環境
- **HSR**： SAP HANA 系統複製

## <a name="overview"></a>總覽
HANA 大型實例支援各種體系結構，可説明您滿足業務需求。 以下各節介紹體系結構方案及其配置詳細資訊。 

派生體系結構設計完全從基礎結構角度設計，您必須諮詢 SAP 或實施合作夥伴進行 HANA 部署。 如果本文未列出您的方案，請與 Microsoft 帳戶團隊聯繫，查看體系結構並為您派生解決方案。

> [!NOTE]
> 這些架構完全符合定制資料整合 （TDI） 設計，並由 SAP 提供支援。

本文介紹了每個受支援的體系結構中的兩個元件的詳細資訊：

- 乙太網路
- 存放裝置

### <a name="ethernet"></a>乙太網路

每個預配伺服器都預配置了乙太網介面集。 在每個 HLI 單元上配置的乙太網介面分為四種類型：

- **A**： 用於用戶端存取或由用戶端存取使用。
- **B**： 用於節點到節點的通信。 此介面在所有伺服器上配置（無論請求的拓撲如何），但僅用於橫向擴展方案。
- **C**： 用於節點到存儲連接。
- **D**： 用於用於 STONITH 設置的節點到 iSCSI 設備連接。 僅當請求 HSR 設置時，才配置此介面。  

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點到節點|
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | STONITH |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 節點到節點|
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | STONITH |

根據在 HLI 單元上配置的拓撲選擇介面。 例如，介面"B"為節點到節點通訊設定，這在配置橫向擴展拓撲時非常有用。 此介面不用於單個節點、放大配置。 有關介面使用方式的詳細資訊，請查看所需的方案（本文後面）。 

如有必要，您可以自行定義其他 NIC 卡。 但是，*無法*更改現有 NIC 的配置。

>[!NOTE]
>您可能會發現其他介面是物理介面或綁定。 您應該只考慮用例前面提到的介面。 任何其他都可以忽略。

具有兩個分配 IP 位址的單位的分佈應如下所示：

- 乙太網"A"應具有分配給的 IP 位址，該 IP 位址在您提交給 Microsoft 的伺服器 IP 池位址範圍內。 此 IP 位址應保存在作業系統的 */etc/主機*目錄中。

- 乙太網"C"應具有用於與 NFS 通信的已分配 IP 位址。 不需要在*等/主機*目錄中維護此位址，以允許租戶中的實例到實例流量。 *not*

對於 HANA 系統複製或 HANA 橫向擴展部署，不適合具有兩個分配的 IP 位址的刀片配置。 如果只有兩個分配的 IP 位址，並且想要部署此類配置，請與 Azure 服務管理上的 SAP HANA 聯繫。 他們可以在第三個 VLAN 中為您分配第三個 IP 位址。 對於在三個 NIC 埠上具有三個分配 IP 位址的 HANA 大型實例單元，適用以下使用規則：

- 乙太網"A"應具有分配給的 IP 位址，該 IP 位址超出您提交給 Microsoft 的伺服器 IP 池位址範圍。 不應在作業系統*的等/主機*目錄中維護此 IP 位址。

- 乙太網"B"應只保存在*等/主機*目錄中，以便在各種實例之間進行通信。 這些是在橫向擴展的 HANA 配置中維護的 IP 位址，因為 HANA 用於節點間配置的 IP 位址。

- 乙太網"C"應具有用於與 NFS 存儲通信的已分配 IP 位址。 此類位址不應在*等/主機*目錄中保留。

- 乙太網"D"應僅用於訪問起搏器的 STONITH 設備。 當您配置 HANA 系統複製並且希望使用基於 SBD 的設備實現作業系統的自動容錯移轉時，需要此介面。


### <a name="storage"></a>存放裝置
存儲根據請求的拓撲進行預配置。 卷大小和裝載點因伺服器數量、SKU 數量和配置的拓撲而異。 有關詳細資訊，請查看所需的方案（本文後面）。 如果您需要更多存儲空間，可以以 1 TB 的增量購買。

>[!NOTE]
>裝載點 /usr/sap/\<SID>是指向 /hana/共用裝載點的符號連結。


## <a name="supported-scenarios"></a>支援的案例

下一節中的體系結構圖使用以下符號：

[![體系結構圖表](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

以下是支援的方案：

* 具有一個 SID 的單一節點
* 單一節點 MCOS
* 帶 DR 的單節點（正常）
* 帶 DR 的單節點（多用途）
* 具有 STONITH 的 HSR
* 帶 DR 的 HSR（普通/多用途） 
* 主機自動容錯移轉 (1+1) 
* 具待命相應放大
* 不具待命相應放大
* 具有 DR 的相應放大

## <a name="single-node-with-one-sid"></a>具有一個 SID 的單一節點

此拓撲支援一個使用一個 SID 的擴展配置中的節點。

### <a name="architecture-diagram"></a>架構圖  

![具有一個 SID 的單一節點](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | HANA 安裝 | 
|/hana/data/SID/mnt00001 | 資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 日誌檔安裝 | 
|/hana/logbackups/SID | 重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。

## <a name="single-node-mcos"></a>單一節點 MCOS

此拓撲支援具有多個 S 的擴展配置中的一個節點。

### <a name="architecture-diagram"></a>架構圖  

![單一節點 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID1 | 用於 SID1 的 HANA 安裝 | 
|/hana/data/SID1/mnt00001 | 為 SID1 安裝資料檔案 | 
|/hana/log/SID1/mnt00001 | SID1 的日誌檔安裝 | 
|/hana/logbackups/SID1 | 適用於 SID1 的重做記錄 |
|/hana/shared/SID2 | SID2 的 HANA 安裝 | 
|/hana/data/SID2/mnt00001 | SID2 的資料檔案安裝 | 
|/hana/log/SID2/mnt00001 | SID2 的日誌檔安裝 | 
|/hana/logbackups/SID2 | 適用於 SID2 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。

## <a name="single-node-with-dr-using-storage-replication"></a>使用存儲複製的 DR 的單節點
 
此拓撲支援具有一個或多個 SID 的擴展配置中的一個節點，並基於存儲複製到主 SID 的 DR 網站。 在圖中，主網站只描述了單 SID 系統，但也支援 MCOS 系統。

### <a name="architecture-diagram"></a>架構圖  

![使用存儲複製的 DR 的單節點](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | 用於 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 為 SID 安裝資料檔案 | 
|/hana/log/SID/mnt00001 | SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 對於 MCOS：卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 網站：為 DR HLI 單元的生產 HANA 實例安裝配置了卷和裝載點（標記為"HANA 安裝需要"。 
- 在 DR 網站：資料、記錄備份和共用卷（標記為"存儲複製"）通過生產網站的快照進行複製。 這些卷僅在容錯移轉期間裝載。 有關詳細資訊，請參閱[災害復原容錯移轉過程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。
- *SKU 類型 I 類*的開機磁碟區將複製到 DR 節點。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>使用存儲複製的具有 DR（多用途）的單節點
 
此拓撲支援具有一個或多個 SID 的擴展配置中的一個節點，並基於存儲複製到主 SID 的 DR 網站。 在圖中，主網站只描述了單 SID 系統，但也支援多 SID （MCOS） 系統。 在 DR 網站，當生產操作從主網站運行時，HLI 單元用於 QA 實例。 在 DR 容錯移轉（或容錯移轉測試）期間，DR 網站的 QA 實例將被關閉。

### <a name="architecture-diagram"></a>架構圖  

![使用存儲複製的具有 DR（多用途）的單節點](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日誌檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 對於 MCOS：卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 網站：為 DR HLI 單元的生產 HANA 實例安裝配置了卷和裝載點（標記為"HANA 安裝需要"。 
- 在 DR 網站：資料、記錄備份和共用卷（標記為"存儲複製"）通過生產網站的快照進行複製。 這些卷僅在容錯移轉期間裝載。 有關詳細資訊，請參閱[災害復原容錯移轉過程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- 在 DR 網站：為 QA 實例安裝配置 QA 的資料、記錄備份、日誌和共用卷（標記為"QA 實例安裝"）。
- *SKU 類型 I 類*的開機磁碟區將複製到 DR 節點。

## <a name="hsr-with-stonith-for-high-availability"></a>具有 STONITH 的 HSR，提供高可用性
 
此拓撲支援 HANA 系統複製配置的兩個節點。 此配置僅支援節點上的單個 HANA 實例。 這意味著*不支援*MCOS 方案。

> [!NOTE]
> 截至 2019 年 12 月，此體系結構僅支援 SUSE 作業系統。


### <a name="architecture-diagram"></a>架構圖  

![具有 STONITH 的 HSR，提供高可用性](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 用於 STONITH |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 用於 STONITH |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要節點上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在次要節點上**|
|/hana/shared/SID | 用於輔助 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 為輔助 SID 安裝資料檔案 | 
|/hana/log/SID/mnt00001 | 為輔助 SID 安裝日誌檔 | 
|/hana/logbackups/SID | 適用於次要 SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 對於 MCOS：卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：已針對 STONITH 安裝程式設定 SBD。 但是，使用 STONITH 是可選的。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>具有 HSR 和 DR 的高可用性以及存儲複製
 
此拓撲支援 HANA 系統複製配置的兩個節點。 支援普通和多用途 DR。 這些配置僅支援節點上的單個 HANA 實例。 這意味著這些配置*不支援*MCOS 方案。

在圖中，在 DR 網站中描述了一個多用途方案，其中 HLI 單元用於 QA 實例，而生產操作從主網站運行。 在 DR 容錯移轉（或容錯移轉測試）期間，DR 網站的 QA 實例將被關閉。 

### <a name="architecture-diagram"></a>架構圖  

![具有 HSR 和 DR 的高可用性以及存儲複製](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 用於 STONITH |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 用於 STONITH |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站的主要節點上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在主要網站的次要節點上**|
|/hana/shared/SID | 用於輔助 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 為輔助 SID 安裝資料檔案 | 
|/hana/log/SID/mnt00001 | 為輔助 SID 安裝日誌檔 | 
|/hana/logbackups/SID | 適用於次要 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日誌檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 對於 MCOS：卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：已針對 STONITH 安裝程式設定 SBD。 但是，使用 STONITH 是可選的。
- 在 DR 網站：主節點和輔助節點複製*需要兩組存儲卷*。
- 在 DR 網站：為 DR HLI 單元的生產 HANA 實例安裝配置了卷和裝載點（標記為"HANA 安裝需要"。 
- 在 DR 網站：資料、記錄備份和共用卷（標記為"存儲複製"）通過生產網站的快照進行複製。 這些卷僅在容錯移轉期間裝載。 有關詳細資訊，請參閱[災害復原容錯移轉過程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- 在 DR 網站：為 QA 實例安裝配置 QA 的資料、記錄備份、日誌和共用卷（標記為"QA 實例安裝"）。
- *SKU 類型 I 類*的開機磁碟區將複製到 DR 節點。


## <a name="host-auto-failover-11"></a>主機自動容錯移轉 (1+1)
 
此拓撲支援主機自動容錯移轉設定中的兩個節點。 有一個節點具有主/輔助角色，另一個節點作為備用節點。 *SAP 僅支援此方案，適用于 S/4 HANA。* 有關詳細資訊，請參閱[OSS 注釋 2408419 - SAP S/4HANA - 多節點支援](https://launchpad.support.sap.com/#/notes/2408419)。



### <a name="architecture-diagram"></a>架構圖  

![主機自動容錯移轉 (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點到節點通信 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 節點到節點通信 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要和待命節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |



### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 待機時：為備用單元的 HANA 實例安裝配置了卷和裝載點（標記為"HANA 安裝需要"。
 

## <a name="scale-out-with-standby"></a>具待命相應放大
 
此拓撲支援相應放大組態中的多個節點。 有一個節點具有主角色，一個或多個節點具有輔助角色，一個或多個節點作為備用節點。 但是，在任何單個時間點只能有一個主節點。


### <a name="architecture-diagram"></a>架構圖  

![具待命相應放大](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點到節點通信 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 節點到節點通信 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要、背景工作角色和待命節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


## <a name="scale-out-without-standby"></a>不具待命相應放大
 
此拓撲支援相應放大組態中的多個節點。 有一個節點具有主角色，一個或多個節點具有輔助角色。 但是，在任何單個時間點只能有一個主節點。


### <a name="architecture-diagram"></a>架構圖  

![不具待命相應放大](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點到節點通信 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 節點到節點通信 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要和背景工作角色節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。

## <a name="scale-out-with-dr-using-storage-replication"></a>使用存儲複製使用 DR 橫向擴展
 
此拓撲支援具 DR 相應放大中的多個節點。 支援普通和多用途 DR。 圖表中僅描繪單一用途 DR。 您可以要求此拓撲，不論是否具有待命節點。


### <a name="architecture-diagram"></a>架構圖  

![使用存儲複製使用 DR 橫向擴展](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點到節點通信 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 節點到節點通信 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
-  在 DR 網站：為 DR HLI 單元的生產 HANA 實例安裝配置了卷和裝載點（標記為"HANA 安裝需要"。 
- 在 DR 網站：資料、記錄備份和共用卷（標記為"存儲複製"）通過生產網站的快照進行複製。 這些卷僅在容錯移轉期間裝載。 有關詳細資訊，請參閱[災害復原容錯移轉過程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- *SKU 類型 I 類*的開機磁碟區將複製到 DR 節點。


## <a name="single-node-with-dr-using-hsr"></a>使用 HSR 使用 DR 的單節點
 
此拓撲支援一個使用一個 SID 進行擴展配置的節點，將 HANA 系統複製到主 SID 的 DR 網站。 在圖中，主網站只描述了單 SID 系統，但也支援多 SID （MCOS） 系統。

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR 使用 DR 的單節點](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI/HSR |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
以下安裝點在 HLI 單元（主和 DR）上進行了預配置：

| 掛接點 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | 用於 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 為 SID 安裝資料檔案 | 
|/hana/log/SID/mnt00001 | SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 對於 MCOS：卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 主節點使用 HANA 系統複製與 DR 節點同步。 
- [全域覆蓋](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用於將 ExpressRoute 電路連結在一起，在區域網路之間建立私人網路絡。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>單個節點 HSR 到 DR（成本優化） 
 
 此拓撲支援一個使用一個 SID 進行擴展配置的節點，將 HANA 系統複製到主 SID 的 DR 網站。 在圖中，主網站只描述了單 SID 系統，但也支援多 SID （MCOS） 系統。 在 DR 網站，當生產操作從主網站運行時，Qa 實例使用 HLI 單元。 在 DR 容錯移轉（或容錯移轉測試）期間，DR 網站的 QA 實例將被關閉。

### <a name="architecture-diagram"></a>架構圖  

![單個節點 HSR 到 DR（成本優化）](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI/HSR |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|/hana/shared/QA-SID | QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日誌檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 對於 MCOS：卷大小分佈基於記憶體中的資料庫大小。 要瞭解多 SID 環境中支援記憶體中的資料庫大小，請參閱[概述和體系結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 網站：為 DR HLI 單元中的生產 HANA 實例安裝配置卷和裝載點（標記為"DR 網站上的 PROD 實例"）。 
- 在 DR 網站：為 QA 實例安裝配置 QA 的資料、記錄備份、日誌和共用卷（標記為"QA 實例安裝"）。
- 主節點使用 HANA 系統複製與 DR 節點同步。 
- [全域覆蓋](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用於將 ExpressRoute 電路連結在一起，在區域網路之間建立私人網路絡。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>使用 HSR 提供高可用性和災害復原 
 
 此拓撲支援兩個節點，用於針對本地區域的高可用性的 HANA 系統複製配置。 對於 DR，DR 區域的第三個節點使用 HSR（非同步模式）與主網站同步。 

### <a name="architecture-diagram"></a>架構圖  

![使用 HSR 提供高可用性和災害復原](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI/HSR |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站：為 DR HLI 單元中的生產 HANA 實例安裝配置卷和裝載點（標記為"PROD DR 實例"）。 
- 主網站節點使用 HANA 系統複製與 DR 節點同步。 
- [全域覆蓋](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用於將 ExpressRoute 電路連結在一起，在區域網路之間建立私人網路絡。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>通過 HSR 的高可用性和災害復原（成本優化）
 
 此拓撲支援兩個節點，用於用於本地區域的高可用性的 HANA 系統複製配置。 對於 DR，DR 區域的第三個節點使用 HSR（非同步模式）與主網站同步，而另一個實例（例如 QA）已經從 DR 節點耗盡。 

### <a name="architecture-diagram"></a>架構圖  

![通過 HSR 的高可用性和災害復原（成本優化）](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 已設定但未使用 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI/HSR |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 已設定但未使用 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 網站上**|
|/hana/shared/SID | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|/hana/shared/QA-SID | QA SID 的 HANA 安裝 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的資料檔案安裝 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日誌檔安裝 |
|/hana/logbackups/QA-SID | 適用於 QA SID 的重做記錄 |

### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站：為 DR HLI 單元中的生產 HANA 實例安裝配置卷和裝載點（標記為"PROD DR 實例"）。 
- 在 DR 網站：為 QA 實例安裝配置 QA 的資料、記錄備份、日誌和共用卷（標記為"QA 實例安裝"）。
- 主網站節點使用 HANA 系統複製與 DR 節點同步。 
- [全域覆蓋](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用於將 ExpressRoute 電路連結在一起，在區域網路之間建立私人網路絡。

## <a name="scale-out-with-dr-using-hsr"></a>使用 HSR 使用 DR 橫向擴展
 
此拓撲支援具 DR 相應放大中的多個節點。 您可以要求此拓撲，不論是否具有待命節點。 主網站節點使用 HANA 系統複製（非同步模式）與 DR 網站節點同步。


### <a name="architecture-diagram"></a>架構圖  

[![使用 HSR 使用 DR 橫向擴展](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>乙太網路
下列網路介面已預先設定：

| NIC 邏輯介面 | SKU 類型 | 以 SUSE 作業系統命名 | 以 RHEL 作業系統命名 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 類型一 | eth0.tenant | eno1.tenant | 用戶端到 HLI/HSR |
| B | 類型一 | eth2.tenant | eno3.tenant | 節點到節點通信 |
| C | 類型一 | eth1.tenant | eno2.tenant | 節點到存儲 |
| D | 類型一 | eth4.tenant | eno4.tenant | 已設定但未使用 |
| A | 類型二 | vlan\<租戶 無> | team0.tenant | 用戶端到 HLI/HSR |
| B | 類型二 | vlan\<租戶 No+2> | team0.tenant+2 | 節點到節點通信 |
| C | 類型二 | vlan\<租戶 No+1> | team0.tenant+1 | 節點到存儲 |
| D | 類型二 | vlan\<租戶 No+3> | team0.tenant+3 | 已設定但未使用 |

### <a name="storage"></a>存放裝置
預配置了以下安裝點：

| 掛接點 | 使用案例 | 
| --- | --- |
|**在主要節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |
|**在 DR 節點上**|
|HANA/shared | 用於生產 SID 的 HANA 安裝 | 
|/hana/data/SID/mnt00001 | 用於生產 SID 的資料檔案安裝 | 
|/hana/log/SID/mnt00001 | 用於生產 SID 的日誌檔安裝 | 
|/hana/logbackups/SID | 適用於生產 SID 的重做記錄 |


### <a name="key-considerations"></a>主要考量
- /usr/sap/SID 是 /hana/shared/SID 的符號連結。
- 在 DR 網站：為 DR HLI 單元的生產 HANA 實例安裝配置卷和裝載點。 
- 主網站節點使用 HANA 系統複製與 DR 節點同步。 
- [全域覆蓋](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用於將 ExpressRoute 電路連結在一起，在區域網路之間建立私人網路絡。


## <a name="next-steps"></a>後續步驟
- HANA 大型實例的[基礎架構和連接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- HANA 大型實例[的高可用性和災害復原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
