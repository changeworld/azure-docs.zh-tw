---
title: 私有雲 VMware 元件
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述如何在私有雲上安裝 VMware 元件
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279503"
---
# <a name="private-cloud-vmware-components"></a>私有雲 VMware 元件

私有雲是一個孤立的 VMware 堆疊（ESXi 主機、vCenter、vSAN 和 NSX），由管理域中的 vCenter 伺服器管理。  CloudSimple 服務允許您在 Azure 位置的 Azure 裸機基礎結構上本機部署 VMware。  私有雲與 Azure 雲的其餘部分集成。  使用以下 VMware 堆疊元件部署私有雲：

* **VMware ESXi -** Azure 專用節點上的虛擬機器管理程式
* **VMware vCenter -** 用於私有雲 vSphere 環境集中管理的裝置
* **VMware vSAN -** 超融合基礎架構解決方案
* **VMware NSX 資料中心 -** 網路虛擬化和安全軟體  

## <a name="vmware-component-versions"></a>VMware 元件版本

私有雲 VMware 堆疊使用以下軟體版本部署。

| 元件 | 版本 | 許可版本 |
|-----------|---------|------------------|
| ESXi | 6.7U2 | 企業加號 |
| vCenter | 6.7U2 | vCenter 標準 |
| vSAN | 6.7 | Enterprise |
| NSX 資料中心 | 2.4.1 | 進階 |

## <a name="esxi"></a>ESXi

當您創建私有雲時，VMware ESXi 安裝在預配的雲簡單節點上。  ESXi 提供用於部署工作負載虛擬機器 （VM） 的虛擬機器。  節點在私有雲上提供超融合基礎架構（計算和存儲）。  節點是私有雲上的 vSphere 群集的一部分。  每個節點有四個物理網路介面連接到底層網路。  兩個物理網路介面用於在 vCenter 上創建**vSphere 分散式交換器 （VDS），** 兩個介面用於創建**NSX 管理的虛擬分散式交換器 （N-VDS）。**  網路介面在主動-主動模式下配置，以便獲得高可用性。

瞭解有關 VMware ESXi 的更多

## <a name="vcenter-server-appliance"></a>vCenter 伺服器設備

vCenter 伺服器設備 （VCSA） 通過雲簡單為 VMware 解決方案提供身份驗證、管理和編排功能。 創建私有雲時，將部署具有嵌入式平臺服務控制器 （PSC） 的 VCSA。  VCSA 部署在部署私有雲時創建的 vSphere 群集上。  每個私有雲都有自己的 VCSA。  私有雲的擴展會將節點添加到私有雲上的 VCSA。

### <a name="vcenter-single-sign-on"></a>vCenter 單點登錄

VCSA 上的嵌入式平臺服務控制器與**vCenter 單點登錄域**相關聯。  功能變數名稱是**雲簡單.local。**  將創建一**CloudOwner@cloudsimple.com**個預設使用者，供您訪問 vCenter。  您可以[為 vCenter](set-vcenter-identity.md)添加本地/Azure 活動目錄標識源。

## <a name="vsan-storage"></a>vSAN 存儲

私有雲使用群集本地的完全配置全快閃記憶體 vSAN 存儲創建。  創建具有 vSAN 資料存儲的 vSphere 群集至少需要三個具有相同 SKU 的節點。  預設情況下，在 vSAN 資料存儲上啟用重復資料消除和壓縮。  在 vSphere 群集的每個節點上創建兩個磁片組。 每個磁片組包含一個緩存磁片和三個容量磁片。

在 vSphere 群集上創建預設 vSAN 存儲策略，並應用於 vSAN 資料存儲。  此策略確定如何在資料存儲中預配和分配 VM 存儲物件，以確保所需的服務等級。  存儲策略定義 **"無法容忍故障 "（FTT）** 和**故障容差方法**。  您可以創建新的存儲策略並將其應用於 VM。 要維護 SLA，必須在 vSAN 資料存儲上保持 25% 的備用容量。  

### <a name="default-vsan-storage-policy"></a>預設 vSAN 存儲策略

下表顯示了預設 vSAN 存儲策略參數。

| vSphere 群集中的節點數 | FTT | 故障容差方法 |
|------------------------------------|-----|--------------------------|
| 3 個節點和 4 個節點 | 1 | RAID 1（鏡像） - 創建 2 個副本 |
| 5 到 16 個節點 | 2 | RAID 1（鏡像） - 創建 3 個副本 |

## <a name="nsx-data-center"></a>NSX 資料中心

NSX 資料中心在您的私有雲上提供網路虛擬化、微分段和網路安全功能。  您可以通過 NSX 配置 NSX 資料中心支援的所有服務。  創建私有雲時，將安裝並配置以下 NSX 元件。

* NSXT 管理器
* 運輸區
* 主機和邊緣上行鏈路設定檔
* 邊緣傳輸、Ext1 和 Ext2 的邏輯交換器
* ESXi 傳輸節點的 IP 池
* 邊緣傳輸節點的 IP 池
* 邊緣節點
* 用於控制器和邊緣 VM 的 DRS 抗關聯規則
* 第 0 層路由器
* 在第 0 層路由器上啟用 BGP

## <a name="vsphere-cluster"></a>vSphere 群集

ESXi 主機配置為群集，以確保私有雲的高可用性。  創建私有雲時，vSphere 的管理元件將部署在第一個群集上。  為管理元件創建資源池，並在此資源池中部署所有管理 VM。 無法刪除第一個群集以收縮私有雲。  vSphere 群集為使用**vSphere HA**的 VM 提供高可用性。  不能容忍的失敗取決於群集中的可用節點數。  您可以使用要容忍的失敗數```Number of nodes = 2N+1```的```N```公式。

### <a name="vsphere-cluster-limits"></a>vSphere 群集限制

| 資源 | 限制 |
|----------|-------|
| 創建私有雲的最小節點數（第一個 vSphere 群集） | 3 |
| 私有雲上的 vSphere 群集中的最大節點數 | 16 |
| 私有雲中的最大節點數 | 64 |
| 私有雲中最大 vSphere 群集數 | 21 |
| 新 vSphere 群集上的最小節點數 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基礎設施維護

有時，有必要更改 VMware 基礎結構的配置。 目前，這些間隔可以每 1-2 個月發生一次，但頻率預計會隨著時間的推移而降低。 這種類型的維護通常可以在不中斷雲簡單服務的正常使用的情況下進行。 在 VMware 維護期間，以下服務將繼續運行，沒有任何影響：

* VMware 管理平面和應用
* vCenter 訪問
* 所有網路和存儲
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升級

CloudSimple 負責私有雲中 VMware 軟體（ESXi、vCenter、PSC 和 NSX）的生命週期管理。

軟體更新包括：

* **補丁**。 VMware 發佈的安全修補程式或錯誤修復。
* **更新**. VMware 堆疊元件的次要版本更改。
* **升級**。 VMware 堆疊元件的主要版本更改。

CloudSimple 在 VMware 提供關鍵安全修補程式後，立即測試它。 根據 SLA，CloudSimple 將在一周內將安全修補程式推出到私有雲環境。

CloudSimple 提供 VMware 軟體元件的季度維護更新。 當新的主要版本的 VMware 軟體可用時，CloudSimple 與客戶合作，協調一個合適的維護視窗進行升級。  

## <a name="next-steps"></a>後續步驟

* [雲簡單維護和更新](cloudsimple-maintenance-updates.md)
