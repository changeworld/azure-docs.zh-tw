---
title: 私用雲端 VMware 元件
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解 CloudSimple 服務如何讓您以原生方式在 Azure 位置部署 VMware。 私人雲端與 Azure 雲端的其餘部分整合。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142497"
---
# <a name="private-cloud-vmware-components"></a>私用雲端 VMware 元件

私人雲端是一種隔離的 VMware 堆疊 (ESXi 主機、vCenter、vSAN 和 NSX) 環境是由管理網域中的 vCenter server 所管理。  CloudSimple 服務可讓您以原生方式在 azure 位置的 Azure 裸機基礎結構上部署 VMware。  私人雲端與 Azure 雲端的其餘部分整合。  私人雲端會使用下列 VMware stack 元件進行部署：

* **VMware ESXi-** Azure 專用節點上的虛擬程式
* **VMware vCenter-** 用於集中管理私用雲端 vSphere 環境的設備
* **VMware vSAN-** 超融合式基礎結構解決方案
* **VMWARE NSX 資料中心-** 網路虛擬化和安全性軟體  

## <a name="vmware-component-versions"></a>VMware 元件版本

使用下列軟體版本部署私用雲端 VMware 堆疊。

| 元件 | 版本 | 授權版本 |
|-----------|---------|------------------|
| ESXi | 6.7 u2 | Enterprise Plus |
| vCenter | 6.7 u2 | vCenter 標準 |
| vSAN | 6.7 | 企業 |
| NSX 資料中心 | 2.4.1 | 進階 |

## <a name="esxi"></a>ESXi

當您建立私人雲端時，會在已布建的 CloudSimple 節點上安裝 VMware ESXi。  ESXi 會提供 (Vm) 部署工作負載虛擬機器的虛擬程式。  節點會在您的私人雲端上，提供 (計算和儲存體) 的超融合式基礎結構。  節點是私人雲端上 vSphere 叢集的一部分。  每個節點都有四個連接到基礎網路的實體網路介面。  有兩個實體網路介面可用來建立 **VSphere 分散式交換器 (** 在 vCenter 上) ，而兩個則用來建立 **受 NSX 管理的虛擬分散式交換器 (N VDS) **。  網路介面會以主動-主動模式設定，以提供高可用性。

深入瞭解 VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter server 設備

vCenter server 設備 (VCSA) 提供 VMware Solution by CloudSimple 的驗證、管理和協調流程功能。 當您建立私人雲端時，會部署 VCSA 與 embedded Platform Services Controller (PSC) 。  VCSA 會部署在您部署私用雲端時所建立的 vSphere 叢集上。  每個私用雲端都有自己的 VCSA。  私用雲端的擴充將節點新增至私人雲端上的 VCSA。

### <a name="vcenter-single-sign-on"></a>vCenter 單一登入

VCSA 上的內嵌 Platform Services Controller 與 **VCenter 單一 Sign-On 網域**相關聯。  功能變數名稱為 **cloudsimple. local**。  **CloudOwner@cloudsimple.com**系統會為您建立預設使用者以存取 vCenter。  您可以為 vCenter 新增內部部署/Azure active directory 身分 [識別來源](set-vcenter-identity.md)。

## <a name="vsan-storage"></a>vSAN 儲存體

私用雲端是使用完整設定的全快閃 vSAN 儲存體（叢集本機）來建立。  建立具有 vSAN 資料存放區的 vSphere 叢集時，需要相同 SKU 的三個節點的最小值。  預設會在 vSAN 資料存放區上啟用重復資料刪除和壓縮。  VSphere 叢集的每個節點上都會建立兩個磁片群組。 每個磁片群組都包含一個快取磁片和三個容量磁片。

在 vSphere 叢集上建立預設 vSAN 儲存體原則，並套用至 vSAN 資料存放區。  此原則會決定如何在資料存放區內布建和配置 VM 儲存體物件，以保證所需的服務層級。  儲存體原則會定義 **可容忍 (FTT) ** 和 **失敗容錯方法**的失敗。  您可以建立新的存放裝置原則，並將其套用至 Vm。 為了維持 SLA，您必須在 vSAN 資料存放區中維護25% 的備用容量。  

### <a name="default-vsan-storage-policy"></a>預設 vSAN 儲存原則

下表顯示預設 vSAN 儲存體原則參數。

| VSphere 叢集中的節點數目 | FTT | 失敗容錯方法 |
|------------------------------------|-----|--------------------------|
| 3和4個節點 | 1 | RAID 1 (鏡像) -建立2個複本 |
| 5至16個節點 | 2 | RAID 1 (鏡像) -建立3個複本 |

## <a name="nsx-data-center"></a>NSX 資料中心

NSX 資料中心在您的私人雲端提供網路虛擬化、微分割和網路安全性功能。  您可以透過 NSX 設定您私人雲端上的 NSX 資料中心所支援的所有服務。  當您建立私人雲端時，會安裝並設定下列 NSX 元件。

* NSXT 管理員
* 傳輸區域
* 主機和邊緣上行連結設定檔
* Edge Transport、Ext1 和 Ext2 的邏輯交換器
* ESXi 傳輸節點的 IP 集區
* Edge Transport 節點的 IP 集區
* 邊緣節點
* 適用于控制器和邊緣 Vm 的 DRS 反親和性規則
* 第0層路由器
* 在 Tier0 路由器上啟用 BGP

## <a name="vsphere-cluster"></a>vSphere 叢集

ESXi 主機會設定為叢集，以確保私用雲端的高可用性。  當您建立私人雲端時，vSphere 的管理元件會部署在第一個叢集上。  系統會為管理元件建立資源集區，而且所有管理 Vm 都會部署在此資源集區中。 無法刪除第一個叢集以縮減私用雲端。  vSphere 叢集會為使用 **VSPHERE HA**的 vm 提供高可用性。  容許的失敗取決於叢集中可用的節點數目。  您可以使用公式， ```Number of nodes = 2N+1``` 其中 ```N``` 是要容許的失敗數目。

### <a name="vsphere-cluster-limits"></a>vSphere 叢集限制

| 資源 | 限制 |
|----------|-------|
| 建立私人雲端 (第一個 vSphere 叢集) 的最小節點數目 | 3 |
| 私人雲端上 vSphere 叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 私人雲端中的 vSphere 叢集數目上限 | 21 |
| 新 vSphere 叢集中的節點數目下限 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基礎結構維護

有時候必須變更 VMware 基礎結構的設定。 目前，這些間隔每1-2 個月會發生一次，但頻率預期會隨著時間而拒絕。 這種類型的維護通常可以在不中斷 CloudSimple 服務的一般耗用量的情況下完成。 在 VMware 維護間隔期間，下列服務會繼續運作，而不會產生任何影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有網路和存放裝置
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升級

CloudSimple 負責管理 VMware 軟體的生命週期管理 (ESXi、vCenter、PSC 和 NSX) 在私用雲端中。

軟體更新包括：

* **修補程式**。 VMware 發行的安全性修補程式或錯誤修正。
* **更新**。 VMware 堆疊元件的次要版本變更。
* **升級**。 VMware 堆疊元件的主要版本變更。

CloudSimple 可從 VMware 取得重大安全性修補程式。 根據 SLA，CloudSimple 會在一周內推出私用雲端環境的安全性修補程式。

CloudSimple 提供 VMware 軟體元件的每季維護更新。 當 VMware 軟體有新的主要版本可供使用時，CloudSimple 會與客戶合作，以協調適當的維護時段以進行升級。  

## <a name="next-steps"></a>後續步驟

* [CloudSimple 維護和更新](cloudsimple-maintenance-updates.md)
